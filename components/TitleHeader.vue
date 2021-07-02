<template>
  <div class="title-header">
    <div class="task-image-container">
      <img src="../assets/task.svg" alt="icon" class="task-icon" />
    </div>
    <div class="task-list">
      <span>Task List</span>
    </div>
  </div>
</template>

<script>
export default {};
</script>

<style lang="scss" scoped>
.title-header {
  display: flex;
  flex-direction: row;

  .task-image-container {
    height: 35px;
    width: 35px;
    margin: 55px 15px;
    margin-left: 0px;
  }
  .task-list {
    align-self: center;
    font-size: 46px;
    font-weight: 500;
  }
}
</style>
