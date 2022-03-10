<template>
  <div class="overlay">
    <div class="base-dialog">
      <div class="base-dialog-header">{{ title }}</div>
      <div class="base-dialog-content">
        <el-input-number v-model="numberValue" :min="1" :max="20" />
      </div>
      <div class="base-dialog-footer">
        <el-button type="primary" @click="confirm">确定</el-button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "BaseDialog",
  emits: ["update:num", "confirm"],
  props: {
    title: {
      type: String,
      default: "五毛想要bubble的个数",
    },
    num: {
      type: Number,
      default: 1,
    },
  },
  data() {
    return {
      numberValue: this.num,
    };
  },
  methods: {
    confirm() {
      alert("success");
      this.$emit("confirm");
      this.$emit("update:num", this.numberValue);
    },
  },
};
</script>

<style lang="scss" scoped>
.overlay {
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  z-index: 2000;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  overflow: auto;
  .base-dialog {
    width: 700px;
    background: #fff;
    &-header {
      height: 80px;
      line-height: 80px;
      border-bottom: 1px solid #ddd;
      text-align: center;
    }
    &-content {
      padding: 40px;
      text-align: center;
    }
    &-footer {
      padding: 20px 0 40px;
      text-align: center;
      border-top: 1px solid #ddd;
    }
  }
}
</style>
