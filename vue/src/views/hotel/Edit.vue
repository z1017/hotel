<template>
  <div style="width: 80%">
<!--    <div style="margin-bottom: 30px">编辑客房</div>-->
    <h3 style="margin-bottom: 20px"></h3>
    <el-form :inline="true" :rules="rules" ref="ruleForm" :model="form" label-width="100px">
      <el-form-item label="名称" prop="name">
        <el-input v-model="form.name" placeholder="请输入名称"></el-input>
      </el-form-item>
      <el-form-item label="描述" prop="description">
        <el-input style="width: 190px" type="textarea" v-model="form.description" placeholder="请输入描述"></el-input>
      </el-form-item>
      <el-form-item label="发布日期" prop="hotelDate">
        <el-date-picker
            style="width: 190px"
            v-model="form.hotelDate"
            value-format="yyyy-MM-dd"
            type="datetime"
            placeholder="请选择发布日期">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="负责人" prop="head">
        <el-input v-model="form.head" placeholder="请输入负责人"></el-input>
      </el-form-item>


      <el-form-item label="分类" prop="category">
        <el-cascader
            style="width: 220px"
            :props="{ value: 'name', label: 'name'}"
            v-model="form.categories"
            :options="categories">
        </el-cascader>
      </el-form-item>

      <el-form-item label="房间号" prop="hotelNo">
        <el-input v-model="form.hotelNo" placeholder="请输入房间号"></el-input>
      </el-form-item>

      <el-form-item label="积分" prop="score">
        <el-input v-model="form.score" placeholder="请输入订房积分"></el-input>
      </el-form-item>

      <!--      <br>-->
      <el-form-item label="封面" prop="cover">
        <el-upload
            class="avatar-uploader"
            :action="'http://localhost:9090/api/hotel/file/upload?token=' + this.admin.token"
            :show-file-list="false"
            :on-success="handleCoverSuccess"
        >
          <img v-if="form.cover" :src="form.cover" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>

      </el-form-item>
    </el-form>

    <div style="text-align: center; margin-top: 20px;">
      <el-button type="primary" round style="width: 200px; font-size: 16px" @click="save" size:="medium">提交</el-button>
    </div>
  </div>
</template>

<script>
import request from "@/utils/request";
import Cookies from "js-cookie";

export default {
  name: 'EditHotel',
  data() {
    return {
      admin: Cookies.get('admin') ? JSON.parse(Cookies.get('admin')) : {},
      form: {score: 10},
      categories: [],
      rules: {
        name: [
          {required: true, message: '请输入房间名称', trigger: 'blur'},
        ],
        hotelNo: [
          {required: true, message: '请输入房间号', trigger: 'blur'}
        ],
        score: [
          {required: true, message: '请输入订房积分', trigger: 'blur'}
        ],

      }

    }
  },

  created() {
    request.get('/category/tree').then(res => {
      this.categories = res.data
    })

    const id = this.$route.query.id
    request.get("/hotel/" + id).then(res => {
      this.form = res.data
      if (this.form.category) {
        this.form.categories = this.form.category.split(' > ')
      }
    })
  },
  methods: {
    handleCoverSuccess(res) {
      if (res.code === '200') {
        this.form.cover = res.data
      }
    },
    save() {
      request.put('/hotel/update', this.form).then(res => {
        if (res.code === '200') {
          this.$notify.success('更新成功')
          this.$router.push("/hotelList")
        } else {
          this.$notify.error(res.msg)
        }
      })
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
</style>
