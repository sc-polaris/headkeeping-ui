<template>
  <div class="category">
    <!-- 按钮 -->
    <div class="btns">
      <el-button type="primary" size="small" @click="toAdd">添加</el-button>
    </div>
    <!-- 表格  -->
    <el-table :data="categories" row-key="id" size="small" :tree-props="{children: 'children'}">
      <el-table-column prop="name" label="栏目名称" width="120"></el-table-column>
      <el-table-column prop="nu" label="序号"></el-table-column>
      <el-table-column fixed="right" label="操作" width="160" align="center">
        <template slot-scope="scope">
          <el-button type="danger" @click="del(scope.row.id)" size="mini">删除</el-button>
          <el-button type="primary" @click="edit(scope.row)" size="mini">编辑</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 表格结束 -->
    <!-- 模态框 -->
    <el-dialog title="栏目信息" :visible.sync="visible">
      <el-form :model="form" :rules="rules" ref="ruleForm" label-width="80px">
        <el-form-item label="栏目名称" prop="name">
          <el-input v-model="form.name" clearable placeholder="请输入栏目名称"></el-input>
        </el-form-item>
        <el-form-item label="数量" prop="nu">
          <el-input v-model="form.nu" clearable placeholder="请输入数量"></el-input>
        </el-form-item>
        <el-form-item label="所属栏目" prop="parentId">
          <el-select v-model="form.parentId" placeholder="请选择所属栏目">
            <el-option v-for="c in categories" :key="c.id" :label="c.name" :value="c.id"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button size="small" @click="close('ruleForm')">取消</el-button>
        <el-button size="small" type="primary" @click="submit('ruleForm')">确定</el-button>
      </div>
    </el-dialog>
    <!-- 模态框结束 -->
  </div>
</template>
<script>
import { get, post } from '@/utils/request'

export default {
  data() {
    return {
      name: '栏目管理',
      visible: false,
      form: {},
      categories: [],
      ruleForm: {
        name: '',
        nu: '',
        parentId: ''
      },
      rules: {
        name: [{ required: true, message: '请输入栏目名称', trigger: 'blur' }],
        nu: [{ required: false, message: '请输入数量', trigger: 'blur' }],
        parentId: [
          { required: false, message: '请选择所属栏目', trigger: 'blur' }
        ]
      }
    }
  },
  created() {
    this.loadCategories()
  },
  methods: {
    // 加载栏目信息
    loadCategories() {
      const url = this.baseUrl + '/category/findAllWithChild'
      get(url).then(response => {
        this.categories = response.data
      })
    },
    toAdd() {
      // 重置form
      this.form = {}
      // 打开模态框
      this.visible = true
    },
    del(id) {
      this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // 删除
        const url = this.baseUrl + '/category/deleteById'
        get(url, { id: id }).then((response) => {
          // 提示
          this.$message({ type: 'success', message: response.message })
          // 刷新数据
          this.loadCategories()
        })
      })
    },
    edit(row) {
      this.visible = true
      this.form = row
    },
    close(form) {
      this.visible = false
      this.$refs[form].resetFields()
    },
    submit(form) {
      this.$refs[form].validate(valid => {
        if (valid) {
          // 表单验证成功的情况
          const url = this.baseUrl + '/category/saveOrUpdate'
          post(url, this.form).then(response => {
            this.$message({ type: 'success', message: response.message })
            this.visible = false
            this.loadCategories()
          })
        } else {
          return false
        }
      })
    }
  }
}
</script>
<style scoped>
.btns {
  margin-bottom: .5em;
}
</style>
