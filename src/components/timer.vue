<template>
    <div class="timer">
    <span id="txt">{{txt}}</span>
    <!-- <input @click="start()" type="button" value="开始"> -->
    <!-- <input @click="stop()" type="button" value="暂停">  -->
    <!-- <input @click="reset()" type="button" value="重置">      -->

      <div class="pop" v-if="showSkipSelect">
        <span id="txt">用时过长，是否跳关？</span>
        <button @click="showSkipSelect=false" class="btn">取消</button>
        <button @click="needSkip=true" class="btn">确认</button>
      </div>
    </div>

</template>
<script>
    export default {
    name: 'Timer',
    data () {
        return { 
            timer: null,
            count: 0,
            clickStart: 0,
            txt: '00:00:00:00',
            showSkipSelect: false,
            lastSkipTime: 0,
            needSkip: false
        }
    },
    mounted(){
        // this.start()
    },
    destroyed(){
        this.stop()
    },
 
methods: {
  // 计时
  start () { // 开始
    this.clickStart++
    // if (this.clickStart % 2 === 0) { // 此处不必判断奇偶数
      clearInterval(this.timer)
      this.clickStart = 0
    // } else {
      this.timer = setInterval(() => {
      let h = parseInt(this.count / 1000 / 60 / 60)
      let m = parseInt(this.count / 1000 / 60) % 60
      let s = parseInt(this.count / 1000) % 60
      let ms = parseInt(this.count / 10) % 100
      h = h < 10 ? '0' + h : h
      m = m < 10 ? '0' + m : m
      s = s < 10 ? '0' + s : s
      ms = ms < 10 ? '0' + ms : ms
      this.txt = h + ':' + m + ':' + s + ':' + ms
      this.count += 10


      let levels = ["puzzle", "maze", "bird", "turtle", "movie", "music", "pond-tutor", "pond-duck"]
      let frame = document.getElementById('myframe')
      let pathname = frame.contentWindow.location.pathname;
      let level = pathname.split('/').pop().replace('.html', '')
      let levelNum = levels.indexOf(level)
      if (levelNum !== 7) {

        if (this.needSkip) {
          this.$router.replace(levels[levelNum + 1])
          this.$router.go(0)
          this.reset()
        }
        if (this.showSkipSelect) {
          this.lastSkipTime = this.count
        } else if (this.count - this.lastSkipTime >= 5 * 1000 * 60) {
          this.showSkipSelect = true
          this.lastSkipTime = this.count
        }
      }

      }, 10)
    // }
  },
  stop () { // 暂停
    window.clearInterval(this.timer)
  },
  reset () { // 重置
    this.count = 0
    this.clickStart = 0
    clearInterval(this.timer)
    this.txt = '00:00:00:00'
    this.lastSkipTime = 0
    this.showSkipSelect = false
    this.needSkip = false
  },
  curtime(){
    return this.txt
  }
 }
}
</script>

<style scoped>
.pop {
  background-color: #fff;

  position: fixed;
  top: 100px;
  left: 300px;
  width: calc(100% - 600px);
  height:calc(100% - 200px);
  z-index: 2
}
.btn {
  background-color: #fff;
  border-radius: 4px;
  border: 1px solid blue;
  padding: 4px 12px;
}
</style>
