<!-- 编辑弹窗 -->
<template>
  <el-dialog
    width="400px"
    :visible="visible"
    :lock-scroll="false"
    :destroy-on-close="true"
    custom-class="ele-dialog-form"
    :title="isUpdate?'修改城市':'添加城市'"
    @update:visible="updateVisible">
    <el-form
      ref="form"
      :model="form"
      :rules="rules"
      label-width="92px"
      @keyup.enter.native="save"
      @submit.native.prevent>
      <el-form-item label="城市名称:" prop="name">
        <el-input
          clearable
          v-model="form.name"
          placeholder="请输入城市名称"/>
      </el-form-item>
      <el-form-item label="城市等级:" prop="level">
        <el-select
          clearable
          class="ele-block"
          v-model="form.level"
          placeholder="请选择城市等级">
          <el-option label="省份" :value="1"/>
          <el-option label="城市" :value="2"/>
          <el-option label="县区" :value="3"/>
          <el-option label="街道" :value="4"/>
        </el-select>
      </el-form-item>
      <el-form-item label="城市编码:" prop="citycode">
        <el-input
          clearable
          v-model="form.citycode"
          placeholder="请输入城市编码"/>
      </el-form-item>
      <el-form-item label="地里编码:" prop="adcode">
        <el-input
          clearable
          v-model="form.adcode"
          placeholder="请输入地里编码"/>
      </el-form-item>
      <el-form-item label="经度:" prop="lng">
        <el-input
          clearable
          v-model="form.lng"
          placeholder="请输入经度"/>
      </el-form-item>
      <el-form-item label="纬度:" prop="lat">
        <el-input
          clearable
          v-model="form.lat"
          placeholder="请输入纬度"/>
      </el-form-item>
      <el-form-item label="排序号:" prop="sortNumber">
        <el-input-number
          :min="0"
          v-model="form.sort"
          placeholder="请输入排序号"
          controls-position="right"
          class="ele-fluid ele-text-left"/>
      </el-form-item>
    </el-form>
    <div slot="footer">
      <el-button @click="updateVisible(false)">取消</el-button>
      <el-button
        type="primary"
        :loading="loading"
        @click="save">保存
      </el-button>
    </div>
  </el-dialog>
</template>

<script>
export default {
  name: 'CityEdit',
  components: {},
  props: {
    // 弹窗是否打开
    visible: Boolean,
    // 修改回显的数据
    data: Object,
    // 全部城市数据
    deptList: Array
  },
  data() {
    return {
      // 表单数据
      form: this.initFormData(this.data),
      // 表单验证规则
      rules: {
        name: [
          {required: true, message: '请输入城市名称', trigger: 'blur'}
        ],
        level: [
          {required: true, message: '请选择城市等级', trigger: 'blur'}
        ],
        citycode: [
          {required: true, message: '请输入城市编码', trigger: 'blur'}
        ],
        sort: [
          {required: true, message: '请输入排序号', trigger: 'blur'}
        ]
      },
      // 提交状态
      loading: false,
      // 是否是修改
      isUpdate: false
    };
  },
  watch: {
    data() {
      this.isUpdate = !!(this.data && this.data.id);
      this.form = this.initFormData(this.data);
    }
  },
  methods: {
    /* 保存编辑 */
    save() {
      this.$refs['form'].validate((valid) => {
        if (valid) {
          this.loading = true;
          this.$http[this.isUpdate ? 'put' : 'post'](this.isUpdate ? '/city/update' : '/city/add',
            Object.assign({}, this.form, {
              pid: this.form.pid || 0
            })
          ).then(res => {
            this.loading = false;
            if (res.data.code === 0) {
              this.$message.success(res.data.msg);
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
    },
    /* 初始化form数据 */
    initFormData(data) {
      // 初始化默认值
      let form = {level: 1};
      if (data) {
        Object.assign(form, data, {
          pid: data.pid === 0 ? null : data.pid,
        });
      }
      return form;
    },
  }
}
</script>

<style scoped>
</style>
