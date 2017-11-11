<template>
  <div id="app" v-touch:swipe="swipeHandler">
    <div v-if="debug">
      <!-- bind a tap event -->
      <span v-touch:tap="touchHandler">Tap Me</span>
      
      <!-- tap is the default event, you can omit it -->
      <span v-touch="touchHandler">Tap Me</span>
      
      <!-- bind the swipe event, no matter direction -->
      <span v-touch:swipe="swipeHandler">Swipe Here</span>
      
      <!-- only when swipe left can trigger the callback -->
      <span v-touch:swipe.left="swipeHandler">Swipe Here</span>
  </div>

    <!-- <input type="text" @keyup.down="move('down')"> -->
      <ul v-if="debug">
        <li>Create a simple 4x4 grid that is randomly populated with two tiles</li>
        <li>Move the tiles when user presses arrow keys</li>
        <li>Generate new tiles for every turn</li>
        <li>Merge two colliding tiles into one</li>
        <li>Apply the same color to tiles of same value</li>
        <li>Display a message when the game is ‘won’ or when no more moves can be made</li>
        <li>Add transitions when tiles are moved and merged</li>
        <li>Add touch/mobile support – swipe to move the tiles</li>
        <li>Add your own flavor and features as time permits</li>
      </ul>
      <p v-if="debug">Has empty tile: {{hasEmptyTile}}</p>
    <!-- <input type="button" value="add tile" @click="spawnTile"/> -->
    <div class="buttonWrapper center"><input type="button" value="new game" @click="newGame"/></div>
    <p v-if="debug" id="clearPanel">
      X: <input type="number" v-model="clearX" min="0" max="3">
      Y: <input type="number" v-model="clearY" min="0" max="3">
        <input type="button" value="clear cell" @click="clearGridCell(clearX, clearY)">
    </p>


<p>{{msg}}</p>
<div>
    <table>
      <tbody>
        <!-- renders one column -->
        <tr v-for="(row,y) in grid" :key="'col'+y">
          <!-- renders each cell for current column -->
          <td :class="{tile:cell}" class="cell" v-for="(cell,x) in row" :key="'row'+x">
            <span class="cords">{{x}}:{{y}}</span>
            <span class="value">{{cell}}</span>
          </td>
        </tr>
      </tbody>
    </table>
</div>
    <div v-if="debug" id="buttonPanel">
      <input type="button" class="u" value="up" @click="move('up')"/>
      <div>
        <input type="button" class="l" value="left" @click="move('left')"/>
        <input type="button" class="r" value="right" @click="move('right')"/>
      </div>
      <input type="button" class="d" value="down" @click="move('down')"/>
    </div>

    <p v-if="debug">free tiles count: {{freeTiles.length}}</p>
    <p v-if="debug">free tiles: {{freeTiles}}</p>

  </div>
</template>

<script>
import Grid from "./components/Grid.vue";

function isTile(value) {
  return Number.isInteger(value);
}

export default {
  components: { Grid },
  name: "app",
  data() {
    return {
      debug: false,
      msg: "Welcome to Your Vue.js App",
      running: false,
      score: 0,
      grid: [
        [null, null, null, null],
        [null, null, null, null],
        [null, null, null, null],
        [null, null, null, null]
      ],
      clearX: 0,
      clearY: 0
    };
  },
  created() {
    // this.msg = "requesting fullscreen"
    // document.body.webkitRequestFullscreen()
    // this.msg = "fullscreen applied"
    // document.body.requestFullscreen();
    this.newGame();
    // window.addEventListener("mousemove", console);
    document.onkeydown = event => {
      // if (this.hasEmptyTile) {
      // return;
      switch (event.keyCode) {
        case 37:
          this.move("left");
          break;
        case 38:
          this.move("up");
          break;
        case 39:
          this.move("right");
          break;
        case 40:
          this.move("down");
          break;
      }
      // }
    };
  },
  destroyed: function() {
    // window.removeEventListener("mousemove", this.move);
  },
  computed: {
    // gameOver() {},
    // isFull() {}
    freeTiles() {
      console.log("computing free tiles");
      let freeTiles = [];
      for (let y = 0; y < this.grid.length; y++) {
        for (let x = 0; x < this.grid[y].length; x++) {
          if (this.grid[y][x] === null) {
            freeTiles.push({ x: x, y: y });
          }
        }
      }
      return freeTiles;
    },
    hasEmptyTile() {
      return this.freeTiles.length > 0;
    }
  },
  methods: {
    touchHandler() {
      this.msg = "tapped";
    },
    swipeHandler(direction) {
      this.msg = "swyped " + direction; // May be left / right / top / bottom
      switch (direction) {
        // case 'left':
        //  case: 'right'
        case "top":
          this.move("up");
          break;
        case "bottom":
          this.move("down");
          break;
        default:
          this.move(direction);
      }
    },
    newGame() {
      this.grid = [];
      this.grid.push([null, null, null, null]);
      this.grid.push([null, null, null, null]);
      this.grid.push([null, null, null, null]);
      this.grid.push([null, null, null, null]);
      this.spawnTile();
      this.spawnTile();
      this.msg = "New game initiated";
    },
    getRandom(min, max) {
      return Math.floor(Math.random() * (max - min + 1) + min); // Math.random() * 4 would be ok for values 0 - 3
    },
    newTile() {
      return Math.random() < 0.5 ? 2 : 4;
    },
    spawnTile() {
      if (this.hasEmptyTile) {
        const randomFreeTile = this.freeTiles[
          this.getRandom(0, this.freeTiles.length - 1)
        ];
        let newTile = this.newTile();
        console.log("spawned new tile: " + newTile);
        this.updateGridCell(randomFreeTile.x, randomFreeTile.y, newTile);
      } else {
        this.msg = "Game over";
      }
    },
    updateGridCell(x, y, val) {
      const oldVal = this.grid[y][x];
      this.$set(this.grid[y], x, val);
      console.log(`${x}:${y}, ${oldVal} => ${val}`);
    },
    clearGridCell(x, y) {
      this.grid[y].splice(x, 1, null);
    },
    /** @augments dir direction of movement */
    move(dir) {
      console.log("key " + dir);

      const l = this.grid.length;

      switch (dir) {
        case "up":
          // check tiles from top to bottom
          // for each column, from the top, check cell, if a tile, check if next can be merged, if not move to the next cell in the row
          for (let x = l - 1; x >= 0; x--) {
            for (let y = l - 1; y > 0; y--) {
              // console.log(`${x}:${y} ${this.grid[y][x]}`);
              const currentCellContent = this.grid[y][x];
              const hasTile = Number.isInteger(currentCellContent);
              const nextCell = this.grid[y - 1][x];
              const nextCellIsEmpty = nextCell === null;
              const nextCellHasTile = Number.isInteger(nextCell);
              if (hasTile && nextCellIsEmpty) {
                console.log("move to next cell");
                this.updateGridCell(x, y - 1, currentCellContent);
                this.clearGridCell(x, y);
              }
              if (
                hasTile &&
                nextCellHasTile &&
                nextCell === currentCellContent
              ) {
                console.log("merge to next cell");
                this.updateGridCell(
                  x,
                  y - 1,
                  currentCellContent + currentCellContent
                );
                this.clearGridCell(x, y);
              }
            }
          }
          break;
        case "right":
          // right to left
          const length = this.grid.length;
          for (let x = length - 1; x >= 0; x--) {
            for (let y = length - 1; y >= 0; y--) {
              // has tile?
              const currentCell = this.grid[x][y];
              const hasTile = Number.isInteger(currentCell);
              for (let i = y - 1; i >= 0; i--) {
                let incoming = this.grid[x][i];
                if (isTile(incoming)) {
                  // aka moving
                  // let incoming = this.grid[x][i];
                  if (currentCell === incoming) {
                    // merge
                    this.grid[x].splice(y, 1, currentCell + incoming);
                    // this.grid[y].splice(x, 1, currentCell + incoming);
                    // this.grid[x][y] = currentCell + incoming;
                    this.grid[x].splice(i, 1, null);
                    // this.grid[y][i] = null;
                  } else if (currentCell === null) {
                    // move incoming to current cell
                    this.grid[x].splice(y, 1, incoming);
                    // this.grid[y].splice(x, 1, incoming);
                    // this.grid[x][y] = incoming;
                    this.grid[x].splice(i, 1, null);
                    // this.grid[y][i] = null;
                  }
                  this.grid = this.grid;
                  // this.grid[x].splice(y, 1, this.grid[x]);
                  break;
                }
              }
            }
          }
          break;
        case "down":
          // check tiles from top to bottom
          // for each column, from the top, check cell, if a tile, check if next can be merged, if not move to the next cell in the row
          for (let x = 0; x < l; x++) {
            for (let y = 0; y + 1 < l; y++) {
              console.log(`${x}:${y} ${this.grid[y][x]}`);
              const currentCellContent = this.grid[y][x];
              const hasTile = Number.isInteger(currentCellContent);
              const nextCell = this.grid[y + 1][x];
              const nextCellIsEmpty = nextCell === null;
              const nextCellHasTile = Number.isInteger(nextCell);
              if (hasTile && nextCellIsEmpty) {
                console.log("move to next cell");
                this.updateGridCell(x, y + 1, currentCellContent);
                this.clearGridCell(x, y);
              }
              if (
                hasTile &&
                nextCellHasTile &&
                nextCell === currentCellContent
              ) {
                console.log("merge to next cell");
                this.updateGridCell(
                  x,
                  y - 1,
                  currentCellContent + currentCellContent
                );
                this.clearGridCell(x, y);
              }
            }
          }
          break;
        case "left":
          // left to right
          for (let x = 0; x < this.grid.length; x++) {
            for (let y = 0; y < this.grid.length; y++) {
              // has tile?
              const currentCell = this.grid[x][y];
              const hasTile = Number.isInteger(currentCell);
              for (let i = y + 1; i < this.grid.length; i++) {
                let incoming = this.grid[x][i];
                if (isTile(incoming)) {
                  // aka moving
                  // let incoming = this.grid[x][i];
                  if (currentCell === incoming) {
                    // merge
                    this.grid[x].splice(y, 1, currentCell + incoming);
                    // this.grid[y].splice(x, 1, currentCell + incoming);
                    // this.grid[x][y] = currentCell + incoming;
                    this.grid[x].splice(i, 1, null);
                    // this.grid[y][i] = null;
                  } else if (currentCell === null) {
                    // move incoming to current cell
                    this.grid[x].splice(y, 1, incoming);
                    // this.grid[y].splice(x, 1, incoming);
                    // this.grid[x][y] = incoming;
                    this.grid[x].splice(i, 1, null);
                    // this.grid[y][i] = null;
                  }
                  // this.grid = this.grid;
                  // this.grid[x].splice(y, 1, this.grid[x]);
                  break;
                }
              }
            }
          }
          break;
      }
      this.spawnTile();
    }
  }
};
</script>

<style>
html,
body,
#app {
  height: 100%;
}
body {
  margin: 0;
}
#app {
  background-color: whitesmoke;
  /* height: 100vh; */
  /* overflow: hidden; */
}
#app > div,
table {
  margin: 0 auto;
}
.buttonWrapper {padding-top: 2em;}
p, .center {
  text-align: center;
}
td {
  text-align: center;
  border: black 1px solid;
}
.cell {
  width: 40px;
  height: 40px;
  position: relative;
}
.tile {
  background-color: antiquewhite;
}

.cell {
  background: antiquewhite;
  /* position: relative; */
  transition: all 200ms linear;
  /* left: 0; */
}

.tile {
  background: yellow;
  /* left: 50px; */
}

.cords {
  font-size: 10px;
  position: absolute;
  top: 1px;
  left: 1px;
}
#buttonPanel {
  width: 120px;
  height: 80px;
  text-align: center;
  padding-top: 2em;
}
#buttonPanel .l {
  float: left;
}
#buttonPanel .r {
  float: right;
}
#clearPanel input[type="number"] {
  padding-left: 0.8em;
  width: 2.8em;
}
</style>