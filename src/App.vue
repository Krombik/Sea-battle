<template>
  <div id="app">
    <GameStart
      v-if="gameStage==0"
      @startgame="startgame"
      :randomShipCreation="shipCreation"
      class="d-flex justify-content-center align-items-center"
    ></GameStart>
    <div v-else class="d-flex justify-content-center align-items-center">
      <PlayerField :turn="turn" @nextturn="nextturn" @endgame="endgame" :ships="playerShips"></PlayerField>
      <ComputerField
        :turn="turn"
        :shipCreation="shipCreation"
        :result="result"
        @endgame="endgame"
        @nextturn="nextturn"
      ></ComputerField>
      <div v-if="gameStage" class="newgame">
        <input type="button" value="New game" @click="gameStage=0; result=1">
      </div>
      <div v-else class="endgame">
        <div v-if="result==1">Congratulations, you win</div>
        <div v-else>Sorry, you lose</div>
        <input type="button" value="Play again" @click="gameStage=0; result=1">
      </div>
    </div>
  </div>
</template>

<script>
import GameStart from "./components/GameStart";
import PlayerField from "./components/PlayerField";
import ComputerField from "./components/ComputerField";

export default {
  name: "app",
  components: {
    GameStart,
    PlayerField,
    ComputerField
  },
  data() {
    return {
      turn: 0,
      gameStage: 0,
      result: 1,
      playerShips: []
    };
  },
  methods: {
    nextturn(turn) {
      this.turn = turn;
    },
    startgame(ships) {
      this.gameStage = 1;
      this.playerShips = ships;
    },
    endgame(result) {
      this.gameStage = 2;
      this.result = result;
    },
    shipCreation() {
      let type,
        pos1,
        res = [],
        temp = [];
      for (let i = 0; i < 10; i++) {
        if (i == 0) type = 3;
        else if (i < 3) type = 2;
        else if (i < 6) type = 1;
        else type = 0;
        do pos1 = Math.floor(Math.random() * 100);
        while (temp.indexOf(pos1) != -1);
        if (type != 0) {
          let pos2 = {},
            a = [],
            t = 0;
          do {
            if (pos1 - type * 10 >= 0 && temp.indexOf(pos1 - type * 10) == -1)
              pos2.t = pos1 - type * 10;
            if (pos1 + type * 10 < 100 && temp.indexOf(pos1 + type * 10) == -1)
              pos2.b = pos1 + type * 10;
            if (
              pos1 - type >= Math.floor(pos1 / 10) * 10 &&
              temp.indexOf(pos1 - type) == -1
            )
              pos2.l = pos1 - type;
            if (
              pos1 + type < Math.ceil(pos1 / 10) * 10 &&
              temp.indexOf(pos1 + type) == -1
            )
              pos2.r = pos1 + type;
            if (!pos2.t && !pos2.b && !pos2.l && !pos2.r)
              do pos1 = Math.floor(Math.random() * 100);
              while (temp.indexOf(pos1) != -1);
          } while (!pos2.t && !pos2.b && !pos2.l && !pos2.r);
          for (let key in pos2) a[t++] = [pos2[key], key];
          pos2 = a[Math.floor(Math.random() * a.length)];
          a = [];
          temp.splice(
            temp.length,
            0,
            pos1,
            pos1 - 10,
            pos1 + 10,
            pos1 - 1,
            pos1 + 1,
            pos1 - 11,
            pos1 + 11,
            pos1 - 9,
            pos1 + 9,
            pos2[0],
            pos2[0] - 10,
            pos2[0] + 10,
            pos2[0] - 1,
            pos2[0] + 1,
            pos2[0] - 11,
            pos2[0] + 11,
            pos2[0] - 9,
            pos2[0] + 9
          );
          for (let i = 0; i <= type; i++) {
            if (pos2[1] == "t")
              t = [Math.floor((pos1 - i * 10) / 10), pos1 % 10];
            else if (pos2[1] == "b")
              t = [Math.floor((pos1 + i * 10) / 10), pos1 % 10];
            else if (pos2[1] == "l")
              t = [Math.floor(pos1 / 10), (pos1 - i) % 10];
            else if (pos2[1] == "r")
              t = [Math.floor(pos1 / 10), (pos1 + i) % 10];
            a.splice(res.length, 0, t);
          }
          res.splice(res.length, 0, a);
        } else {
          temp.splice(
            temp.length,
            0,
            pos1,
            pos1 - 10,
            pos1 + 10,
            pos1 - 1,
            pos1 + 1,
            pos1 - 11,
            pos1 + 11,
            pos1 - 9,
            pos1 + 9
          );
          res.splice(res.length, 0, [[Math.floor(pos1 / 10), pos1 % 10]]);
        }
      }
      return res;
    }
  }
};
</script>

<style>
#app {
  margin-top: 30px;
  position: relative;
}
table,
.buttons {
  width: 240px;
  margin: 0 15px;
}
.endgame,
.newgame {
  font-family: Stroke;
  color: #1986be;
  text-align: center;
  font-size: 20px;
  position: absolute;
  top: 104%;
}
</style>
