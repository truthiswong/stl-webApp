<template>
  <div>
    <a-card>
      <a-breadcrumb style="font-size: 16px;">
        <a-breadcrumb-item>设置</a-breadcrumb-item>
        <a-breadcrumb-item>风险源类型管理</a-breadcrumb-item>
      </a-breadcrumb>
      <a-divider style="margin: 10px 0" />
      <a-button type="primary" icon="plus" style="margin-bottom:15px" @click="visible=true">添加</a-button>
      <a-table :columns="columns" :dataSource="data" bordered>
        <template slot="type" slot-scope="row">
          <viewer >
            <img :src="row.icon" alt="" style="width:80px;">
          </viewer>
        </template>
        <template slot="operation" slot-scope="row">
          <a @click="add(row.id)">编辑</a>
          <a-divider type="vertical" />
          <a-popconfirm
            title="是否确认删除?"
            @confirm="confirm"
            @cancel="cancel"
            okText="确认"
            cancelText="取消"
          >
            <a @click="del(row.id)">删除</a>
          </a-popconfirm>
        </template>
      </a-table>
    </a-card>
    <a-modal title="添加/编辑风险源" v-model="visible"
      @ok="submitUpload"
      @cancel="handleCancel" 
    >
      <a-form>
        <a-form-item label="风险源类型名称" :label-col="{ span: 7 }" :wrapper-col="{ span: 16 }">
          <a-input placeholder="请输入风险源类型名称" v-model="list.name"/>
        </a-form-item>
        <a-form-item label="标注样式" :label-col="{ span: 7 }" :wrapper-col="{ span: 16 }">
          <el-upload
            class="upload-demo"
            ref="upload"
            :data="list"
            name="icon"
            :headers="headers"
            action="/server/data/admin/param/save"
            :on-preview="handlePreview"
            :on-success="handleSuccess"
            :on-change="handleChange"
            :on-remove="handleRemove"
            :file-list="fileList"
            :limit='1'
            :auto-upload="false">
            <a-button type="primary" icon="plus" >添加</a-button>
          </el-upload>
          <viewer >
            <img  :src="attachmentJpg" alt="" style="height:70px;">
          </viewer >
        </a-form-item>
      </a-form>
    </a-modal>
  </div>
</template>

<script>
import Vue from 'vue'
import { ACCESS_TOKEN } from '@/store/mutation-types'
import { paramList, paramSave,paramDel } from '@/api/login'
const columns = [
  {
    title: '序号',
    width: 80,
    dataIndex: 'key'
  },
  {
    title: '风险源类型名称',
    dataIndex: 'name'
  },
  {
    title: '标注样式',
    scopedSlots: { customRender: 'type' },
  },
  {
    title: '操作',
    scopedSlots: { customRender: 'operation' },
    width: 160
  }
]
export default {
  data() {
    return {
      fileList:[],
      file:false,
      attachmentJpg:'',
      id:'',
      list: {
        name: '',
        id:'',
        type:'risk_source_type'
      },
      ruleValidate: {
        name: [{ required: true, message: '不能为空', trigger: 'blur' }]
      },
      visible: false,
      columns,
      data: [
       
      ],
      headers: {
        Authorization: '',
        'X-TENANT-ID': 'jl:jlgis@2019' 
      },
    }
  },
  mounted(){
    this.getList()
    this.headers.Authorization=Vue.ls.get(ACCESS_TOKEN)
  },
  methods: {
    getList(){
      var data ={
        type:'risk_source_type'
      }
      paramList(data).then(res => {
         var arr = res.data
         for (let i = 0; i < arr.length; i++) {
          arr[i].key=i+1
         }
         this.data = arr
        }).catch(err => {

      })
    },
    handleCancel(e) {
      this.visible = false
      this.file = false
      this.fileList=[]
      this.list.id=''
      this.list.name=''
      this.attachmentJpg=''
    },
    add(id){
      for (let i = 0; i < this.data.length; i++) {
        if (this.data[i].id==id) {
          this.list.name =this.data[i].name
          this.attachmentJpg = this.data[i].icon
          break
        }  
      }
      this.list.id=id
      this.visible=true
    },
    del(id){
      this.id=id
    },
    confirm(e) {
      var data ={
        id:this.id
      }
      paramDel(data).then(res => {
          this.$message.success('删除成功');
          this.getList()
        }).catch(err => {
          this.$message.error(err.response.data.message);
      })
    },
    cancel(e) {

    },
    submitUpload() {
      if (this.attachmentJpg !='') {
        if (this.fileList.length == 0) {
          var data = this.list
          paramSave(data).then(res => {
              this.$message.success('保存成功');
              this.visible = false
              this.list.id=''
              this.list.name=''
              this.attachmentJpg=''
              this.getList()
          }).catch(err => {
              this.$message.error(err.response.data.message);
          })
        }else{
          this.$refs.upload.submit();
        }
      }else{
        this.$message.error('图片不能为空');
      }
      
    },
    handleSuccess(response, file, fileList){
      this.attachmentJpg=''
      this.visible = false;
      this.$message.success('保存成功');
      this.list.id=''
      this.list.name=''
      this.getList()
      this.attachmentJpg=''
    },
    handleChange(file, fileList){
      if(this.fileList.length==0){
        this.fileList=fileList
      }else{
        this.fileList=[]
      }
      console.log(file, fileList);
      this.attachmentJpg=URL.createObjectURL(file.raw)
    },
    handleRemove(file, fileList) {
      
    },
    handlePreview(file) {
    }
  }
}
</script>

<style lang="less" >
.ant-form input[type='file'] {
  display: none; 
}
</style>
