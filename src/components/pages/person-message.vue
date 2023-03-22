<template>
  <div class="personMessageDiv">
    <h2>我的消息</h2>
    <div class="message-list">
      <!-- <div v-for="(message, index) in currentPageMessages" :key="index" class="message-item">
          <div class="message-left">
            <img :src="message.imgUrl" alt="用户头像"  class="avatar"/>
          </div>
          <div class="message-right">
            <div class="message-header">
              <p><span>{{' 用户' }}</span>{{message.userName}} <span>{{' '+ message.time+' 对你说：' }}</span></p>
            </div>
            <div class="message-content">
              <p>{{ message.message }}</p>
            </div>
          </div>
          </div> -->

      <el-table :data="messages" style="width: 100%">

        <el-table-column label="商品图" width="100">

          <template slot-scope="scope">
            <img :src="scope.row.imgUrl" alt="加载中..." width="80" height="80">
          </template>
        </el-table-column>
        <el-table-column prop="time" label="消息时间" width="180">
        </el-table-column>
        <el-table-column prop="userName" label="用户" width="120">
        </el-table-column>

        <el-table-column prop="message" label="对你说">
        </el-table-column>

        <el-table-column align="right" width="200">
          <template slot="header" slot-scope="scope">
            <el-input v-model="search" size="mini" placeholder="搜索消息" />
          </template>
          <template slot-scope="scope">
            <el-button size="mini" @click="reply(scope.row)">回复Ta</el-button>
          </template>
        </el-table-column>
      </el-table>



    </div>

    <div class="block">
      <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="currentPage"
        :page-sizes="[5, 10, 20]" :page-size="100" layout="total, sizes, prev, pager, next, jumper" :total="total">
      </el-pagination>
    </div>

    <el-dialog title="回复Ta" :visible.sync="showReplyDialog" width="30%" center>
      <el-form>
        <el-form-item label="回复内容">
          <el-input v-model="replyMessageDto.message" type="textarea" :rows="3" placeholder="请输入回复内容"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitReply()">提交回复</el-button>
        </el-form-item>
      </el-form>

    </el-dialog>




  </div>
</template>
<script>
export default {
  name: "person-message",
  data() {
    return {
      showReplyDialog: false,
      search: '',
      userId: window.sessionStorage.getItem("userId"),
      pageSize: 5,
      currentPage: 1,
      totalPages: 1,
      messages: [],
      replyMessageDto: {
        imgUrl:"",
        message:"",
        time:"",
        userName:""
      }
    };
  },
  methods: {
    reply(row) {
      this.showReplyDialog = true;
      console.log("row", row);
      // 可以将row等信息传递给弹窗组件
      this.replyMessageDto = row;
    },

    submitReply() {
      // 获取表单数据
      // TODO: 提交表单数据到后端
      const params = {
        senderId: window.sessionStorage.getItem("userId"),
        imgUrl: this.replyMessageDto.imgUrl,
        message: this.replyMessageDto.message,
        recipientName: this.replyMessageDto.userName,
        time: this.replyMessageDto.time
        
      };
      console.log("params", params);

      $.post('http://localhost:8083/message/reply', params)
        .then(function (response) {
          console.log(response);
        })
        .catch(function (error) {
          console.error(error);
        });
      // 关闭弹窗
      this.showReplyDialog = false;

    },

    getMessageList() {
      const params = {
        userId: this.userId,
        currentPage: this.currentPage,
        pageSize: this.pageSize
      };

      var self = this;

      $.get('http://localhost:8083/message/getPage', params)
        .then(function (response) {
          self.messages = response.data;
          self.total = response.total;
          self.currentPage = response.currentPage;
          self.pageSize = response.pageSize;
          console.log('messages:', self.messages);
          console.log('total:', self.total);
          console.log('currentPage:', self.currentPage);
          console.log('pageSize:', self.pageSize);
          self.totalPages = Math.ceil(response.headers.get("x-total-count") / self.pageSize);
        })
        .catch(function (error) {
          console.error(error);
        });
    },
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      this.getMessageList();
    },
  },

  computed: {
    currentPageMessages() {
      const startIndex = (this.currentPage - 1) * this.pageSize;
      const endIndex = startIndex + this.pageSize;
      return this.messages.slice(startIndex, endIndex);
    },
    pages() {
      return Array.from({ length: this.totalPages }, (_, index) => index + 1);
    },
  },
  async mounted() {
    await this.getMessageList();
  },
};
</script>
    
<style scoped>
.personMessageDiv {
  width: 80%;
  margin: 0 auto;
  padding-top: 80px;
}

h2 {
  font-weight: 400;
  color: #1f2f3d;
  font-size: 28px;
}



.message-item {
  display: flex;
  margin-bottom: 20px;
  margin-top: 20px;
}

.message-left {
  margin-right: 20px;
}

.message-right {
  display: flex;
  flex-direction: column;
}

.message-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

.avatar {
  max-width: 100px;
  max-height: 100px;
}

.message-header p {
  font-weight: bold;
  color: #1f2f3d;
  font-size: 18px;
}

.message-header span {
  font-weight: normal;
  color: #999;
  font-size: 14px;
}

.message-content p {
  color: #666;
  font-size: 16px;
  line-height: 1.5;
}

img {
  max-width: 100%;
  height: auto;
}
</style>
    