<template>
  <div class="ele-body">
    <el-card
      shadow="never"
      body-style="padding-bottom: 0;">
      <el-row :gutter="15">
        <el-col :lg="6" style="margin-bottom: 15px;">
          <!-- 数据表格 -->
          <ele-pro-table
            ref="table"
            :datasource="url"
            :columns="columns"
            height="calc(100vh - 261px)"
            :need-page="false"
            :toolkit="[]"
            :current.sync="current"
            highlight-current-row
            class="dict-table"
            tool-class="ele-toolbar-actions"
            @done="done">
            <!-- 表头工具栏 -->
            <template slot="toolbar">
              <el-button
                @click="openEdit(null)"
                type="primary"
                icon="el-icon-plus"
                class="ele-btn-icon"
                size="small"
                v-if="permission.includes('sys:config:add')">添加
              </el-button>
              <el-button
                @click="openEdit(current)"
                type="warning"
                icon="el-icon-edit"
                class="ele-btn-icon"
                :disabled="!current"
                size="small"
                v-if="permission.includes('sys:config:update')">修改
              </el-button>
              <el-button
                @click="remove"
                type="danger"
                icon="el-icon-delete"
                class="ele-btn-icon"
                :disabled="!current"
                size="small"
                v-if="permission.includes('sys:config:delete')">删除
              </el-button>
            </template>
          </ele-pro-table>
        </el-col>
        <el-col :lg="18" style="margin-bottom: 15px;">
          <!-- 数据字典项列表 -->
          <config-data v-if="current" :config-id="current.id"/>
        </el-col>
      </el-row>
    </el-card>
    <!-- 编辑弹窗 -->
    <config-edit
      :visible.sync="showEdit"
      :data="editData"
      @done="reload"/>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import ConfigData from './config-data';
import ConfigEdit from './config-edit';

export default {
  name: 'SystemConfig',
  components: {ConfigData, ConfigEdit},
  computed: {
    ...mapGetters(["permission"]),
  },
  data() {
    return {
      // 表格数据接口
      url: '/config/list',
      // 表格列配置
      columns: [
        {
          columnKey: 'index',
          type: 'index',
          width: 45,
          align: 'center',
          showOverflowTooltip: true
        },
        {
          prop: 'name',
          label: '字典名称',
          showOverflowTooltip: true
        }
      ],
      // 当前编辑数据
      current: null,
      // 是否显示编辑弹窗
      showEdit: false,
      // 编辑回显数据
      editData: null
    };
  },
  methods: {
    /* 表格渲染完成回调 */
    done(res) {
      if (res.data.length > 0) {
        this.$refs.table.setCurrentRow(res.data[0]);
      }
    },
    /* 刷新表格 */
    reload() {
      this.$refs.table.reload();
    },
    /* 显示编辑 */
    openEdit(row) {
      this.editData = row;
      this.showEdit = true;
    },
    /* 删除 */
    remove() {
      this.$confirm('确定要删除选中的字典吗?', '提示', {
        type: 'warning'
      }).then(() => {
        const loading = this.$loading({lock: true});
        this.$http.delete('/config/delete/' + this.current.id).then(res => {
          loading.close();
          if (res.data.code === 0) {
            this.$message.success(res.data.msg);
            this.reload();
          } else {
            this.$message.error(res.data.msg);
          }
        }).catch(e => {
          loading.close();
          this.$message.error(e.message);
        });
      }).catch(() => {
      });
    }
  }
}
</script>

<style scoped>
.dict-table ::v-deep .el-table__row {
  cursor: pointer;
}

.dict-table ::v-deep .el-table__row > td:last-child:after {
  content: "\e6e0";
  font-family: element-icons !important;
  font-style: normal;
  font-variant: normal;
  text-transform: none;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  line-height: 1;
  position: absolute;
  right: 10px;
  top: 50%;
  margin-top: -7px;
}

.dict-table ::v-deep .el-table__row > td:last-child .cell {
  padding-right: 20px;
}
</style>
