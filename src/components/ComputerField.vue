<template>
  <div class="computer-field">
    <table>
      <tr v-for="(items,i) in field" :key="i">
        <td v-for="(item,j) in items" :key="j">
          <span v-if="!result && item>0" class="alive"></span>
          <span v-else-if="item>=0" class="empty" @click="shot(i,j,item)"></span>
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
  data() {
    return {
      ships: [],
      field: []
    };
  },
  props: ["turn", "shipCreation", "result"],
  methods: {
    shot(i, j, item) {
      if (item == 0) {
        this.field[i][j] = "m";
        this.$emit("nextturn", 1);
      } else
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
              this.ships[item].forEach(item => {
                this.field[item[0]][item[1]] = "d";
                if (item[0] - 1 >= 0 && this.field[item[0] - 1][item[1]] == 0)
                  this.field[item[0] - 1][item[1]] = "n";
                if (item[1] - 1 >= 0 && this.field[item[0]][item[1] - 1] == 0)
                  this.field[item[0]][item[1] - 1] = "n";
                if (item[0] + 1 < 10 && this.field[item[0] + 1][item[1]] == 0)
                  this.field[item[0] + 1][item[1]] = "n";
                if (item[1] - 1 < 10 && this.field[item[0]][item[1] + 1] == 0)
                  this.field[item[0]][item[1] + 1] = "n";
              });
              delete this.ships[item];
            }
          }
        }
      this.$set(this.field, i, this.field[i]);
      if (this.ships.every(item => item == undefined)) this.$emit("endgame", 1);
    }
  },
  created() {
    this.ships = [undefined].concat(this.shipCreation());
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
.empty {
  background: #fff;
}
.alive {
  background: url("../assets/alive.png");
}
.hit {
  background: url("../assets/hit.png"), url("../assets/alive.png");
}
.dead {
  background: url("../assets/dead.png");
}
.miss {
  background: url(/img/miss.60ae0c4b.png) no-repeat 7px 8px;
  background-size: 32% !important;
}
.nosence {
  background: url("../assets/nosence.png");
}
</style>
