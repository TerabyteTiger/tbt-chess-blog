<template>
  <div :class="theme">
    <div class="header">
      <b>{{ whitePlayer }} vs. {{ blackPlayer }}</b>
      <br />
      {{ event }}
    </div>
    <div class="flex-board">
      <div class="board">
        <div class="row" v-for="r in 8" :key="r">
          <div
            class="square"
            v-for="c in 8"
            :key="c"
            v-bind:class="[
              (c & 1 && r & 1) || (!(c & 1) && !(r & 1)) ? 'black' : 'white',
            ]"
          >
            <div v-bind:class="[getPiece(r, c), 'piece']"></div>
          </div>
        </div>
      </div>
      <div class="notation">
        <div
          v-for="(moveRow, index) in Math.ceil(moves.length / 2)"
          :key="index"
        >
          {{ moveRow }}.
          <span
            class="pointer"
            :class="currentMove == index * 2 + 1 ? 'highlight' : ''"
            @click="goToMove(2 * index + 1)"
            >{{ moves[2 * index] }}</span
          >
          &nbsp;
          <span
            class="pointer"
            @click="goToMove(2 * index + 2)"
            :class="currentMove == index * 2 + 2 ? 'highlight' : ''"
            >{{ moves.length > index + 1 ? moves[2 * index + 1] : "" }}</span
          >
        </div>
      </div>
    </div>

    <div class="moveControls">
      <button @click.prevent="goToMove(0)">
        |&lt;&lt;
      </button>
      <button @click.prevent="prevMove()">
        &lt;
      </button>
      <button @click.prevent="nextMove()">&gt;</button>
      <button @click.prevent="goToMove(moves.length)">
        &gt;&gt;|
      </button>
    </div>
    <br />
    <p class="comments" v-if="Object.entries(comments).length !== 0">
      {{ comments[game.fen()] }}
    </p>
    <p class="faLicense">
      Icons provided via
      <a href="https://fontawesome.com/license">Font Awesome</a>
    </p>
  </div>
</template>

<script>
import Chess from "chess.js";
// Major Thanks to https://github.com/deemaagog/vue-pgn
// for functioning as a guideline to piece together the last bits of how chess.js works
export default {
  data() {
    return {
      game: null,
      moves: [],
      position: [],
      currentMove: 0,
      comments: {},
    };
  },
  props: {
    pgnFile: {
      type: String,
      default: "",
    },
    startPosition: {
      default: 0,
    },
    whitePlayer: {
      type: String,
      default: "Unknown",
    },
    blackPlayer: {
      type: String,
      default: "Unknown",
    },
    event: {
      type: String,
      default: "",
    },
    theme: {
      type: String,
      default: "standard",
    },
  },
  watch: {
    pgnFile: function() {
      this.loadPgn();
    },
  },
  created() {
    this.game = new Chess();
  },
  mounted() {
    this.loadPgn();
    this.currentMove = this.startPosition;
  },
  methods: {
    getPiece(row, col) {
      if (this.position.length == 0) return null;
      let piece = this.position[row - 1][col - 1];
      return piece === null ? "" : piece.color + piece.type;
    },
    loadPgn() {
      this.game.load_pgn(this.pgnFile);
      this.moves = this.game.history();
      this.comments = this.setComments();
      this.game.reset();
      this.position = this.game.board();
      this.goToMove(this.startPosition);
    },
    goToMove(moveNo) {
      if (moveNo > 0 && moveNo < this.moves.length + 1) {
        this.game.reset();
        for (var i = 0; i < moveNo; i++) {
          this.game.move(this.moves[i]);
        }
        this.position = this.game.board();
        this.currentMove = moveNo;
      }
      if (moveNo === 0) {
        this.game.reset();
        this.position = this.game.board();
        this.currentMove = 0;
      }
    },
    nextMove() {
      this.currentMove = this.currentMove + 1;
      if (this.currentMove >= this.moves.length) {
        this.currentMove = this.moves.length;
      }
      this.goToMove(this.currentMove);
    },
    prevMove() {
      this.currentMove = this.currentMove - 1;
      if (this.currentMove <= 0) {
        this.currentMove = 0;
      }
      this.goToMove(this.currentMove);
    },
    setComments() {
      let commentsArray = this.game.get_comments();
      let commentsObj = {};
      commentsArray.forEach((el) => {
        commentsObj[el.fen] = el.comment;
      });
      return commentsObj;
    },
  },
};
</script>

<style scoped>
/* Themes */
.purple {
  --darkSquares: hsl(237, 51%, 73%);
  --lightSquares: hsl(235, 100%, 95%);
}
.standard {
  --darkSquares: hsl(32, 53%, 59%);
  --lightSquares: hsl(32, 76%, 77%);
}
.pink {
  --darkSquares: hsl(298, 66%, 64%);
  --lightSquares: hsl(290, 76%, 77%);
}
.green {
  --darkSquares: hsl(100, 66%, 64%);
  --lightSquares: hsl(100, 76%, 77%);
}
.red {
  --darkSquares: rgb(224, 103, 103);
  --lightSquares: hsl(0, 76%, 77%);
}
.gray {
  --darkSquares: hsl(0, 0%, 50%);
  --lightSquares: hsl(0, 0%, 75%);
}
.grey {
  --darkSquares: hsl(0, 0%, 50%);
  --lightSquares: hsl(0, 0%, 75%);
}
.blue {
  --darkSquares: hsl(200, 66%, 64%);
  --lightSquares: hsl(200, 76%, 77%);
}
.moveControls {
  margin: 10px auto;
  text-align: center;
}
.flex-board {
  display: flex;
  justify-content: center;
}
.notation {
  width: 150px;
  max-height: 300px;
  overflow-y: auto;
  text-align: left;
  padding-left: 15px;
}
.board {
  flex-grow: 0;
  flex-shrink: 0;
  position: relative;
  height: 300px;
  width: 300px;
}
.black {
  background-color: var(--darkSquares);
}
.white {
  background-color: var(--lightSquares);
}
.square {
  width: 12.5%;
  height: 100%;
  float: left;
}
.row {
  height: 12.5%;
}
.piece {
  background-repeat: no-repeat;
  background-position: center;
  width: 100%;
  height: 100%;
}
.wp {
  background-image: url("../assets/svgs/wp.svg");
  background-size: 50%;
  filter: drop-shadow(0 0 0px var(--darkSquares));
  -webkit-filter: drop-shadow(0 0 1px var(--darkSquares));
}
.wq {
  background-image: url("../assets/svgs/wq.svg");
  background-size: 80%;
  filter: drop-shadow(0 0 0px var(--darkSquares));
  -webkit-filter: drop-shadow(0 0 1px var(--darkSquares));
}
.wk {
  background-image: url("../assets/svgs/wk.svg");
  background-size: 70%;
  filter: drop-shadow(0 0 0px var(--darkSquares));
  -webkit-filter: drop-shadow(0 0 1px var(--darkSquares));
}
.wn {
  background-image: url("../assets/svgs/wn.svg");
  background-size: 60%;
  filter: drop-shadow(0 0 0px var(--darkSquares));
  -webkit-filter: drop-shadow(0 0 1px var(--darkSquares));
}
.wb {
  background-image: url("../assets/svgs/wb.svg");
  background-size: 50%;
  filter: drop-shadow(0 0 0px var(--darkSquares));
  -webkit-filter: drop-shadow(0 0 1px var(--darkSquares));
}
.wr {
  background-image: url("../assets/svgs/wr.svg");
  background-size: 60%;
  filter: drop-shadow(0 0 0px var(--darkSquares));
  -webkit-filter: drop-shadow(0 0 1px var(--darkSquares));
}
.bp {
  background-image: url("../assets/svgs/bp.svg");
  background-size: 50%;
  filter: drop-shadow(0 0 0px #fff);
  -webkit-filter: drop-shadow(0 0 1px #fff);
}
.bq {
  background-image: url("../assets/svgs/bq.svg");
  background-size: 80%;
  filter: drop-shadow(0 0 0px #fff);
  -webkit-filter: drop-shadow(0 0 1px #fff);
}
.bk {
  background-image: url("../assets/svgs/bk.svg");
  background-size: 70%;
  filter: drop-shadow(0 0 0px #fff);
  -webkit-filter: drop-shadow(0 0 1px #fff);
}
.bn {
  background-image: url("../assets/svgs/bn.svg");
  background-size: 60%;
  filter: drop-shadow(0 0 0px #fff);
  -webkit-filter: drop-shadow(0 0 1px #fff);
}
.bb {
  background-image: url("../assets/svgs/bb.svg");
  background-size: 50%;
  filter: drop-shadow(0 0 0px #fff);
  -webkit-filter: drop-shadow(0 0 1px #fff);
}
.br {
  background-image: url("../assets/svgs/br.svg");
  background-size: 60%;
  filter: drop-shadow(0 0 0px #fff);
  -webkit-filter: drop-shadow(0 0 1px #fff);
}
.highlight {
  background-color: #fff365;
}
span {
  padding: 2px;
}
.header {
  font-size: 1.2rem;
  padding: 7px;
}

.header * {
  margin: auto;
}

.comments {
  min-height: 100px;
  overflow-y: auto;
  border: 1px solid black;
  width: 50%;
  margin: auto;
  padding: 7px;
  text-align: left;
}
.faLicense {
  text-align: center;
  color: hsl(0, 0%, 30%);
  font-style: italic;
}

button {
  min-width: 50px;
  padding: 10px 5px;
  margin: 0 10px;
}
</style>
