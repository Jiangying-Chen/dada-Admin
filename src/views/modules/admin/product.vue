<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
         <el-button  type="primary" @click="addHandle()">新增</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column label="商品名称" align="center" prop="goodsName"/>
       <el-table-column label="市场价" align="center">
       <template slot-scope="scope">
              ￥{{scope.row.originalPrice ||0}}
          </template>
       </el-table-column>
       <el-table-column label="结算价" align="center">
         <template slot-scope="scope">
              ￥{{scope.row.settlementPrice ||0}}
          </template>
       </el-table-column>
      <el-table-column label="库存" align="center" prop="activeStock"/>
      <el-table-column label="兑换积分" align="center" prop="points"/>
      <el-table-column label="分类" align="center" prop="pointsGoodsCategoryName"/>
      <el-table-column fixed="right" header-align="center" align="center" width="150" label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <el-dialog :title="title" :visible.sync="addOrUpdateVisible" width="780px" append-to-body @close="close">
      <el-form ref="form" :model="form" :rules="rules" label-width="130px">
        <el-row>
          <el-col :span="24">
            <el-form-item label="id" style="display: none">
              <el-input v-model="form.id" autocomplete="off"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="22">
              <el-form-item label="商品名称" prop="goodsName" >
                  <el-input v-model="form.goodsName" autocomplete="off" placeholder="请输入商品名称"></el-input>
              </el-form-item>
          </el-col>
           <el-col :span="22">
              <el-form-item label="商品市场价" prop="originalPrice" >
                  <el-input v-model="form.originalPrice" autocomplete="off" placeholder="请输入商品市场价"></el-input>
              </el-form-item>
          </el-col>
          <el-col :span="22">
              <el-form-item label="原价" prop="price" >
                  <el-input v-model="form.price" autocomplete="off" placeholder="请输入原价"></el-input>
              </el-form-item>
          </el-col>
          <el-col :span="22">
            <el-form-item label="结算价格"  prop="settlementPrice" >
                 <el-input v-model="form.settlementPrice" autocomplete="off" placeholder="请输入结算价格"></el-input>
              <!-- <el-input v-model="form.settlementPrice" autocomplete="off"></el-input> -->
            </el-form-item>
          </el-col>
          <el-col :span="22">
            <el-form-item label="分类" prop="pointsGoodsCategoryName">
               <el-select  v-model="form.pointsGoodsCategoryName" placeholder="分类" clearable>
                  <el-option
                    v-for="dict in typeList"
                    :key="dict.id"
                    :label="dict.name"
                    :value="dict.name"
                  />
                </el-select>
            </el-form-item>
          </el-col>
           <el-col :span="22">
              <el-form-item label="库存" prop="activeStock" >
                  <el-input v-model="form.activeStock" autocomplete="off" placeholder="请输入库存"></el-input>
              </el-form-item>
          </el-col>
           <el-col :span="22">
            <el-form-item label="兑换积分" prop="points">
              <el-input v-model.number="form.points" autocomplete="off" placeholder="请输入兑换积分"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="22">
            <el-form-item label="已购数量" prop="buyCount">
              <el-input v-model.number="form.buyCount" autocomplete="off" placeholder="请输入已购数量"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="22">
            <el-form-item label="剩余数量" prop="residueStock">
              <el-input v-model.number="form.residueStock" autocomplete="off" placeholder="请输入剩余数量"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="22">
            <el-form-item label="兑换地址" prop="address">
              <el-input type="textarea" v-model="form.address" placeholder="请输入兑换地址"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="22">
            <el-form-item label="缩略图" prop="thumbnail" required>
              <el-upload
                class="avatar-uploader"
                :action="url"
                :show-file-list="false"
                :on-success="handleIconSuccess"
              >
                <img v-if="form.thumbnail" :src="form.thumbnail" class="avatar" />
                <i v-else class="el-icon-plus avatar-uploader-icon" />
              </el-upload>
            </el-form-item>
          </el-col>

          <!-- <el-col :span="22">
            <el-form-item label="轮播图" required>
              <el-upload
                :action="url"
                :file-list="myfileList"
                list-type="picture-card"
                :on-success="handleIconSuccess1"
                :on-remove="handleRemove">
                <i class="el-icon-plus"></i>
              </el-upload>
            </el-form-item>
          </el-col> -->
          <el-col :span="22">
            <el-form-item label="描述" prop="info">
              <el-input type="textarea" v-model="form.info" placeholder="请输入描述"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="22">
              <el-form-item label="是否上架" prop="enable" >
                <el-switch v-model="form.enable"></el-switch>
              </el-form-item>
          </el-col>
        </el-row>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="dataFormSubmit">确 定</el-button>
        <el-button @click="addOrUpdateVisible=false">取 消</el-button>
      </div>
    </el-dialog>


  </div>
</template>

<script>
  import qs from 'qs'
  import AddOrUpdate from './product-add-or-update'
  export default {
    data() {
      return {
        myfileList:[],
        slideshowList:[],
        slideList:[],
        dataForm: {
          key: ''
        },
        title:"",
        rules:{
          goodsName: [
            { required: true, message: "名称不能为空", trigger: "change" }
          ],
          originalPrice: [
            { required: true, message: "市场价格不能为空", trigger: "change" }
          ],
          settlementPrice:[
            { required: true, message: "结算价格不能为空", trigger: "change" }
          ],
          activeStock:[
            { required: true, message: "库存不能为空", trigger: "change" }
          ],
          points:[
            { required: true, message: "兑换积分不能为空", trigger: "change" }
          ],
          address:[
            { required: true, message: "兑换地址不能为空", trigger: "change" }
          ],
          buyCount:[
            { required: true, message: "已购数量不能为空", trigger: "change" }
          ],
          price:[
            { required: true, message: "原价不能为空", trigger: "change" }
          ],
          residueStock:[
            { required: true, message: "剩余数量不能为空", trigger: "change" }
          ],
          thumbnail:[
            { required: true, message: "缩略图不能为空", trigger: "change" }
          ],
        },
        form:{
          enable:true
        },
        url:'',
        typeList:[],
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        userId:'',
        userName:''
      }
    },
    components: {
      AddOrUpdate
    },
    created() {
      this.url=this.$http.adornUrl(
        `/sys/oss/upload?token=${this.$cookie.get("token")}`
      );
    },
    activated() {
      this.getDataList();
      this.getUserInfo();
      this.getTypeList();

    },
    methods: {
      // 重置表单
      close(){
         this.form={}
          // 2.清空校验
        this.$refs.form.resetFields()

      },
       handleRemove(file) {
       this.slideshowList.splice(file,1)
       console.log(this.slideshowList,'this.myfileList')
      },
      handleIconSuccess(response) {
        this.form.thumbnail = response.url;
        this.$forceUpdate();
      },
      handleIconSuccess1(response){
        if(response.code==0){
          this.slideList.push(response.url);
          let arr=this.slideList.map(v=>({
            url:v
            }))
          this.slideshowList =this.myfileList.concat(arr)
           console.log(arr,'====arrlist')
        }
        console.log(this.myfileList,'====arrlist11')
        this.$forceUpdate();
      },
      // 获取当前管理员信息
      getUserInfo () {
        this.$http({
          url: this.$http.adornUrl('/sys/user/info'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.loading = false
            this.userId = data.user.userId
            this.userName = data.user.username
          }
        })
      },
      getTypeList() {
         this.$http({
           url: this.$http.adornUrl('/admin/pointGoodsCategory'),
           method: 'get',
           params: this.$http.adornParams({
             'pageNumber': this.pageIndex,
             'pageSize': 100
           })
         }).then(({
           data
         }) => {
           if (data && data.code === 0) {
             this.typeList = data.result.records;
             console.log( this.typeList,' this.typeList==')
           } else {
             this.dataList = []
           }

         })
      },
      // 获取数据列表
      getDataList() {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/admin/pointGoods'),
          method: 'get',
          params: this.$http.adornParams({
            'pageNumber': this.pageIndex,
            'pageSize': this.pageSize,
          })
        }).then(({
          data
        }) => {
          if (data && data.code === 0) {
            this.dataList = data.result.records
            this.totalPage = data.result.total
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 每页数
      sizeChangeHandle(val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle(val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle(val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle(row) {
        this.addOrUpdateVisible = true;
        this.title = "修改";
        this.myfileList=[];
        this.form={...row};
        console.log(this.form,'this.form===1')
        // let arrList=row.slideshow?JSON.parse(row.slideshow):[];
        // this.myfileList=arrList.map(v=>({url:v.url}));
        console.log(this.myfileList,' this.myfileList====xiaugai')
      },
      addHandle(){
        this.addOrUpdateVisible = true;
        this.title = "添加";
        this.myfileList=[];
        this.form={
          enable:true
        }
      },
      dataFormSubmit () {
        this.$refs['form'].validate((valid) => {
          if (valid) {
            console.log(this.form,'form===')
           // let arrSlide= this.slideshowList.map(v=>({url:v.url}))
           //  this.form.slideshow = JSON.stringify(arrSlide);
            console.log(this.form.slideshow,'this.form.slideshow')
            if(this.form.id){
              let datas =  this.form;
              if(this.form.pointsGoodsCategoryName){
                datas.pointsGoodsCategoryId = this.typeList.filter(v=>v.name==this.form.pointsGoodsCategoryName)[0].id
              }
              console.log(datas,'datas===')
              this.$http({
                url: this.$http.adornUrl("/admin/pointGoods"),
                method: 'PUT',
                data:datas,
              }).then(({data}) => {
                if (data && data.code === 0) {
                  this.$message({
                    message: '操作成功',
                    type: 'success',
                    duration: 1500,
                    onClose: () => {
                      this.addOrUpdateVisible = false;
                      this.form={};
                      this.slideshowList=[];
                      this.slideList=[];
                      this.getDataList()
                    }
                  })
                } else {
                  this.$message.error(data.msg)
                }
              })
            }else{
              let datas = [
                this.form
              ];
              if(this.form.pointsGoodsCategoryName){
                datas[0].pointsGoodsCategoryId = this.typeList.filter(v=>v.name==this.form.pointsGoodsCategoryName)[0].id
              }
              this.$http({
                url: this.$http.adornUrl("/admin/pointGoods"),
                method: 'post',
                data:datas,
              }).then(({data}) => {
                if (data && data.code === 0) {
                  this.$message({
                    message: '操作成功',
                    type: 'success',
                    duration: 1500,
                    onClose: () => {
                      this.addOrUpdateVisible = false;
                      this.form={};
                      this.getDataList();
                      this.slideshowList=[];
                      this.slideList=[];
                      // this.$emit('refreshDataList')
                    }
                  })
                } else {
                  this.$message.error(data.msg)
                }
              })
            }


             // datas=this.form;
            let datas =  [this.form];
            if(this.form.pointsGoodsCategoryName){
                datas[0].pointsGoodsCategoryId = this.typeList.filter(v=>v.name==this.form.pointsGoodsCategoryName)[0].id
              }
            let datasList=this.form;
            if(this.form.pointsGoodsCategoryName){
                datasList.pointsGoodsCategoryId = this.typeList.filter(v=>v.name==this.form.pointsGoodsCategoryName)[0].id
              }



          }
        })
      },
      // 删除
      deleteHandle(id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/admin/pointGoods/'+ id),
            method: 'DELETE',
          }).then(({
            data
          }) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList();

                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      },
      //清理话题下的所有帖子
      deleteForce(id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]下的所有帖子分类进行[${id ? '删除' : '批量删除'}]操作(话题不删除)?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/admin/pointGoods/deletePostInDiscuss'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({
            data
          }) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      }
    }
  }
</script>

<style lang="scss">
.formInfo {
  line-height: 0px;
  color: #999999;
}
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409eff;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 100px;
  height: 100px;
  line-height: 100px;
  text-align: center;
}
.avatar {
  width: 100px;
  height: 100px;
  display: block;
}
</style>


