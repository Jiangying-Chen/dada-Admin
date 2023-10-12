<template>
  <div class="mod-config">
      <el-dialog
          :title="!dataForm.id ? '新增' : '修改'"
          :close-on-click-modal="false"
          :visible.sync="visible">
          <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
          <el-form-item label="名称" prop="name">
            <el-input v-model="dataForm.name" placeholder="名称"></el-input>
          </el-form-item>
            <el-form-item label="奖品类型" prop="prizeType">
              <el-radio-group v-model="dataForm.prizeType">
                <el-radio :label="1">积分</el-radio>
                <el-radio :label="2">谢谢惠顾</el-radio>
                <!-- <el-radio :label="3">红包</el-radio> -->
                <el-radio :label="4">商品</el-radio>
              </el-radio-group>
            </el-form-item>
            <el-form-item label="产品id" prop="goodsId" v-if="dataForm.prizeType==4">
              <el-input v-model="dataForm.goodsId" style="width: 370px;" disabled/>
              <el-button  type="primary" plain  icon="el-icon-plus" style="margin-left: 10px;" @click="selectAdd" >选择产品id</el-button>

            </el-form-item>
            <el-form-item label="图片" prop="image">
              <el-upload
                class="avatar-uploader"
                :action="url"
                :show-file-list="false"
                :on-success="handleBgImageSuccess"
              >
                <img v-if="dataForm.image" :src="dataForm.image" class="avatar" />
                <i v-else class="el-icon-plus avatar-uploader-icon" />
              </el-upload>
              <p class="formInfo">建议尺寸：200*200像素，jpg、png图片类型</p>
            </el-form-item>
          <el-form-item label="奖品数量" prop="number">
            <el-input v-model="dataForm.number" placeholder="奖品数量"></el-input>
          </el-form-item>
          <el-form-item label="抽奖几率" prop="probability">
            <el-input v-model="dataForm.probability" placeholder="抽奖几率"></el-input>
          </el-form-item>
          <el-form-item label="排序" prop="sort">
            <el-input v-model="dataForm.sort" placeholder="排序"></el-input>
          </el-form-item>
          <el-form-item label="抽奖状态" prop="status">
              <el-radio-group v-model="dataForm.status">
                <el-radio :label="1">正常</el-radio>
                <el-radio :label="0">禁用</el-radio>
              </el-radio-group>
          </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="visible = false">取消</el-button>
            <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
          </span>
        </el-dialog>
        <el-dialog title="产品列表" :visible.sync="dialogVisible" width="80%">
            <el-row>
              <el-table v-loading="dataListLoading"  highlight-current-row
                  @current-change="handleCurrentChange" :data="productdataList" border ref="singleTable" >
                 <el-table-column label="选择" width="50" align="center" >
                    <template scope="scope" >
                      <el-radio :label="scope.row.goodsId" v-model="mygoodsId" >
                        <span ></span>
                      </el-radio>
                    </template>
                  </el-table-column>
                 <el-table-column label="商品名称" align="center" prop="goodsName" width="150px"/>
                <el-table-column label="id" align="center" prop="id" width="180px">
                </el-table-column>
                <el-table-column label="库存" align="center" prop="activeStock"/>
                <el-table-column label="兑换积分" align="center" prop="points" />
                <el-table-column label="结算价" align="center" prop="settlementPrice" width="80px"/>
                <el-table-column label="市场价" align="center" prop="originalPrice" width="140px"/>
              </el-table>
            </el-row>
            <el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="queryParams.pageNumber"
              :page-sizes="[10, 20, 50, 100]" :page-size="queryParams.pageSize" :total="producttotalPage"
              layout="total, sizes, prev, pager, next, jumper">
            </el-pagination>
            <div slot="footer" class="dialog-footer">
              <el-button type="primary" @click="handleSubmit">确 定</el-button>
              <el-button @click="dialogVisible  = false">取 消</el-button>
            </div>
          </el-dialog>

  </div>


</template>

<script>

  export default {

    data () {
      return {
        mygoodsId:0,
        dialogVisible:false,
        mythumbnail:'',
        visible: false,
        isVisible:false,
        producttotalPage:0,
        dataListLoading:false,
        productOther:[],
        ids:[],
        productdataList:[],
        queryParams:{
          pageNumber: 1,
          pageSize: 10
        },
        url:'',
        dataForm: {
          id: 0,
          prizeType: '',
          name: '',
          image: '',
          number: '',
          probability: '',
          sort: '',
          status: '',
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          prizeType: [
            { required: true, message: '奖品类型不能为空', trigger: 'blur' }
          ],
          name: [
            { required: true, message: '名称不能为空', trigger: 'blur' }
          ],
          image: [
            { required: true, message: '图片不能为空', trigger: 'blur' }
          ],
          number: [
            { required: true, message: '奖品数量不能为空', trigger: 'blur' }
          ],
          probability: [
            { required: true, message: '抽奖几率不能为空', trigger: 'blur' }
          ],
          sort: [
            { required: true, message: '排序不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '抽奖状态不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          updateTime: [
            { required: true, message: '更新时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    created() {

    },
    methods: {
      init (id) {
      this.url = this.$http.adornUrl(
        `/sys/oss/upload?token=${this.$cookie.get("token")}`
        );
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/admin/luckdraw/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.prizeType = data.luckdraw.prizeType
                this.dataForm.name = data.luckdraw.name
                this.dataForm.image = data.luckdraw.image
                this.dataForm.number = data.luckdraw.number
                this.dataForm.probability = data.luckdraw.probability
                this.dataForm.sort = data.luckdraw.sort
                this.dataForm.status = data.luckdraw.status
                this.dataForm.createTime = data.luckdraw.createTime
                this.dataForm.updateTime = data.luckdraw.updateTime
                this.dataForm.goodsId=data.luckdraw.goodsId;
                // this.productOther= [{
                //   goodsId:this.dataForm.goodsId
                // }]
                this.mygoodsId = Number(data.luckdraw.goodsId);
                console.log(this.productOther,'this.productOther===')
              }
            })
          }else{
            this.dataForm={};
             this.mygoodsId = 0;
             this.mythumbnail='';
             this.productOther=[];
          }
        })
      },
      handleCurrentChange(currentRow, oldCurrentRow) {
        if(currentRow){
           this.mygoodsId = currentRow.goodsId;
            this.mythumbnail=currentRow.thumbnail
        }
        console.log(currentRow,oldCurrentRow,'单选===')
      },
      handleSubmit(){
          this.dataForm.goodsId= this.mygoodsId;
          this.dataForm.image = this.mythumbnail;
          console.log( this.dataForm.image,' this.dataForm.image===')
           this.dialogVisible=false;
      },
      handleBgImageSuccess(response) {
      this.dataForm.image = response.url;
      this.$forceUpdate();
      },
      // 选择产品
      selectAdd(){
        this.dialogVisible=true;
        // console.log(this.dataForm.goodsId,'goodsId=====')
        this.getproductDataList()
        // this.isVisible=true;
        // this.ids=[];
        // this.ids = [...this.productOther];
        // this.getproductDataList()
      },
      handleSelectionChange(selection) {
        this.productOther = selection.map(v=>({
          goodsId:v.id
        }));
         console.log(selection, this.productOther,'otherproductOther')

      },
      // 每页数
      sizeChangeHandle(val) {
        this.queryParams.pageSize = val
        this.queryParams.pageNumber = 1
        this.getproductDataList()
      },
      // 当前页
      currentChangeHandle(val) {
        console.log(val,'val====当前页')
        this.queryParams.pageNumber = val
        this.getproductDataList()
      },
      // 获取产品数据列表
      getproductDataList() {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/admin/pointGoods'),
          method: 'get',
          params: this.$http.adornParams({
            'pageNumber': this.queryParams.pageNumber,
            'pageSize': this.queryParams.pageSize,
          })
        }).then(({
          data
        }) => {
          if (data && data.code === 0) {
            data.result.records.forEach(v=>{
              v.goodsId=v.id;
            });
            this.productdataList = data.result.records;
            this.producttotalPage = data.result.total;
            // this.$nextTick(() => {
            //   console.log(this.ids, data.result.records,'====ids')
            //   data.result.records.forEach(item => {
            //       if(this.ids.length>0){
            //         this.ids.forEach(row => {
            //           if (item.id == row.goodsId) {
            //             this.$refs.productTable.toggleRowSelection(item,true);
            //           }
            //         });
            //       }
            //   })
            // });
          } else {
            this.productdataList = []
            this.producttotalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 单选
      rowSelectable(row, index) {
        let allids=this.productOther.map(v=>v.goodsId);
        if (this.productOther.length==1) {
          return allids.includes(row.goodsId);
        }else{
          return true
        }

      },
      productsubmitForm(){
            this.ids = [...this.productOther];
            console.log(this.ids,'this.ids=======')
            this.isVisible=false;
            this.dataForm.goodsId= this.ids[0].goodsId;
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/admin/luckdraw/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'prizeType': this.dataForm.prizeType,
                'name': this.dataForm.name,
                'image': this.dataForm.image,
                'number': this.dataForm.number,
                'probability': this.dataForm.probability,
                'sort': this.dataForm.sort,
                'status': this.dataForm.status,
                'createTime': this.dataForm.createTime,
                'updateTime': this.dataForm.updateTime,
                'goodsId':this.dataForm.goodsId
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
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
