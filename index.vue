<template>
  <template v-if="show">
    <div class="xp_fab" v-el:fab>
      <div>
        <span class='ink' v-show="fabInkShow"></span>
        <div v-el:sub-fabs class="sub_fab_btns_wrapper" :style="subFabWrapperStyle" v-show="subFabsShow">
          <div v-for="fab in subFabs">
            <button @click="subFabClick(fab)"
                    :data-link-title="fab.title || 'item' + ($index + 1)"
                    :data-link-href="fab.url"
                    :data-link-target="fab.target"
                    class="sub_fab_btn"
                    :style="subFabStyle(fab)">
              <span :style="subFabIconStyle(fab)">{{{ fab.icon }}}</span>
              <slot name="sub-{{icon}}"></slot>
            </button>
          </div>
        </div>
        <button v-el:main-fab
                v-if="mainFabShow"
                :draggable="draggable"
                @touchstart="handleTouchStart"
                @touchmove="handleTouchMove"
                @touchend="handleTouchEnd"
                @click="mainFabClick"
                :data-link-href="mainFabHref"
                :data-link-target="mainFabTarget"
                class="kc_fab_main_btn"
                :class="{'open': subFabsShow && subFabs}"
                :style="mainFabStyle">
          <span class="ink animate" style="height: 60px; width: 60px; top: 2.34344px; left: 11.3434px;"></span>
          <span :style="mainFabIconStyle">{{{ mainFab.icon }}}</span>
          <slot name="main"></slot>
        </button>
      </div>
    </div>
    <div class="xp_fab_overlay" v-if="fabOverlayShow" @click="subFabsShow = !subFabsShow"></div>
  </template>
</template>

<script type="text/ecmascript-6">
  export default {
    data () {
      return {
        switchPos: {},
        subFabsShow: false,
        fabInkShow: false
      }
    },
    props: {
      show: {
        type: Boolean,
        default: true
      },
      draggable: {
        type: Boolean,
        default: false
      },
      links: {
        type: [String, Array],
        require: true
      }
    },
    ready () {
      if (this.mainFab.url && (this.subFabs && this.subFabs.length > 0)) {
        console.log('url of main-fab will be ingnored, because there are sub-fabs')
      }
      var switchX = localStorage.getItem('fab_switch_x')
      var switchY = localStorage.getItem('fab_switch_y')
      if (switchX && switchY) {
        this.switchPos.x = switchX
        this.switchPos.y = switchY
        this.$els.fab.style.right = switchX + 'px'
        this.$els.fab.style.bottom = switchY + 'px'
      } else if (this.mainFab.right && this.mainFab.bottom) {
        if (typeof this.mainFab.right !== 'number' || typeof this.mainFab.bottom !== 'number') {
          console.log('right/bottom in links item must be number')
        }
        this.switchPos.x = this.mainFab.right
        this.switchPos.y = this.mainFab.bottom
        this.$els.fab.style.right = this.mainFab.right + 'px'
        this.$els.fab.style.bottom = this.mainFab.bottom + 'px'
      }
    },
    computed: {
      mainFab () {
        return this.links[0]
      },
      subFabs () {
        return this.links.slice(1)
      },
      mainFabShow () {
        return (this.mainFab.url && this.mainFab.url.length > 0) || (this.subFabs && this.subFabs.length > 0)
      },
      fabOverlayShow () {
        return this.subFabsShow
      },
      mainFabStyle () {
        let style = {}
        this.mainFab.bgcolor && _.extend(style, { backgroundColor: this.mainFab.bgcolor })
        this.mainFab.iconSize && _.extend(style, { fontSize: this.mainFab.iconSize })
        this.mainFab.width && _.extend(style, { width: this.mainFab.width })
        this.mainFab.height && _.extend(style, { height: this.mainFab.height })
        return style
      },
      mainFabIconStyle () {
        let style = {}
        return style
      },
      subFabWrapperStyle () {
        let style = {}
        this.mainFab.right && _.extend(style, { right: this.mainFab.right })
        this.mainFab.bottom && _.extend(style, { right: this.mainFab.bottom })
        return style
      }
    },
    methods: {
      subFabStyle (fab) {
        let style = {}
        fab.bgcolor && _.extend(style, { backgroundColor: fab.bgcolor })
        return style
      },
      subFabIconStyle (fab) {
        let style = {}
        fab.iconColor && _.extend(style, { color: fab.iconColor })
        return style
      },
      openHref (fab) {
        let href = fab.url
        let target = fab.target
        if (href && href.length > 0) {
          if (target) {
            window.open(href, target)
          } else {
            window.location.href = href
          }
        }
      },
      showSubFabs () {
        if (this.subFabs && this.subFabs.length > 0) {
          this.subFabsShow = !this.subFabsShow
        }
      },
      mainFabClick () {
        if (!this.subFabs || this.subFabs.length === 0) {
          this.openHref(this.mainFab)
        } else {
          this.showSubFabs()
          if (this.subFabsShow) {
            if (this.mainFab.onOpen) {
              this.mainFab.onOpen()
            }
          } else {
            if (this.mainFab.onClose) {
              this.mainFab.onClose()
            }
          }
        }
        this.fabInkShow = true
        // ink = $(this).find(".ink");
        //
        // if(!ink.height() && !ink.width()){
        //   d = Math.max($(this).outerWidth(), $(this).outerHeight());
        //   ink.css({height: d, width: d});
        // }
        //
        // x = e.pageX - $(this).offset().left - ink.width()/2;
        // y = e.pageY - $(this).offset().top - ink.height()/2;
        //
        // ink.css({top: y+'px', left: x+'px'}).addClass("animate");sub_fab_btns.toggleClass('show');
      },
      subFabClick (fab) {
        this.subFabsShow = !this.subFabsShow
        this.openHref(fab)
        if (fab.onOpen) {
          fab.onOpen()
        }
      },
      handleTouchStart (e) {
        this.switchPos.startX = e.touches[0].pageX
        this.switchPos.startY = e.touches[0].pageY
      },
      handleTouchMove (e) {
        if (e.touches.length > 0) {
          var offsetX = e.touches[0].pageX - this.switchPos.startX
          var offsetY = e.touches[0].pageY - this.switchPos.startY
          var x = this.switchPos.x - offsetX
          var y = this.switchPos.y - offsetY
          if (x < 0) {
            x = 0
          }
          if (y < 0) {
            y = 0
          }
          if (x + this.$els.mainFab.offsetWidth > document.body.offsetWidth) {
            x = document.body.offsetWidth - this.$els.mainFab.offsetWidth
          }
          if (y + this.$els.fab.offsetHeight > document.body.offsetHeight) {
            y = document.body.offsetHeight - this.$els.fab.offsetHeight
          }
          this.$els.fab.style.right = x + 'px'
          this.$els.fab.style.bottom = y + 'px'
          this.switchPos.endX = x
          this.switchPos.endY = y
          e.preventDefault()
        }
      },
      handleTouchEnd (e) {
        if (this.switchPos.endX !== 0 || this.switchPos.endY !== 0) {
          this.switchPos.x = this.switchPos.endX
          this.switchPos.y = this.switchPos.endY
          this.switchPos.startX = 0
          this.switchPos.startY = 0
          this.switchPos.endX = 0
          this.switchPos.endY = 0
          if (this.switchPos.x !== NaN && this.switchPos.y !== NaN) {
            localStorage.setItem('fab_switch_x', this.switchPos.x)
            localStorage.setItem('fab_switch_y', this.switchPos.y)
          }
        }
      }
    }
  }
</script>

<style lang="scss">
  @import '~assets/sass/config';
  .xp_fab {
    z-index: 9999;
    width: 100%;
    height: 240px;
    position: fixed;
    right: 0px;
    bottom: 0px;
    pointer-events: none;

    .sub_fab_btns_wrapper {
      right: 0;
      bottom: 60px;
      position: absolute;
      display: block;
      opacity: 1;
      -webkit-transition: opacity 0.3s ease-in;
      -moz-transition: opacity 0.3s ease-in;
      -ms-transition: opacity 0.3s ease-in;
      -o-transition: opacity 0.3s ease-in;
      transition: opacity 0.3s ease-in;
      pointer-events: all;
    }

    .sub_fab_btns_wrapper button[data-link-title]:after {
      content: attr(data-link-title);
      opacity: 0.8;
      transition: all 0.5s;
      background: rgba(0, 0, 0, 0.4);
      padding: 4px 10px;
      border-radius: 3px;
      color: rgba(255, 255, 255, 0.8);
      font-size: 13px;
      pointer-events: none;
      position: absolute;
      right: 110%;
      white-space:nowrap;
      overflow:hidden;
    }

    .sub_fab_btns_wrapper button {
      width: 40px;
      height: 40px;
      border-radius: 100%;
      background: #F44336;
      margin-bottom: 10px;
      padding: 0;
      border: none;
      outline: none;
      color: #FFF;
      font-size: 19px;
      box-shadow: 0 3px 6px rgba(0, 0, 0, 0.16), 0 3px 6px rgba(0, 0, 0, 0.23);
      transition: .3s;
      pointer-events: all;
    }

    button.kc_fab_main_btn {
      background-color: #F44336;
      width: 60px;
      height: 60px;
      border-radius: 100%;
      background: #F44336;
      right: 0;
      bottom: 0;
      position: absolute;
      margin-right: 0;
      margin-bottom: 0;
      padding: 0;
      border: none;
      outline: none;
      color: #FFF;
      font-size: 36px;
      box-shadow: 0 3px 6px rgba(0, 0, 0, 0.16), 0 3px 6px rgba(0, 0, 0, 0.23);
      transition: .3s;
      -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
      pointer-events: all;
    }

    .kc_fab_main_btn span {
      transition: .5s;
    }

    .open {
      transform: scale(1.1);
      transform: rotate(45deg);
      -ms-transform: rotate(45deg);
      -webkit-transform: rotate(45deg);
    }

    .ink {
      display: block;
      position: absolute;
      background: rgba(255, 255, 255, 0.3);
      border-radius: 100%;
      -webkit-transform: scale(0);
      -moz-transform: scale(0);
      -o-transform: scale(0);
      transform: scale(0);
      pointer-events: all;
    }

    .animate {
      -webkit-animation: ripple 0.65s linear;
      -moz-animation: ripple 0.65s linear;
      -ms-animation: ripple 0.65s linear;
      -o-animation: ripple 0.65s linear;
      animation: ripple 0.65s linear;
    }

    @-webkit-keyframes ripple {
      100% {
        opacity: 0;
        -webkit-transform: scale(2.5);
      }
    }

    @-moz-keyframes ripple {
      100% {
        opacity: 0;
        -moz-transform: scale(2.5);
      }
    }

    @-o-keyframes ripple {
      100% {
        opacity: 0;
        -o-transform: scale(2.5);
      }
    }

    @keyframes ripple {
      100% {
        opacity: 0;
        transform: scale(2.5);
      }
    }
  }

  .xp_fab_overlay {
    z-index: 9998;
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 10;
    background-color: rgba(255, 255, 255, 0.5); /*dim the background*/
  }
</style>
