<template xmlns:v-lazy="http://www.w3.org/1999/xhtml">
  <div class="pCard" ref="pCard">
    <div @click="pushProfile(msg.author.id)">
      <div class="left-side avatar" v-lazy:background-image="msg.author.avatar" v-if="lazyload"></div>
      <div class="left-side avatar" :style="`background-image:url(${msg.author.avatar})`" v-else></div>
    </div>
    <div class="right-side">
      <div class="cardTopBar">
        <div class="cardTopBar-top">
          <span class="author" @click="pushProfile(msg.author.id)">{{msg.author.name}}</span>
          <span v-if="msg.band" class="band"> {{msg.band}} </span>
          <span v-if="msg.extraUser" @click="pushProfile(msg.extraUser.id)">{{msg.extraUser.name}}</span>
          <span class="operation" v-if="!msg.hideExtra"><span class="star" v-if="msg.showStar">关注</span><x-icon
            type="ios-arrow-down"
            size="15"></x-icon></span>
        </div>
        <div class="cardTopBar-extra">
          <span class="hasbeenfrom">{{relativeTime(msg.publishTime)}}</span>
          <span class="position" v-if="msg.position">
            <i class="icon-location1 iconfont icon"></i>{{msg.position.name}} · {{msg.position.distance}}
          </span>
        </div>
      </div>
      <pre class="content">{{msg.content}}</pre>
      <div
        v-if="msg.media && msg.media.type==='url'"
        class="url"
        @click="handleClickUrl(msg.media.value)"
      >
        <img src="../../assets/images/web.jpg" class="url-img"/>
        <div class="url-side">
          <p class="url-title">{{msg.media.title}}</p>
          <p class="url-domain">{{getDomain(msg.media.value)}}</p>
        </div>

      </div>
      <div
        class="media-imgs"
        v-if="msg.media && msg.media.type==='img' && msg.media.imgs.length>0"
      >
        <div
          v-once
          v-for="(src,index) of msg.media.imgs"
          class="media-muti-img"
        >
          <img
            class="imgcenter"
            v-lazy="src"
            v-lazy:background-image="src"
            @click="$event.stopPropagation()"
            :preview="`id${msg.info.id}type${msg.info.type}`"
          >
        </div>
      </div>

      <div
        v-if="msg.topic"
        class="topic-box"
      >
        <span class="topic">{{msg.topic.name}}</span>
      </div>
      <share-bar
        v-if="msg.shareInfo"
        :like="msg.shareInfo.like"
        :comment="msg.shareInfo.comment"
        :share="msg.shareInfo.share"
        :footprint="msg.footprint"
        class="shareBar"
        @onClickShareButton="handleClickShareButton"
      ></share-bar>
    </div>
  </div>
</template>

<script>
  import ShareBar from 'components/shareBar/shareBar'
  import relativeTime from 'assets/js/relativeTime'

  export default {
    name: "userMessageCard",
    components: {ShareBar},
    props: {
      msg: {
        type: Object,
        require: true,
      },
      lazyload: {
        type: Boolean,
        default: true
      }
    },
    data: () => ({
      rightSideWidth: 0
    }),
    mounted() {
      // this.$nextTick(() => {
      //   this.initRightSideWidth()
      // })
      // window.addEventListener('resize', this.initRightSideWidth)
    },
    methods: {
      pushProfile(uid) {
        this.$router.push({path: '/profile', query: {id: uid}})
        event.stopPropagation()
      },
      handleClickUrl(url) {
        this.$router.push({path: '/seo', query: {url, title: this.msg.media.title}})
        event.stopPropagation()
      },
      // handleClickImg(event, srcArray, index) {
      //   // this.$emit('onClickImg', Array.from(event.target.parentElement.parentElement.childNodes), srcArray, index)
      //   event.stopPropagation()
      // },
      // initRightSideWidth() {
      //   // console.log(window.getComputedStyle(document.querySelector('.neighborhood-card'))['width'])
      //   let p = this.$refs.pCard
      //
      //   let s = getComputedStyle(p)
      //   this.rightSideWidth = Number.parseInt(s['width']) - document.querySelector('.left-side').clientWidth
      // },
      relativeTime(t) {
        return relativeTime(t)
      },
      handleClickShareButton() {
        this.$emit('onClickShareButton', ...arguments)
      },
      getDomain(url) {
        let s = url.replace(/.+:\/\//, '')
        let l = s.indexOf('/')
        return s.substring(0, l > 0 ? l : s.length)
      }
    }
  }
</script>

<style lang="scss" scoped>
  @import "userMessageCard";
</style>
