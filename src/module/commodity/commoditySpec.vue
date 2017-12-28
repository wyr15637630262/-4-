<!--
  - commoditySpec Vue 文件
  - @JsName commoditySpec
  - @Description
  - @DateTime 2017/12/11 16:31
  - @author 亚茹
  -->
<template>
  <div>
    <m-Breadcrumb mTitle1="商品管理" mTitle2="商品规格"></m-Breadcrumb>
    <div class="commoditySpec_content">
      <div class="query_criteria">
        <div class="query_name"><label>名称</label><input type="text" v-model="queryParams.specName"></div>
        <div class="query_ensure"><input type="button" value="查询" @click="querySpec()"></div>
        <div class="query_recycled" @click="rubish()"><a>回收站</a><img src="../../../static/images/select.png" v-if="imgShow"></div>
        <div class="query_newAdd" @click="increaseSpec"><a>+新增</a></div>
      </div>
      <table class="commoditySpec_table">
        <thead>
        <tr>
          <td style="width: 16%">编号</td>
          <td style="width: 16%">名称</td>
          <td style="width: 16%">排序</td>
          <td style="width: 20%">创建时间</td>
          <td style="width: 16%">操作</td>
        </tr>
        </thead>
        <tbody v-if="specList.length!==0">
        <tr v-for="(item,index) in specList">
          <td>00{{index+1}}</td>
          <td>{{item.specName}}</td>
          <td>{{item.sortPriority}}</td>
          <td>{{item.createTime|date('YYYY-MM-DD')}}</td>
          <td>
            <a @click="goEditor(item)">{{msg1}}</a>
            <a style="padding: 0 10px">|</a>
            <a @click="goDelete(item.id)">{{msg2}}</a>
            <a style="padding: 0 10px">|</a>
            <a @click="editParams(item.id)">参数</a>
          </td>
        </tr>
        </tbody>
        <tbody v-else-if="specList.length===0">
        <tr>
          <td colspan="7" >暂无数据...</td>
        </tr>
        </tbody>
      </table>
      <div class="commoditySpec_page" v-if="specList.length!==0">
        <div class="pages">
          <el-pagination
            :page-size="specListPageSize"
            layout="prev, pager, next,jumper"
            :total="specListTotal"
            @current-change="handleCurrentChange">
          </el-pagination>
        </div>
      </div>
    </div>
    <!--增加-->
    <div class="addMask" v-if="adds">
      <div class="addMask_content">
        <div class="addMaskC_top">
          <a>商品规格</a>
          <a>-</a>
          <a style="color:#ec6a47">新增规格</a>
        </div>
        <div class="addMaskC_center">
          <div class="adc_content">
            <div class="specName">
              <label >规格名称</label>
              <input type="text" v-model="addSpecParams.specName">
            </div>
            <div class="sort">
              <label >排&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;序</label>
              <input type="number" v-model="addSpecParams.sortPriority">
            </div>
            <div class="category">
              <label >类&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;别</label>
              <template>
                <el-select v-model="value1" placeholder="请选择" @change="changeValue">
                  <el-option
                    v-for="item in categoryList"
                    :key="item.id"
                    :label="item.categoryName"
                    :value="item.categoryName">
                  </el-option>
                </el-select>
              </template>
            </div>
          </div>
        </div>
        <div class="addMaskC_bottom">
          <div class="adb_content">
            <input type="button" value="取消" @click="increaseCancle()">
            <input type="button" value="保存" @click="increaseConfirm()">
          </div>
        </div>
      </div>
    </div>
    <!--编辑-->
    <div class="mask" v-if="edit">
      <div class="mask_content">
        <div class="maskC_top">
          <a>商品规格</a>
          <a>-</a>
          <a style="color:#ec6a47">编辑规格</a>
        </div>
        <div class="maskC_center">
          <div class="mc_content">
            <div class="specName">
              <label >规格名称</label>
              <input type="text" v-model="editParam.specName">
            </div>
            <div class="sort">
              <label >排&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;序</label>
              <input type="number" v-model="editParam.sortPriority">
            </div>
            <div class="category">
              <label >类&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;别</label>
              <template>
                <el-select v-model="value2" placeholder="请选择" @change="changeValue">
                  <el-option
                    v-for="item in categoryList"
                    :key="item.id"
                    :label="item.categoryName"
                    :value="item.categoryName">
                  </el-option>
                </el-select>
              </template>
            </div>
          </div>
        </div>
        <div class="maskC_bottom">
          <div class="mb_content">
            <input type="button" value="取消" @click="editCancle()">
            <input type="button" value="保存" @click="editConfirm()">
          </div>
        </div>
      </div>
    </div>
    <!--参数列表-->
    <div class="paramDialog" v-if="dialogTableVisible">
      <div class="paramDialog_content">
        <div class="dialogTop">
          <div><a>规格参数</a></div>
          <div><a @click="closeDiolog">&times;</a></div>
        </div>
        <div class="dialogCenter">
          <div class="query_criteria">
            <div class="query_name"><label>名称</label><input type="text" v-model="checkParam.parameter"></div>
            <div class="query_ensure"><input type="button" value="查询" @click="searchSpecParams"></div>
            <div class="query_newAdd" @click="addParams"><a>+新增</a></div>
          </div>
          <table class="dialog_table">
            <thead>
              <tr>
                <td style="width: 187px;">编号</td>
                <td style="width: 187px;">参数</td>
                <td style="width: 189px;">创建时间</td>
                <td style="width: 187px;">操作</td>
              </tr>
            </thead>
            <tbody>
              <tr v-if="addExit">
                <td>#</td>
                <td><input type="text" style="width: 100%;height: 70%;border: 0;border-bottom: 1px solid #cccccc;outline: none;text-align: center;margin-top: 5%" v-model="increaseParams.parameter"></td>
                <td></td>
                <td>
                  <span style="color:#ec6a47; cursor: pointer;" @click="saveIncrease">{{msg3}}</span>
                  <span>|</span>
                  <span style="cursor: pointer;" @click="cancleIncrease">{{msg4}}</span>
                </td>
              </tr>
              <tr v-for="(item,index) in paramsList">
                <td>00{{index+1}}</td>
                <td>{{item.parameter}}</td>
                <td>{{item.createTime|date('YYYY-MM-DD')}}</td>
                <td>
                  <!--<span style="color:#ec6a47; cursor: pointer;" @click="reviseParam">{{msg5}}</span>-->
                  <!--<span>|</span>-->
                  <span style="cursor: pointer;" @click="removeParam(item.id)">{{msg6}}</span>
                </td>
              </tr>
            </tbody>
          </table>
          <div class="dialog_page" v-if="paramsList.length!==0">
            <div class="pages">
              <el-pagination
                :page-size="paramsListPageSize"
                layout="prev, pager, next,jumper"
                :total="paramsListTotal"
                @current-change="currentChange">
              </el-pagination>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import rq from './../../http/require.js'
  import mBreadcrumb from './../../component/headerTag.vue'
  export default {
    name: 'commoditySpec',
    data () {
      return {
        specParams: {isRecycled: 1, page: 1, size: 10}, // 规格参数
        specParamsRecycle: {isRecycled: 0, page: 1, size: 10}, // 回收站规格参数
        addSpecParams: {specName: '', categoryId: '', sortPriority: '', parentId: ''}, // 新增规格参数
        editParam: {specName: '', categoryId: '', sortPriority: '', parentId: '', id: ''}, // 编辑规格参数
        queryParams: {specName: '', isRecycled: 1, page: 1, size: 10}, // 查询参数
        specList: [], // 规格列表
        specListPageSize: 10, // 每页个数
        specListTotal: 10, // 规格总个数
        imgShow: false, // 回收站标识
        value1: '', // 新增类别值
        value2: '', // 编辑类别值
        adds: false, // 增加标识
        edit: false, // 增加标识
        typeParams: {isRecycled: 1, page: 1, size: 10}, // 商品类别参数
        msg1: '编辑',
        msg2: '删除',
        msg3: '保存',
        msg4: '取消',
        msg5: '编辑',
        msg6: '删除',
        dialogTableVisible: false, // 参数列表标识
        paramsList: [], // 参数列表
        addExit: false, // 增加参数标识
        obtainSpecParams: {specId: '', page: 1, size: 10}, // 根据规格ID获取参数列表的参数
        paramsListTotal: 10, // 参数总个数
        paramsListPageSize: 10, // 每页个数
        increaseParams: {parameter: '', specId: ''}, // 添加规格参数的参数
        checkParam: {parameter: '', specId: '', page: 1, size: 10} // 查询规格参数的参数
      }
    },
    methods: {
      // 参数
      editParams: function (id) {
        // console.log(id)
        var that = this
        that.dialogTableVisible = true
        this.addExit = false
        that.obtainSpecParams.specId = id
        that.increaseParams.specId = id
        that.checkParam.specId = id
        rq.searchAllSpecParam(that.obtainSpecParams).then(function (data) {
          if (data.result === '成功') {
            console.log(data)
            that.paramsList = data.data.data
            that.paramsListTotal = data.data.totalRecord
            that.paramsListPageSize = data.data.pageSize
          } else if (data.result === '失败') {
            that.paramsList = []
          }
        })
      },
      // 关闭参数弹框
      closeDiolog: function () {
        this.dialogTableVisible = false
      },
      // 参数页码改变
      currentChange: function (val) {
        var that = this
        that.obtainSpecParams.page = val
        rq.searchAllSpecParam(that.obtainSpecParams).then(function (data) {
          if (data.result === '成功') {
            that.paramsList = data.data.data
          } else if (data.result === '失败') {
            that.paramsList = []
          }
        })
      },
      // 查询规格参数
      searchSpecParams: function () {
        var that = this
        rq.checkWordSpecParam(that.checkParam).then(function (data) {
          if (data.result === '成功') {
            console.log(data)
            that.paramsList = data.data.data
            that.paramsListTotal = data.data.totalRecord
            that.paramsListPageSize = data.data.pageSize
            that.checkParam.parameter = ''
          } else if (data.result === '失败') {
            that.paramsList = []
          }
        })
      },
      // 新增规格参数
      addParams: function () {
        this.addExit = true
      },
      // 新增规格参数保存
      saveIncrease: function () {
        var that = this
        if (that.increaseParams.parameter === '') {
          that.$message('请输入相应的参数')
        } else {
          rq.checkSpecParameter(that.increaseParams).then(function (data) {
            if (data.result === '成功') {
              rq.addSpecParam(that.increaseParams).then(function (data) {
                if (data.result === '成功') {
                  rq.searchAllSpecParam(that.obtainSpecParams).then(function (data) {
                    if (data.result === '成功') {
                      console.log(data)
                      that.paramsList = data.data.data
                      that.paramsListTotal = data.data.totalRecord
                      that.paramsListPageSize = data.data.pageSize
                      that.increaseParams.parameter = ''
                    } else if (data.result === '失败') {
                      that.paramsList = []
                    }
                  })
                } else if (data.result === '失败') {
                  console.log('111')
                }
              })
            } else if (data.result === '失败') {
              that.$message('该规格参数已存在')
            }
          })
          that.addExit = false
        }
      },
      // 新增规格参数取消
      cancleIncrease: function () {
        this.addExit = false
      },
      // 编辑参数
      // reviseParam: function () {
      // },
      // 删除规格参数
      removeParam: function (id) {
        // console.log(id)
        var that = this
        that.$confirm('此操作将删除该规格参数, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          rq.removeSpecParam(id).then(function (data) {
            rq.searchAllSpecParam(that.obtainSpecParams).then(function (data) {
              if (data.result === '成功') {
                console.log(data)
                that.paramsList = data.data.data
                that.paramsListTotal = data.data.totalRecord
                that.paramsListPageSize = data.data.pageSize
              } else if (data.result === '失败') {
                that.paramsList = []
              }
            })
          })
        }).catch(() => {
          that.$message({
            type: 'info',
            message: '已取消删除'
          })
        })
      },
      // 查询规格
      querySpec: function () {
        var that = this
        rq.searchSpec(that.queryParams).then(function (data) {
          if (data.result === '成功') {
            console.log(data)
            that.specList = data.data.data
            that.specListTotal = data.data.totalRecord
            that.specListPageSize = data.data.pageSize
          } else if (data.result === '失败') {
            that.specList = []
          }
        })
      },
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
      // 增加判断条件
      validator () {
        var that = this
        if (that.addSpecParams.specName === '' || that.addSpecParams.specName === undefined) {
          that.showMessage('info', 2000, '请输入规格名称'); return false
        } else if (that.addSpecParams.sortPriority === '' || that.addSpecParams.sortPriority === undefined) {
          that.showMessage('info', 2000, '请输入排序权重'); return false
        } else if (that.value1 === '' || that.value1 === undefined) {
          that.showMessage('info', 2000, '请选择类别'); return false
        } else {
          return true
        }
      },
      // 编辑判断条件
      editEalidator () {
        var that = this
        if (that.editParam.specName === '' || that.editParam.specName === undefined) {
          that.showMessage('info', 2000, '请输入规格名称'); return false
        } else if (that.editParam.sortPriority === '' || that.editParam.sortPriority === undefined) {
          that.showMessage('info', 2000, '请输入排序权重'); return false
        } else if (that.value2 === '' || that.value2 === undefined) {
          that.showMessage('info', 2000, '请选择类别'); return false
        } else {
          return true
        }
      },
      // 改变页码
      handleCurrentChange: function (val) {
        var that = this
        // console.log(val)
        that.specParams.page = val
        rq.obtainAllSpec(that.specParams).then(function (data) {
          if (data.result === '成功') {
            // console.log(data)
            that.specList = data.data.data
          } else if (data.result === '失败') {
            that.specList = []
          }
        })
      },
      // 选择类别
      changeValue: function (value) {
        let obj = {}
        obj = this.categoryList.find((item) => {
          return item.categoryName === value
        })
        // console.log(obj.id)
        this.addSpecParams.categoryId = obj.id
        this.addSpecParams.parentId = obj.parentId
        this.editParam.categoryId = obj.id
        this.editParam.parentId = obj.parentId
      },
      // 新增规格
      increaseSpec: function () {
        this.adds = true
      },
      // 增加取消
      increaseCancle: function () {
        this.adds = false
      },
      // 增加确定
      increaseConfirm: function () {
        var that = this
        if (that.validator()) {
          // 验证规格
          rq.verifySpec(that.addSpecParams).then(function (data) {
            if (data.result === '成功') {
              // 添加规格
              rq.addSpec(that.addSpecParams).then(function (data) {
                if (data.result === '成功') {
                  // 规格列表
                  rq.obtainAllSpec(that.specParams).then(function (data) {
                    if (data.result === '成功') {
                      // console.log(data)
                      that.specList = data.data.data
                      that.specListTotal = data.data.totalRecord
                      that.specListPageSize = data.data.pageSize
                      that.addSpecParams.specName = ''
                      that.addSpecParams.sortPriority = ''
                      that.value1 = ''
                    } else if (data.result === '失败') {
                      that.specList = []
                    }
                  })
                } else if (data.result === '失败') {
                  that.showMessage('info', 2000, '添加失败')
                }
              })
            } else if (data.result === '失败') {
              that.showMessage('info', 2000, '该规格已存在')
            }
          })
          that.adds = false
        }
      },
      // 编辑取消
      editCancle: function () {
        this.edit = false
      },
      // 编辑确定
      editConfirm: function () {
        var that = this
        if (that.editEalidator()) {
          // 验证规格
          rq.verifySpec(that.editParam).then(function (data) {
            if (data.result === '成功') {
              // 修改规格
              rq.modifySpec(that.editParam).then(function (data) {
                if (data.result === '成功') {
                  // 规格列表
                  rq.obtainAllSpec(that.specParams).then(function (data) {
                    if (data.result === '成功') {
                      // console.log(data)
                      that.specList = data.data.data
                      that.specListTotal = data.data.totalRecord
                      that.specListPageSize = data.data.pageSize
                      that.addSpecParams.specName = ''
                      that.addSpecParams.sortPriority = ''
                      that.value1 = ''
                    } else if (data.result === '失败') {
                      that.specList = []
                    }
                  })
                } else if (data.result === '失败') {
                  that.showMessage('info', 2000, '修改失败')
                }
              })
            } else if (data.result === '失败') {
              that.showMessage('info', 2000, '该规格已存在')
            }
          })
          this.edit = false
        }
      },
      // 编辑及恢复
      goEditor: function (item) {
        var that = this
        var categoryId = item.categoryId
        if (that.imgShow === false) {
          that.edit = true
          var object = {}
          object = this.categoryList.find((item) => {
            return item.id === categoryId
          })
          that.value2 = object.categoryName
          that.editParam.specName = item.specName
          that.editParam.sortPriority = item.sortPriority
          that.editParam.id = item.id
        } else {
          rq.removeSpecRecycle(item.id).then(function (data) {
            if (data.result === '成功') {
              rq.obtainAllSpec(that.specParamsRecycle).then(function (data) {
                if (data.result === '成功') {
                  // console.log(data)
                  that.specList = data.data.data
                  that.specListTotal = data.data.totalRecord
                  that.specListPageSize = data.data.pageSize
                } else if (data.result === '失败') {
                  that.specList = []
                }
              })
            } else if (data.result === '失败') {
              that.showMessage('info', 2000, '恢复失败')
            }
          })
        }
      },
      // 移入回收站及彻底删除
      goDelete: function (id) {
        var that = this
        if (that.imgShow === false) {
          // 移入回收站
          that.$confirm('此操作将删除该规格, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            rq.increaseSpecRecycle(id).then(function (data) {
              if (data.result === '成功') {
                that.showMessage('info', 2000, '删除成功')
                rq.obtainAllSpec(that.specParams).then(function (data) {
                  if (data.result === '成功') {
                    // console.log(data)
                    that.specList = data.data.data
                    that.specListTotal = data.data.totalRecord
                    that.specListPageSize = data.data.pageSize
                  } else if (data.result === '失败') {
                    that.specList = []
                  }
                })
              } else if (data.result === '失败') {
                that.showMessage('info', 2000, '删除失败')
              }
            })
          }).catch(() => {
            that.$message({
              type: 'info',
              message: '已取消删除'
            })
          })
        } else {
          // 彻底删除
          that.$confirm('此操作将彻底删除该规格, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            rq.removeSpec(id).then(function (data) {
              if (data.result === '成功') {
                rq.obtainAllSpec(that.specParamsRecycle).then(function (data) {
                  if (data.result === '成功') {
                    // console.log(data)
                    that.specList = data.data.data
                    that.specListTotal = data.data.totalRecord
                    that.specListPageSize = data.data.pageSize
                  } else if (data.result === '失败') {
                    that.specList = []
                  }
                })
              } else if (data.result === '失败') {
                that.showMessage('info', 2000, '删除失败')
              }
            })
          }).catch(() => {
            that.$message({
              type: 'info',
              message: '已取消删除'
            })
          })
        }
      },
      // 回收站
      rubish: function () {
        var that = this
        that.imgShow = !that.imgShow
        if (that.imgShow === true) {
          that.msg1 = '恢复'
          that.msg2 = '彻底删除'
          rq.obtainAllSpec(that.specParamsRecycle).then(function (data) {
            if (data.result === '成功') {
              // console.log(data)
              that.specList = data.data.data
              that.specListTotal = data.data.totalRecord
              that.specListPageSize = data.data.pageSize
            } else if (data.result === '失败') {
              that.specList = []
            }
          })
        } else {
          that.msg1 = '编辑'
          that.msg2 = '删除'
          rq.obtainAllSpec(that.specParams).then(function (data) {
            if (data.result === '成功') {
              // console.log(data)
              that.specList = data.data.data
              that.specListTotal = data.data.totalRecord
              that.specListPageSize = data.data.pageSize
            } else if (data.result === '失败') {
              that.specList = []
            }
          })
        }
      }
    },
    mounted () {
      var that = this
      rq.obtainAllSpec(that.specParams).then(function (data) {
        if (data.result === '成功') {
          // console.log(data)
          that.specList = data.data.data
          that.specListTotal = data.data.totalRecord
          that.specListPageSize = data.data.pageSize
        } else if (data.result === '失败') {
          that.specList = []
        }
      })
      rq.searchAllCategory(that.typeParams).then(function (data) {
        if (data.result === '成功') {
          // console.log(data)
          that.categoryList = data.data.data
        } else if (data.result === '失败') {
          that.categoryList = []
        }
      })
    },
    components: {
      mBreadcrumb
    },
    computed: {
    }
  }
</script>
<style>
  .pages .el-pagination .el-pager li.number{
    margin-right: 5px;
    border:1px solid #9c9b9c;
  }
  .pages .el-pagination .el-pager li.number:hover{
    color: #ec6a47;
  }
  .pages .el-pagination button:hover{
    color: #ec6a47;
  }
  .pages .el-pagination .el-pager li.active{
    color: #494d56;
    border:1px solid #494d56;
  }
  .category .el-select{
    display: inline-block;
    width: 154px;
    border: 1px solid #cccccc;
    height: 28px;
    padding-left: 10px;
    border-radius: 3px;
  }
  .category .el-select .el-input{
    width: 154px;
    height: 28px;
    border: 0;
  }
  .category .el-select .el-input .el-input__inner{
    margin: 0;
    padding: 0;
    width: 154px;
    height: 28px;
    border: 0;
  }
</style>
<!-- 引入 commodityVariety Scss 文件  -->
<style lang="scss" scoped>@import "scss/commoditySpec";</style>
