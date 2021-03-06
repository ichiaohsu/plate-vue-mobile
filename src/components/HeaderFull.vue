<template>
  <header>
    <nav class="headerFull" :style="{ opacity: opacity }" v-show="defaultNav">
      <div class="headerFull__menu">
        <a @click="openSideBar()"><img src="/assets/mirrormedia/icon/hamburger_white.png" alt="開啟側邊欄"></a>
      </div>
      <div class="headerFull__logo" v-if="(showLogo === undefined || showLogo === true)">
        <router-link :to=" '/section/' + sectionName "><img :src="getSectionLogoUrl()"></router-link>
      </div>
      <div class="headerFull__link">
        <router-link to="/">
          <img class="headerFull__link--icon desktop-only" src="/assets/mirrormedia/icon/logo_black@3x.png" alt="鏡週刊 Mirror Media">
        </router-link>
        <a :href="socialLink.FACEBOOK" target="_blank">
          <img class="headerFull__link--icon desktop-only" src="/assets/mirrormedia/icon/facebook_white.png" alt="Facebook">
        </a>
        <a :href="socialLink.LINE" target="_blank">
          <img class="headerFull__link--icon desktop-only" src="/assets/mirrormedia/icon/line_white.png" alt="Line">
        </a>
        <a :href="socialLink.WEIBO" target="_blank">
          <img class="headerFull__link--icon desktop-only" src="/assets/mirrormedia/icon/weibo_white.png" alt="微博">
        </a>
        <a @click="openSearchBar()">
          <img class="headerFull__link--icon" src="/assets/mirrormedia/icon/search_white.png" alt="開啟搜尋列">
        </a>
      </div>
    </nav>
    <nav class="headerFull headerFull--black" :style="{opacity: 1-opacity}" v-show="blackNav">
      <div class="headerFull__menu">
        <a @click="openSideBar()"><img src="/assets/mirrormedia/icon/hamburger_white.png" alt="開啟側邊欄"></a>
      </div>
      <div class="headerFull__logo" v-if="(showLogo === undefined || showLogo === true)">
        <router-link :to=" '/section/' + sectionName "><img :src="getSectionLogoUrl()"></router-link>
      </div>
      <div class="headerFull__link">
        <router-link to="/">
          <img class="headerFull__link--icon desktop-only" src="/assets/mirrormedia/icon/logo_black@3x.png" alt="鏡週刊 Mirror Media">
        </router-link>
        <a :href="socialLink.FACEBOOK" target="_blank">
          <img class="headerFull__link--icon desktop-only" src="/assets/mirrormedia/icon/facebook_white.png" alt="Facebook">
        </a>
        <a :href="socialLink.LINE" target="_blank">
          <img class="headerFull__link--icon desktop-only" src="/assets/mirrormedia/icon/line_white.png" alt="Line">
        </a>
        <a :href="socialLink.WEIBO" target="_blank">
          <img class="headerFull__link--icon desktop-only" src="/assets/mirrormedia/icon/weibo_white.png" alt="微博">
        </a>
        <a @click="openSearchBar()">
          <img class="headerFull__link--icon" src="/assets/mirrormedia/icon/search_white.png" alt="開啟搜尋列">
        </a>
      </div>
    </nav>
    <div class="sidebarFull">
      <div class="sidebarFull-container" :class="{ open: openSide }">
        <div class="sidebarFull__close">
          <a class="sidebarFull__close--icon" @click="closeSideBar()">
            <img src="/assets/mirrormedia/icon/close.png" alt="關閉側邊欄">
          </a>
          <a class="sidebarFull__close--text" @click="closeSideBar()">CLOSE THE MENU</a>
        </div>
        <nav class="sidebarFull__menu">
          <router-link :to="'/section/' + sectionName" class="sidebarFull__menu--main">主頁</router-link>
          <div class="sidebarFull__menu--horizDivider"></div>
          <router-link class="sidebarFull__menu--item" :to="'/category/' + item.name" v-for="(item, i) in menuItem" v-text="item.title" :key="`${i}-${Date.now()}`"></router-link>
        </nav>
      </div>
      <div class="sidebarFull-curtain" @click="closeSideBar()" v-show="openSide"></div>
    </div>
    <div class="searchFull" v-show="openSearch">
      <div class="searchFull-container">
        <form action="." v-on:submit.prevent="search(searchVal)">
          <input type="search" placeholder="Search" @input="searchValueChange" @keyup.enter="search(searchVal)" @focusout="search(searchVal)" v-model="searchVal">
        </form>
        <a @click="closeSearchBar()">
          <img src="/assets/mirrormedia/icon/close.png" alt="關閉搜尋列">
        </a>
      </div>
      <div class="searchFull-curtain" @click="closeSearchBar()"></div>
    </div>
  </header>
</template>
<script>

import { SECTION_WATCH_ID, SOCIAL_LINK, TOPIC_WATCH_ID } from '../constants/index'
import { currentYPosition } from 'kc-scroll'
import { find, get } from 'lodash'

export default {
  name: 'header-full',
  props: [ 'commonData', 'sectionName', 'sections', 'showLogo' ],
  data () {
    return {
      isChanged: false,
      blackNav: false,
      defaultNav: true,
      opacity: 1,
      openSearch: false,
      openSide: false,
      searchVal: ''
    }
  },
  computed: {
    menuItem () {
      return get(find(get(this.sections, [ 'items' ]), { name: this.sectionName }), [ 'categories' ])
    },
    sectionLogo () {
      return get(find(get(this.commonData, [ 'sections', 'items' ]), { name: this.sectionName }), [ 'image' ], null)
    },
    socialLink () {
      return SOCIAL_LINK
    }
  },
  methods: {
    closeSearchBar () {
      this.openSearch = false
    },
    closeSideBar () {
      this.openSide = false
    },
    currentYPosition,
    getHeaderDFPHeight () {
      this.headerDFPHeight = document.getElementById('dfp-HD').offsetHeight + 35
    },
    getSectionLogoUrl () {
      if (get(this.$route, [ 'params', 'topicId' ]) === TOPIC_WATCH_ID) {
        const section = find(get(this.sections, [ 'items' ]), { id: SECTION_WATCH_ID })
        return get(section, [ 'image', 'image', 'resizedTargets', 'desktop', 'url' ])
      } else {
        return get(this.sectionLogo, [ 'image', 'url' ]) ? get(this.sectionLogo, [ 'image', 'url' ]) : '/asset/logo.png'
      }
    },
    get,
    openSearchBar () {
      this.openSearch = true
    },
    openSideBar () {
      this.openSide = true
    },
    search () {
      if (this.isChanged) {
        this.$router.push('/search/' + this.searchVal)
        this.openSearch = false
      } else {
        this.openSearch = false
      }
    },
    searchValueChange () {
      const currentKeyword = get(this.$route, [ 'params', 'keyword' ])
      if (this.searchVal !== currentKeyword && this.searchVal !== '') {
        this.isChanged = true
      } else {
        this.isChanged = false
      }
    },
    handleScroll () {
      window.onscroll = () => {
        this.opacity = 1 - this.currentYPosition() / 300
        this.opacity < 0 ? this.defaultNav = false : this.defaultNav = true
        this.opacity < 1 ? this.blackNav = true : this.blackNav = false
      }
    }
  },
  mounted () {
    this.handleScroll()
  }
}

</script>
<style lang="stylus" scoped>

header
  position relative

.headerFull
  display flex
  justify-content space-between
  align-items flex-start
  position fixed
  top 0
  left 0
  z-index 500
  width 100%
  height 85px
  padding 20px 30px 0 25px
  background linear-gradient(#000,transparent)
  
  a
    display block
    line-height 1
  
  > div
    font-size 0

  &__logo
    position absolute
    top 22px
    left 50%
    transform translateX(-50%)
    
  &__link
    display flex
    justify-content space-between
    width 250px
    &--icon
      height 20px
      
  &--black
    align-items center
    height 55px
    padding-top 0
    background-color #000
    .headerFull
      &__logo
        top 50%
        left 50%
        transform translate(-50%, -50%)

.sidebarFull
  &-container
    position fixed
    top 0
    z-index 530
    width 100%
    height 100%
    padding 20px 25px 0
    background-color #333
    transform translate3d(-100%,0,0)
    transition .5s ease
    &.open
      transform translate3d(0,0,0)
      transition .5s ease
  &-curtain
    position fixed
    top 0
    left 0
    z-index 520
    width 100%
    height 100%
    background-color rgba(0, 0, 0, .5)
  &__close
    display flex
    align-items center
    margin-bottom 46px
    a
      color #fff
    &--iconf
      font-size 0
    &--text
      margin-left 10px
      font-size 13px
  &__menu
    display flex
    flex-direction column
    align-items center
    &--main
      font-size 15px
      color #7e7e7e
      margin-bottom 20px
    &--horizDivider
      width 100%
      height 1px
      margin-bottom 28px
      background-color #fff
    &--item
      font-size 18px
      color #fff
      margin-bottom 48px

.searchFull
  &-container
    position fixed
    top 0
    left 0
    z-index 520
    display flex
    justify-content space-between
    align-items center
    width 100%
    height 55px
    padding 0 22px 0 30px
    background-color #dcdcdc
    form
      display flex
      align-items center
      flex-grow 1
      height 55px
      padding 0
      margin-right 15px
      font-size 20px
      color #8c8c8c
    input
      width 100%
      background-color transparent
      border none
      box-shadow none
    > a
      font-size 0
      
  &-curtain
    position fixed
    top 0
    left 0
    z-index 510
    width 100%
    height 100%
    background-color rgba(0, 0, 0, .5)
</style>
