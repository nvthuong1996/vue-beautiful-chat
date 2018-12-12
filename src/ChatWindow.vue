<template>
  <div class="sc-chat-window">
    <Header
      :title="title"
      :imageUrl="titleImageUrl"
      :onClose="onClose"
      :colors="colors"
      :closeBtn="closeBtn"
      :online="getOnline"
      :canReply="canReply"
      :andanh="andanh"
      :onSave="onSave"
      @userList="handleUserListToggle"
      :backToConversation="backToConversation"
    />
    <UserList 
      v-if="showUserList"
      :participants="participants"
      :selectUser="selectUser"
      :me="me"
    />
    <UserInfo
     :me="me"
     :participants="participants"
     :conversation_info="conversation_info"
     :user_info="user_info"
     :currentUser="currentUser"
     v-if="showUserInfo"
    />
    <MessageList
      v-if="!showUserList&&!showUserInfo"
      :messages="messages"
      :me="me"
      :participants="participants"
      :showTypingIndicator="showTypingIndicator"
      :colors="colors"
      :alwaysScrollToBottom="alwaysScrollToBottom"
      :showLoadMessage ="showLoadMessage"
      :loadNewMessage="loadNewMessage"
      :showImage ="showImage"
    />
    <UserInput
      v-if="!showUserList&&!showUserInfo"
      :showEmoji="showEmoji"
      :onSubmit="onUserInputSubmit"
      :showFile="showFile"
      :placeholder="placeholder"
      :colors="colors"
      :me="me" 
      :uploadFile="uploadFile"
      :canReply="canReply"
      />
    <b-modal v-model="modalShow" centered hide-header hide-footer body-class="modal-show-img" size="lg">
      <div style="position: relative;">
        <img width="100%" :src="imgSourse">
        <b-btn @click="modalShow=false" style="position: absolute;top: 0;right: 0;width: 37px;" variant="ghost-danger" block><i class="fas fa-times"></i></b-btn>
      </div>
    </b-modal>
  </div>
</template>

<script>
import Header from './Header.vue'
import MessageList from './MessageList.vue'
import UserInput from './UserInput.vue'
import UserList from './UserList.vue'
import UserInfo from './UserInfo.vue';

export default {
  components: {
    Header,
    MessageList,
    UserInput,
    UserList,
    UserInfo
  },
  props: {
    onSave: {
      type: Function,
      required: false
    },
    canReply:{
      type:Boolean,
      required:true
    },
    andanh:{
      type:Boolean,
      required:false
    },
    loadNewMessage:{
      type:Function,
      required:true
    },
    conversation_info:{
      type:Object,
      required:true
    },
    user_info:{
      type:Object,
      required:true
    },
    uploadFile:{
      type:Function,
      required:true
    },
    showLoadMessage: {
      type: Boolean,
      required: true
    },
    me:{
      type: String,
      required: true
    },
    closeBtn:{
      type: Boolean,
      default: false
    },
    showEmoji: {
      type: Boolean,
      default: false
    },
    showFile: {
      type: Boolean,
      default: false
    },
    participants: {
      type: Array,
      required: true
    },
    title: {
      type: String,
      required: true
    },
    titleImageUrl: {
      type: String,
      default: ''
    },
    onUserInputSubmit: {
      type: Function,
      required: true
    },
    onClose: {
      type: Function,
      required: true
    },
    messageList: {
      type: Array,
      default: () => []
    },
    isOpen: {
      type: Boolean,
      default: () => false
    },
    placeholder: {
      type: String,
      default: 'Write a reply'
    },
    showTypingIndicator: {
      type: String,
      required: true
    },
    colors: {
      type: Object,
      required: true
    },
    alwaysScrollToBottom: {
      type: Boolean,
      required: true
    }
  },
  data() {
    return {
      showUserList: false,
      showUserInfo:false,
      currentUser:null,
      modalShow:false,
      imgSourse:''
    }
  },
  computed: {
    messages() {
      let messages = this.messageList

      return messages
    },
    getOnline(){
      if(this.conversation_info&&this.conversation_info.type=='friend'){
        for(let i in this.participants){
          const member = this.participants[i];
          if(member.id!=this.me&&member.state=='online'){
            return true;
          }
        }
      }
      return false;
    }
  },
  methods: {
    showImage(url){
      this.modalShow = true;
      this.imgSourse = url;
    },
    backToConversation(){
      if(!this.showUserInfo&&!this.showUserInfo&&!this.showUserList){
        if (this.conversation_info.type == "friend") {
          return this.$router.push({ path: "/chats/conversation/friend" });
        } else {
          return this.$router.push({ path: "/chats/conversation/group" });
        }
      }else if(this.showUserList){
        this.showUserList = false;
      }else if(!this.showUserInfo&&!this.showUserList){
        this.showUserList = true;
      }else if(this.showUserInfo){
        this.showUserList = true;
      }
    },
    handleUserListToggle(showUserList) {
      if(this.showUserInfo){
        this.showUserList = true;
      }else if(this.showUserList){
        this.showUserList = false;
      }else if(!this.showUserInfo&&!this.showUserList){
        this.showUserList = true;
      }
    },
    selectUser(mssv){
      this.currentUser = mssv;
      this.showUserInfo=true;
    }
  },
  watch:{
    showUserList:function(newVal){
      this.showUserInfo = newVal?false:this.showUserInfo;
    },
    showUserInfo:function(newVal){
      this.showUserList = newVal?false:this.showUserList;
    }
  }
}
</script>
<style scoped>
.sc-chat-window {
  flex-grow: 999;
  height: 100%;
  right: 25px;
  bottom: 100px;
  box-sizing: border-box;
  box-shadow: 0px 7px 40px 2px rgba(148, 149, 150, 0.1);
  background: white;
  display: flex;
  flex-direction: column;
  transition: 0.3s ease-in-out;
  border-radius: 10px;
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
}

.sc-chat-window.closed {
  opacity: 0;
  visibility: hidden;
  bottom: 90px;
}

.sc-message--me {
  text-align: right;
}
.sc-message--them {
  text-align: left;
}

@media (max-width: 450px) {
  .sc-chat-window {
    width: 100%;
    height: 100%;
    max-height: 100%;
    right: 0px;
    bottom: 0px;
    border-radius: 0px;
  }
  .sc-chat-window {
    transition: 0.1s ease-in-out;
  }
  .sc-chat-window.closed {
    bottom: 0px;
  }
}
</style>

