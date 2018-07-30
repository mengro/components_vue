<template>
  <el-upload
    :on-success="_handleSuccess"
    :before-upload="beforeAvatarUpload"
    :action="action"
    :data="data"
  >
    <div class="imgContainer">
      <img v-if="value" :src="value" class="avatar">
      <i v-else class="el-icon-plus avatar-uploader-icon"></i>
    </div>
    <p class="describe">
      仅支持文件格式为{{this.acceptTypes.join('、')}}，大小在{{sizeLimit / 1024 /1024 + 'MB'}}以下的文件
    </p>
  </el-upload>
</template>
<script>
  export default {
    name: 'formImageUploadItem',
    props: {
      value: {
        required: true,
        default: '',
      },
      handleSuccess: {
        required: false,
        type: Function,
      },
      acceptTypes: {
        required: false,
        type: Array,
        default: ['jpeg', 'png', 'jpg'],
      },
      sizeLimit: {
        required: false,
        default: 1024 * 1024
      },
      action: {
        required: true,
      },
      data: {
        required: true,
      }
    },
    methods: {
      _handleSuccess(res, file) {
        this.$emit('input', res.full_url);
        if (this.handleSuccess) {
          this.handleSuccess(res, file);
        }
      },
      beforeAvatarUpload(file) {
        let acceptTypes = null;
        if (this.acceptTypes) {
          acceptTypes = this.acceptTypes.map(item => `image/${item}`);
        }
        const isJPG = file.type === 'image/jpeg';
        const isLt2M = file.size / 1024 / 1024 < 1;

        if (acceptTypes.indexOf(file.type) < 0) {
          this.$message.error(`上传头像图片只能是 ${this.acceptTypes.join('、')} 格式!`);
        }
        if (!isLt2M) {
          this.$message.error('上传头像图片大小不能超过 2MB!');
        }
        return isJPG && isLt2M;
      }
    }
  }
</script>
<style scoped>
.avatar-uploader .el-upload {

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
  width: 100%;
  height: 100%;
  line-height: 120px;
  text-align: center;
}
.imgContainer {
  width: 120px;
  height: 120px;
  display: block;
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  float: left;
  margin-right: 20px;
}
.imgContainer img {
  height: 100%;
  width: 100%;
  display: block;
}
.describe {
  text-indent: 20px;
  text-align: left;
}
</style>
