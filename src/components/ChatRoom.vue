<template>
  <el-form ref="messageForm"
           :model="form">
    <el-input v-model="textarea"
              :rows="3"
              type="textarea"
              ref="messageBox"
              placeholder="Please input" />
    <el-form-item>
      <el-button type="primary"
                 @click="sendMessage">发送消息</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
import { ref } from 'vue'
export default {
  name: 'ChatRoom',
  props: {
    msg: String
  },
  mounted () {
    let token = localStorage.getItem('token');
    if (token == '' || token == null) {
      console.info('ghost')
    } else {
      console.info('user')
    }
    this.initWebSocket();
  },
  methods: {
    //
    sendMessage () {
      console.info('sendMessage is :' + this.textarea);
      let messsage = this.textarea;
      this.websocketsend(messsage);
    },
    initWebSocket () { //初始化weosocket
      const wsuri = "ws://127.0.0.1:8888/ws";
      this.websock = new WebSocket(wsuri);
      this.websock.onmessage = this.websocketonmessage;
      this.websock.onopen = this.websocketonopen;
      this.websock.onerror = this.websocketonerror;
      this.websock.onclose = this.websocketclose;
    },
    websocketonopen () { //连接建立之后执行send方法发送数据
      let actions = { "test": "12345" };
      this.websocketsend(JSON.stringify(actions));
    },
    websocketonerror () {//连接建立失败重连
      this.initWebSocket();
    },
    websocketonmessage (e) { //数据接收
      const redata = JSON.parse(e.data);
    },
    websocketsend (Data) {//数据发送
      this.websock.send(Data);
    },
    websocketclose (e) {  //关闭
      console.log('断开连接', e);
    }
  },
  setup () {
    return {
      textarea: ref('')
    }
  }
}
</script>

