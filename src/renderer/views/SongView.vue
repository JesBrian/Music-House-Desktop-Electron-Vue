<template>
	<div id="songView">

    <!-- 上部分 -->
		<div class="song-view-top">
      <div @click="goBackView" class="go-back super-btn-out">
        <i class="super-btn-in mh-if lessen"></i>
      </div>

      <div class="song-album-container">
        <div class="song-album-img">
          <div class="box-show">
            <div :style="{'transform' : 'rotate(' + nowPlayTime / 4 % 360 + 'deg)'}" class="disk-bg">
              <img v-lazy="'http://p2.music.126.net/kaISxJU3yP0Qvw6H_vUyAQ==/18984167765401316.jpg?param=80y80'" class="glass-bg play-list-img" />
            </div>
          </div>
        </div>
        <div :class="{'active' : !$store.state.Music.playStatus}" id="playPointer">
          <img src="../../../static/images/default/Neo-Player.png" style="width:100%;"/>
        </div>
        <div class="btn-container">
          <div class="super-btn-out">
            <span class="super-btn-in mh-if non-colloection"> 喜欢</span>
          </div>
          <div @click="showModal('AddAlbum')" class="super-btn-out">
            <span class="super-btn-in mh-if collection-music"> 收藏</span>
          </div>
          <div class="super-btn-out">
            <span class="super-btn-in mh-if download"> 下载</span>
          </div>
          <div @click="showModal('Share')" class="super-btn-out">
            <span class="super-btn-in mh-if share"> 分享</span>
          </div>
        </div>
      </div>

      <div class="song-info-container">
        <div class="base-info">
          <p class="song-title">The Name of the Song</p>
          <!--<p class="" style="line-height:1.6em; font-size:15px;">sdvsdvsdbdfbhusibhfdbukvsodvsdvsdrhukvfsdbhifsdvbuigesdd</p>-->
        </div>
        <div class="lyrics-content">
          <div v-if="songLyric" style="width:100%; height:100%; overflow:auto;">
            <p v-for="(item, index) in songLyric.lyric" :class="['song-lyric-cell', {'active': index === songLyric.nowLyricIndex}]">{{ item.text }}</p>
          </div>
          <i class="mh-if question" style="right:35px; bottom:3px; position:absolute; font-size:28px;"></i>
        </div>
      </div>
    </div>

    <!-- 下部分 -->
    <div class="song-view-bottom">
      <div class="song-comment-container">
        <div class="song-comment-title">
          <i class="mh-if music-box"></i>
          <span class="song-comment-title-label">听友评论</span>
          <span class="song-comment-title-num">（已有 6946 条评论）</span>
        </div>

        <comment-total class="song-comment-total" />
      </div>

      <div class="song-recommend-container">
        <section class="song-recommend-section">
          <div class="song-recommend-title">包含这首歌曲的歌单</div>
          <div v-for="n in 3" class="box-show" style="width:100%; height:50px; margin-bottom:8px; border-radius:3px;">
            <img class="box-show" src="http://p2.music.126.net/kaISxJU3yP0Qvw6H_vUyAQ==/18984167765401316.jpg?param=80y80" style="width:35px; height:35px; margin:8px; padding:2px; float:left;"/>
            <div>
              <p class="text-hidden" style="padding:9px 0 4px;">JesBrian</p>
              <p style="font-size:13px;">播放：666888</p>
            </div>
          </div>
        </section>
        <section class="song-recommend-section">
          <div class="song-recommend-title">相似的歌曲</div>
          <div v-for="n in 5" class="box-show" style="width:100%; height:50px; margin-bottom:8px; border-radius:3px;">
            <img class="box-show" src="http://p2.music.126.net/kaISxJU3yP0Qvw6H_vUyAQ==/18984167765401316.jpg?param=80y80" style="width:35px; height:35px; margin:8px; padding:2px; float:left;"/>
            <div>
              <p class="text-hidden" style="padding:9px 0 4px;">The Name of the Song</p>
              <p style="font-size:13px;">JesBrian</p>
            </div>
          </div>
        </section>
        <section class="song-recommend-section">
          <div class="song-recommend-title">喜欢这首歌的人</div>
          <div v-for="n in 5" class="box-show" style="width:100%; height:50px; margin-bottom:8px; border-radius:3px; line-height:50px;">
            <img class="box-show" src="http://p2.music.126.net/kaISxJU3yP0Qvw6H_vUyAQ==/18984167765401316.jpg?param=80y80" style="width:35px; height:35px; margin:8px; padding:2px; float:left;"/>
            Jesbrian
          </div>
        </section>
      </div>
    </div>
	</div>
</template>

<script>
  import CommentTotal from '../components/extends/comment/CommentTotal.vue'

  export default {
    name: 'SongView',

    components: {
      CommentTotal
    },

    data () {
      return {
        nowPlayTime: 0,
        songLyric: null,
        timer: null,
        lyricTimer: null
      }
    },

    watch: {
      playStatus () {
        this.setLyricTimer()
        this.setRoateTimer()
      }
    },

    beforeCreate () {
      this.$http.get('http://music.jesbrian.local/resource/lyric/test.json').then(result => {
        this.songLyric = result.data
        this.$set(this.songLyric, 'nowLyricIndex', 0)
        this.$set(this.songLyric, 'lyricTotal', result.data.lyric.length)
      }).catch(error => {
        console.log(error)
      })
    },

    mounted () {
      this.setRoateTimer()
    },

    computed: {
      playStatus () {
        return this.$store.state.Music.playStatus
      }
    },

    methods: {
      showModal (type = '') {
        this.$store.commit('CHANGE_MODAL_TYPE', type)
      },

      goBackView () {
        this.$root.eventHub.$emit('closeCloverComponent')
        this.$store.commit('CLOSE_MUSIC_VIEW')
      },

      setLyricTimer () {
        if (this.$store.state.Music.playStatus) {
          this.lyricTimer = setInterval(() => {
            this.setNowLyricIndex(this.$store.state.Music.musicSource.currentTime)
          }, 500)
        } else if (this.lyricTimer !== null) {
          clearInterval(this.lyricTimer)
        }
      },

      setRoateTimer () {
        if (this.$store.state.Music.playStatus) {
          this.timer = setInterval(() => {
            this.nowPlayTime++
          }, 38)
        } else if (this.timer !== null) {
          clearInterval(this.timer)
        }
      },

      setNowLyricIndex (timestamp) {
        for (let i = this.songLyric.nowLyricIndex + 1; i < this.songLyric.lyricTotal; i++) {
          if (timestamp > this.songLyric.lyric[i].timestamp) {
            this.songLyric.nowLyricIndex = i
            continue
          }
          break
        }
      }
    }
  }
</script>

<style scoped>
  #songView {
    width:100%; height:100%; box-sizing:border-box; overflow:auto;
  }

  /**
   * 上部分
   */
  .song-view-top {
    width:95%; height:468px; margin:0 auto 68px; position:relative;
  }
  .go-back.super-btn-out {
    width:38px; height:38px; top:16px; right:8px; position:absolute;
  }
  .go-back > .super-btn-in {
    width:30px; height:30px; font-size:26px; line-height:32px;
  }
  /**
   * 音乐封面图片
   */
  #playPointer {
    width:138px; top:8px; left:-23px; display:inline-block; position:absolute;
    transform-origin:12.8px 12.8px;
    transform:rotate(-13deg);
    transition:transform 0.28s;
  }
  #playPointer.active {
    transform:rotate(-38deg);
  }
  .song-album-container {
    width:40%; height:100%; float:left; position:relative;
  }
  .song-album-container > .song-album-img {
    width:100%; margin:38px 0 28px; box-sizing:border-box;
  }
  .song-album-container > .song-album-img > .box-show {
    width:320px; height:320px; left:-8px; margin:12px 28px; display:inline-block; border-radius:50%;
  }
  .play-list-img {
    width:63%; height:63%; top:50%; left:50%; position:absolute; border-radius:50%; transform:translate(-50%, -50%);
  }
  .disk-bg {
    width:100%; height:100%; position:relative; background:url(../../../static/images/default/disk.png) no-repeat; background-size:100% 100%; border-radius:50%;
  }
  /**
   * 歌词区域
   */
  .lyrics-content {
    width:100%; height:438px; padding:38px 78px 56px 108px; box-sizing:border-box; background:url(../../../static/images/default/lyric-bg.png) no-repeat; background-size:100% 100%;
  }
  .song-lyric-cell {
    margin:12px 0; color:#888;
  }
  .song-lyric-cell.active {
    color:#EEE;
  }
  /**
   * 按钮组
   */
  .btn-container {
    width:88%; text-align:center;
  }
  .btn-container > .super-btn-out {
    width:72px; height:32px; margin:10px 8px 0;
  }
  .btn-container .super-btn-in {
    width:62px; height:23px; line-height:23px;
  }
  .song-info-container {
    width:58%; margin-right:18px; float:right;
  }
  .song-info-container > .base-info {
    width:92%; margin:18px auto 0; color:#DDD;
  }
  .song-info-container > .base-info > .song-title {
    line-height:2em; font-size:22px;
  }


  /**
   * 下部分
   */
  .song-view-bottom {
    width:92%; margin:0 auto 28px;
  }

  /**
   * 评论
   */
  .song-comment-container {
    width:72%; height:100%; padding:0 48px 0 5px; box-sizing:border-box; display:inline-block; position:relative;
  }
  .song-comment-title {
    width:93%; height:53px; top:-50px; left:0; margin:0 auto; padding:0 28px; position:absolute; box-sizing:border-box; box-shadow:0 3px 3px -3px #20dbfc; line-height:68px; text-shadow:1px 1px 0.5px #000;
  }
  .song-comment-title > .mh-if {
    margin-right:6px; font-size:23px; color:#00d8ff;
  }
  .song-comment-title > .song-comment-title-label {
    margin-right:3px; font-size:23px; font-weight:700; color:#CCC;
  }
  .song-comment-title > .song-comment-title-num {
    font-size:23px; color:#BBB;
  }
  .song-comment-total {
    margin-left:-12px; padding:0;
  }

  /**
   * 推荐
   */
  .song-recommend-container {
    width:28%; float:right;
  }
  .song-recommend-section {
    width:100%; margin-bottom:48px;
  }
  .song-recommend-section > .song-recommend-title {
    margin:0 0 12px; padding-left:13px; border-left:3px solid #00C4E1; font-size:21px;
  }




</style>