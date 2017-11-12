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
    <div class="buttonWrapper center">
      <!-- <input type="button" value="new game" @click="newGame"/> -->
      <button @click="newGame"><span :class="{underline:altPressed}">n</span>ew game</button>
    </div>

    <p v-if="debug" id="clearPanel">
      X: <input type="number" v-model="clearX" min="0" max="3">
      Y: <input type="number" v-model="clearY" min="0" max="3">
        <input type="button" value="clear cell" @click="clearGridCell(clearX, clearY)">
    </p>


<p>{{msg}}</p>
<p>Last move: {{direction}}</p>
<p>Score: {{score}}</p>
<p>Match exists: {{hasMatches}}</p>
<div>
    <table>
      <tbody>
        <!-- renders one column -->
        <tr v-for="(row,y) in grid" :key="'col'+y">
          <!-- renders each cell for current column -->
          <td :class="classTileCell(cell,cell)" class="cell" :style="getColor(cell)" v-for="(cell,x) in row" :key="'row'+x">
            <span class="cords" v-if="debug">{{x}}:{{y}}</span>
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
      clearY: 0,
      direction: "none",
      altPressed: false,
      gameOver: false
    };
  },
  created() {
    this.newGame();
    document.addEventListener("keydown", event => {
      if (event.keyCode === 17) {
        this.altPressed = true;
      }
      if (this.altPressed && event.keyCode === 78) {
        console.log("alt + n pressed");
        this.newGame();
      }
    });
    document.addEventListener("keyup", event => {
      if (event.keyCode === 17) {
        this.altPressed = false;
      }
    });
    document.addEventListener("keydown", event => {
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
    });
  },
  destroyed: function() {},
  computed: {
    // gameOver() {},
    // isFull() {}

    hasMatches() {
      for (let y = 0; y < this.grid.length; y++) {
        for (let x = 0; x < this.grid[y].length; x++) {
          if (
            this.grid[y][x] === this.getNeighbour(x, y, "up") ||
            this.grid[y][x] === this.getNeighbour(x, y, "right") ||
            this.grid[y][x] === this.getNeighbour(x, y, "down") ||
            this.grid[y][x] === this.getNeighbour(x, y, "left")
          ) {
            return true;
          }
        }
      }
      return false;
    },
    // score() {
    //   // sums up all current scores
    //   let score = 0;
    //   for (let y = 0; y < this.grid.length; y++) {
    //     for (let x = 0; x < this.grid[y].length; x++) {
    //       if (Number.isInteger(this.grid[y][x])) {
    //         score += this.grid[y][x];
    //       }
    //     }
    //   }
    //   return score;
    // },
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
    getNeighbour(x, y, dir) {
      switch (dir) {
        case "up":
          y--;
          break;
        case "right":
          x++;
          break;
        case "down":
          y++;
          break;
        case "left":
          x--;
          break;
      }
      // validate if cords are inside grid
      if (x < 0 || x > 3 || y < 0 || y > 3) {
        return undefined;
      } else {
        return this.grid[y][x];
      }
    },
    classTileCell: function(cell) {
      const isTile = Number.isInteger(cell);
      return {
        tile: isTile,
        left: isTile && this.$data.direction === "left",
        right: isTile && this.$data.direction === "right",
        up: isTile && this.$data.direction === "up",
        down: isTile && this.$data.direction === "down"
      };
    },
    getColor(value) {
      if (value === null) {
        return "";
      }
      // let color;
      // Hue from 300 (2p) - 60 (2048p)
      // 300 - 60 = 240
      // 240 / 11 = 21.82
      let hue = 60 + Math.log(value) / Math.log(2) * 21.81;

      // 80 -> 50 (2-2048p)
      const step = Math.log(value) / Math.log(2);
      let sat = 80 - 3 * step;

      let color = `hsl(${hue}, 100%, ${sat}%)`;
      //  color = `hsl(60, 100%, 100%)`;
      return `background-color:${color};`;
    },
    touchHandler() {
      this.msg = "tapped";
    },
    swipeHandler(direction) {
      this.msg = "swyped " + direction; // May be left / right / top / bottom
      switch (direction) {
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
      this.score = 0;
      this.gameOver = false;
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
      } else if (!this.hasMatches) {
        this.msg = "Game over";
        this.gameOver = true;
      }
    },
    updateGridCell(x, y, val) {
      function validateCord(cord) {
        if (cord < 0 || cord > 3) throw `error, cord ${cord} is out of bound")`;
      }
      validateCord(x);
      validateCord(y);

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

      this.direction = dir;

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
                this.score += currentCellContent + currentCellContent;
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
          for (let y = length - 1; y >= 0; y--) {
            for (let x = length - 1; x >= 0; x--) {
              // has tile?
              const currentCell = this.grid[y][x];
              const hasTile = Number.isInteger(currentCell);
              for (let i = x - 1; i >= 0; i--) {
                let incoming = this.grid[y][i];
                if (isTile(incoming)) {
                  // incoming/ moving
                  if (currentCell === incoming) {
                    // merge
                    this.score += currentCell + incoming;
                    this.grid[y].splice(x, 1, currentCell + incoming);
                    this.grid[y].splice(i, 1, null);
                  }
                  if (currentCell === null) {
                    // move incoming to current cell
                    this.grid[y].splice(x, 1, incoming);
                    this.grid[y].splice(i, 1, null);

                    // check if incoming cell has on self incoming with same value, if so merge before move
                    for (let j = i - 1; j >= 0; j--) {}
                    this.grid[y];
                  }
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
            // last cell does not need to checked for merge or movement
            for (let y = 0; y < l - 1; y++) {
              // console.log(`${x}:${y} ${this.grid[y][x]}`);
              const currentCellContent = this.grid[y][x];
              const currentCellHasTile = Number.isInteger(currentCellContent);
              const currentCellIsEempty = currentCellContent === null;
              // next cell in movement direction
              const nextCellContent = this.grid[y + 1][x]; // below
              const nextCellIsEmpty = nextCellContent === null;
              const nextCellHasTile = Number.isInteger(nextCellContent);
              if (currentCellHasTile && nextCellIsEmpty) {
                console.log("move to next cell");
                // add animation to current cell
                this.updateGridCell(x, y + 1, currentCellContent);
                this.clearGridCell(x, y);
              }
              if (
                currentCellHasTile &&
                nextCellHasTile &&
                nextCellContent === currentCellContent
              ) {
                console.log("merge to next cell");
                this.score += currentCellContent + currentCellContent;
                this.updateGridCell(
                  x,
                  y + 1,
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
                    this.score += currentCell + incoming;
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
.buttonWrapper {
  padding-top: 2em;
}
p,
.center {
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
}

.tile {
  background: yellow;
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
.underline {
  text-decoration: underline;
}
body {
  overflow-y: hidden;
}
</style>
