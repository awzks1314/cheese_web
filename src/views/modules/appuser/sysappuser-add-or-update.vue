<template>
  <el-dialog
    :title="!dataForm.userId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户状态" prop="userSts">
      <el-input v-model="dataForm.userSts" placeholder="用户状态"></el-input>
    </el-form-item>
    <el-form-item label="会员号" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="会员号"></el-input>
    </el-form-item>
    <el-form-item label="名称" prop="userNm">
      <el-input v-model="dataForm.userNm" placeholder="名称"></el-input>
    </el-form-item>
    <el-form-item label="密码" prop="userPswd">
      <el-input v-model="dataForm.userPswd" placeholder="密码"></el-input>
    </el-form-item>
    <el-form-item label="性别" prop="userSex">
      <el-input v-model="dataForm.userSex" placeholder="性别"></el-input>
    </el-form-item>
    <el-form-item label="注册日期" prop="userRgstDt">
      <el-input v-model="dataForm.userRgstDt" placeholder="注册日期"></el-input>
    </el-form-item>
    <el-form-item label="信息变更日期" prop="userUpdtDt">
      <el-input v-model="dataForm.userUpdtDt" placeholder="信息变更日期"></el-input>
    </el-form-item>
    <el-form-item label="用户头像" prop="userImg">
      <el-input v-model="dataForm.userImg" placeholder="用户头像"></el-input>
    </el-form-item>
    <el-form-item label="电话号码" prop="userTel">
      <el-input v-model="dataForm.userTel" placeholder="电话号码"></el-input>
    </el-form-item>
    <el-form-item label="VIP状态" prop="vipSts">
      <el-input v-model="dataForm.vipSts" placeholder="VIP状态"></el-input>
    </el-form-item>
    <el-form-item label="签到状态" prop="signSts">
      <el-input v-model="dataForm.signSts" placeholder="签到状态"></el-input>
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
          userId: 0,
          userSts: '',
          tbId: '',
          userNm: '',
          userPswd: '',
          userSex: '',
          userRgstDt: '',
          userUpdtDt: '',
          userImg: '',
          userTel: '',
          vipSts: '',
          signSts: ''
        },
        dataRule: {
          userSts: [
            { required: true, message: '用户状态 0-正常 2-冻结 3-销户不能为空', trigger: 'blur' }
          ],
          tbId: [
            { required: true, message: '用户会员号不能为空', trigger: 'blur' }
          ],
          userNm: [
            { required: true, message: '用户名称不能为空', trigger: 'blur' }
          ],
          userPswd: [
            { required: true, message: '密码不能为空', trigger: 'blur' }
          ],
          userSex: [
            { required: true, message: '性别不能为空', trigger: 'blur' }
          ],
          userRgstDt: [
            { required: true, message: '注册日期不能为空', trigger: 'blur' }
          ],
          userUpdtDt: [
            { required: true, message: '信息变更日期不能为空', trigger: 'blur' }
          ],
          userImg: [
            { required: true, message: '用户头像不能为空', trigger: 'blur' }
          ],
          userTel: [
            { required: true, message: '电话号码不能为空', trigger: 'blur' }
          ],
          vipSts: [
            { required: true, message: 'VIP状态不能为空', trigger: 'blur' }
          ],
          signSts: [
            { required: true, message: '签到状态不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.tbId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.tbId) {
            this.$http({
              url: this.$http.adornUrl(`/user/info/${this.dataForm.tbId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userSts = data.sysAppUser.userSts
                this.dataForm.tbId = data.sysAppUser.tbId
                this.dataForm.userNm = data.sysAppUser.userNm
                this.dataForm.userPswd = data.sysAppUser.userPswd
                this.dataForm.userSex = data.sysAppUser.userSex
                this.dataForm.userRgstDt = data.sysAppUser.userRgstDt
                this.dataForm.userUpdtDt = data.sysAppUser.userUpdtDt
                this.dataForm.userImg = data.sysAppUser.userImg
                this.dataForm.userTel = data.sysAppUser.userTel
                this.dataForm.vipSts = data.sysAppUser.vipSts
                this.dataForm.signSts = data.sysAppUser.signSts
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
              url: this.$http.adornUrl(`/user/${!this.dataForm.userId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'userId': this.dataForm.userId || undefined,
                'userSts': this.dataForm.userSts,
                'tbId': this.dataForm.tbId,
                'userNm': this.dataForm.userNm,
                'userPswd': this.dataForm.userPswd,
                'userSex': this.dataForm.userSex,
                'userRgstDt': this.dataForm.userRgstDt,
                'userUpdtDt': this.dataForm.userUpdtDt,
                'userImg': this.dataForm.userImg,
                'userTel': this.dataForm.userTel,
                'vipSts': this.dataForm.vipSts,
                'signSts': this.dataForm.signSts
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
