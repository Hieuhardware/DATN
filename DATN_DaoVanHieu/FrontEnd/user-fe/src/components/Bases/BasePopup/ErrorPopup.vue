<template>
  <div class="popup-container">
    <div class="m-popup error-popup">
      <div class="popup-content">
        <i class="fas fa-exclamation-triangle" style="color: red"></i>
        <div class="popup-text">{{ title }}</div>
      </div>
      <div class="popup-footer" @click="$emit('close')">
        <div class="list-button">
          <p @click="$emit('close')">Đóng</p>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
@import url("../../../styles/Bases/popup.css");
</style>
<script>
export default {
  props: ["title"],
  components: {},
  data() {
    return {};
  },
};
</script>