<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
  >
  <!-- @keyup.enter.native="dataFormSubmit()" -->
    <el-form
      :model="dataForm"
      :rules="dataRule"
      ref="dataForm"

      label-width="80px"
    >
      <el-form-item label="标题" prop="title">
        <el-input v-model="dataForm.title" placeholder="标题"></el-input>
      </el-form-item>
      <el-form-item label="图片" prop="coverImage">
        <el-upload
          class="avatar-uploader"
          :action="url"
          :show-file-list="false"
          :on-success="handleIconSuccess"
        >
          <img v-if="dataForm.img" :src="dataForm.img" class="avatar" />
          <i v-else class="el-icon-plus avatar-uploader-icon" />
        </el-upload>
        <p class="formInfo">建议尺寸：500*250像素，jpg、png图片类型</p>
      </el-form-item>
     <!-- <el-form-item label="路径" prop="url">
        <el-input v-model="dataForm.url" placeholder="路径"></el-input>
      </el-form-item> -->
      <!-- @change="onEditorChange($event)" -->
      <el-form-item label="详情" prop="linkInfo">
        <quill-editor
        ref="myQuillEditor"
              v-model="dataForm.linkInfo"
              :options="editorOption"
            >
            </quill-editor>
      </el-form-item>
      <!--  -->
      <el-form-item  style="display: none;">
        <el-upload
              drag
              multiple
              class="quill-upload"
              :on-success="quillSuccess"
              :action="url"
            >
             <i class="el-icon-upload"></i>
              <div class="el-upload__text">
                将文件拖到此处，或
                <em>点击上传</em>
              </div>
              <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
            </el-upload>
      </el-form-item>
      <!-- <el-form-item label="跳转类型" prop="type">
        <el-radio-group v-model="dataForm.type">
          <el-radio :label="3">页面</el-radio>
          <el-radio :label="1">外链</el-radio>
        </el-radio-group>
        <p class="formInfo">外链就是外部网站的链接，页面指项目内部页面的跳转</p>
      </el-form-item>
      <el-form-item label="分类" prop="cateId">
        <el-radio-group v-model="dataForm.cateId">
          <el-radio :label="0">广场页</el-radio>
          <el-radio :label="1">个人页</el-radio>
        </el-radio-group>
      </el-form-item> -->
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import { quillEditor,Quill } from "vue-quill-editor";
  import 'quill/dist/quill.core.css'
  import 'quill/dist/quill.snow.css'
  import 'quill/dist/quill.bubble.css'
  //设置字体大小
  let fontSizeStyle=Quill.import('attributors/style/size') //引入这个后会把样式写在style上
      fontSizeStyle.whitelist=['45px','60px','90px']
      Quill.register(fontSizeStyle,true)

  //设置字体样式
  let Font = Quill.import('attributors/style/font') //引入这个后会把样式写在style上
      let fonts = [false, 'SimSun', 'SimHei','Microsoft-YaHei','KaiTi','FangSong','Arial']
      Font.whitelist = fonts //将字体加入到白名单
      Quill.register(Font, true)


const toolbarOptions = [
    ['bold', 'italic', 'underline', 'strike'],        // 加粗，斜体，下划线，删除线
    ['blockquote', 'code-block'],                      //引用，代码块
    [{ 'header': 1 }, { 'header': 2 }],               // 几级标题
    [{ 'list': 'ordered'}, { 'list': 'bullet' }],     // 有序列表，无序列表
    [{ 'script': 'sub'}, { 'script': 'super' }],      // 下角标，上角标
    [{ 'indent': '-1'}, { 'indent': '+1' }],          // 缩进
    [{ 'direction': 'rtl' }],                         // 文字输入方向
    [{ 'size': ['45px','60px','90px'] }],  // 字体大小
    [{ 'header': [1, 2, 3, 4, 5, 6, false] }],// 标题
    [{ 'color': [] }, { 'background': [] }],          // 颜色选择
    [{ 'font': ['SimSun', 'SimHei','Microsoft-YaHei','KaiTi','FangSong','Arial'] }],// 字体
    [{ 'align': [] }], // 居中
    ['clean'],                                   // 清除样式
    ["image"], //, "video"上传图片、上传视频
]
export default {

  components:{
    quillEditor
  },
  data() {
    return {
            editorOption: {//配置工具栏
            placeholder:'请输入内容',
              modules: {
                toolbar: {
                  container: toolbarOptions,
                  handlers: {
                    // 重写点击组件上的图片按钮要执行的代码
                        'image': function (value) {
                          document.querySelector('.quill-upload .el-icon-upload').click()
                        }
                      }
                }
              },

       },
      url: "",
      visible: false,
      dataForm: {
        id: 0,
        title: "",
        url: "",
        img: "",
        type: 3,
        createTime: "",
        cateId:0,
        linkInfo:''
      },
      dataRule: {
        title: [{ required: true, message: "标题不能为空", trigger: "blur" }],
        // url: [{ required: true, message: "路径不能为空", trigger: "blur" }],
        img: [{ required: true, message: "图片不能为空", trigger: "blur" }],
        type: [
          { required: true, message: "跳转类型不能为空", trigger: "blur" },
        ],

      },
    };
  },
  methods: {
    // beforeUploadHandle (file) {
    //   if (file.type !== 'image/jpg' && file.type !== 'image/jpeg' && file.type !== 'image/png' && file.type !== 'image/gif') {
    //     this.$message.error('只支持jpg、png、gif格式的图片！')
    //     return false
    //   }
    //   this.num++
    // },
    quillSuccess(response) {
          if(response){
            console.log(response,'response==')
          // 获取文本编辑器
          const quill=this.$refs.myQuillEditor.quill
          // 获取光标位置
          const pos=quill.getSelection().index
          // 插入图片到光标位置
          quill.insertEmbed(pos,'image',response.url)
          }else{
            this.$essage.error('图片插入失败')
          }
    },
     // onEditorChange(editor) {
     //    console.log(editor,'editor==')
     // },
    init(id) {
      this.dataForm.id = id || 0;
      this.url = this.$http.adornUrl(
        `/sys/oss/upload?token=${this.$cookie.get("token")}`
      );
      this.visible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].resetFields();
        if (this.dataForm.id) {
          this.$http({
            url: this.$http.adornUrl(`/admin/link/info/${this.dataForm.id}`),
            method: "get",
            params: this.$http.adornParams(),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.title = data.link.title;
              this.dataForm.url = data.link.url;
              this.dataForm.img = data.link.img;
              this.dataForm.type = data.link.type;
              this.dataForm.cateId = data.link.cateId;
              this.dataForm.createTime = data.link.createTime;
              this.dataForm.linkInfo=data.link.linkInfo
            }
          });
        }else{
          this.dataForm= {
            id: 0,
            title: "",
            url: "",
            img: "",
            type: 3,
            createTime: "",
            cateId:0,
            linkInfo:''
          }
        }
      });
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs["dataForm"].validate((valid) => {
        if (valid) {
          console.log('dataForm',this.dataForm)
          this.$http({
            url: this.$http.adornUrl(
              `/admin/link/${!this.dataForm.id ? "save" : "update"}`
            ),
            method: "post",
            data: this.$http.adornData({
              id: this.dataForm.id || undefined,
              title: this.dataForm.title,
              url: this.dataForm.url,
              img: this.dataForm.img,
              type: this.dataForm.type,
              cateId: this.dataForm.cateId,
              createTime: this.dataForm.createTime,
              linkInfo:this.dataForm.linkInfo
            }),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.visible = false;
                  this.$emit("refreshDataList");
                },
              });
            } else {
              this.$message.error(data.msg);
            }
          });
        }
      });
    },
    handleIconSuccess(response) {
      this.dataForm.img = response.url;
      this.$forceUpdate();
    },
  },
};
</script>
<style scoped>
  .quill-editor >>> .ql-container {
     min-height: 300px;
   }
 .quill-editor >>> .ql-container {
    min-height: 300px;
  }
  .quill-editor >>> .ql-picker.ql-font .ql-picker-label[data-value=SimSun]::before,
  .quill-editor >>> .ql-picker.ql-font .ql-picker-item[data-value=SimSun]::before {
    content: "宋体";
    font-family: "SimSun"!important;
  }
  .quill-editor >>> .ql-picker.ql-font .ql-picker-label[data-value=SimHei]::before,
  .quill-editor >>> .ql-picker.ql-font .ql-picker-item[data-value=SimHei]::before {
    content: "黑体";
    font-family: "SimHei";
  }
  .quill-editor >>> .ql-picker.ql-font .ql-picker-label[data-value=Microsoft-YaHei]::before,
  .quill-editor >>> .ql-picker.ql-font .ql-picker-item[data-value=Microsoft-YaHei]::before {
    content: "微软雅黑";
    font-family: "Microsoft YaHei";
  }
  .quill-editor >>> .ql-picker.ql-font .ql-picker-label[data-value=KaiTi]::before,
  .quill-editor >>> .ql-picker.ql-font .ql-picker-item[data-value=KaiTi]::before {
    content: "楷体";
    font-family: "KaiTi"!important;
  }
  .quill-editor >>> .ql-picker.ql-font .ql-picker-label[data-value=FangSong]::before,
  .quill-editor >>> .ql-picker.ql-font .ql-picker-item[data-value=FangSong]::before {
    content: "仿宋";
    font-family: "FangSong";
  }
  .quill-editor >>> .ql-snow .ql-picker.ql-size .ql-picker-label[data-value='45px']::before,
  .quill-editor >>> .ql-snow .ql-picker.ql-size .ql-picker-item[data-value='45px']::before {
      content: '45px';
  }
  .quill-editor >>> .ql-snow .ql-picker.ql-size .ql-picker-label[data-value='60px']::before,
  .quill-editor >>> .ql-snow .ql-picker.ql-size .ql-picker-item[data-value='60px']::before {
      content: '60px';
  }
  .quill-editor >>> .ql-snow .ql-picker.ql-size .ql-picker-label[data-value='90px']::before,
  .quill-editor >>> .ql-snow .ql-picker.ql-size .ql-picker-item[data-value='90px']::before {
      content: '90px';
  }
</style>
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
  width: 200px;
  height: 100px;
  line-height: 100px;
  text-align: center;
}
.avatar {
  width: 200px;
  height: 100px;
  display: block;
}
</style>
