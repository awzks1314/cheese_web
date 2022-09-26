<template>
  <el-dialog
    :title="!dataForm.msgId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="会话ID" prop="chatId">
      <el-input v-model="dataForm.chatId" placeholder="会话ID"></el-input>
    </el-form-item>
    <el-form-item label="消息内容" prop="msgContent">
      <el-input v-model="dataForm.msgContent" placeholder="消息内容"></el-input>
    </el-form-item>
    <el-form-item label="创建日期" prop="createDt">
      <el-input v-model="dataForm.createDt" placeholder="创建日期"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTm">
      <el-input v-model="dataForm.createTm" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="发送人" prop="sender">
      <el-input v-model="dataForm.sender" placeholder="发送人"></el-input>
    </el-form-item>
    <el-form-item label="发送人头像" prop="headImg">
      <el-input v-model="dataForm.headImg" placeholder="发送人头像"></el-input>
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
          msgId: 0,
          chatId: '',
          msgContent: '',
          createDt: '',
          createTm: '',
          sender: '',
          headImg: ''
        },
        dataRule: {
          chatId: [
            { required: true, message: '会话ID不能为空', trigger: 'blur' }
          ],
          msgContent: [
            { required: true, message: '消息内容不能为空', trigger: 'blur' }
          ],
          createDt: [
            { required: true, message: '创建日期不能为空', trigger: 'blur' }
          ],
          createTm: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          sender: [
            { required: true, message: '发送人不能为空', trigger: 'blur' }
          ],
          headImg: [
            { required: true, message: '发送人头像不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.msgId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.msgId) {
            this.$http({
              url: this.$http.adornUrl(`/chat/chatmsg/info/${this.dataForm.msgId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.chatId = data.chatMsg.chatId
                this.dataForm.msgContent = data.chatMsg.msgContent
                this.dataForm.createDt = data.chatMsg.createDt
                this.dataForm.createTm = data.chatMsg.createTm
                this.dataForm.sender = data.chatMsg.sender
                this.dataForm.headImg = data.chatMsg.headImg
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
              url: this.$http.adornUrl(`/chat/chatmsg/${!this.dataForm.msgId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'msgId': this.dataForm.msgId || undefined,
                'chatId': this.dataForm.chatId,
                'msgContent': this.dataForm.msgContent,
                'createDt': this.dataForm.createDt,
                'createTm': this.dataForm.createTm,
                'sender': this.dataForm.sender,
                'headImg': this.dataForm.headImg
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
