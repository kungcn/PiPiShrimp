<template>
  <div class="ticket-choose">
    <el-card class="box-card">
        <div class="book-seat">
            <p class="screen">Screen</p>
            <div class="seat-group" v-for="item in items">
              <img src="/static/img/select-ed.svg" class="seat" alt="avaliable seat" v-if="item.isChosen">
              <img src="/static/img/select-ing.svg" class="seat" alt="avaliable seat" v-if="!item.isToChoose" @click="chooseHandler(item)">
              <img src="/static/img/select-will.svg" class="seat" alt="avaliable seat" v-if="item.isToChoose" @click="chooseHandler(item)">
            </div>
            <el-row class="legend">
              <img src="/static/img/select-ed.svg" class="seat" alt="avaliable seat"><span>不可选座位</span>
              <img src="/static/img/select-ing.svg" class="seat" alt="avaliable seat"><span>已选座位</span>
              <img src="/static/img/select-will.svg" class="seat" alt="avaliable seat"><span>可选座位</span>
            </el-row>
        </div>
        <div class="film-info">
          <p>影片: {{ movie.name }}</p>
          <p>影院: {{ movie.hall }}</p>
          <p>影厅: {{ movie.hnum }}号厅</p>
          <p>场次: {{ movie.tnum }}</p>
          <p>座位: 
              <span :v-if='seats.length == 0'>未选座</span>
              <el-row :v-if = 'seats.length != 0' v-for="seat in seats">
                <span>{{seat/10}}排{{seat%10}}座</span>
              </el-row>
          </p>
          <p>单价: {{ movie.per }}</p>
          <p>总计: {{ total }}</p>
          <el-row type='flex' justify="end" class="choose-btn">
            <el-button type="primary" @click="confirmHandler">确认下单</el-button>
          </el-row>
        </div>
    </el-card>

  </div>  
</template>

<script>
import Api from '../api'

export default {
  name: 'seat-choose',
  data () {
    return {
      chosen: false,
      items: [],
      seatNum: 90,
      seats:[],
      mid: 1,
      cid: 1,
      tnum: 1,
      hnum: 1,
      movie: {
        name: '神奇女侠',
        hall: '上海宝山万画影城北上海店',
        hnum: 5,
        tnum: '6月4日(周日) 20:20',
        per: 33
      },
      total: null,
      ticketNum: null
    }
  },
  mounted() {
    this.init();
  },
  computed() {
    ticketNum() {
      return this.movie.per * this.ticketNum;
    }
  }
  methods: {
    init() {
      const vm = this;
      Api.getSeat(this.mid, this.cid, this.tnum, this.hnum)
          .then(data => {
            if (data) {
              const length = 0;
              for (let i = 0; i < length; i++) {
                if (data[i] == '1') {
                  this.items.push({
                      isChosen: true,
                      isToChoose: false,
                      index: i,
                      x: parseInt(i/10) + 1,
                      y: i % 10

                  })
                }
                else if (data[i] == '0') {
                  this.items.push({
                      isChosen: false,
                      isToChoose: true,
                      index: i
                      x: parseInt(i/10) + 1,
                      y: i % 10
                  })
                }
              }
            } else {
              vm.$message({
                showClose: true,
                message: 'fail to login',
                type: 'warning'
              }
          }).catch(err => {
                vm.$message({
                  showClose: true,
                  message: 'error,try again please',
                  type: 'warning'
                })
                console.log(err)
              })
          })
    },
    chooseHandler(item) {
      if (item.isChosen != true) {
          item.isToChoose = !item.isToChoose;
          if (item.isToChoose == true) {
            this.ticketNum--;
            this.seats.splice(item.index, 1);
          } else {
            this.ticketNum++;
            this.seats.push(item.index);
          }
      } else {
        vm.$message({
          showClose: true,
          message: 'The seat is booked.',
          type: 'warning'
        })
      }
    },
    confirmHandler() {
      this.seats.split('').join('_');
    }
  }
}
</script>

<style>
.ticket-choose {
  overflow: auto;
}

.seats {
  /*width: 25px;*/
  /*height: 25px;*/

  box-sizing: border-box;
  border-radius: 4px;
  border: 1px solid #bfcbd9;
  display: inline-block;
}

.empty, .book {
  cursor: pointer;
}

.seat-group {
  display: inline-block;
}

.screen {
  box-sizing: border-box;
  border: 2px #eee solid;

  text-align: center;
  max-width: 490px;
  min-width: 490px;
  width: 490px;
}

.box-card {
  width: 80%;
  height: 90%;
  margin: auto;
  position: absolute;
  top: 100px;
  left: 0;
  right: 0;
  bottom: 0;
  text-align: left;
}

.book-seat {
  float: left;
  width: 538px;
  min-width: 538px;
  max-width: 538px;

  position: absolute;
  left: 110px;
  top: 10px;
}

.seat {
  width: 25px;
  cursor: pointer;

  margin: 8px 12px;
}

.film-info {
  float: right;
  padding: 10px;
}

.ticket-choose .el-card__body {
  padding: 0;
}

.film-info {
  background-color: #FFF5F5;
  width: 25%;
  min-height: 760px;
  overflow: hidden;
}

.choose-btn {
  position: relative;
  top: 60px;
  margin-right: 20px;
}

.legend {
  text-align: center;
}

.legend img {
  vertical-align: middle;
}
</style>
