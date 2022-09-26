<template>
  <el-dialog
    :title="!dataForm.cmmtId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="话题ID" prop="topicId">
      <el-input v-model="dataForm.topicId" placeholder="话题ID"></el-input>
    </el-form-item>
    <el-form-item label="评论者ID" prop="cmmtUserId">
      <el-input v-model="dataForm.cmmtUserId" placeholder="评论者ID"></el-input>
    </el-form-item>
    <el-form-item label="评论内容" prop="cmmtContent">
      <el-input v-model="dataForm.cmmtContent" placeholder="评论内容"></el-input>
    </el-form-item>
    <el-form-item label="评论日期" prop="createDt">
      <el-input v-model="dataForm.createDt" placeholder="评论日期"></el-input>
    </el-form-item>
    <el-form-item label="评论时间" prop="createTm">
      <el-input v-model="dataForm.createTm" placeholder="评论时间"></el-input>
    </el-form-item>
    <el-form-item label="评论状态" prop="cmmtSts">
      <el-input v-model="dataForm.cmmtSts" placeholder="评论状态"></el-input>
    </el-form-item>
    <el-form-item label="评论被赞量" prop="cmmtLikeCt">
      <el-input v-model="dataForm.cmmtLikeCt" placeholder="评论被赞量"></el-input>
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
          cmmtId: 0,
          topicId: '',
          cmmtUserId: '',
          cmmtContent: '',
          createDt: '',
          createTm: '',
          cmmtSts: '',
          cmmtLikeCt: ''
        },
        dataRule: {
          topicId: [
            { required: true, message: '话题ID不能为空', trigger: 'blur' }
          ],
          cmmtUserId: [
            { required: true, message: '评论者ID不能为空', trigger: 'blur' }
          ],
          cmmtContent: [
            { required: true, message: '评论内容不能为空', trigger: 'blur' }
          ],
          createDt: [
            { required: true, message: '评论日期不能为空', trigger: 'blur' }
          ],
          createTm: [
            { required: true, message: '评论时间不能为空', trigger: 'blur' }
          ],
          cmmtSts: [
            { required: true, message: '评论状态 1-有效 2-违规隐藏不能为空', trigger: 'blur' }
          ],
          cmmtLikeCt: [
            { required: true, message: '评论被赞量不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.cmmtId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.cmmtId) {
            this.$http({
              url: this.$http.adornUrl(`/topic/cmmt/info/${this.dataForm.cmmtId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.topicId = data.topicCommnet.topicId
                this.dataForm.cmmtUserId = data.topicCommnet.cmmtUserId
                this.dataForm.cmmtContent = data.topicCommnet.cmmtContent
                this.dataForm.createDt = data.topicCommnet.createDt
                this.dataForm.createTm = data.topicCommnet.createTm
                this.dataForm.cmmtSts = data.topicCommnet.cmmtSts
                this.dataForm.cmmtLikeCt = data.topicCommnet.cmmtLikeCt
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
              url: this.$http.adornUrl(`/topic/cmmt/${!this.dataForm.cmmtId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'cmmtId': this.dataForm.cmmtId || undefined,
                'topicId': this.dataForm.topicId,
                'cmmtUserId': this.dataForm.cmmtUserId,
                'cmmtContent': this.dataForm.cmmtContent,
                'createDt': this.dataForm.createDt,
                'createTm': this.dataForm.createTm,
                'cmmtSts': this.dataForm.cmmtSts,
                'cmmtLikeCt': this.dataForm.cmmtLikeCt
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
