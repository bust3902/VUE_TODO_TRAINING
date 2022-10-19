<template>
  <div class="modal-wrapper" tabindex="-1">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">
            <slot name="title"></slot>
          </h5>
          <button type="button" class="btn-close" @click="onClose"></button>
        </div>
        <div class="modal-body">
          <slot name="body"></slot>
        </div>
        <div class="modal-footer">
          <slot name="footer"></slot>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import {getCurrentInstance} from "vue";

export default {
  setup() {
    const { emit } = getCurrentInstance();
    const onClose = () => {
      emit('close')
    }

    return {
      onClose,
    }
  }
}
</script>

<style scoped>
  .modal-wrapper {
    z-index: 200;
    top: 0;
    left: 0;
    height: 100%;
  }
</style>