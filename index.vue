<template>
  <v-container>
    <div class="container">
      <h3 v-if="turn == 1">白の順番</h3>
      <h3 v-if="turn == -1">黒の順番</h3>
      <h2>{{result}}</h2>
      <v-row v-for="i in 8" :key="i" no-gutters>
        <v-col v-for="j in 8" :key="j">
          <v-btn
            color="green"
            width="100%"
            height="60"
            @click="setCell(i-1,j-1,ban_ar,line_reverse,turn,result,setResult)"
          >
            <div v-if="(ban_ar[i-1][j-1])== 1">○</div>
            <div v-if="(ban_ar[i-1][j-1]) == -1">●</div>
          </v-btn>
        </v-col>
      </v-row>
    </div>
  </v-container>
</template>


<script>
export default {
  data() {
    return {
      turn: 1,
      ban_ar: [
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0, 0]
      ],
      result: "引き分けです"
    };
  },
  created() {
    this.turn = -1;
    //初期化
    this.ban_ar[3][3] = 1;
    this.ban_ar[3][4] = -1;
    this.ban_ar[4][3] = -1;
    this.ban_ar[4][4] = 1;
  },
  methods: {
    setCell(row, col, ban_ar, callback, turn, result, callback2) {
      this.ban_ar = ban_ar;
      callback(row, col, -1, 0, this.ban_ar, turn);
      callback(row, col, -1, 1, this.ban_ar, turn);
      callback(row, col, 0, 1, this.ban_ar, turn);
      callback(row, col, 1, 1, this.ban_ar, turn);
      callback(row, col, 1, 0, this.ban_ar, turn);
      callback(row, col, 1, -1, this.ban_ar, turn);
      callback(row, col, 0, -1, this.ban_ar, turn);
      callback(row, col, -1, -1, this.ban_ar, turn);
      let preResult = callback2(this.ban_ar);
      this.result = preResult;
      // this.$set(this.result, preResult);
      this.turn = turn * -1;
    },
    line_reverse(row_index, cell_indx, add_x, add_y, ban_ar, turn) {
      this.ban_ar = ban_ar;
      this.turn = turn;
      // 最初に今の盤状況を退避する
      var bakup = new Array(8);
      for (var i = 0; i < ban_ar.length; i++) {
        bakup[i] = new Array(8);
      }
      for (var x = 0; x < 8; x++) {
        for (var y = 0; y < 8; y++) {
          bakup[x][y] = this.ban_ar[x][y];
        }
      }

      var reverse = 0; // 裏返した数
      var turn_flg = 0; // 自分の色の石があるのか
      var x = row_index;
      var y = cell_indx;
      console.log(this.turn);

      while (true) {
        x = x + add_x;
        y = y + add_y;
        // 盤の端に到達したら抜ける
        if (x < 0 || x > 7 || y < 0 || y > 7) {
          break;
        }
        // 移動先のセルに石がなかったら抜ける
        if (ban_ar[x][y] == 0) {
          break;
        }
        // 移動先のセルが自分自身であれば、石があった事を判定し抜ける
        if (ban_ar[x][y] == this.turn) {
          turn_flg = 1;
          break;
        }

        const newRow = this.ban_ar[x].slice(0);
        newRow[y] = ban_ar[x][y] * -1;
        this.$set(this.ban_ar, x, newRow);
        reverse++;
      }

      if (reverse > 0) {
        //変更したものの、変更した先に自分の色がなかった場合
        if (turn_flg == 0) {
          for (var i = 0; i < 8; i++) {
            for (var j = 0; j < 8; j++) {
              const newRow = this.ban_ar[i].slice(0);
              newRow[j] = bakup[i][j];
              this.$set(this.ban_ar, i, newRow);
            }
          }
          reverse = 0;
        } else {
          // 
          const newRow = this.ban_ar[row_index].slice(0);
          newRow[cell_indx] = turn;
          this.$set(this.ban_ar, row_index, newRow);
        }
      }
    },
    setResult(bar_ar) {
      let white = 0;
      let black = 0;

      for (let i = 0; i < 8; i++) {
        for (let j = 0; j < 8; j++) {
          if (bar_ar[i][j] == 1) {
            white++;
          } else if (bar_ar[i][j] == -1) {
            black++;
          }
        }
      }

      if (white == black) {
        return "勝負は引分です。";
      } else if (white > black) {
        return `勝負は、黒：  ${black} 対、白：${white}で、白の勝ちです。`;
      } else {
        return `勝負は、黒：  ${black} 対、白：${white}で、黒の勝ちです。`;
      }
    }
  }
};
</script>

<style >
.container {
  width: 640px !important;
}
.arrnow {
  width: 80px !important ;
}
</style>