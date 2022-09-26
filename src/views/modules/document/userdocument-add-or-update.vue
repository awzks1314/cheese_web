<template>
  <el-dialog
    :title="!dataForm.documentId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="资料名称" prop="docmtNm">
      <el-input v-model="dataForm.docmtNm" placeholder="资料名称"></el-input>
    </el-form-item>
    <el-form-item label="资料类型" prop="docmtType">
      <el-input v-model="dataForm.docmtType" placeholder="资料类型"></el-input>
    </el-form-item>
    <el-form-item label="文件拥有者" prop="docmtOwn">
      <el-input v-model="dataForm.docmtOwn" placeholder="文件拥有者"></el-input>
    </el-form-item>
    <el-form-item label="上传日期" prop="docmtUpDt">
      <el-input v-model="dataForm.docmtUpDt" placeholder="上传日期"></el-input>
    </el-form-item>
    <el-form-item label="上传时间" prop="docmtUpTm">
      <el-input v-model="dataForm.docmtUpTm" placeholder="上传时间"></el-input>
    </el-form-item>
    <el-form-item label="点赞数量" prop="docmtLikeCt">
      <el-input v-model="dataForm.docmtLikeCt" placeholder="点赞数量"></el-input>
    </el-form-item>
    <el-form-item label="收藏数量" prop="docmtFvrtCt">
      <el-input v-model="dataForm.docmtFvrtCt" placeholder="收藏数量"></el-input>
    </el-form-item>
    <el-form-item label="下载次数" prop="docmtDlCt">
      <el-input v-model="dataForm.docmtDlCt" placeholder="下载次数"></el-input>
    </el-form-item>
    <el-form-item label="文档价格 下载需要的积分单价 " prop="docmtPrice">
      <el-input v-model="dataForm.docmtPrice" placeholder="文档价格 下载需要的积分单价 "></el-input>
    </el-form-item>
    <el-form-item label="是否免费" prop="freeType">
      <el-input v-model="dataForm.freeType" placeholder="是否免费"></el-input>
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
          documentId: 0,
          docmtNm: '',
          docmtType: '',
          docmtOwn: '',
          docmtUpDt: '',
          docmtUpTm: '',
          docmtLikeCt: '',
          docmtFvrtCt: '',
          docmtDlCt: '',
          docmtPrice: '',
          freeType: ''
        },
        dataRule: {
          docmtNm: [
            { required: true, message: '资料名称不能为空', trigger: 'blur' }
          ],
          docmtType: [
            { required: true, message: '资料类型不能为空', trigger: 'blur' }
          ],
          docmtOwn: [
            { required: true, message: '文件拥有者 就上传文件的人不能为空', trigger: 'blur' }
          ],
          docmtUpDt: [
            { required: true, message: '上传日期不能为空', trigger: 'blur' }
          ],
          docmtUpTm: [
            { required: true, message: '上传时间不能为空', trigger: 'blur' }
          ],
          docmtLikeCt: [
            { required: true, message: '点赞数量不能为空', trigger: 'blur' }
          ],
          docmtFvrtCt: [
            { required: true, message: '收藏数量不能为空', trigger: 'blur' }
          ],
          docmtDlCt: [
            { required: true, message: '下载次数不能为空', trigger: 'blur' }
          ],
          docmtPrice: [
            { required: true, message: '文档价格 下载需要的积分单价 不能为空', trigger: 'blur' }
          ],
          freeType: [
            { required: true, message: '是否免费 不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.documentId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.documentId) {
            this.$http({
              url: this.$http.adornUrl(`/document/info/${this.dataForm.documentId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.docmtNm = data.userDocument.docmtNm
                this.dataForm.docmtType = data.userDocument.docmtType
                this.dataForm.docmtOwn = data.userDocument.docmtOwn
                this.dataForm.docmtUpDt = data.userDocument.docmtUpDt
                this.dataForm.docmtUpTm = data.userDocument.docmtUpTm
                this.dataForm.docmtLikeCt = data.userDocument.docmtLikeCt
                this.dataForm.docmtFvrtCt = data.userDocument.docmtFvrtCt
                this.dataForm.docmtDlCt = data.userDocument.docmtDlCt
                this.dataForm.docmtPrice = data.userDocument.docmtPrice
                this.dataForm.freeType = data.userDocument.freeType
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
              url: this.$http.adornUrl(`/document/${!this.dataForm.documentId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'documentId': this.dataForm.documentId || undefined,
                'docmtNm': this.dataForm.docmtNm,
                'docmtType': this.dataForm.docmtType,
                'docmtOwn': this.dataForm.docmtOwn,
                'docmtUpDt': this.dataForm.docmtUpDt,
                'docmtUpTm': this.dataForm.docmtUpTm,
                'docmtLikeCt': this.dataForm.docmtLikeCt,
                'docmtFvrtCt': this.dataForm.docmtFvrtCt,
                'docmtDlCt': this.dataForm.docmtDlCt,
                'docmtPrice': this.dataForm.docmtPrice,
                'freeType': this.dataForm.freeType
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
