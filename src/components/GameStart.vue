<template>
  <div class="player-field">
    <table @mouseleave="shipEndCreation">
      <tr v-for="(items,i) in field" :key="i">
        <td v-for="(item,j) in items" :key="j" @mouseup.left="shipEndCreation">
          <span
            v-if="item==0"
            class="empty"
            @mouseenter="shipCreation(i,j)"
            @mousedown.left="shipStartCreation(i, j)"
          ></span>
          <span
            v-else-if="item>0"
            class="alive"
            @click.right.prevent="shipDelete(item)"
            @mouseenter="shipCreationDelete(i,j,item)"
          ></span>
        </td>
      </tr>
    </table>
    <div class="buttons">
      <input type="button" value="Random" @click="randomShipsCreation" class="rnd">
      <input type="button" value="Start game" @click="startGame" class="start">
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      ships: [undefined],
      field: [],
      shipSizeOnfield: 0,
      shipCounteOnField: [4, 3, 2, 1],
      prev: [-1, -1],
      shipIsPainting: false,
      ship: []
    };
  },
  props: ["randomShipCreation"],
  methods: {
    startGame() {
      if (this.ships.filter(item => item !== undefined).length == 10)
        this.$emit("startgame", this.ships);
    },
    randomShipsCreation() {
      this.field.forEach((items, i) =>
        items.forEach((item, j) => (this.field[i][j] = 0))
      );
      this.shipCounteOnField = [0, 0, 0, 0];
      this.ships = [undefined].concat(this.randomShipCreation());
      for (let i = 1; i < this.ships.length; i++)
        this.ships[i].forEach(item => (this.field[item[0]][item[1]] = i));
      this.$set(this.field, 0, this.field[0]);
    },
    shipDelete(item) {
      this.ships[item].forEach(item => (this.field[item[0]][item[1]] = 0));
      this.shipCounteOnField[this.ships[item].length - 1]++;
      this.ships[item] = undefined;
      this.$set(this.field, 0, this.field[0]);
    },
    shipCreationDelete(i, j, item) {
      if (
        this.shipIsPainting &&
        this.ships.indexOf(item) == -1 &&
        this.ship.length != 1
      ) {
        let t = this.ship[this.ship.length - 1];
        let k = this.ship[this.ship.length - 2];
        this.field[t[0]][t[1]] = 0;
        [this.prev[0], this.prev[1]] = [k[0], k[1]];
        this.ship.splice(-1);
        if (this.ship.length == 1) this.prev.splice(-1);
        this.shipSizeOnfield--;
        this.$set(this.field, 0, this.field[0]);
      }
    },
    shipEndCreation() {
      if (
        this.ships.filter(item => item !== undefined).length < 10 &&
        this.shipIsPainting
      ) {
        if (this.shipCounteOnField[this.shipSizeOnfield - 1] != 0) {
          this.shipCounteOnField[this.shipSizeOnfield - 1]--;
          let pos;
          if (!this.ships.lastIndexOf(undefined)) pos = this.ships.length;
          else pos = this.ships.lastIndexOf(undefined);
          this.ships[pos] = this.ship;
        } else this.ship.forEach(item => (this.field[item[0]][item[1]] = 0));
        this.shipIsPainting = false;
        this.$set(this.field, 0, this.field[0]);
        this.shipSizeOnfield = 0;
        this.ship = [];
      }
    },
    shipStartCreation(i, j) {
      if (this.ships.filter(item => item !== undefined).length < 10) {
        let pos;
        if (!this.ships.lastIndexOf(undefined)) pos = this.ships.length;
        else pos = this.ships.lastIndexOf(undefined);
        if (
          (i - 1 < 0 ||
            j - 1 < 0 ||
            [0, pos].indexOf(this.field[i - 1][j - 1]) != -1) &&
          (i - 1 < 0 || [0, pos].indexOf(this.field[i - 1][j]) != -1) &&
          (j - 1 < 0 || [0, pos].indexOf(this.field[i][j - 1]) != -1) &&
          (i + 1 == 10 ||
            j + 1 == 10 ||
            [0, pos].indexOf(this.field[i + 1][j + 1]) != -1) &&
          (i - 1 < 0 ||
            j + 1 == 10 ||
            [0, pos].indexOf(this.field[i - 1][j + 1]) != -1) &&
          (i + 1 == 10 ||
            j - 1 < 0 ||
            [0, pos].indexOf(this.field[i + 1][j - 1]) != -1) &&
          (j + 1 == 10 || [0, pos].indexOf(this.field[i][j + 1]) != -1) &&
          (i + 1 == 10 || [0, pos].indexOf(this.field[i + 1][j]) != -1)
        ) {
          this.shipIsPainting = true;
          this.ship[this.ship.length] = [i, j];
          this.field[(this.prev[0] = i)][(this.prev[1] = j)] = pos;
          this.shipSizeOnfield = 1;
          this.prev = [i, j];
          this.$set(this.field, i, this.field[i]);
        }
      }
    },
    shipCreation(i, j) {
      if (this.shipIsPainting) {
        let maxShipSize;
        let pos;
        if (!this.ships.lastIndexOf(undefined)) pos = this.ships.length;
        else pos = this.ships.lastIndexOf(undefined);
        if (
          (i - 1 < 0 ||
            j - 1 < 0 ||
            [0, pos].indexOf(this.field[i - 1][j - 1]) != -1) &&
          (i - 1 < 0 || [0, pos].indexOf(this.field[i - 1][j]) != -1) &&
          (j - 1 < 0 || [0, pos].indexOf(this.field[i][j - 1]) != -1) &&
          (i + 1 == 10 ||
            j + 1 == 10 ||
            [0, pos].indexOf(this.field[i + 1][j + 1]) != -1) &&
          (i - 1 < 0 ||
            j + 1 == 10 ||
            [0, pos].indexOf(this.field[i - 1][j + 1]) != -1) &&
          (i + 1 == 10 ||
            j - 1 < 0 ||
            [0, pos].indexOf(this.field[i + 1][j - 1]) != -1) &&
          (j + 1 == 10 || [0, pos].indexOf(this.field[i][j + 1]) != -1) &&
          (i + 1 == 10 || [0, pos].indexOf(this.field[i + 1][j]) != -1)
        ) {
          for (
            maxShipSize = this.shipCounteOnField.length - 1;
            maxShipSize >= 0;
            maxShipSize--
          )
            if (this.shipCounteOnField[maxShipSize] != 0) {
              maxShipSize++;
              break;
            }
          if (
            this.shipSizeOnfield < maxShipSize &&
            ((i == this.prev[0] && Math.abs(j - this.prev[1]) == 1) ||
              (j == this.prev[1] && Math.abs(i - this.prev[0]) == 1))
          ) {
            if (this.prev[2] == undefined) {
              if (i == this.prev[0] && Math.abs(j - this.prev[1]) == 1)
                this.prev[2] = "h";
              else if (j == this.prev[1] && Math.abs(i - this.prev[0]) == 1)
                this.prev[2] = "w";

              this.field[i][j] = pos;
              this.shipSizeOnfield++;
              this.$set(this.field, i, this.field[i]);
              this.ship[this.ship.length] = [i, j];
              [this.prev[0], this.prev[1]] = [i, j];
            } else if (
              (this.prev[2] == "h" &&
                i == this.prev[0] &&
                Math.abs(j - this.prev[1]) == 1) ||
              (this.prev[2] == "w" &&
                j == this.prev[1] &&
                Math.abs(i - this.prev[0]) == 1)
            ) {
              let pos;
              if (!this.ships.lastIndexOf(undefined)) pos = this.ships.length;
              else pos = this.ships.lastIndexOf(undefined);
              this.field[i][j] = pos;
              this.shipSizeOnfield++;
              this.$set(this.field, i, this.field[i]);
              this.ship[this.ship.length] = [i, j];
              [this.prev[0], this.prev[1]] = [i, j];
            }
          }
        }
      }
    }
  },
  created() {
    for (let i = 0; i < 10; i++) {
      this.field[i] = new Array(10);
      for (let j = 0; j < 10; j++) this.field[i][j] = 0;
    }
  }
};
</script>

<style>
@font-face {
  font-family: Stroke;
  src: url("../assets/fonts/Stroke.otf");
}
input[type="button"] {
  padding: 0;
  border: 4px solid;
  border-image: url("../assets/border.png") 36 / 30px round;
  font-family: Stroke;
  border-radius: 5px;
  color: #1986be;
  margin: auto;
  width: 125px;
  height: 40px;
  font-size: 22px;
  margin-bottom: 10px;
  display: block;
  background: #fff;
}
</style>
