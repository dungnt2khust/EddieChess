<template>
  <div class="chessman">
    <img v-if="type" class="chessman__img" height="60" :src="srcImage" alt="" />
  </div>
</template>

<script>
import { EnumChessman } from "../models/enums/EnumChessman";
export default {
  name: "Chessman",
  props: {
    type: {
      type: String,
      default: null,
    },
  },
  computed: {
    srcImage() {
      var img;
      switch (this.type) {
        case EnumChessman.White.Pawn.One:
        case EnumChessman.White.Pawn.Two:
        case EnumChessman.White.Pawn.Three:
        case EnumChessman.White.Pawn.Four:
        case EnumChessman.White.Pawn.Five:
        case EnumChessman.White.Pawn.Six:
        case EnumChessman.White.Pawn.Seven:
        case EnumChessman.White.Pawn.Eight:
          // img = "../assets/image/chessman/white_pawn.png";
          img =
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/wp.png";
          break;
        case EnumChessman.White.Rock.One:
        case EnumChessman.White.Rock.Two:
        case EnumChessman.White.Rock.Three:
        case EnumChessman.White.Rock.Four:
        case EnumChessman.White.Rock.Five:
        case EnumChessman.White.Rock.Six:
        case EnumChessman.White.Rock.Seven:
        case EnumChessman.White.Rock.Eight:
        case EnumChessman.White.Rock.Nine:
        case EnumChessman.White.Rock.Ten:
          img =
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/wr.png";
          break;
        case EnumChessman.White.Knight.One:
        case EnumChessman.White.Knight.Two:
        case EnumChessman.White.Knight.Three:
        case EnumChessman.White.Knight.Four:
        case EnumChessman.White.Knight.Five:
        case EnumChessman.White.Knight.Six:
        case EnumChessman.White.Knight.Seven:
        case EnumChessman.White.Knight.Eight:
        case EnumChessman.White.Knight.Nine:
        case EnumChessman.White.Knight.Ten:
          img =
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/wn.png";
          break;
        case EnumChessman.White.Bishop.One:
        case EnumChessman.White.Bishop.Two:
        case EnumChessman.White.Bishop.Three:
        case EnumChessman.White.Bishop.Four:
        case EnumChessman.White.Bishop.Five:
        case EnumChessman.White.Bishop.Six:
        case EnumChessman.White.Bishop.Seven:
        case EnumChessman.White.Bishop.Eight:
        case EnumChessman.White.Bishop.Nine:
        case EnumChessman.White.Bishop.Ten:
          img =
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/wb.png";
          break;
        case EnumChessman.White.King:
          img =
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/wk.png";
          break;
        case EnumChessman.White.Queen.One:
        case EnumChessman.White.Queen.Two:
        case EnumChessman.White.Queen.Three:
        case EnumChessman.White.Queen.Four:
        case EnumChessman.White.Queen.Five:
        case EnumChessman.White.Queen.Six:
        case EnumChessman.White.Queen.Seven:
        case EnumChessman.White.Queen.Eight:
        case EnumChessman.White.Queen.Nine:
        case EnumChessman.White.Queen.Ten:
          img =
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/wq.png";
          break;
        case EnumChessman.Black.Pawn.One:
        case EnumChessman.Black.Pawn.Two:
        case EnumChessman.Black.Pawn.Three:
        case EnumChessman.Black.Pawn.Four:
        case EnumChessman.Black.Pawn.Five:
        case EnumChessman.Black.Pawn.Six:
        case EnumChessman.Black.Pawn.Seven:
        case EnumChessman.Black.Pawn.Eight:
          img =
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/bp.png";
          break;
        case EnumChessman.Black.Rock.One:
        case EnumChessman.Black.Rock.Two:
        case EnumChessman.Black.Rock.Three:
        case EnumChessman.Black.Rock.Four:
        case EnumChessman.Black.Rock.Five:
        case EnumChessman.Black.Rock.Six:
        case EnumChessman.Black.Rock.Seven:
        case EnumChessman.Black.Rock.Eight:
        case EnumChessman.Black.Rock.Nine:
        case EnumChessman.Black.Rock.Ten:
          img =
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/br.png";
          break;
        case EnumChessman.Black.Knight.One:
        case EnumChessman.Black.Knight.Two:
        case EnumChessman.Black.Knight.Three:
        case EnumChessman.Black.Knight.Four:
        case EnumChessman.Black.Knight.Five:
        case EnumChessman.Black.Knight.Six:
        case EnumChessman.Black.Knight.Seven:
        case EnumChessman.Black.Knight.Eight:
        case EnumChessman.Black.Knight.Nine:
        case EnumChessman.Black.Knight.Ten:
          img =
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/bn.png";
          break;
        case EnumChessman.Black.Bishop.One:
        case EnumChessman.Black.Bishop.Two:
        case EnumChessman.Black.Bishop.Three:
        case EnumChessman.Black.Bishop.Four:
        case EnumChessman.Black.Bishop.Five:
        case EnumChessman.Black.Bishop.Six:
        case EnumChessman.Black.Bishop.Seven:
        case EnumChessman.Black.Bishop.Eight:
        case EnumChessman.Black.Bishop.Nine:
        case EnumChessman.Black.Bishop.Ten:
          img =
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/bb.png";
          break;
        case EnumChessman.Black.King:
          img =
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/bk.png";
          break;
        case EnumChessman.Black.Queen.One:
        case EnumChessman.Black.Queen.Two:
        case EnumChessman.Black.Queen.Three:
        case EnumChessman.Black.Queen.Four:
        case EnumChessman.Black.Queen.Five:
        case EnumChessman.Black.Queen.Six:
        case EnumChessman.Black.Queen.Seven:
        case EnumChessman.Black.Queen.Eight:
        case EnumChessman.Black.Queen.Nine:
        case EnumChessman.Black.Queen.Ten:
          img =
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/bq.png";
          break;
        default:
          break;
      }
      return img;
    },
  },
};
</script>

<style>
.chessman {
  height: 60px;
}
</style>