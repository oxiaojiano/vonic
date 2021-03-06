<template>
  <div class="popup-container" :effect="effect"
       :class="{'popup-showing active': state == 1, 'popup-showing popup-hidden': state == 2}">
    <div class="popup von-popup" :class="cssClass">
      <div v-if="title" class="popup-head">
        <div class="popup-title">
          {{{ title }}}
        </div>
      </div>

      <div class="popup-body" :class="{'no-content': state == 0}">
        <slot></slot>
      </div>

      <div v-if="buttons.length > 0" class="popup-buttons">
        <button v-for="($index, b) in buttons" class="{{ 'button button-' + (b.theme || default_button_theme) + ' button-block' }}" @click="hide($index)">
          {{{ b.text }}}
        </button>
      </div>
    </div>
  </div>
</template>

<style lang="scss">
  @import "../../../components/popup/popup";
  @import "../../../components/scss/variables";
  @import "../../../components/scss/mixins";

  .popup-container .von-popup {
    background-color: #fff;

    .popup-head {
      border-bottom: none;
    }

    .popup-body {
      padding: 22px 19px;
      p {
        font-weight: 100 !important;
        -webkit-font-smoothing: subpixel-antialiased;
      }

      &.no-content * {
        opacity: 0;
      }
    }

    .popup-buttons {
      padding: 0;
      min-height: 45px;

      .button {
        min-height: 45px;
        font-size: 14px;
        line-height: 20px;

        // border & border-radius
        border-top: 1px solid #eee;
        border-right: 1px solid #eee;
        border-top-left-radius: 0;
        border-top-right-radius: 0;

        border-bottom-right-radius: 0;
        border-bottom-left-radius: 0;

        &:first-of-type {
          border-bottom-left-radius: 4px;
        }

        &:last-of-type {
          border-right: none;
          border-bottom-right-radius: 4px;
        }

        margin-right: 0;
        background-color: transparent;

        &.button-light {
          background-color: #fff;
          &:active {
            background-color: rgba(0,0,0,0.1);
          }
        }

        &.button-assertive {
          border-top: none;
          background-color: $assertive;
          &:active {
            background-color: darken($assertive, 6.5%);
          }
        }

      }
    }
  }

</style>

<script>
  const popup_enter_duration = 200
  const popup_leave_duration = 300
  const backdrop_fadein_duration = 100

  export default {
    props: {
      effect: {
        type: String,
        default: 'default'
      },
      title: {
        type: String,
        default: ''
      },
      cssClass: {
        type: String,
        default: ''
      }
    },

    data() {
      return {
        state: 0, // 0: hidden, 1: showing, 2: active
        default_button_theme: 'light',

        buttons: []
      }
    },

    methods: {
      show() {
        let backdrop = document.querySelector('[backdrop]')
        backdrop.classList.add('visible')
        setTimeout(() => {
          backdrop.classList.add('active')
        }, backdrop_fadein_duration)

        this.state = 1

        return new Promise((resolve, reject) => {
          this.$on('PopupButtonClickEvent', (data) => {
            resolve(data.buttonIndex)
          })
        });
      },

      hide(buttonIndex) {
        let backdrop = document.querySelector('[backdrop]')
        backdrop.classList.remove('active')
        setTimeout(() => {
          backdrop.classList.remove('visible')
        }, backdrop_fadein_duration)

        this.state = 2

        setTimeout(() => {
          this.state = 0
          this.$emit('PopupButtonClickEvent', {buttonIndex: buttonIndex})
        }, popup_leave_duration)
      },

      setButtons(buttons) {
        this.buttons = buttons
      }
    }
  }
</script>
