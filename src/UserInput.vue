<template>
  <div v-if="canReply">
    <div v-if="file" class='file-container' :style="{backgroundColor: colors.userInput.text, color: colors.userInput.bg}">
      <div>
         <span class='icon-file-message'><img src="./assets/file.svg" alt='genericFileIcon' height="15" /></span>
        {{file.name}}
        <span class='delete-file-message' @click="cancelFile()" ><img src="./assets/close.svg" alt='close icon' height="10" title='Remove the file' /></span>
      </div>
      <div>
        <b-progress v-if="showProgress" striped :animated="animate" height="2px" :value="progressValue"></b-progress>
      </div>
     </div>
    <form class="sc-user-input" :class="{active: inputActive}" :style="{background: colors.userInput.bg}">
      <div
        role="button"
        tabIndex="0"
        @focus="setInputActive(true)"
        @blur="setInputActive(false)"
        @keydown="handleKey"
        contentEditable="true"
        :placeholder="placeholder"
        class="sc-user-input--text"
        ref="userInput"
        :style="{color: colors.userInput.text}"
      >
      </div>
      <div class="sc-user-input--buttons">
        <div class="sc-user-input--button"></div>
        <div v-if="showEmoji" class="sc-user-input--button">
          <EmojiIcon :onEmojiPicked="_handleEmojiPicked" :color="colors.userInput.text" />
        </div>
        <div v-if="showFile" class="sc-user-input--button">
          <FileIcons :onChange="_handleFileSubmit" :color="colors.userInput.text" />
        </div>
        <div class="sc-user-input--button">
          <SendIcon :disabled="uploadfile" :onClick="_submitText" :color="colors.userInput.text" />
          
        </div>
      </div>
    </form>
  </div>
  <div v-else>
    <div class="sc-user-input" :class="{active: inputActive}" :style="{background: colors.userInput.bg}">
      <span style="color: rgb(86, 88, 103);margin: auto;">Cuộc trò chuyện đã kết thúc</span>
    </div>
  </div>
</template>


<script>
import EmojiIcon from './EmojiIcon.vue'
import FileIcons from './FileIcons.vue'
import SendIcon from './SendIcon.vue'

export default {
  components: {
    EmojiIcon,
    FileIcons,
    SendIcon
  },
  props: {
    canReply:{
      type:Boolean,
      default:true
    },
    uploadFile: {
      type: Function,
      required: true
    },
    me: {
      type: String,
      required: true
    },
    showEmoji: {
      type: Boolean,
      default: () => false
    },
    showFile: {
      type: Boolean,
      default: () => false
    },
    onSubmit: {
      type: Function,
      required: true
    },
    placeholder: {
      type: String,
      default: 'Write a reply'
    },
    colors: {
      type: Object,
      required: true
    },
    uploadFile: {
      type: Function,
      required: true
    }
  },
  data() {
    return {
      file: null,
      inputActive: false,
      uploadfile: false,
      progressValue: 25,
      showProgress: false,
      animate: true
    }
  },
  methods: {
    showProgressMethod() {
      this.showProgress = true
      this.progressValue = 10
      setInterval(() => {
        this.progressValue += 10
      }, 1000)
    },
    hiddenProgress() {
      this.progressValue = 100
      this.showProgress = false
    },
    cancelFile() {
      this.file = null
    },
    setInputActive(onoff) {
      this.inputActive = onoff
    },
    handleKey(event) {
      if (event.keyCode === 13 && !event.shiftKey) {
        this._submitText(event)
        event.preventDefault()
      }
    },
    async _submitText(event) {
      const text = this.$refs.userInput.textContent
      const file = this.file

      // upload file

      if (file) {
        // upload file
        debugger
        this.showProgressMethod()

        this.uploadfile = true

        const file_url = await this.uploadFile(file)

        this.uploadfile = false
        this.hiddenProgress()

        if (text && text.length > 0) {
          if (file_url) {
            this.onSubmit({
              author: this.me,
              type: 'file',
              data: {
                text,
                file: {
                  url: file_url,
                  name: file.name,
                  type: this.isImage(file.name) ? 'img' : 'n/a'
                }
              }
            })
          }
          this.file = null
          this.$refs.userInput.innerHTML = ''
        } else {
          if (file_url) {
            this.onSubmit({
              author: this.me,
              type: 'file',
              data: {
                file: {
                  url: file_url,
                  name: file.name,
                  type: this.isImage(file.name) ? 'img' : 'n/a'
                }
              }
            })
          }
          this.file = null
        }
      } else {
        if (text && text.length > 0) {
          this.onSubmit({
            author: this.me,
            type: 'text',
            data: { text }
          })
          this.$refs.userInput.innerHTML = ''
        }
      }
    },
    _handleEmojiPicked(emoji) {
      this.onSubmit({
        author: this.me,
        type: 'emoji',
        data: { emoji }
      })
    },
    _handleFileSubmit(file) {
      this.file = file
    },
    isImage(filename) {
      var ext = this.getExtension(filename)
      switch (ext.toLowerCase()) {
        case 'jpg':
        case 'gif':
        case 'bmp':
        case 'png':
          //etc
          return true
      }
      return false
    },
    getExtension(filename) {
      var parts = filename.split('.')
      return parts[parts.length - 1]
    }
  }
}
</script>

<style>
.sc-user-input {
  min-height: 55px;
  margin: 0px;
  position: relative;
  bottom: 0;
  display: flex;
  background-color: #f4f7f9;
  border-bottom-left-radius: 10px;
  border-bottom-right-radius: 10px;
  transition: background-color 0.2s ease, box-shadow 0.2s ease;
}

.sc-user-input--text {
  width: 300px;
  resize: none;
  border: none;
  outline: none;
  border-bottom-left-radius: 10px;
  box-sizing: border-box;
  padding: 18px;
  font-size: 15px;
  font-weight: 400;
  line-height: 1.33;
  white-space: pre-wrap;
  word-wrap: break-word;
  color: #565867;
  -webkit-font-smoothing: antialiased;
  max-height: 200px;
  overflow: scroll;
  bottom: 0;
  overflow-x: hidden;
  overflow-y: auto;
}

.sc-user-input--text:empty:before {
  content: attr(placeholder);
  display: block; /* For Firefox */
  /* color: rgba(86, 88, 103, 0.3); */
  filter: contrast(15%);
  outline: none;
}

.sc-user-input--buttons {
  width: 100px;
  position: absolute;
  right: 30px;
  height: 100%;
  display: flex;
}

.sc-user-input--button:first-of-type {
  width: 40px;
}

.sc-user-input--button {
  width: 30px;
  height: 55px;
  margin-left: 2px;
  margin-right: 2px;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.sc-user-input.active {
  box-shadow: none;
  background-color: white;
  box-shadow: 0px -5px 20px 0px rgba(150, 165, 190, 0.2);
}

.sc-user-input--button label {
  position: relative;
  height: 24px;
  padding-left: 3px;
  cursor: pointer;
}

.sc-user-input--button label:hover path {
  fill: rgba(86, 88, 103, 1);
}

.sc-user-input--button input {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  z-index: 99999;
  height: 100%;
  opacity: 0;
  cursor: pointer;
  overflow: hidden;
}

.file-container {
  background-color: #f4f7f9;
  border-top-left-radius: 10px;
  padding: 5px 20px;
  color: #565867;
}

.delete-file-message {
  font-style: normal;
  float: right;
  cursor: pointer;
  color: #c8cad0;
}

.delete-file-message:hover {
  color: #5d5e6d;
}

.icon-file-message {
  margin-right: 5px;
}
</style>
