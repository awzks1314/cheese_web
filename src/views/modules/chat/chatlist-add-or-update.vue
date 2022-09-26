<template>
  <el-dialog
    :title="!dataForm.chatId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="最后聊天日期" prop="lstDt">
      <el-input v-model="dataForm.lstDt" placeholder="最后聊天日期"></el-input>
    </el-form-item>
    <el-form-item label="最后聊天时间" prop="lstTm">
      <el-input v-model="dataForm.lstTm" placeholder="最后聊天时间"></el-input>
    </el-form-item>
    <el-form-item label="来源" prop="fromUserId">
      <el-input v-model="dataForm.fromUserId" placeholder="来源"></el-input>
    </el-form-item>
    <el-form-item label="去向" prop="toUserId">
      <el-input v-model="dataForm.toUserId" placeholder="去向"></el-input>
    </el-form-item>
    <el-form-item label="状态" prop="sts">
      <el-input v-model="dataForm.sts" placeholder="状态"></el-input>
    </el-form-item>
    <el-form-item label="头像A" prop="headImgA">
      <el-input v-model="dataForm.headImgA" placeholder="头像A"></el-input>
    </el-form-item>
    <el-form-item label="头像B" prop="headImgB">
      <el-input v-model="dataForm.headImgB" placeholder="头像B"></el-input>
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
          chatId: 0,
          lstDt: '',
          lstTm: '',
          fromUserId: '',
          toUserId: '',
          sts: '',
          headImgA: '',
          headImgB: ''
        },
        dataRule: {
          lstDt: [
            { required: true, message: '最后聊天日期不能为空', trigger: 'blur' }
          ],
          lstTm: [
            { required: true, message: '最后聊天时间不能为空', trigger: 'blur' }
          ],
          fromUserId: [
            { required: true, message: '来源不能为空', trigger: 'blur' }
          ],
          toUserId: [
            { required: true, message: '去向不能为空', trigger: 'blur' }
          ],
          sts: [
            { required: true, message: '状态不能为空', trigger: 'blur' }
          ],
          headImgA: [
            { required: true, message: '头像A不能为空', trigger: 'blur' }
          ],
          headImgB: [
            { required: true, message: '头像B不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.chatId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.chatId) {
            this.$http({
              url: this.$http.adornUrl(`/chat/chatlist/info/${this.dataForm.chatId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.lstDt = data.chatList.lstDt
                this.dataForm.lstTm = data.chatList.lstTm
                this.dataForm.fromUserId = data.chatList.fromUserId
                this.dataForm.toUserId = data.chatList.toUserId
                this.dataForm.sts = data.chatList.sts
                this.dataForm.headImgA = data.chatList.headImgA
                this.dataForm.headImgB = data.chatList.headImgB
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
              url: this.$http.adornUrl(`/chat/chatlist/${!this.dataForm.chatId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'chatId': this.dataForm.chatId || undefined,
                'lstDt': this.dataForm.lstDt,
                'lstTm': this.dataForm.lstTm,
                'fromUserId': this.dataForm.fromUserId,
                'toUserId': this.dataForm.toUserId,
                'sts': this.dataForm.sts,
                'headImgA': this.dataForm.headImgA,
                'headImgB': this.dataForm.headImgB
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
