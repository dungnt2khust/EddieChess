<template>
  <div class="promote">
    <div class="promote__overlay"></div>
    <div class="promote__main">
      <div
        :key="index"
        v-for="(item, index) in listChessman"
        class="promote__item"
        @click="$emit('promote', item.name)"
      >
        <img :src="type ? item.srcWhite : item.srcBlack" />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "PromotePopup",
  props: {
    type: {
      type: Boolean,
      default: true,
    },
  },
  data() {
    return {
      listChessman: [
        {
          name: "Rock",
          srcWhite:
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/wr.png",
          srcBlack:
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/br.png",
        },
        {
          name: "Knight",
          srcWhite:
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/wn.png",
          srcBlack:
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/bn.png",
        },
        {
          name: "Bishop",
          srcWhite:
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/wb.png",
          srcBlack:
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/bb.png",
        },
        {
          name: "Queen",
          srcWhite:
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/wq.png",
          srcBlack:
            "https://images.chesscomfiles.com/chess-themes/pieces/neo/150/bq.png",
        },
      ],
    };
  },
};
</script>

<style>
.promote {
  position: fixed;
  top: 0;
  bottom: 0;
  right: 0;
  left: 0;
}

.promote .promote__overlay {
  background-color: rgba(0, 0, 0, 0.7);
  height: 100%;
}

.promote .promote__main {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #fff;
  border-radius: 8px;
  display: flex;
}

.promote .promote__main .promote__item {
  margin: 2px 4px;
  padding: 8px 10px;
  cursor: pointer;
}

.promote .promote__main .promote__item img {
  height: 100px;
}

.promote .promote__main .promote__item:hover {
  background: radial-gradient(#fff, blue);
  /* background: radial-gradient(yellow, #e66465); */
}
</style>