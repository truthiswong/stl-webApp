<template>
  <a-modal
    title="风险源信息"
    :width="450"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleSubmit"
    @cancel="handleCancel"
    :mask="true"
    :centered="true"
    class="comment_model"
    :bodyStyle="{margin: 0, left:0}"
    :footer="null"
  >
    <a-row>
      <a-col :span="12">
        <p>内部编码: CDSCD</p>
        <p>准确位置: 上海黄浦区九江</p>
        <p>面积: 45646平方公里</p>
      </a-col>
      <a-col :span="12">
        <p>风险源类别: 4564654</p>
        <p>首次发现时间: 2019-06-06</p>
        <p>河道所属: 黄浦江</p>
        <router-link to="#111">
          <a-button type="primary" ghost size="small">查看详情</a-button>
        </router-link>
      </a-col>
    </a-row>
    <a-list
      class="comment-list custom_comment"
      :header="`${data.length} 条评论`"
      itemLayout="horizontal"
      :dataSource="data"
      size="small"
    >
      <a-list-item slot="renderItem" slot-scope="item" class="comment_list">
        <a-comment :author="item.author" :avatar="item.avatar">
          <div class="comment_level">
            <p
              :class="{'danger_level0': item.dangerLevel == 0, 'danger_level1': item.dangerLevel == 1,'danger_level2': item.dangerLevel == 2}"
            >{{item.dangerDescribe}}</p>
            <span>{{item.dangerContent}}</span>
          </div>
          <p slot="content" style="ma">{{item.content}}</p>
          <div class="comment_img">
            <img v-for="img in item.imgList" :key="img.id" :src="img.url" :alt="img.alt" />
          </div>
          <a-tooltip slot="datetime" :title="item.datetime">
            <span>{{item.datetime}}</span>
          </a-tooltip>
        </a-comment>
      </a-list-item>
    </a-list>
    <a-button type="primary" block>更新风险状态</a-button>
  </a-modal>
</template>

<script>
export default {
  data() {
    return {
      visible: false,
      confirmLoading: false,

      data: [
        {
          author: '李白',
          avatar: 'https://zos.alipayobjects.com/rmsportal/ODTLcjxAfvqbxHnVXCYX.png',
          content: '管理建议: 持续监督.',
          dangerLevel: 2,
          dangerDescribe: 'II 级',
          dangerContent: '存在',
          datetime: '2019-10-29',
          imgList: [
            {
              id: 0,
              url: require('../../../../public/avatar2.jpg'),
              alt: '风险图片'
            },
            {
              id: 1,
              url: require('../../../../public/avatar2.jpg'),
              alt: '风险图片'
            },
            {
              id: 2,
              url: require('../../../../public/avatar2.jpg'),
              alt: '风险图片'
            },
            {
              id: 3,
              url: require('../../../../public/avatar2.jpg'),
              alt: '风险图片'
            }
          ]
        },
        {
          author: '王昭君',
          avatar: 'https://zos.alipayobjects.com/rmsportal/ODTLcjxAfvqbxHnVXCYX.png',
          content: '管理建议: 暂无风险.',
          dangerLevel: 0,
          dangerDescribe: '0 级',
          dangerContent: '不存在',
          datetime: '2019-10-01',
          imgList: [
            {
              id: 0,
              url: require('../../../../public/avatar2.jpg'),
              alt: '风险图片'
            },
            {
              id: 1,
              url: require('../../../../public/avatar2.jpg'),
              alt: '风险图片'
            }
          ]
        }
      ]
    }
  },
  computed: {},
  methods: {
    riskInfo() {
      this.visible = true
    },
    // 添加河流
    addRiver(value) {
      console.log(value)
    },
    // 风险源类型
    handleChange(selectedItems) {
      this.selectedItems = selectedItems
      console.log(selectedItems)
    },
    // 文件上传
    fileUpload(info) {
      if (info.file.status !== 'uploading') {
        console.log(info.file, info.fileList)
      }
      if (info.file.status === 'done') {
        this.$message.success(`${info.file.name} file uploaded successfully`)
      } else if (info.file.status === 'error') {
        this.$message.error(`${info.file.name} file upload failed.`)
      }
    },
    // 标签
    handleChangeTag(value) {
      console.log(`selected ${value}`)
    },
    handleSubmit() {
      const {
        form: { validateFields }
      } = this
      this.confirmLoading = true
      validateFields((errors, values) => {
        if (!errors) {
          console.log('values', values)
          setTimeout(() => {
            this.visible = false
            this.confirmLoading = false
            this.$emit('ok', values)
          }, 1500)
        } else {
          this.confirmLoading = false
        }
      })
    },
    handleCancel() {
      this.visible = false
    }
  }
}
</script>
<style lang="less" scoped>
p {
  margin: 0 10px 0 0;
  text-align: justify;
}
.comment_level {
  p {
    display: inline-block;
    background-color: red;
    font-size: 12px;
    width: 40px;
    line-height: 20px;
    text-align: center;
    border-radius: 4px;
    color: white;
    margin: 0 6px 6px 0;
  }
  .danger_level0 {
    background-color: #52c41a;
  }
  .danger_level1 {
    background-color: #faad14;
  }
  .danger_level2 {
    background-color: #f5222d;
  }
  span {
    font-size: 14px;
    color: #333;
  }
}
.comment_img {
  display: flex;
  display: -webkit-flex;
  justify-content: space-between;
  -webkit-justify-content: space-between;
  flex-wrap: wrap;
  img {
    width: 80px;
    height: 80px;
    margin: 0 10px 10px 0;
  }
  img:nth-last-child(1) {
    margin: 0;
  }
}
</style>
