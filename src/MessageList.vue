<template>
  <div class="sc-message-list" ref="scrollList" :style="{backgroundColor: colors.messageList.bg} ">
    <Message :showImage="showImage" class="showLoadMessage" v-if="showLoadMessage" :message="{author: 'abc', type: 'typing'}" :chatImageUrl="chatImageUrl(showTypingIndicator)" :colors="colors"  :me="me"/>
    <Message :showImage="showImage" v-for="message in messages" :message="message" :chatImageUrl="chatImageUrl(message.author)" :authorName="authorName(message.author)" :key="message['.key']" :id="message['.key']" :me="me" :colors="colors" :timestamp="message.timestamp" />
    <Message :showImage="showImage" v-show="showTypingIndicator !== ''" :message="{author: showTypingIndicator, type: 'typing'}" :chatImageUrl="chatImageUrl(showTypingIndicator)" :colors="colors"  :me="me"/>
  </div>
</template>


<script>
import Message from './Message.vue'
import chatIcon from './assets/chat-icon.svg'
import { setTimeout } from 'timers';

export default {
  components: {
    Message
  },
  props: {
    showImage:{
      type:Function,
      required:true
    },
    loadNewMessage:{
      type:Function,
      required:true
    },
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
//      debugger
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
      return this.profile(author).avatar
    },
    authorName(author) {
      return this.profile(author).name
    },
    async handleScroll(event) {
      if (event.srcElement.scrollTop === 0) {
        await this.loadNewMessage();
        // load message
        this.$refs.scrollList.scrollTop = 5;

        
      }
    }
  },
  computed: {
    defaultChatIcon() {
      return chatIcon
    }
  },
  updated () {
    if (this.shouldScrollToBottom()){
      this.$nextTick(this._scrollDown())
    }else if(!this.showLoadMessage){
      this.$nextTick(this._scrollTop())
    }
  },
  mounted() {
    this._scrollDown()
    document.querySelector(".sc-message-list") &&
      document
        .querySelector(".sc-message-list")
        .addEventListener("scroll", this.handleScroll);
  }
}
</script>

<style>
  .modal-show-img{
    padding: 5px!important;
  }
</style>

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
