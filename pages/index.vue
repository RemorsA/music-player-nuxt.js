<template>

  <section class="main">
    <div class="containerApp">
      <h4 class="nameApp">easyMusicPlayer</h4>
      <div class="containerMenu">

        <div class="containerMusicCover">
          <div class="musicCover" :style="{ backgroundImage: `url(${current.cover})`}"></div>
        </div>

        <div class="containerForCurentTime">
          <span class="h4" v-if="currentTime === 0">0:00</span>
          <span class="currentTime" v-else > {{ formattedCurrentTime }}</span>
        </div>

        <div class="containerForDuration">
          <span class="h4" v-if="duration === 0">0:00</span>
          <span class="durration" v-else >{{ formattedDuration }}</span>
        </div>

        <div class="containerLine">
          <input ref="line" type="range"  @click="clickProgress" class="line" :max="duration" :value="currentTime" >
        </div>

        <div class="nameContainer">
          <h4 class="title">{{ current.title }}</h4>
          <h4 class="songName">{{ current.artist }}</h4>
        </div>

        <div class="navContainer">

          <div class="containerLoopTrack">
            <div class="loopTrack" v-if="repeat === false" @click="toggleRepeat"></div>
            <div class="loopTrack1" v-else @click="toggleRepeat"></div>
          </div>

          <div class="containerRewindLeft">
            <div class="rewindLeft" @click='rewindLeft'></div>
          </div>

          <div class="containerStopPlay">
            <div class="play" v-if='!isPlaying' @click='play'></div>
            <div class="stop" v-else @click='stop'></div>
          </div>

          <div class="containerRewindRight">
            <div class="rewindRight" @click='rewindRight'></div>
          </div>

        </div>
        <div class="containerVolume">
          <input v-model.lazy.number="volume" type="range" class="volume" min="0" max="100">
        </div>
      </div>
    </div>
  </section>
</template>

<script>
  export default {
  name: 'app',
  data () {
    return {
      current: {},
      index: 0,
      duration: 0,
      currentTime: 0,
      volume: 100,
      isPlaying: false,
      repeat: false,
      songs: [
        {
          cover: '/cover/capture0.jpg',
          title: 'Beating The Blade',
          artist: 'Capture The Crown',
          src: '/music/capture0.mp3'
        },
        {
          cover: '/cover/capture1.jpg',
          title: 'Fork Tongued',
          artist: 'Capture The Crown',
          src: '/music/capture1.mp3'
        },
        {
          cover: '/cover/capture2.jpg',
          title: 'Red Light District',
          artist: 'Capture The Crown',
          src: '/music/capture2.mp3'
        }
      ],
      player: new Audio()
    }
  },
  methods: {
    play (song) {
      if (typeof song.src != "undefined") {
        this.current = song;
        this.player.src = this.current.src;
      }
      this.player.play();
      
      this.isPlaying = true;
    },
    timeLine() {
      this.duration = Math.floor(this.player.duration)
      this.currentTime = Math.floor(this.player.currentTime)
    },
    stop () {
      this.player.pause();
      this.isPlaying = false;
    },
    rewindRight () {
      this.index++;
      if (this.index > this.songs.length - 1) {
        this.index = 0;
      }
      this.current = this.songs[this.index];
      this.play(this.current);
    },
    rewindLeft () {
      this.index--;
      if (this.index < 0) {
        this.index = this.songs.length - 1;
      }
      this.current = this.songs[this.index];
      this.play(this.current);
    },
    updateBar(x) {
      let progress = this.$refs.line
      let maxduration = this.player.duration
      let position = x - progress.offsetLeft;
      let percentage = (100 * position) / progress.offsetWidth;
      if (percentage > 100) {
        percentage = 100;
      }
      if (percentage < 0) {
        percentage = 0;
      }
      this.player.currentTime = (maxduration * percentage) / 100;
      this.player.play();
    },
    clickProgress(e) {
      this.isPlaying = true;
      this.player.pause();
      this.updateBar(e.pageX);
    },
    toggleRepeat() {
      this.repeat = !this.repeat
    }
  },
  created () {
    this.player.addEventListener('ended', function () {
      if (!this.repeat) {
        this.index++;
        if (this.index > this.songs.length - 1) {
          this.index = 0;
        }
        this.current = this.songs[this.index];
      }
      this.play(this.current);
      }.bind(this));

    this.current = this.songs[this.index];

    this.player.src = this.current.src;

    this.player.ontimeupdate = () => {
      this.timeLine();
    }
  },
  computed: {
    formattedDuration() {
      let minutes = Math.floor(this.duration / 60)
      let seconds = Math.floor(this.duration % 60)
      return minutes + ':' + seconds
    },
    formattedCurrentTime() {
      let minutes = Math.floor(this.currentTime / 60)
      let seconds = Math.floor(this.currentTime - minutes * 60)
      return minutes + ':' + seconds
    },
  },
  watch: {
    volume() {
			this.player.volume = this.volume / 100;
    }
  }
}

</script>

<style>

  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    
  }

  body {
    background-color: grey;
  }

  .h4 {
    font-family: Segoe UI;
    font-style: normal;
    font-weight: 20;
    font-size: 10px;
    color: #000000;
  }

  .containerApp {
    position: relative;
    width: 360px;
    height: 640px;
    background: #212121;
    margin: auto;
    margin-top: 100px;
  }

  .nameApp {
    font-weight: 20;
    font-size: 20px;
    text-align: center;
    color: white;
    font-family: Segoe UI;
  }

  .containerMenu {
    position: absolute;
    width: 360px;
    height: 615px;
    left: 0px;
    top: 25px;
    background: #EAEAEA;
    border-radius: 10px 10px 0px 0px;
  }

  .containerMusicCover {
    position: absolute;
    padding-left: 30px;
    padding-right: 30px;
    top: 10px;
    border-radius: 10px;
  }

  .musicCover {
    height: 300px;
    width: 300px;
  }

  .containerForCurentTime {
    position: absolute;
    top: 330px;
    padding-left: 20px;
  }

  .containerForDuration {
    position: absolute;
    top: 330px;
    padding-left: 320px;
  }

  .currentTime {
    font-family: Segoe UI;
    font-style:normal;
    font-weight: 20;
    font-size: 10px;
    color: #000000;
    padding-right: 280px;
  }

  .durration {
    font-family: Segoe UI;
    font-style: normal;
    font-weight: 20;
    font-size: 10px;
    color: #000000;
  }

  .containerLine {
    position: absolute;
    top: 350px;
    left: 10px;
  }

  .line {
    user-select: none;
    overflow: hidden;
    width: 335px;
    height: 10px;
    -webkit-appearance: none;
    background-color: #434343;
    border-radius: 5px;
    cursor: pointer;
  }
    
  .line::-webkit-slider-thumb {
    user-select: none;
    width: 10px;
    -webkit-appearance: none;
    height: 10px;
    cursor: pointer;
    background: white;
    box-shadow: -200px 25px 0 200px #CA7979;
    border-radius: 5px 5px 5px 5px;
  }

  .nameContainer {
    position: absolute;
    padding-left: 100px;
    top: 400px;
    text-align: center;
  }

  .title {
    padding-left: 10px;
    padding-right: 10px;
    font-family: Segoe UI;
    font-style: normal;
    font-weight: normal;
    font-size: 18px;
    color: #000000;
    
  }

  .songName {
    font-family: Segoe UI;
    font-style: normal;
    font-weight: normal;
    font-size: 18px;
    color: #738ED3;
    cursor: pointer;
    margin: 0px;
  }

  .navContainer {
    display: flex;
    position: absolute;
    top: 491px;
    margin-bottom: 20px;
  }

  .containerLoopTrack {
    width: auto;
    height: auto;
    margin-left: 30px;
  }

  .loopTrack {
    width: 32px;
    height: 32px;
    cursor: pointer;
    background: url(/images/repeat.svg);
  }
  .loopTrack1 {
    width: 32px;
    height: 32px;
    cursor: pointer;
    background: url(/images/repeat1.svg);
  }

  .containerRewindLeft {
    width: auto;
    height: auto;
    margin-left: 30px; 
  }
  .rewindLeft {
    width: 32px;
    height: 32px;
    cursor: pointer;
    background: url(/images/left.svg);
  }

  .containerStopPlay {
    width: auto;
    height: auto;
    margin-left: 40px;
  }
  .play {
    width: 32px;
    height: 32px;
    cursor: pointer;
    background: url(/images/play.svg);
  }
  .stop {
    width: 32px;
    height: 32px;
    cursor: pointer;
    background: url(/images/pause.svg);
  }

  .containerRewindRight {
    width: auto;
    height: auto;
    margin-left: 40px;
  }
  .rewindRight {
    width: 32px;
    height: 32px;
    cursor: pointer;
    background: url(/images/right.svg);
  }

  .containerVolume {
    position: absolute;
    top: 560px;
    left: 100px;
  }

  .volume {
    user-select: none;
    overflow: hidden;
    width: 150px;
    height: 10px;
    -webkit-appearance: none;
    background-color: #434343;
    border-radius: 5px;
    cursor: pointer;
  }

  .volume::-webkit-slider-thumb {
    user-select: none;
    width: 10px;
    -webkit-appearance: none;
    height: 10px;
    cursor: pointer;
    background: white;
    box-shadow: -200px 25px 0 200px #00528CFF;
    border-radius: 5px 5px 5px 5px;
  }
</style>