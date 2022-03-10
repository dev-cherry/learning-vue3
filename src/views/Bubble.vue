<template>
  <div class="bubble">
    <div class="bubble-content" ref="bubbleContent">
      <canvas id="canvas"></canvas>
    </div>
    <div class="bubble-handle-bar">
      <el-button type="primary" @click="dialogVisible = true"
        >点下试试</el-button
      >
    </div>
  </div>
  <BaseDialog
    v-if="dialogVisible"
    v-model:num="num"
    @confirm="confirm"
  ></BaseDialog>
</template>

<script>
import BaseDialog from "../components/BaseDialog.vue";
export default {
  name: "Bubble",
  components: {
    BaseDialog,
  },
  data() {
    return {
      canvas: null,
      context: null,
      speedMin: -2,
      speedMax: 2,
      FPS: 1000 / 60,
      x: 0,
      y: 0,
      points: [],
      dialogVisible: false,
      num: 10,
      startTimer: null,
    };
  },
  mounted() {
    this.initCanvas();
    // this.start();
    // this.run();
  },
  methods: {
    initCanvas() {
      this.canvas = document.getElementById("canvas");
      this.canvas.width = this.$refs.bubbleContent.clientWidth;
      this.canvas.height = this.$refs.bubbleContent.clientHeight;
      this.context = this.canvas.getContext("2d");
    },
    start() {
      this.addCir();
      clearInterval(this.startTimer);
      this.startTimer = setInterval(() => {
        if (this.points.length <= this.num) {
          this.addCir();
        } else {
          alert("泡泡吐完了");
          clearInterval(this.startTimer);
        }
      }, 1000);
    },
    run() {
      const _w = this.canvas.width;
      const _h = this.canvas.height;
      this.context.clearRect(0, 0, _w, _h);
      this.context.strokeStyle = "rgba(255,255,255,0)";
      for (let i = 0; i < this.points.length; i++) {
        this.drawCir(this.points[i]);
        this.isKnock(i);
        this.move(this.points[i]);
      }
      requestAnimationFrame(this.run);
    },
    //碰撞算法
    isKnock(j) {
      let _tPoint = this.points[j];
      for (let i = 0; i < this.points.length; i++) {
        if (i != j) {
          let _x =
            this.points[i].x + this.points[i].sx - (_tPoint.x + _tPoint.sx);
          let _y =
            this.points[i].y + this.points[i].sy - (_tPoint.y + _tPoint.sy);
          if (Math.sqrt(_x * _x + _y * _y) <= this.points[i].yr + _tPoint.yr) {
            let dvx = _tPoint.sx - this.points[i].sx;
            let dvy = _tPoint.sy - this.points[i].sy;
            let dx = _tPoint.x - this.points[i].x;
            let dy = _tPoint.y - this.points[i].y;
            let xx_yy = dx * dx + dy * dy;
            let v_dvx = (dvx * dx * dx + dvy * dx * dy) / xx_yy;
            let v_dvy = (dvy * dy * dy + dvx * dx * dy) / xx_yy;
            //碰撞后的速度
            _tPoint.sx = this.checkSpeed(_tPoint.sx - v_dvx);
            _tPoint.sy = this.checkSpeed(_tPoint.sy - v_dvy);
            this.points[i].sx = this.checkSpeed(this.points[i].sx + v_dvx);
            this.points[i].sy = this.checkSpeed(this.points[i].sy + v_dvy);
          }
        }
      }
    },
    checkSpeed(speed) {
      if (speed < this.speedMin) {
        speed = this.speedMin;
      } else if (speed > this.speedMax) {
        speed = this.speedMax;
      }
      return speed;
    },
    addCir() {
      const _w = this.canvas.width;
      const _h = this.canvas.height;
      let x = this.getRandom(0, _w);
      let y = this.getRandom(0, _h);
      let yr = this.getRandom(30, 60);
      let isDown = false;
      for (let i = 0; i < this.points.length; i++) {
        let _tLength = Math.sqrt(
          Math.pow(this.points[i].x - x, 2) + Math.pow(this.points[i].y - y, 2)
        );
        if (_tLength <= yr + this.points[i].yr) {
          isDown = true;
        }
      }
      if (x - yr <= 0 || x + yr >= _w || y - yr <= 0 || y + yr >= _h) {
        isDown = true;
      }
      if (isDown) {
        this.addCir();
      } else {
        this.points.push({
          x: x,
          y: y,
          yr: yr,
          sx: this.getRandom(this.speedMin, this.speedMax),
          sy: this.getRandom(this.speedMin, this.speedMax),
          cirimg: this.getCirimg(this.getRandom(this.speedMin, this.speedMax)),
        });
      }
    },
    drawCir(point) {
      this.context.beginPath();
      this.context.lineWidth = 1;
      this.context.arc(point.x, point.y, point.yr, 0, 2 * Math.PI, true);
      this.context.drawImage(
        point.cirimg,
        point.x - point.yr,
        point.y - point.yr,
        point.yr * 2,
        point.yr * 2
      );
      this.context.stroke();
    },
    move(point) {
      const _w = this.canvas.width;
      const _h = this.canvas.height;
      if (point.x - point.yr <= 0) {
        point.sx = -point.sx;
      } else if (point.x + point.yr >= _w) {
        point.sx = -point.sx;
      }
      if (point.y - point.yr <= 0) {
        point.sy = -point.sy;
      } else if (point.y + point.yr >= _h) {
        point.sy = -point.sy;
      }
      point.x = point.x + point.sx;
      point.y = point.y + point.sy;
    },
    getRandom(min, max) {
      return Math.floor(Math.random() * (max - min)) + min;
    },
    getCirimg(random) {
      let circleImg = new Image();
      let src = require(`../assets/bubble/circle${
        random < 0 ? "1" : random > 0 ? "2" : ""
      }.png`);
      circleImg.src = src;
      return circleImg;
    },
    confirm() {
      this.points = [];
      this.start();
      this.run();
      this.dialogVisible = false;
    },
  },
};
</script>

<style lang="scss" scoped>
.bubble {
  display: flex;
  flex-direction: column;
  height: 100%;
  &-content {
    flex: 1;
  }
  &-handle-bar {
    height: 80px;
    padding: 10px 0 60px;
    text-align: center;
  }
}
</style>
