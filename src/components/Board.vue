<template>
  <div class="main position-relative">
    <table class="board position-absolute">
      <tr class="vertical" v-for="vertical in 8" :key="vertical">
        <td
          class="horizontal"
          :class="[
            (vertical % 2 && horizontal % 2) ||
            (vertical % 2 == 0 && horizontal % 2 == 0)
              ? 'dark'
              : 'light',
            { 'have-chessman': determindChessman(vertical, horizontal) },
            {
              'is-currchessman':
                vertical == currChessman.y && horizontal == currChessman.x,
            },
            {
              'is-suggestposition': isSuggestPosition(horizontal, vertical),
            },
          ]"
          @click="clickToSquare(vertical, horizontal)"
          v-for="horizontal in 8"
          :key="horizontal"
        >
          <Chessman :type="determindChessman(vertical, horizontal)" />
        </td>
      </tr>
    </table>
    <!-- <div class="controls">
      <div class="turn">{{ turn ? "White turn" : "Black turn" }}</div>
      <div class="restart" @click="restart">Restart</div>
    </div> -->
    <PromotePopup
      v-if="showPromotePopup"
      :type="turn"
      @promote="handlePromotePawn"
    />
  </div>
</template>

<script>
import { EnumChessman } from "../models/enums/EnumChessman";
import { InitPositionChessman } from "../models/models/InitPositionChessman";
import Chessman from "./Chessman.vue";
import PromotePopup from "./PromotePopup.vue";

export default {
  name: "Board",
  components: {
    Chessman,
    PromotePopup,
  },
  data() {
    return {
      positions: [...InitPositionChessman],
      currChessman: { y: 0, x: 0 },
      nextStepSuggestions: [{ y: 0, x: 0 }],
      turn: true, // luot di true: white , false: black,
      knightPosition: [
        { x: 1, y: 2 },
        { x: 1, y: -2 },
        { x: -1, y: 2 },
        { x: -1, y: -2 },
        { x: 2, y: -1 },
        { x: 2, y: 1 },
        { x: -2, y: -1 },
        { x: -2, y: 1 },
      ],
      showPromotePopup: false,
      promotePawnName: "",
      // rock one - king, rock two - queen
      kingsideCastle: {
        Black: true,
        White: true,
      },
      queensideCastle: {
        Black: true,
        White: true,
      },
    };
  },
  methods: {
    /**
     * Dinh nghia quan co nao dang o vi tri nay
     * created by ntdung5 13.02.2022
     */
    determindChessman(vertical, horizontal) {
      var chessman = this.positions.find((position) => {
        return position.y === vertical && position.x === horizontal;
      });
      if (chessman) return chessman.name;
      else return null;
    },
    /**
     * Xu ly su kien click vao o trong ban co
     * created by ntdung5 13.02.2022
     */
    clickToSquare(vertical, horizontal) {
      var chessman = this.determindChessman(vertical, horizontal);
      if (this.isSuggestPosition(horizontal, vertical)) {
        var currChessman = this.determindChessman(
          this.currChessman.y,
          this.currChessman.x
        );
        var foundIdx = this.positions.findIndex(
          (position) => position.name == currChessman
        );
        // Voi truong hop nhap thanh
        if (
          currChessman.includes("King") &&
          chessman &&
          chessman.includes("Rock")
        ) {
          var idxKing, idxRock;
          this.positions.forEach((position, index) => {
            if (position.name == currChessman) {
              idxKing = index;
            }
            if (position.name == chessman) {
              idxRock = index;
            }
          });
          if (currChessman.includes("White") && chessman.includes("White")) {
            this.kingsideCastle.White = false;
            this.queensideCastle.White = false;
          }
          if (currChessman.includes("Black") && chessman.includes("Black")) {
            this.kingsideCastle.Black = false;
            this.queensideCastle.Black = false;
          }
          if (chessman.includes("One")) {
            this.$set(this.positions, idxKing, {
              name: currChessman,
              x: this.currChessman.x - 2,
              y: this.currChessman.y,
            });
            this.$set(this.positions, idxRock, {
              name: chessman,
              x: horizontal + 2,
              y: vertical,
            });
          }
          if (chessman.includes("Two")) {
            this.$set(this.positions, idxKing, {
              name: currChessman,
              x: this.currChessman.x + 2,
              y: this.currChessman.y,
            });
            this.$set(this.positions, idxRock, {
              name: chessman,
              x: horizontal - 3,
              y: vertical,
            });
          }
          this.currChessman = { x: 0, y: 0 };
          this.turn = !this.turn;
          alert("Nhap thanh");
          this.nextStepSuggestions = [];
          return;
        }

        if (foundIdx != -1) {
          // Dat vi tri moi
          this.$set(this.positions, foundIdx, {
            name: currChessman,
            x: horizontal,
            y: vertical,
          });
          // Xac dinh dieu kien nhap thanh
          switch (currChessman) {
            case EnumChessman.White.Rock.One:
              this.kingsideCastle.White = false;
              break;
            case EnumChessman.White.Rock.Two:
              this.queensideCastle.White = false;
              break;
            case EnumChessman.Black.Rock.One:
              this.kingsideCastle.Black = false;
              break;
            case EnumChessman.Black.Rock.Two:
              this.queensideCastle.Black = false;
              break;
            case EnumChessman.Black.King:
              this.kingsideCastle.Black = false;
              this.queensideCastle.Black = false;
              break;
            case EnumChessman.White.King:
              this.kingsideCastle.White = false;
              this.queensideCastle.White = false;
              break;
            default:
              break;
          }
          // Kiem tra phong tot
          if (
            currChessman.includes("Pawn") &&
            (vertical == 1 || vertical == 8)
          ) {
            if (currChessman.includes("Black"))
              this.checkPromotePawn(
                vertical,
                horizontal,
                "Black",
                currChessman
              );
            else
              this.checkPromotePawn(
                vertical,
                horizontal,
                "White",
                currChessman
              );
          }
          // Doi luot
          else this.turn = !this.turn;
        }
        if (chessman) {
          // Xoa quan da bi an
          var rejectChessman = this.positions.findIndex(
            (position) => position.name == chessman
          );
          if (rejectChessman != -1) {
            this.$set(this.positions, rejectChessman, {
              name: chessman,
              x: 0,
              y: 0,
            });
          }
        }
        // Kiem tra het co
        setTimeout(() => {
          this.checkEndGame();
        }, 500);

        this.currChessman = { y: 0, x: 0 };
        this.nextStepSuggestions = [];
      } else {
        if (chessman) {
          // Kiem tra dung luot
          if (
            (chessman.includes("Black") && !this.turn) ||
            (chessman.includes("White") && this.turn)
          ) {
            this.currChessman = { y: vertical, x: horizontal };
            // Goi x nuoc di tiep theo
            this.suggestNextStep(chessman, vertical, horizontal);
          } else {
            this.currChessman = { y: 0, x: 0 };
            this.nextStepSuggestions = [];
            setTimeout(() => {
              if (this.turn) alert("This is White turn");
              else alert("This is Black turn");
            }, 100);
          }
        } else {
          // Neu dang duoc chon quan co thi thiet lap nuoc tiep theo
          this.currChessman = { y: 0, x: 0 };
          this.nextStepSuggestions = [];
        }
      }
    },
    /**
     * Goi x nuoc di tiep theo
     * created by ntdung5 13.02.2022
     */
    suggestNextStep(name, vertical, horizontal) {
      var initPosition = InitPositionChessman.find(
        (chessman) => chessman.name == name
      );
      this.nextStepSuggestions = [];
      switch (name) {
        case EnumChessman.White.Pawn.One:
        case EnumChessman.White.Pawn.Two:
        case EnumChessman.White.Pawn.Three:
        case EnumChessman.White.Pawn.Four:
        case EnumChessman.White.Pawn.Five:
        case EnumChessman.White.Pawn.Six:
        case EnumChessman.White.Pawn.Seven:
        case EnumChessman.White.Pawn.Eight:
          // Kiem tra nuoc tot dau tien
          if (
            this.isInitPosition(initPosition, { x: horizontal, y: vertical })
          ) {
            this.nextStepSuggestions.push({ y: vertical + 2, x: horizontal });
          }
          // Kiem tra nuoc tot dang truoc
          if (!this.determindChessman(vertical + 1, horizontal))
            this.nextStepSuggestions.push({ y: vertical + 1, x: horizontal });
          // Kiem tra an tot
          if (
            this.determindChessman(vertical + 1, horizontal + 1) &&
            this.determindChessman(vertical + 1, horizontal + 1).includes(
              "Black"
            )
          ) {
            this.nextStepSuggestions.push({
              y: vertical + 1,
              x: horizontal + 1,
            });
          }
          if (
            this.determindChessman(vertical + 1, horizontal - 1) &&
            this.determindChessman(vertical + 1, horizontal - 1).includes(
              "Black"
            )
          ) {
            this.nextStepSuggestions.push({
              y: vertical + 1,
              x: horizontal - 1,
            });
          }
          break;
        case EnumChessman.White.Rock.One:
        case EnumChessman.White.Rock.Two:
        case EnumChessman.White.Rock.Three:
        case EnumChessman.White.Rock.Four:
        case EnumChessman.White.Rock.Five:
        case EnumChessman.White.Rock.Six:
        case EnumChessman.White.Rock.Seven:
        case EnumChessman.White.Rock.Eight:
        case EnumChessman.White.Rock.Night:
        case EnumChessman.White.Rock.Ten:
          this.suggestRock(vertical, horizontal, "Black");
          break;
        case EnumChessman.White.Knight.One:
        case EnumChessman.White.Knight.Two:
        case EnumChessman.White.Knight.Three:
        case EnumChessman.White.Knight.Four:
        case EnumChessman.White.Knight.Five:
        case EnumChessman.White.Knight.Six:
        case EnumChessman.White.Knight.Seven:
        case EnumChessman.White.Knight.Eight:
        case EnumChessman.White.Knight.Night:
        case EnumChessman.White.Knight.Ten:
          this.suggestKnight(vertical, horizontal, "Black");
          break;
        case EnumChessman.White.Bishop.One:
        case EnumChessman.White.Bishop.Two:
        case EnumChessman.White.Bishop.Three:
        case EnumChessman.White.Bishop.Four:
        case EnumChessman.White.Bishop.Five:
        case EnumChessman.White.Bishop.Six:
        case EnumChessman.White.Bishop.Seven:
        case EnumChessman.White.Bishop.Eight:
        case EnumChessman.White.Bishop.Night:
        case EnumChessman.White.Bishop.Ten:
          this.suggestBishop(vertical, horizontal, "Black");
          break;
        case EnumChessman.White.King:
          this.suggestKing(vertical, horizontal, "Black");
          break;
        case EnumChessman.White.Queen.One:
        case EnumChessman.White.Queen.Two:
        case EnumChessman.White.Queen.Three:
        case EnumChessman.White.Queen.Four:
        case EnumChessman.White.Queen.Five:
        case EnumChessman.White.Queen.Six:
        case EnumChessman.White.Queen.Seven:
        case EnumChessman.White.Queen.Eight:
        case EnumChessman.White.Queen.Night:
          this.suggestRock(vertical, horizontal, "Black");
          this.suggestBishop(vertical, horizontal, "Black");
          break;
        case EnumChessman.Black.Pawn.One:
        case EnumChessman.Black.Pawn.Two:
        case EnumChessman.Black.Pawn.Three:
        case EnumChessman.Black.Pawn.Four:
        case EnumChessman.Black.Pawn.Five:
        case EnumChessman.Black.Pawn.Six:
        case EnumChessman.Black.Pawn.Seven:
        case EnumChessman.Black.Pawn.Eight:
          if (
            this.isInitPosition(initPosition, { x: horizontal, y: vertical })
          ) {
            this.nextStepSuggestions.push({ y: vertical - 2, x: horizontal });
          }
          if (!this.determindChessman(vertical - 1, horizontal))
            this.nextStepSuggestions.push({ y: vertical - 1, x: horizontal });
          // Kiem tra an tot
          if (
            this.determindChessman(vertical - 1, horizontal + 1) &&
            this.determindChessman(vertical - 1, horizontal + 1).includes(
              "White"
            )
          ) {
            this.nextStepSuggestions.push({
              y: vertical - 1,
              x: horizontal + 1,
            });
          }
          if (
            this.determindChessman(vertical - 1, horizontal - 1) &&
            this.determindChessman(vertical - 1, horizontal - 1).includes(
              "White"
            )
          ) {
            this.nextStepSuggestions.push({
              y: vertical - 1,
              x: horizontal - 1,
            });
          }

          break;
        case EnumChessman.Black.Rock.One:
        case EnumChessman.Black.Rock.Two:
        case EnumChessman.Black.Rock.Three:
        case EnumChessman.Black.Rock.Four:
        case EnumChessman.Black.Rock.Five:
        case EnumChessman.Black.Rock.Six:
        case EnumChessman.Black.Rock.Seven:
        case EnumChessman.Black.Rock.Eight:
        case EnumChessman.Black.Rock.Night:
        case EnumChessman.Black.Rock.Ten:
          this.suggestRock(vertical, horizontal, "White");
          break;
        case EnumChessman.Black.Knight.One:
        case EnumChessman.Black.Knight.Two:
        case EnumChessman.Black.Knight.Three:
        case EnumChessman.Black.Knight.Four:
        case EnumChessman.Black.Knight.Five:
        case EnumChessman.Black.Knight.Six:
        case EnumChessman.Black.Knight.Seven:
        case EnumChessman.Black.Knight.Eight:
        case EnumChessman.Black.Knight.Night:
        case EnumChessman.Black.Knight.Ten:
          this.suggestKnight(vertical, horizontal, "White");
          break;
        case EnumChessman.Black.Bishop.One:
        case EnumChessman.Black.Bishop.Two:
        case EnumChessman.Black.Bishop.Three:
        case EnumChessman.Black.Bishop.Four:
        case EnumChessman.Black.Bishop.Five:
        case EnumChessman.Black.Bishop.Six:
        case EnumChessman.Black.Bishop.Seven:
        case EnumChessman.Black.Bishop.Eight:
        case EnumChessman.Black.Bishop.Night:
        case EnumChessman.Black.Bishop.Ten:
          this.suggestBishop(vertical, horizontal, "White");
          break;
        case EnumChessman.Black.King:
          this.suggestKing(vertical, horizontal, "White");
          break;
        case EnumChessman.Black.Queen.One:
        case EnumChessman.Black.Queen.Two:
        case EnumChessman.Black.Queen.Three:
        case EnumChessman.Black.Queen.Four:
        case EnumChessman.Black.Queen.Five:
        case EnumChessman.Black.Queen.Six:
        case EnumChessman.Black.Queen.Seven:
        case EnumChessman.Black.Queen.Eight:
        case EnumChessman.Black.Queen.Night:
          this.suggestRock(vertical, horizontal, "White");
          this.suggestBishop(vertical, horizontal, "White");
          break;
        default:
          break;
      }
    },
    /**
     * Kiem tra co phai dang o vi tri ban dau khong
     * created by ntdung 14.02.2022
     */
    isInitPosition(initPosition, currPosition) {
      return (
        initPosition.x == currPosition.x && initPosition.y == currPosition.y
      );
    },
    /**
     * Kiem tra co nam trong nhung nuoc duoc goi y khong
     * created by ntdung 14.02.2022
     */
    isSuggestPosition(x, y) {
      return (
        this.nextStepSuggestions.findIndex(
          (suggestion) => suggestion.x == x && suggestion.y == y
        ) != -1
      );
    },
    /**
     * Kiem tra het co
     * created by ntdung 14.02.2022
     */
    checkEndGame() {
      var blackKing = this.positions.find(
        (position) => position.name == EnumChessman.Black.King
      );
      var whiteKing = this.positions.find(
        (position) => position.name == EnumChessman.White.King
      );
      if (!blackKing.x && !blackKing.y) {
        alert("White wins");
        this.restart();
      }
      if (!whiteKing.x && !whiteKing.y) {
        alert("Black wins");
        this.restart();
      }
    },
    /**
     * Bat dau lai
     * created by ntdung 03.03.2022
     */
    restart() {
      this.turn = true;
      this.positions = [...InitPositionChessman];
      this.kingsideCastle = { White: true, Black: true };
      this.queensideCastle = { White: true, Black: true };
      this.showPromotePopup = false;
    },
    /**
     * Goi y quan tuong
     * created by ntdung 14.02.2022
     */
    suggestBishop(vertical, horizontal, nameDeleted) {
      var cloneVertical, cloneHorizontal;
      cloneVertical = vertical + 1;
      cloneHorizontal = horizontal + 1;
      while (true) {
        if (cloneVertical > 8 || cloneHorizontal > 8) break;
        let chessman = this.determindChessman(cloneVertical, cloneHorizontal);
        if (!chessman) {
          this.nextStepSuggestions.push({
            x: cloneHorizontal,
            y: cloneVertical,
          });
        } else if (chessman.includes(nameDeleted)) {
          this.nextStepSuggestions.push({
            x: cloneHorizontal,
            y: cloneVertical,
          });
          break;
        } else break;

        cloneVertical++;
        cloneHorizontal++;
      }
      cloneVertical = vertical - 1;
      cloneHorizontal = horizontal - 1;
      while (true) {
        if (cloneVertical < 1 || cloneHorizontal < 1) break;
        let chessman = this.determindChessman(cloneVertical, cloneHorizontal);
        if (!chessman) {
          this.nextStepSuggestions.push({
            x: cloneHorizontal,
            y: cloneVertical,
          });
        } else if (chessman.includes(nameDeleted)) {
          this.nextStepSuggestions.push({
            x: cloneHorizontal,
            y: cloneVertical,
          });

          break;
        } else break;

        cloneVertical--;
        cloneHorizontal--;
      }
      cloneVertical = vertical + 1;
      cloneHorizontal = horizontal - 1;
      while (true) {
        if (cloneVertical > 8 || cloneHorizontal < 1) break;
        let chessman = this.determindChessman(cloneVertical, cloneHorizontal);
        if (!chessman) {
          this.nextStepSuggestions.push({
            x: cloneHorizontal,
            y: cloneVertical,
          });
        } else if (chessman.includes(nameDeleted)) {
          this.nextStepSuggestions.push({
            x: cloneHorizontal,
            y: cloneVertical,
          });

          break;
        } else break;

        cloneVertical++;
        cloneHorizontal--;
      }
      cloneVertical = vertical - 1;
      cloneHorizontal = horizontal + 1;
      while (true) {
        if (cloneVertical < 1 || cloneHorizontal > 8) break;
        let chessman = this.determindChessman(cloneVertical, cloneHorizontal);
        if (!chessman) {
          this.nextStepSuggestions.push({
            x: cloneHorizontal,
            y: cloneVertical,
          });
        } else if (chessman.includes(nameDeleted)) {
          this.nextStepSuggestions.push({
            x: cloneHorizontal,
            y: cloneVertical,
          });

          break;
        } else break;

        cloneVertical--;
        cloneHorizontal++;
      }
    },
    /**
     * Goi y quan xe
     * created by ntdung 14.02.2022
     */
    suggestRock(vertical, horizontal, nameDeleted) {
      for (let hor = horizontal + 1; hor <= 8; hor++) {
        let chessman = this.determindChessman(vertical, hor);
        if (!chessman) {
          this.nextStepSuggestions.push({ x: hor, y: vertical });
        } else if (chessman.includes(nameDeleted)) {
          this.nextStepSuggestions.push({ x: hor, y: vertical });
          break;
        } else break;
      }
      for (let hor = horizontal - 1; hor >= 1; hor--) {
        let chessman = this.determindChessman(vertical, hor);
        if (!chessman) {
          this.nextStepSuggestions.push({ x: hor, y: vertical });
        } else if (chessman.includes(nameDeleted)) {
          this.nextStepSuggestions.push({ x: hor, y: vertical });
          break;
        } else break;
      }
      for (let ver = vertical + 1; ver <= 8; ver++) {
        let chessman = this.determindChessman(ver, horizontal);
        if (!chessman) {
          this.nextStepSuggestions.push({ x: horizontal, y: ver });
        } else if (chessman.includes(nameDeleted)) {
          this.nextStepSuggestions.push({ x: horizontal, y: ver });
          break;
        } else break;
      }
      for (let ver = vertical - 1; ver >= 1; ver--) {
        let chessman = this.determindChessman(ver, horizontal);
        if (!chessman) {
          this.nextStepSuggestions.push({ x: horizontal, y: ver });
        } else if (chessman.includes(nameDeleted)) {
          this.nextStepSuggestions.push({ x: horizontal, y: ver });
          break;
        } else break;
      }
    },
    /**
     * Goi y quan vua
     * created by ntdung 14.02.2022
     */
    suggestKing(vertical, horizontal, nameDeleted) {
      // Kiem tra cac nuoc nhap thanh
      if (nameDeleted == "Black") {
        if (this.kingsideCastle.White) {
          if (
            !this.determindChessman(vertical, horizontal - 1) &&
            !this.determindChessman(vertical, horizontal - 2)
          ) {
            this.nextStepSuggestions.push({
              x: horizontal - 3,
              y: vertical,
            });
          }
        }
        if (this.queensideCastle.White) {
          if (
            !this.determindChessman(vertical, horizontal + 1) &&
            !this.determindChessman(vertical, horizontal + 2) &&
            !this.determindChessman(vertical, horizontal + 3)
          ) {
            this.nextStepSuggestions.push({
              x: horizontal + 4,
              y: vertical,
            });
          }
        }
      }
      if (nameDeleted == "White") {
        if (this.kingsideCastle.Black) {
          if (
            !this.determindChessman(vertical, horizontal - 1) &&
            !this.determindChessman(vertical, horizontal - 2)
          ) {
            this.nextStepSuggestions.push({
              x: horizontal - 3,
              y: vertical,
            });
          }
        }
        if (this.queensideCastle.Black) {
          if (
            !this.determindChessman(vertical, horizontal + 1) &&
            !this.determindChessman(vertical, horizontal + 2) &&
            !this.determindChessman(vertical, horizontal + 3)
          ) {
            this.nextStepSuggestions.push({
              x: horizontal + 4,
              y: vertical,
            });
          }
        }
      }
      for (var ver = -1; ver <= 1; ver++) {
        for (var hor = -1; hor <= 1; hor++) {
          if (ver || hor) {
            var chessman = this.determindChessman(
              vertical + ver,
              horizontal + hor
            );
            if (!chessman || (chessman && chessman.includes(nameDeleted))) {
              this.nextStepSuggestions.push({
                x: horizontal + hor,
                y: vertical + ver,
              });
            }
          }
        }
      }
    },
    /**
     * Goi y quan ma
     * created by ntdung 14.02.2022
     */
    suggestKnight(vertical, horizontal, nameDeleted) {
      this.knightPosition.forEach((position) => {
        var chessman = this.determindChessman(
          vertical + position.y,
          horizontal + position.x
        );
        if (!chessman || (chessman && chessman.includes(nameDeleted))) {
          this.nextStepSuggestions.push({
            x: position.x + horizontal,
            y: position.y + vertical,
          });
        }
      });
    },
    /**
     * So thanh chu
     * created by ntdung 14.02.2022
     */
    numberToString(number) {
      switch (number) {
        case 1:
          return "One";
        case 2:
          return "Two";
        case 3:
          return "Three";
        case 4:
          return "Four";
        case 5:
          return "Five";
        case 6:
          return "Six";
        case 7:
          return "Seven";
        case 8:
          return "Eight";
        case 9:
          return "Nine";
        case 10:
          return "Ten";
        default:
          return "";
      }
    },
    /**
     * Kiem tra phong tot
     * created by ntdung 14.02.2022
     */
    checkPromotePawn(vertical, horizontal, nameDeleted, promotePawnName) {
      if (vertical == 1 || vertical == 8) {
        this.showPromotePopup = true;
        this.promotePawnName = promotePawnName;
      }
    },
    /**
     * Phong tot
     * created by ntdung 14.02.2022
     */
    handlePromotePawn(name) {
      this.showPromotePopup = false;
      // Kiem tra tren ban co co bao nhieu quan roi
      var sameChessman = this.positions.filter((position) =>
        position.name.includes(this.turn ? "White" + name : "Black" + name)
      );
      // Doi vi tri tot voi quan moi
      var foundIdx = this.positions.findIndex(
        (position) => position.name == this.promotePawnName
      );
      if (foundIdx != -1) {
        this.$set(this.positions, foundIdx, {
          name:
            EnumChessman[this.turn ? "White" : "Black"][name][
              this.numberToString(sameChessman.length + 1)
            ],
          x: this.positions[foundIdx].x,
          y: this.positions[foundIdx].y,
        });
        this.turn = !this.turn;
      }
    },
  },
};
</script>

<style>
.main {
  width: calc(min(100vh, 100vw));
  padding-top: calc(min(100vh, 100vw));
  display:flex;
  overflow: hidden;
  min-height: 0;
}
.board {
  border: 1px solid #ccc;
  border-collapse: collapse;
  table-layout: auto;
  display: flex;
  flex-direction: column;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  min-width: 0;
  min-height: 0;
  overflow: hidden;
}
.board .vertical {
  flex-basis: 12.5%;
  display: flex;
  min-height: 0;
  max-height: fit-content;
}

.board .vertical .horizontal {
  border: 1px solid #ccc;
  flex-basis: 12.5%;
  min-width: 0;
}

.board .vertical .horizontal.dark {
  background-color: #ddd;
}

.board .vertical .horizontal.light {
  background-color: #fff;
}

.board .vertical .horizontal.have-chessman {
  cursor: pointer;
}

.board .vertical .horizontal.have-chessman:hover {
  background: radial-gradient(#fff, #888);
}

.board .vertical .horizontal.is-currchessman {
  background: radial-gradient(yellow, #e66465) !important;
}

.board .vertical .horizontal.is-suggestposition {
  background: radial-gradient(aqua, #9198e5) !important;
  cursor: pointer;
}


.controls {
  display: flex;
  flex-direction: column;
}

.turn {
  margin-left: 20px;
  font-size: 30px;
  padding: 10px 20px;
  background: radial-gradient(#fff, #888) !important;
}
.restart {
  margin-top: 10px;
  margin-left: 20px;
  font-size: 30px;
  padding: 10px 20px;
  background: radial-gradient(#fff, green) !important;
}
</style>