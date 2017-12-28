<!--
  - editCategory Vue 文件
  - @JsName editCategory
  - @Description
  - @DateTime 2017/12/27 10:03
  - @author 亚茹
  -->
<template>
    <div>
      <m-Breadcrumb mTitle1="商品管理" mTitle2="商品类别"></m-Breadcrumb>
      <div class="editType_content">
        <div class="editTypeContent">
          <!--类别名称-->
          <div class="categoryName">
            <label>名称</label>
            <input type="text" v-model="modifyTypeParams.categoryName">
          </div>
          <!--级别-->
          <div class="categoryLevel">
            <label>级别</label>
            <input type="text" v-model="modifyTypeParams.categoryLevel">
          </div>
          <!--图片-->
          <div class="categoryPicture">
            <label>图片</label>
            <div class="picture" @click="transfer()">
              <span v-if="pictureExit">+</span>
              <img :src="'http://10.0.0.17:8888/'+pictureSrc"  v-else="pictureExit">
            </div>
          </div>
          <!--父级-->
          <div class="categoryParent">
            <label>父级</label>
            <el-select v-model="value1" placeholder="请选择"  @change="changeValue">
              <el-option
                v-for="item in gradeList"
                :key="item.id"
                :label="item.gradeValue"
                :value="item.gradeValue">
              </el-option>
            </el-select>
          </div>
          <!--排序-->
          <div class="sortPriority">
            <label>排序</label>
            <input type="number" v-model="modifyTypeParams.sortPriority">
          </div>

          <div class="editType_bottom">
            <input type="button" value="取消" @click="categoryCancle">
            <input type="button" value="保存" @click="categorySure">
          </div>
        </div>
      </div>
      <p-Resource @deliverMsg="change" :msg="msg" class="resource"></p-Resource>
    </div>
</template>
<!-- 引入 editCategory Js 文件  -->
<script>
  import rq from './../../http/require.js'
  import mBreadcrumb from './../../component/headerTag.vue'
  import pResource from '../../component/pictureSource.vue'
  import $ from 'jquery'
  export default {
    name: 'editCategory',
    data () {
      return {
        value1: '', // 父级级别
        gradeList: [{
          id: '1',
          gradeValue: 1
        }, {
          id: '2',
          gradeValue: 2
        }, {
          id: '3',
          gradeValue: 3
        }], // 级别列表
        pictureExit: true, // 图片标识
        pictureSrc: '', // 图片src
        msg: [], // 图片信息
        modifyTypeParams: {
          id: '',
          categoryName: '',
          categoryLevel: '',
          sortPriority: '',
          isRecycled: 1,
          pictureId: '',
          parentId: ''
        }, // 修改类别参数
        categoryValue: [] // 编辑前类别值
      }
    },
    methods: {
      // 提示信息
      showMessage (type, duration, message, callback) {
        this.$message({
          type: type,
          duration: duration,
          showClose: false,
          message: message,
          onClose: (el) => {
            if (callback) callback(el)
          }
        })
      },
      // 判断条件
      validator () {
        var that = this
        if (that.typeParams.categoryName === '' || that.typeParams.categoryName === undefined) {
          that.showMessage('info', 2000, '请输入类别名称'); return false
        } else if (that.typeParams.categoryLevel === '' || that.typeParams.categoryLevel === undefined) {
          that.showMessage('info', 2000, '请选择级别'); return false
        } else if (that.typeParams.sortPriority === '' || that.typeParams.sortPriority === undefined) {
          that.showMessage('info', 2000, '请输入排序权重'); return false
        } else if (that.typeParams.pictureId === '' || that.typeParams.pictureId === undefined) {
          that.showMessage('info', 2000, '请选择图片'); return false
        } else if (that.typeParams.parentId === '' || that.typeParams.parentId === undefined) {
          that.showMessage('info', 2000, '请选择父级级别'); return false
        } else {
          return true
        }
      },
      // 调取图片
      transfer: function () {
        $('.resource').css('display', 'block')
      },
      // 添加图片
      change: function (msg) {
        this.pictureParams = msg
        if (this.msg.length === 0) {
          this.pictureExit = false
          this.pictureSrc = msg.pictureSrc
          this.modifyTypeParams.pictureId = msg.id
        } else {
          this.pictureExit = true
          this.pictureSrc = ''
        }
      },
      // 选择父级级别
      changeValue: function (value) {
        let obj = {}
        obj = this.gradeList.find((item) => {
          return item.gradeValue === value
        })
        console.log(obj.id)
        this.modifyTypeParams.parentId = obj.id
      },
      // 取消
      categoryCancle: function () {
        var that = this
        that.$router.push({name: 'commodityType'})
      },
      // 修改确定
      categorySure: function () {
        var that = this
        if (that.validator()) {
          console.log(that.modifyTypeParams)
          rq.checkCategory(that.modifyTypeParams.categoryName).then(function (data) {
            if (data.result === '成功') {
              rq.modifyCategory(that.modifyTypeParams).then(function (data) {
                if (data.result === '成功') {
                  that.showMessage('info', 2000, '修改成功')
                  that.$router.push({name: 'commodityType'})
                } else if (data.result === '失败') {
                  that.showMessage('info', 2000, '修改失败')
                }
              })
            } else if (data.result === '失败') {
              that.showMessage('info', 2000, '类别名重复')
            }
          })
        }
      }
    },
    mounted () {
      $('.resource').css('display', 'none')
      this.categoryValue = this.$route.params.categoryParams[0]
      // console.log(this.categoryValue)
      this.modifyTypeParams.id = this.categoryValue.id
      this.modifyTypeParams.categoryName = this.categoryValue.categoryName
      this.modifyTypeParams.categoryLevel = this.categoryValue.categoryLevel
      this.modifyTypeParams.sortPriority = this.categoryValue.sortPriority
      this.value1 = this.categoryValue.parentId
    },
    components: {
      mBreadcrumb, pResource
    }
  }
</script>
<style>
  .categoryParent .el-select{
    height: 32px;
  }
  .categoryParent .el-select .el-input{
    height: 32px;
  }
  .categoryParent .el-select .el-input .el-input__inner{
    height: 32px;
  }
</style>
<!-- 引入 editCategory Scss 文件  -->
<style lang="scss" scoped>@import "scss/editCategory";</style>
