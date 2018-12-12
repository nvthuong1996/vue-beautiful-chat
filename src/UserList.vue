<template>
  <div class="user-list">
    <table class="" style="padding-top: 5px;width:100%;">
      <tbody>
        
        <tr v-for="user in getUserList" :key="user.id" @click="selectUser(user.id)" :class="{'me-user':user.me}">
          <a href="#" class="list-user-item">
            <td style="text-align: center;position:relative;"><img :src="user.avatar" class="img-msg"/>
              <span v-if="user.state=='online'" class="online"></span>
            </td>
            <td>{{user.name}}</td>
            
          </a>
        </tr>
        
      </tbody>
  </table>
  </div>
</template>
<style scoped>
.me-user{
  background-color: rgba(0,0,0,0.1);
}
.list-user-item{
  text-decoration: none;
}
.online {
  content:'';
  background: rgb(66, 183, 42);
  border-radius: 50%;
  display: inline-block;
  height: 10px;
  margin-left: 4px;
  width: 10px;
  position: absolute;
  left: 35px;
  bottom: 4px;
}
</style>

<script>
export default {
  props: {
    participants: {
      type: Array,
      required: true
    },
    selectUser:{
      type:Function,
      required:true
    },
    me:{
      type:String,
      required:true
    }
  },
  computed:{
    getUserList(){
      const result = [];
      debugger
      for(let i in this.participants){
        const user = this.participants[i];
        if(user.id === this.me){
          user['me'] = true;
        }
        result.push(user)
      }
      return result;
    }
  }
}
</script>
<style scoped>
  .user-list {
    height: 100%;
    overflow: auto;
    padding-left: 5px;
    padding-top: 8px;
  }
  .img-msg {
    border-radius: 50%;
    width: 50px;
    margin-right: 5px;
  }
</style>
