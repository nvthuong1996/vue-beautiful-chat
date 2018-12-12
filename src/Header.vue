<template>
  <div @click="fixScrollMobile" class="sc-header" :style="{background: colors.header.bg, color: colors.header.text}">
    <img class="sc-header--img" :src="imageUrl" alt="" v-if="imageUrl" />
    <span v-if="online" class="online"></span>
    <div class="sc-header--title" @click="toggleUserList"> {{title}} </div>
    <div v-if="closeBtn&&canReply" class="sc-header--close-button" @click="_onClose">
      <i class="fas fa-times fa-lg"></i>
    </div>
    <div class="sc-header--close-button" @click="_onSave" v-if="canReply&&andanh">
      <i class="fas fa-heart fa-lg"></i>
    </div>
    <div class="sc-header--close-button" @click="onClose(true)" v-if="!canReply&&andanh">
      <i class="fas fa-sync fa-lg"></i>
    </div>
    <div class="sc-header--close-button" v-if="this.$route.name!=='chatandanh'" @click="backToConversation">
        <i class="fas fa-chevron-left"></i>
    </div>


  <div v-if="canReply">
    <b-modal v-model="modalShow" centered size="sm" @ok="_handleModalOk" hide-header body-class="m-modal-exit">
      <h6 style="color: rgba(0, 0, 0, 0.54);">
        {{contentModal}}
      </h6>
    </b-modal>
  </div>
  </div>
</template>


<style>
  .m-modal-exit{
    max-width: 200px!important;
  }
</style>


<style scoped>

  .online {
    content:'';
    background: rgb(66, 183, 42);
    border-radius: 50%;
    display: inline-block;
    height: 10px;
    margin-left: 4px;
    width: 10px;
    position: absolute;
    left: 47px;
    bottom: 18px;
  }
</style>

<script>  
import  {AsideToggler} from '@coreui/vue'

export default {
  components:{
    AsideToggler
  },
  props: {
    backToConversation:{
      type:Function
    },
    canReply:{
      type:Boolean,
      default:true
    },
    andanh:{
      type:Boolean,
      default:false
    },
    closeBtn:{
      type:Boolean
    },
    imageUrl: {
      type: String,
      required: true
    },
    title: {
      type: String
    },
    onClose: {
      type: Function,
      required: true
    },
    onSave: {
      type: Function,
      required: false
    },
    colors: {
      type: Object,
      required: true
    },
    online:{
      type:Boolean,
      default:false
    }
  },
  methods: {
    _handleModalOk(){
      if(this.contentModal == 'Thoát trò chuyện ?'){
        return this.onClose();
      }else if(this.contentModal == 'Lưu trò chuyện ?'){
        return this.onSave();
      }
    },
    _onClose(){
      if(this.canReply){
        this.contentModal = 'Thoát trò chuyện ?';
        this.modalShow =  true;
      }else{
        return this.onClose();
      }
    },
    _onSave(){
      if(this.canReply){
        this.contentModal = 'Lưu trò chuyện ?';
        this.modalShow =  true;
      }
    },
    fixScrollMobile(){
      window.scrollTo(0,0);
    },
    toggleUserList() {
      this.inUserList = !this.inUserList
      this.$emit("userList", this.inUserList)
    }
  },
  data() {
    return {
      modalShow:false,
      inUserList: false,
      contentModal:''
    }
  }
}
</script>
<style scoped>
.sc-header {
  min-height: 75px;
  padding: 10px;
  box-shadow: 0 1px 4px rgba(0,0,0,.2);
  position: relative;
  box-sizing: border-box;
  display: flex;
}

.sc-header--img {
  border-radius: 50%;
  align-self: center;
  padding: 10px;
}

.sc-header--title {
  align-self: center;
  padding: 10px;
  flex: 1;
  user-select: none;
  cursor: pointer;
  border-radius: 5px;
  box-shadow: 0px 3px 2px rgba(0.2, 0.2, 0.5, .1);
}

.sc-header--title:hover {
  box-shadow: 0px 2px 5px rgba(0.2, 0.2, 0.5, .1);
}

.sc-header--close-button {
  width: 40px;
  align-self: center;
  height: 40px;
  margin-right: 0px;
  box-sizing: border-box;
  cursor: pointer;
  border-radius: 5px;
}

.sc-header--close-button:hover {
  box-shadow: 0px 2px 5px rgba(0.2, 0.2, 0.5, .1);
}

.sc-header--close-button img {
  width: 100%;
  height: 100%;
  padding: 13px;
  box-sizing: border-box;
}
.sc-header--close-button i {
  width: 100%;
  height: 100%;
  padding: 13px;
  box-sizing: border-box;
}
@media (max-width: 450px) {
  .sc-header {
    border-radius: 0px;
  }
}
</style>
