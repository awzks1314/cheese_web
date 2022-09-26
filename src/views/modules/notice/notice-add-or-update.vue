<template>
  <el-dialog
    :title="!dataForm.noticeId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="标题" prop="title">
      <el-input v-model="dataForm.title" placeholder="标题"></el-input>
    </el-form-item>
    <el-form-item label="内容" prop="content">
      <el-input v-model="dataForm.content" placeholder="内容"></el-input>
    </el-form-item>
    <el-form-item label="广播对象" prop="noticeTo">
      <el-input v-model="dataForm.noticeTo" placeholder="广播对象"></el-input>
    </el-form-item>
    <el-form-item label="类型" prop="type">
      <el-input v-model="dataForm.type" placeholder="类型"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          noticeId: 0,
          title: '',
          content: '',
          noticeTo: '',
          type: ''
        },
        dataRule: {
          content: [
            { required: true, message: '内容不能为空', trigger: 'blur' }
          ],
          noticeTo: [
            { required: true, message: '广播对象不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '类型不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.noticeId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.noticeId) {
            this.$http({
              url: this.$http.adornUrl(`/notice/info/${this.dataForm.noticeId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.title = data.notice.title
                this.dataForm.content = data.notice.content
                this.dataForm.noticeTo = data.notice.noticeTo
                this.dataForm.type = data.notice.type
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/notice/${!this.dataForm.noticeId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'noticeId': this.dataForm.noticeId || undefined,
                'title': this.dataForm.title,
                'content': this.dataForm.content,
                'noticeTo': this.dataForm.noticeTo,
                'type': this.dataForm.type
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
