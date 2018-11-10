<template>
  <div class="sc-message-list" ref="scrollList" :style="{backgroundColor: colors.messageList.bg} ">
    <Message class="showLoadMessage" v-if="showLoadMessage" :message="{author: 'abc', type: 'typing'}" :chatImageUrl="chatImageUrl(showTypingIndicator)" :colors="colors"  :me="me"/>
    <Message v-for="message in messages" :message="message" :chatImageUrl="chatImageUrl(message.author)" :authorName="authorName(message.author)" :key="message.id" :id="message.id" :me="me" :colors="colors" :timestamp="message.timestamp" />
    <Message v-show="showTypingIndicator !== ''" :message="{author: showTypingIndicator, type: 'typing'}" :chatImageUrl="chatImageUrl(showTypingIndicator)" :colors="colors"  :me="me"/>
  </div>
</template>
<script>
import Message from './Message.vue'
import chatIcon from './assets/chat-icon.svg'

export default {
  components: {
    Message
  },
  props: {
    showLoadMessage: {
      type: Boolean,
      required: true
    },
    me: {
      type: String,
      required: true
    },
    participants: {
      type: Array,
      required: true
    },
    messages: {
      type: Array,
      required: true
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
  methods: {
    _scrollDown () {
      this.$refs.scrollList.scrollTop = this.$refs.scrollList.scrollHeight
    },
    _scrollTop () {
      this.$refs.scrollList.scrollTop = 100;
    },
    shouldScrollToBottom() {
      return this.alwaysScrollToBottom || (this.$refs.scrollList.scrollTop > this.$refs.scrollList.scrollHeight - 600)
    },
    profile(author) {
      const profile = this.participants.find(profile => profile.id === author)

      // A profile may not be found for system messages or messages by 'me'
      return profile || {imageUrl: '', name: ''}
    },
    chatImageUrl(author) {
      return this.profile(author).imageUrl
    },
    authorName(author) {
      return this.profile(author).name
    }
  },
  computed: {
    defaultChatIcon() {
      return chatIcon
    }
  },
  mounted () {
    this._scrollDown()
  },
  updated () {
    if (this.shouldScrollToBottom()){
      this.$nextTick(this._scrollDown())
    }else if(!this.showLoadMessage){
      this.$nextTick(this._scrollTop())
    }
      
  }
}
</script>

<style>
  .showLoadMessage .sc-message--avatar{
    display: none!important;
  }
  .showLoadMessage .sc-typing-indicator{
    background: none!important;
  }
  .showLoadMessage .sc-message--content{
    justify-content: center;
  }
</style>

<style scoped>
.sc-message-list {
  flex-grow: 999;
  height: 80%;
  overflow-y: auto;
  background-size: 100%;
  padding: 10px 0px;
}
</style>
