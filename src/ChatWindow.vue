<template>
  <div class="sc-chat-window">
    <Header
      :title="title"
      :imageUrl="titleImageUrl"
      :onClose="onClose"
      :colors="colors"
      :closeBtn="closeBtn"
      @userList="handleUserListToggle"
    />
    <UserList 
      v-if="showUserList"
      :participants="participants"
    />
    <MessageList
      v-if="!showUserList"
      :messages="messages"
      :me="me"
      :participants="participants"
      :showTypingIndicator="showTypingIndicator"
      :colors="colors"
      :alwaysScrollToBottom="alwaysScrollToBottom"
      :showLoadMessage ="showLoadMessage"
    />
    <UserInput
      v-if="!showUserList"
      :showEmoji="showEmoji"
      :onSubmit="onUserInputSubmit"
      :showFile="showFile"
      :placeholder="placeholder"
      :colors="colors"
      :me="me" 
      :uploadFile="uploadFile"/>
  </div>
</template>

<script>
import Header from './Header.vue'
import MessageList from './MessageList.vue'
import UserInput from './UserInput.vue'
import UserList from './UserList.vue'

export default {
  components: {
    Header,
    MessageList,
    UserInput,
    UserList
  },
  props: {
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
      showUserList: false
    }
  },
  computed: {
    messages() {
      let messages = this.messageList

      return messages
    }
  },
  methods: {
    handleUserListToggle(showUserList) {
      this.showUserList = showUserList
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