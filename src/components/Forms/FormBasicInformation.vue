<template>
  <div id="FormBasicInformation">
    <div style="width:90%;margin: 0% auto;" class="animated fadeIn" v-if="!loading" >
      <div style="width:100%;height:10px">
      </div>
      <h2 style="width:140px;text-align: right;display: inline-block;font-size: 30px">基本信息</h2>
      <h4 style="display: inline-block;margin-left: 20px;font-weight: normal">请保证基本信息的准确无误</h4>
      <div style="width:100%;height:10px">
      </div>
      <el-form label-position="labelPosition" label-width="200px" :rules="rules" :model="formBasic" ref="formBasic" >

        <el-form-item label="上传照片" style="width: 50%;position: absolute;left:55%">
          <el-upload
            class="avatar-uploader"
            :action="$axios.defaults.baseURL+'/upload-photo'"
            :headers="header"
            :show-file-list="false"
            :on-success="handleAvatarSuccess"
            :before-upload="beforeAvatarUpload">
            <img v-if="imageUrl" :src="imageUrl" class="avatar">
            <i v-else class="el-icon-plus
          avatar-uploader-icon"></i>
          </el-upload>
        </el-form-item>
        <el-form-item label="姓名" style="width: 50%" prop="name">
          <el-input v-model="formBasic.name"></el-input>
        </el-form-item>
        <el-form-item label="性别" prop="sex">
          <el-select v-model="formBasic.sex">
            <el-option label="男" :value="1"></el-option>
            <el-option label="女" :value="2"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="证件类型及号码" style="width: 60%" prop="idNumber">
          <el-input class="input-with-select" v-model="formBasic.idNumber">
            <el-select v-model="formBasic.idType" slot="prepend"  placeholder="证件类型" style="width: 150px">
              <el-option label="身份证" :value="1"></el-option>
              <el-option label="港澳台通行证" :value="2"></el-option>
              <el-option label="护照" :value="3"></el-option>
            </el-select>
          </el-input>
        </el-form-item>
        <el-form-item label="出生日期" prop="birthday">
          <el-date-picker type="date" placeholder="选择日期" v-model="formBasic.birthday"
                          class="input-date"></el-date-picker>
        </el-form-item>
        <el-form-item label="电子邮箱" style="width: 50%" prop="email">
          <el-input v-model="formBasic.email"></el-input>
        </el-form-item>
        <el-form-item label="手机号码" style="width: 50%" prop="telephone">
          <el-input v-model="formBasic.telephone"></el-input>
        </el-form-item>
        <el-form-item label="婚姻状况" prop="maritalStatus">
          <el-select placeholder="请选择" v-model="formBasic.maritalStatus">
            <el-option label="已婚" :value="1"></el-option>
            <el-option label="未婚" :value="2"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="工作年限" style="width: 50%" prop="workSeniority">
          <el-input v-model.number="formBasic.workSeniority"></el-input>
        </el-form-item>
        <el-form-item label="政治面貌" style="width: 50%" prop="politicalStatus">
          <el-input v-model="formBasic.politicalStatus"></el-input>
        </el-form-item>
        <el-form-item label="现居住地" style="width: 50%" prop="presentAddress">
          <el-input v-model="formBasic.presentAddress"></el-input>
        </el-form-item>
        <div class="needMarginBorder"></div>
        <el-form-item  style="width: 25%">
          <el-button type="primary" class="button4forms" @click="nextStep('formBasic')">保存并进行下一步</el-button>
        </el-form-item>
      </el-form>
      <div style="width:100%;height:30px">
      </div>
    </div>
  </div>
</template>
<script>

import { isvalidPhone, isvalidEmail, isvlaidIDnumber, isvalidPassport, isvalidPass, isvalidAddress, isName } from '../../util/Validate'

var checkEmail = (rule, value, callback) => { // 检查邮箱格式
  if (!value) {
    callback(new Error('请输入邮箱'))
  } else if (!isvalidEmail(value)) {
    callback(new Error('邮箱格式不正确'))
  } else {
    callback()
  }
}
var checkPhone = (rule, value, callback) => { // 检查手机号格式是否正确
  if (!value) {
    callback(new Error('请输入手机号'))
  } else if (!isvalidPhone(value)) {
    callback(new Error('请输入11位手机号'))
  } else {
    callback()
  }
}

var checkAddress = (rule, value, callback) => { // 检查住宅地址
  if (!value) {
    callback(new Error('请输入住址'))
  } else if (!isvalidAddress(value)) {
    callback(new Error('住址不合法'))
  } else {
    callback()
  }
}

var checkName = (rule, value, callback) => {
  if (!value) {
    callback(new Error('请输入名字'))
  } else if (!isName(value)) {
    callback(new Error('名字不合法！'))
  } else {
    callback()
  }
}

export default {

  data () {
    var checkId = (rule, value, callback) => { // 检查证件格式是否正确
      var key1 = 1
      var key2 = 2
      var key3 = 3
      if (!value) {
        callback(new Error('请输入证件号码'))
      } else if (this.formBasic.idType === key1) {
        if (!isvlaidIDnumber(value)) {
          callback(new Error('身份证号格式不正确'))
        } else {
          callback()
        }
      } else if (this.formBasic.idType === key3) {
        if (!isvalidPassport(value)) {
          callback(new Error('护照格式不正确'))
        } else {
          callback()
        }
      } else if (this.formBasic.idType === key2) {
        if (!isvalidPass(value)) {
          callback(new Error('通行证格式不正确'))
        } else if (this.$refs.formBasic.idType === null) {
          callback(new Error('请选择证件类型'))
        } else {
          callback()
        }
      } else {
        callback(new Error('请选择证件类型'))
      }
    }
    return {
      header: {
      },
      imageUrl: null,
      loading: true,
      formBasic: {
        ID: '',
        resumeId: '',
        name: '',
        sex: '',
        idType: '',
        idNumber: '',
        birthday: '',
        email: '',
        telephone: '',
        maritalStatus: '',
        workSeniority: '',
        politicalStatus: '',
        presentAddress: '',
      },
      rules: {
        email: [
          {validator: checkEmail, trigger: 'change'},
          {required: true, message: '请输入邮箱', trigger: 'change'}
        ],
        telephone: [
          {validator: checkPhone, trigger: 'change'},
          {required: true, message: '请输入手机号', trigger: 'change'}
        ],
        birthday: [
          {type: 'date', required:true, message: '请选择日期', trigger: 'change'}
        ],
        name: [
          {required: true, message: '请输入名字', trigger: 'change'},
          {validator: checkName, trigger: 'change'}
        ],
        sex: [
          {required: true, message: '请选择性别', trigger: 'change'}
        ],
        workSeniority: [
          {required: true, message: '请输入工作年限', trigger: 'blur'}
        ],
        idNumber: [
          {validator: checkId, trigger: 'change'},
          {required: true, message: '请输入证件号码', trigger: 'change'}
        ],
        politicalStatus: [
          {required: true, message: '请选择政治面貌', trigger: 'change'}
        ],
        maritalStatus: [
          {required: true, message: '请选择婚姻状况', trigger: 'change'}
        ],
        presentAddress: [
          {validator: checkAddress, trigger: 'change'},
          {required: true, message: '请输入住址', trigger: 'change'}
        ]

      }

    }
  },
  created () {
    let _this = this
    this.$axios({
      method: 'get',
      url: '/person'
    }).then(function (response) {
      _this.$nextTick(() => {
        _this.$data.formBasic = response.data === ''?_this.$data.formBasic:response.data
        _this.$data.loading = false
        _this.$data.header = {
          'authorization': _this.$axios.defaults.headers.Authorization
        }
      })
    })
    this.$axios.get(this.$axios.defaults.baseURL + '/download-photo',{
      responseType: 'arraybuffer'
    })
      .then(function (response) {
        console.log(response.data)
        if(response.data.length!=0) {
          _this.$data.imageUrl = 'data:image/png;base64,' + btoa(
            new Uint8Array(response.data)
              .reduce((data, byte) => data + String.fromCharCode(byte), '')
          )
        }

      })
      .catch(function (error) {
        console.log(error)
      })
  },
  methods: {
    nextStep (formName) {
      this.$data.formBasic.birthday=new Date(this.$data.formBasic.birthday);
      this.$data.formBasic.birthday.setTime(this.$data.formBasic.birthday.getTime()+1000*3600*8);
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.$axios({
            method: 'post',
            url: '/person',
            data: this.$data.formBasic
          }).then(function (response) {
            console.log(response.data)
          })
          this.$router.push('/ResumeForm/2')
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
    handleAvatarSuccess () {
      let _this = this
      this.$axios.get(this.$axios.defaults.baseURL + '/download-photo',{
        responseType: 'arraybuffer'
      })
        .then(function (response) {
          console.log(response.data)
          _this.$data.imageUrl = 'data:image/png;base64,' + btoa(
            new Uint8Array(response.data)
              .reduce((data, byte) => data + String.fromCharCode(byte), '')
          )

        })
        .catch(function (error) {
          console.log(error)
        })
    },
    beforeAvatarUpload (file) {
      const isJPG = file.type === 'image/jpeg'
      const isLt2M = file.size / 1024 / 1024 < 2

      if (!isJPG) {
        this.$message.error('上传头像图片只能是 JPG 格式!')
      }
      if (!isLt2M) {
        this.$message.error('上传头像图片大小不能超过 2MB!')
      }
      return isJPG && isLt2M
    }
  }

}
</script>
<style>
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
.input-date{
  width:40.3%;
}
.input-with-select .el-input-group__prepend {
  background-color: #fff;
}
</style>
