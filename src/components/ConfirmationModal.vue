<template>
  <div class="confirmationModal adventFont">
    <div class="confirmationModal-backDrop" @click="cancelFunc"></div>

    <div class="confirmationModal-box">
      <div class="confirmationModal-content">
        <div class="confirmationModal-header">
          <p class="confirmationModal-title">
            {{ title }}
          </p>
          <p class="confirmationModal-message" v-if="message">
            {{ message }}
          </p>
        </div>

        <slot></slot>

        <div class="confirmationModal-btns">
          <button v-if="!withoutCancelBtn" class="confirmationModal-button cancel" @click="clickCancel">
            {{ cancelBtnText }}
          </button>

          <button class="confirmationModal-button confirm" @click="clickConfirm">
            {{ confirmBtnText }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ConfirmationModal",
  props: {
    confirmFunc: {
      type: Function,
      default: () => {}
    },
    cancelFunc: {
      type: Function,
      default: () => {}
    },
    title: {
      type: String,
      default: "You are sure?"
    },
    message: {
      type: String,
      default: ""
    },
    confirmBtnText: {
      type: String,
      default: "Confirm"
    },
    cancelBtnText: {
      type: String,
      default: "Cancel"
    },
    withoutCancelBtn: {
      type: Boolean,
      default: false,
    },
  },
  methods: {
    clickConfirm: function() {
      this.confirmFunc();
      this.cancelFunc();
    },
    clickCancel: function() {
      this.cancelFunc();
    },
  }
};
</script>

<style lang="scss" scoped>
.confirmationModal {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 1112;

  &-backDrop {
    position: absolute;
    background-color: rgba(204, 204, 204, 0.6);
    width: 100%;
    height: 100%;
  }

  &-box {
    max-width: 400px;
    margin: 0 auto;
    display: flex;
    align-items: center;
    height: 100%;

    @media (max-width: 450px) {
      max-width: 280px;
    }
  }

  &-content {
    z-index: 1113;
    width: 100%;
    height: 200px;
    border-radius: 10px;
    padding: 20px;
    border: 1px #a1a1aa solid;
    background-color: #fff;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }

  &-btns {
    display: flex;
    justify-content: flex-end;
    width: 100%;
  }

  &-title {
    text-align: left; 
  }

  &-message {
    text-align: left; 
    font-size: 18px;
  }

  &-header {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
  }

  &-button {
    border: none;
    text-transform: uppercase;
    border-radius: 10px;
    color: #fff;
    font-weight: bold;
    padding: 10px 20px;
    background-color: #222a4f;
    margin-left: 10px;

    &.confirm {
      background-color: #e55353;
    }
  }
}
</style>
