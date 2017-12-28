<!--
  - onOffer Vue 文件
  - @JsName onOffer
  - @Description
  - @DateTime 2017/12/28 16:08
  - @author 亚茹
  -->
<template>
  <div>
    <div class="onOffer_content">
      <div class="query_criteria">
        <div class="query_name"><label>商品名称</label><input type="text"></div>
        <div class="query_category"><label>商品分类</label><input type="text"></div>
        <div class="query_ensure"><input type="button" value="查询" @click="check"></div>
        <div class="query_recycled"><a>回收站</a><img src="./../../../static/images/select.png"></div>
      </div>
      <table class="onOffer_table">
        <thead>
        <tr>
          <td style="width: 5%">编号</td>
          <td style="width: 10%">名称</td>
          <td style="width: 15%">缩略图</td>
          <td style="width: 7%">分类</td>
          <td style="width: 6%">排序</td>
          <td style="width: 10%">价格</td>
          <td style="width: 10%">库存</td>
          <td style="width: 10%">销量</td>
          <td style="width: 14%">创建时间</td>
          <td style="width: 13%">操作</td>
        </tr>
        </thead>
      </table>
    </div>
  </div>
</template>

<script></script>
<!-- 引入 onOffer Scss 文件  -->
<style lang="scss" scoped>@import "scss/onOffer";</style>
