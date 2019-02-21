<template>
  <div class="player-field">
    <table>
      <tr v-for="(items,i) in field" :key="i">
        <td v-for="(item,j) in items" :key="j">
          <span v-if="item==0" class="empty"></span>
          <span v-else-if="item>0" class="alive"></span>
          <span v-else-if="item=='n'" class="nosence"></span>
          <span v-else-if="item=='m'" class="miss"></span>
          <span v-else-if="item<0" class="hit"></span>
          <span v-else class="dead"></span>
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  props: ["turn", "ships"],
  data() {
    return {
      field: new Array(10),
      firstHit: []
    };
  },

  watch: {
    turn: "shot"
  },
  methods: {
    shot() {
      let i,
        j,
        hit = false;
      do {
        i = Math.floor(Math.random() * 10);
        j = Math.floor(Math.random() * 10);
      } while (!(this.field[i][j] >= 0));
      if (this.firstHit[2]) {
        if (
          this.firstHit[0] - 1 >= 0 &&
          this.field[this.firstHit[0] - 1][this.firstHit[1]] >= 0
        ) {
          i = this.firstHit[0] - 1;
          j = this.firstHit[1];
        } else if (
          this.firstHit[1] - 1 >= 0 &&
          this.field[this.firstHit[0]][this.firstHit[1] - 1] >= 0
        ) {
          i = this.firstHit[0];
          j = this.firstHit[1] - 1;
        } else if (
          this.firstHit[0] + 1 < 10 &&
          this.field[this.firstHit[0] + 1][this.firstHit[1]] >= 0
        ) {
          i = this.firstHit[0] + 1;
          j = this.firstHit[1];
        } else if (
          this.firstHit[1] + 1 < 10 &&
          this.field[this.firstHit[0]][this.firstHit[1] + 1] >= 0
        ) {
          i = this.firstHit[0];
          j = this.firstHit[1] + 1;
        }
      }
      let item = this.field[i][j];
      do {
        if (this.turn == 1) {
          if (item == 0) {
            hit = false;
            this.field[i][j] = "m";
            if (this.firstHit.length == 2) this.firstHit[2] = 1;
            this.$emit("nextturn", 0);
          } else {
            hit = true;
            if (this.firstHit.length == 0) this.firstHit = [i, j];
            if (this.ships[item] != undefined)
              for (let k = 0, a = this.ships[item]; k < a.length; k++) {
                if (a[k] != undefined && a[k][0] == i && a[k][1] == j) {
                  this.field[i][j] = -item;
                  if (i - 1 >= 0 && j - 1 >= 0 && this.field[i - 1][j - 1] == 0)
                    this.field[i - 1][j - 1] = "n";
                  if (i - 1 >= 0 && j + 1 < 10 && this.field[i - 1][j + 1] == 0)
                    this.field[i - 1][j + 1] = "n";
                  if (i + 1 < 10 && j - 1 >= 0 && this.field[i + 1][j - 1] == 0)
                    this.field[i + 1][j - 1] = "n";
                  if (i + 1 < 10 && j + 1 < 10 && this.field[i + 1][j + 1] == 0)
                    this.field[i + 1][j + 1] = "n";
                  this.ships[item][k][2] = 1;
                  if (this.ships[item].every(item => item[2])) {
                    this.firstHit = [];
                    this.ships[item].forEach(item => {
                      this.field[item[0]][item[1]] = "d";
                      if (
                        item[0] - 1 >= 0 &&
                        this.field[item[0] - 1][item[1]] == 0
                      )
                        this.field[item[0] - 1][item[1]] = "n";
                      if (
                        item[1] - 1 >= 0 &&
                        this.field[item[0]][item[1] - 1] == 0
                      )
                        this.field[item[0]][item[1] - 1] = "n";
                      if (
                        item[0] + 1 < 10 &&
                        this.field[item[0] + 1][item[1]] == 0
                      )
                        this.field[item[0] + 1][item[1]] = "n";
                      if (
                        item[1] + 1 < 10 &&
                        this.field[item[0]][item[1] + 1] == 0
                      )
                        this.field[item[0]][item[1] + 1] = "n";
                    });
                    delete this.ships[item];
                  }
                }
              }
            let next = [],
              t = 0;
            if (i - 1 >= 0 && this.field[i - 1][j] >= 0) next[t++] = [i - 1, j];
            else if (i - 1 < 0)
              next[t++] = [this.firstHit[0] + 1, this.firstHit[1]];
            if (j - 1 >= 0 && this.field[i][j - 1] >= 0) next[t++] = [i, j - 1];
            else if (j - 1 < 0)
              next[t++] = [this.firstHit[0], this.firstHit[1] + 1];
            if (i + 1 < 10 && this.field[i + 1][j] >= 0) next[t++] = [i + 1, j];
            else if (i + 1 == 10)
              next[t++] = [this.firstHit[0] - 1, this.firstHit[1]];
            if (j + 1 < 10 && this.field[i][j + 1] >= 0) next[t++] = [i, j + 1];
            else if (j + 1 == 10)
              next[t++] = [this.firstHit[0], this.firstHit[1] + 1];
            if (next.length > 0) {
              next = next[Math.floor(Math.random() * next.length)];
              i = next[0];
              j = next[1];
            }
            if (i == undefined || j == undefined || next.length == 0)
              do {
                i = Math.floor(Math.random() * 10);
                j = Math.floor(Math.random() * 10);
              } while (!(this.field[i][j] >= 0));
            item = this.field[i][j];
          }
          this.$set(this.field, i, this.field[i]);
        }
        if (this.ships.every(item => item == undefined)) {
          alert("you lost!");
          break;
        }
      } while (hit);
    }
  },
  created() {
    for (let i = 0; i < 10; i++) {
      this.field[i] = new Array(10);
      for (let j = 0; j < 10; j++) this.field[i][j] = 0;
    }
    for (let i = 1; i < this.ships.length; i++)
      this.ships[i].forEach(item => (this.field[item[0]][item[1]] = i));
  }
};
</script>

<style>
td {
  height: 25px;
  width: 25px;
  text-align: center;
  border: 1px solid black;
  padding: 0;
  margin: -2px -1px -1px 0px;
  display: inline-block;
}
td span {
  display: block;
  height: 100%;
  width: 100%;
  user-select: none;
}
div {
  float: left;
}
</style>
