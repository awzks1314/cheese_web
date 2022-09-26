<template>
  <el-dialog
    :title="!dataForm.topicId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="发布者 " prop="topicPublisher">
      <el-input v-model="dataForm.topicPublisher" placeholder="发布者 "></el-input>
    </el-form-item>
    <el-form-item label="类型" prop="topicKind">
      <el-input v-model="dataForm.topicKind" placeholder="类型"></el-input>
    </el-form-item>
    <el-form-item label="摘要" prop="topicAbstract">
      <el-input v-model="dataForm.topicAbstract" placeholder="摘要"></el-input>
    </el-form-item>
    <el-form-item label="内容" prop="topicContent">
      <el-input v-model="dataForm.topicContent" placeholder="内容"></el-input>
    </el-form-item>
    <el-form-item label="发布日期" prop="topicPubDt">
      <el-input v-model="dataForm.topicPubDt" placeholder="发布日期"></el-input>
    </el-form-item>
    <el-form-item label="发布时间" prop="topicPubTm">
      <el-input v-model="dataForm.topicPubTm" placeholder="发布时间"></el-input>
    </el-form-item>
    <el-form-item label="收藏量" prop="topicFvrtCt">
      <el-input v-model="dataForm.topicFvrtCt" placeholder="收藏量"></el-input>
    </el-form-item>
    <el-form-item label="点赞量" prop="topicLikeCt">
      <el-input v-model="dataForm.topicLikeCt" placeholder="点赞量"></el-input>
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
          topicId: 0,
          topicPublisher: '',
          topicKind: '',
          topicAbstract: '',
          topicContent: '',
          topicPubDt: '',
          topicPubTm: '',
          topicFvrtCt: '',
          topicLikeCt: ''
        },
        dataRule: {
          topicPublisher: [
            { required: true, message: '发布者 不能为空', trigger: 'blur' }
          ],
          topicKind: [
            { required: true, message: '话题类型不能为空', trigger: 'blur' }
          ],
          topicAbstract: [
            { required: true, message: '话题摘要不能为空', trigger: 'blur' }
          ],
          topicContent: [
            { required: true, message: '话题内容不能为空', trigger: 'blur' }
          ],
          topicPubDt: [
            { required: true, message: '话题发布日期不能为空', trigger: 'blur' }
          ],
          topicPubTm: [
            { required: true, message: '话题发布时间不能为空', trigger: 'blur' }
          ],
          topicFvrtCt: [
            { required: true, message: '话题收藏计数不能为空', trigger: 'blur' }
          ],
          topicLikeCt: [
            { required: true, message: '话题点赞计数不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.topicId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.topicId) {
            this.$http({
              url: this.$http.adornUrl(`/topic/info/${this.dataForm.topicId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.topicPublisher = data.userTopic.topicPublisher
                this.dataForm.topicKind = data.userTopic.topicKind
                this.dataForm.topicAbstract = data.userTopic.topicAbstract
                this.dataForm.topicContent = data.userTopic.topicContent
                this.dataForm.topicPubDt = data.userTopic.topicPubDt
                this.dataForm.topicPubTm = data.userTopic.topicPubTm
                this.dataForm.topicFvrtCt = data.userTopic.topicFvrtCt
                this.dataForm.topicLikeCt = data.userTopic.topicLikeCt
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
              url: this.$http.adornUrl(`/topic/${!this.dataForm.topicId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'topicId': this.dataForm.topicId || undefined,
                'topicPublisher': this.dataForm.topicPublisher,
                'topicKind': this.dataForm.topicKind,
                'topicAbstract': this.dataForm.topicAbstract,
                'topicContent': this.dataForm.topicContent,
                'topicPubDt': this.dataForm.topicPubDt,
                'topicPubTm': this.dataForm.topicPubTm,
                'topicFvrtCt': this.dataForm.topicFvrtCt,
                'topicLikeCt': this.dataForm.topicLikeCt
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
