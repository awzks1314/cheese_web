<template>
  <el-dialog
    :title="!dataForm.likeDtlId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="文档ID" prop="docmId">
      <el-input v-model="dataForm.docmId" placeholder="文档ID"></el-input>
    </el-form-item>
    <el-form-item label="点赞人ID" prop="docmLikeUser">
      <el-input v-model="dataForm.docmLikeUser" placeholder="点赞人ID"></el-input>
    </el-form-item>
    <el-form-item label="点赞日期" prop="docmLikeDt">
      <el-input v-model="dataForm.docmLikeDt" placeholder="点赞日期"></el-input>
    </el-form-item>
    <el-form-item label="点赞时间" prop="docmLikeTm">
      <el-input v-model="dataForm.docmLikeTm" placeholder="点赞时间"></el-input>
    </el-form-item>
    <el-form-item label="类名" prop="typename">
      <el-input v-model="dataForm.typename" placeholder="类名"></el-input>
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
          likeDtlId: 0,
          docmId: '',
          docmLikeUser: '',
          docmLikeDt: '',
          docmLikeTm: '',
          typename: ''
        },
        dataRule: {
          docmId: [
            { required: true, message: '文档ID不能为空', trigger: 'blur' }
          ],
          docmLikeUser: [
            { required: true, message: '点赞人ID不能为空', trigger: 'blur' }
          ],
          docmLikeDt: [
            { required: true, message: '点赞日期不能为空', trigger: 'blur' }
          ],
          docmLikeTm: [
            { required: true, message: '点赞时间不能为空', trigger: 'blur' }
          ],
          typename: [
            { required: true, message: '类名不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.likeDtlId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.likeDtlId) {
            this.$http({
              url: this.$http.adornUrl(`/document/documentlikedtl/info/${this.dataForm.likeDtlId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.docmId = data.documentLikeDtl.docmId
                this.dataForm.docmLikeUser = data.documentLikeDtl.docmLikeUser
                this.dataForm.docmLikeDt = data.documentLikeDtl.docmLikeDt
                this.dataForm.docmLikeTm = data.documentLikeDtl.docmLikeTm
                this.dataForm.typename = data.documentLikeDtl.typename
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
              url: this.$http.adornUrl(`/document/documentlikedtl/${!this.dataForm.likeDtlId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'likeDtlId': this.dataForm.likeDtlId || undefined,
                'docmId': this.dataForm.docmId,
                'docmLikeUser': this.dataForm.docmLikeUser,
                'docmLikeDt': this.dataForm.docmLikeDt,
                'docmLikeTm': this.dataForm.docmLikeTm,
                'typename': this.dataForm.typename
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
