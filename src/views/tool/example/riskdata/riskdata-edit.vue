<!-- 安全风险库编辑弹窗 -->
<template>
  <el-dialog
    :title="isUpdate?'修改安全风险库':'添加安全风险库'"
    :visible="visible"
    width="460px"
    :destroy-on-close="true"
    :lock-scroll="false"
    @update:visible="updateVisible">
    <el-form
      ref="form"
      :model="form"
      :rules="rules"
      label-width="82px">
      
      
      
      <el-form-item
        label="标题:"
        prop="title">
        <el-input
          :maxlength="20"
          v-model="form.title"
          placeholder="请输入标题"
          clearable/>
      </el-form-item>
      
      
      
      
      
      
      <el-form-item label="状态:" prop="status">
        <el-radio-group
          v-model="form.status">
          
          <el-radio :label="1">在用</el-radio>
          
          <el-radio :label="2">停用</el-radio>
          
        </el-radio-group>
      </el-form-item>
      
      
      
      
      
      
      <el-form-item
        label="排序:"
        prop="sort">
        <el-input
          :maxlength="20"
          v-model="form.sort"
          placeholder="请输入排序"
          clearable/>
      </el-form-item>
      
      
      
    </el-form>
    <div slot="footer">
      <el-button @click="updateVisible(false)">取消</el-button>
      <el-button
        type="primary"
        @click="save"
        :loading="loading">保存
      </el-button>
    </div>
  </el-dialog>
</template>

<script>




export default {
  name: 'RiskdataEdit',
  components: {

  },
  props: {
    // 弹窗是否打开
    visible: Boolean,
    // 修改回显的数据
    data: Object
  },
  data() {
    return {
      // 表单数据
      form: Object.assign({}, this.data),
      // 表单验证规则
      rules: {
	
		
		
		title: [
          {required: true, message: '请输入标题', trigger: 'blur'}
        ],
		
		
	
		
		
		status: [
          {required: true, message: '请选择状态', trigger: 'blur'}
        ],
		
		
	
		
		
		sort: [
          {required: true, message: '请输入排序', trigger: 'blur'}
        ],
		
		
	
      },
      // 提交状态
      loading: false,
      // 是否是修改
      isUpdate: false
    };
  },
  watch: {
    data() {
      if (this.data) {
        this.form = Object.assign({}, this.data);
        this.isUpdate = true;
      } else {
        this.form = {};
        this.isUpdate = false;
      }
    }
  },
  computed: {
    // 初始化富文本
    initEditor() {
      return {
        height: 300,
        branding: false,
        skin_url: '/tinymce/skins/ui/oxide',
        content_css: '/tinymce/skins/content/default/content.css',
        language_url: '/tinymce/langs/zh_CN.js',
        language: 'zh_CN',
        plugins: 'code print preview fullscreen paste searchreplace save autosave link autolink image imagetools media table codesample lists advlist hr charmap emoticons anchor directionality pagebreak quickbars nonbreaking visualblocks visualchars wordcount',
        toolbar: 'fullscreen preview code | undo redo | forecolor backcolor | bold italic underline strikethrough | alignleft aligncenter alignright alignjustify | outdent indent | numlist bullist | formatselect fontselect fontsizeselect | link image media emoticons charmap anchor pagebreak codesample | ltr rtl',
        toolbar_drawer: 'sliding',
        images_upload_handler: (blobInfo, success, error) => {
          let file = blobInfo.blob();
          // 使用axios上传
          const formData = new FormData();
          formData.append('file', file, file.name);
          this.$http.post('/upload/uploadImage', formData).then(res => {
            if (res.data.code == 0) {
              success(res.data.data.fileUrl);
            } else {
              error(res.data.msg);
            }
          }).catch(e => {
            console.error(e);
            error(e.message);
          });
        },
        file_picker_types: 'media',
        file_picker_callback: () => {
        }
      }
    }
  },
  methods: {
    /* 保存编辑 */
    save() {
      this.$refs['form'].validate((valid) => {
        if (valid) {
          this.loading = true;
          this.$http[this.form.id ? 'put' : 'post'](this.isUpdate ? '/riskdata/update' : '/riskdata/add', this.form).then(res => {
            this.loading = false;
            if (res.data.code === 0) {
              this.$message.success(res.data.msg);
              if (!this.isUpdate) {
                this.form = {};
              }
              this.updateVisible(false);
              this.$emit('done');
            } else {
              this.$message.error(res.data.msg);
            }
          }).catch(e => {
            this.loading = false;
            this.$message.error(e.message);
          });
        } else {
          return false;
        }
      });
    },
    /* 更新visible */
    updateVisible(value) {
      this.$emit('update:visible', value);
    }
  }
}
</script>

<style scoped>
</style>
