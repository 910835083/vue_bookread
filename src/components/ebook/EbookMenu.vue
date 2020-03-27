<template>
<div>
    <transition name="slide-up">
      <div class="menu-wrapper" :class="{'hide-box-shadow':!menuVisible || settingVisible >= 0}" v-show="menuVisible">
        <div class="icon-wrapper">
          <span class="iconfont iconmenu" @click="showSetting(3)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="iconfont iconprogress" @click="showSetting(2)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="iconfont iconbright" @click="showSetting(1)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="iconfont iconA" @click="showSetting(0)"></span>
        </div>
      </div>
    </transition>
    <EbookSettingFont></EbookSettingFont>
    <EbookSettingFontPopup></EbookSettingFontPopup>
</div>
</template>

<script>
import EbookSettingFont from '../ebook/EbookSettingFont';
import EbookSettingFontPopup from '../ebook/EbookSettingFontPopup';
import {ebookMixin} from '../../utills/mixin'
export default {
  mixins: [ebookMixin],
  components: {
      EbookSettingFont,
      EbookSettingFontPopup
  },
  props: {

  },
  data() {
    return {
    
    }
  },
  methods: {
  showSetting(key){
  this.setSettingVisible(key)
  }
  }
}

</script>

<style lang='scss' scoped>
@import '../../assets/styles/global';
.menu-wrapper {
    position: absolute;
    bottom: 0;
    left: 0;
    z-index: 102;
    display: flex;
    width: 100%;
    height: px2rem(48);
    background: white;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, .15);
    font-size: px2rem(20);
    &.hide-box-shadow {
      box-shadow: none;
    }
    .icon-wrapper {
      flex: 1;
      @include center;
      .icon-progress {
        font-size: px2rem(28);
      }
      .icon-bright {
        font-size: px2rem(22);
      }
    }
}
</style>
