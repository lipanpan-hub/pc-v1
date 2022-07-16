<template>
  <div class="skeleton">
    <div class="item">
      <div class="left-item"></div>
      <div class="right-item">
        <div class="item-title"></div>
        <div class="item-info"></div>
      </div>
      <div class="button"></div>
    </div>
    <div class="item">
      <div class="left-item"></div>
      <div class="right-item">
        <div class="item-title"></div>
        <div class="item-info"></div>
      </div>
      <div class="button"></div>
    </div>
    <div class="item">
      <div class="left-item"></div>
      <div class="right-item">
        <div class="item-title"></div>
        <div class="item-info"></div>
      </div>
      <div class="button"></div>
    </div>
    <div class="item">
      <div class="left-item"></div>
      <div class="right-item">
        <div class="item-title"></div>
        <div class="item-info"></div>
      </div>
      <div class="button"></div>
    </div>
    <div class="item">
      <div class="left-item"></div>
      <div class="right-item">
        <div class="item-title"></div>
        <div class="item-info"></div>
      </div>
      <div class="button"></div>
    </div>
    <div class="item">
      <div class="left-item"></div>
      <div class="right-item">
        <div class="item-title"></div>
        <div class="item-info"></div>
      </div>
      <div class="button"></div>
    </div>
  </div>
</template>
<style lang="less" scoped>
@keyframes el-skeleton-loading {
  0% {
    background-position: 100% 50%;
  }
  to {
    background-position: 0 50%;
  }
}
.skeleton {
  width: 100%;
  float: left;
  height: auto;
  display: flex;
  flex-direction: column;
  .item {
    width: 100%;
    height: 120px;
    display: flex;
    flex-direction: row;
    margin-bottom: 30px;
    &:last-child {
      margin-bottom: 0px;
    }
    .left-item {
      width: 160px;
      height: 120px;
      border-radius: 4px;
      margin-right: 30px;
      background: linear-gradient(90deg, #f2f2f2 25%, #e6e6e6 37%, #f2f2f2 63%);
      background-size: 400% 100%;
      animation: el-skeleton-loading 1.4s ease infinite;
    }
    .right-item {
      flex: 1;
      height: 120px;
      margin-right: 30px;
      .item-title {
        width: 816px;
        height: 16px;
        margin-top: 30px;
        background: linear-gradient(
          90deg,
          #f2f2f2 25%,
          #e6e6e6 37%,
          #f2f2f2 63%
        );
        background-size: 400% 100%;
        animation: el-skeleton-loading 1.4s ease infinite;
      }
      .item-info {
        width: 816px;
        height: 14px;
        display: flex;
        flex-direction: row;
        margin-top: 35px;
        background: linear-gradient(
          90deg,
          #f2f2f2 25%,
          #e6e6e6 37%,
          #f2f2f2 63%
        );
        background-size: 400% 100%;
        animation: el-skeleton-loading 1.4s ease infinite;
      }
    }
    .button {
      width: 104px;
      height: 46px;
      border-radius: 4px;
      margin-top: 37px;
      background: linear-gradient(90deg, #f2f2f2 25%, #e6e6e6 37%, #f2f2f2 63%);
      background-size: 400% 100%;
      animation: el-skeleton-loading 1.4s ease infinite;
    }
  }
}
</style>
