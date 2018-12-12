<template>
<div class="container-user-info">
  <div class="profile large">
    <div class="content-user-info">
      <div class="cover">
        <div class="cover-img" :style="{ 'background-image': `url('${getCover}')`}">
          <div :class="{layer:true,visible:loadCoverUpload}">
            <div class="loader"></div>
          </div>
            <a class="image-wrapper" href="#" v-if="me===currentUser" >
              <form id="coverForm" class="form-input-img" action="#">
                <input class="hidden-input"  @change.prevent="_handleChangeCover" accept="image/*" hidden id="changeCover" type="file"/>
                <label class="edit fas fa-pencil-alt" for="changeCover" title="Change cover"></label>
              </form>
            </a>
        </div>
      </div>
      <div class="user-info">
        <div class="profile-pic"><img :src="getAvatar"/>
          <div :class="{layer:true,visible:loadAvatarUpload}">
            <div class="loader"></div>
          </div><a class="image-wrapper" href="#" v-if="me===currentUser">
            <form  id="profilePictureForm" class="form-input-img" action="#">
              <input accept="image/*" @change.prevent="_handleChangeAvatar" class="hidden-input" id="changePicture" type="file"/>
              <label class="edit fas fa-pencil-alt" for="changePicture" type="file" title="Change picture"></label>
            </form></a>
        </div>
        <div class="username">
          <div class="name"><span class="verified"></span>@{{getName}}</div>
          <div class="about">sinh viên</div>

          <div class="list-info-user">
            <div class="item-info-user">
              <div class="item-name-user">
                <h6>
                  <strong>Thông tin cơ bản</strong>
                </h6>
                <b-button v-if="me===currentUser" size="sm" @click="addBasicInfoShowModal()" style="padding:2px;" variant="ghost-success">Add Info</b-button>
              </div>
 
              <div class="info-item-user" v-for="(item,key) in getBasicInfo" :key="key">
                <h6>{{item.key}}</h6>
                <h6>{{item.value}}
                  <!--
                  <a v-if="me===currentUser" @click="removeBasicInfo(item.key)" href="#"><i class="hidden-item-info fas fa-minus-circle"></i></a>
                  -->
                </h6>
              </div>
            </div>

            <div class="item-info-user">
              <div class="item-name-user">
                <h6>
                  <strong>Khoe điểm</strong>
                </h6>
                <b-button v-if="me===currentUser" @click="addMarksShowModal()" size="sm" style="padding:2px;" variant="ghost-success">Add Info</b-button>
              </div>
              <div class="info-item-user"  v-for="(item,key) in getMark" :key="key">
                <h6 style="max-width: 80%;">{{item.key}}</h6>
                <h6>{{item.value}} 
                  <!--
                  <a v-if="me===currentUser" @click="removeMark(item.msmh)" href="#"><i class="hidden-item-info fas fa-minus-circle"></i></a>
                  -->
                </h6>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
    <div>
      <b-modal centered hide-footer v-model="showModal"
              title="Thêm thông tin"
              :header-bg-variant="headerBgVariant"
              :header-text-variant="headerTextVariant"
              :body-bg-variant="bodyBgVariant"
              :body-text-variant="bodyTextVariant">
          <b-form v-if="type==='MARK'">
            <b-form-input id="exampleInput1"
                          type="email"
                          v-model="form.search"
                          required
                          placeholder="Tìm kiếm ex: A+ , lập trình c, bas12415...">
            </b-form-input>
          </b-form>
          <div v-if="type==='INFO'" class="list-info-user">
            <div class="item-info-user">
              <div class="info-item-user" v-if="checkAtt('ten_sv')">
                <h6>Tên</h6>
                <h6>{{user_info.ten_sv}} 
                  <a @click="addBasicInfo('ten_sv')" href="#"><i class="add-item-info fas fa-plus"></i></a>
                </h6>
              </div>
              <div class="info-item-user" v-if="checkAtt('gioi_tinh')">
                <h6>Giới tính</h6>
                <h6>{{user_info.gioi_tinh}} 
                  <a @click="addBasicInfo('gioi_tinh')" href="#"><i class="add-item-info fas fa-plus"></i></a>
                </h6>
              </div>
              <div class="info-item-user" v-if="checkAtt('ngay_sinh')">
                <h6>Ngày sinh</h6>
                <h6>{{user_info.ngay_sinh}} 
                  <a @click="addBasicInfo('ngay_sinh')" href="#"><i class="add-item-info fas fa-plus"></i></a>
                </h6>
              </div>
              <div class="info-item-user" v-if="checkAtt('noi_sinh')">
                <h6>Quê quán</h6>
                <h6>{{user_info.noi_sinh}} 
                  <a @click="addBasicInfo('noi_sinh')" href="#"><i class="add-item-info fas fa-plus"></i></a>
                </h6>
              </div>
              <div class="info-item-user" v-if="checkAtt('khoa')">
                <h6>Khoa</h6>
                <h6>{{user_info.khoa}} 
                  <a @click="addBasicInfo('khoa')" href="#"><i class="add-item-info fas fa-plus"></i></a>
                </h6>
              </div>
              <div class="info-item-user" v-if="checkAtt('lop')">
                <h6>Lớp</h6>
                <h6>{{user_info.lop}} 
                  <a @click="addBasicInfo('lop')" href="#"><i class="add-item-info fas fa-plus"></i></a>
                </h6>
              </div>
              <div class="info-item-user"  v-if="checkAtt('he_dao_tao')">
                <h6>Hệ đào tạo</h6>
                <h6>{{user_info.he_dao_tao}} 
                  <a @click="addBasicInfo('he_dao_tao')" href="#"><i class="add-item-info fas fa-plus"></i></a>
                </h6>
              </div>
            </div>
          </div>
         <div v-if="type==='MARK'" class="list-info-user">
            <div class="item-info-user">
              <div class="info-item-user" v-for="(item,key) in searchMark" :key="key">
                <h6>{{item.tenmh}}</h6>
                <h6 style="width:40px">{{item.xeploai}}
                  <a @click="addMark(item.msmh)" href="#"><i class="add-item-info fas fa-plus"></i></a>
                </h6>
              </div>
            </div>
          </div>
      </b-modal>
      <b-modal size="lg" centered hide-footer v-model="showModalResizeImage"
              title="Thay đổi kích thước hình ảnh"
              :header-bg-variant="headerBgVariant"
              :header-text-variant="headerTextVariant"
              :body-bg-variant="bodyBgVariant"
              :body-text-variant="bodyTextVariant">
              <div style="height:400px;" 
                v-show="typeUpload=='cover'"
              >
                <vue-croppie 
                  ref="croppieRef1" 
                  :enableOrientation="true"
                  :mouseWheelZoom="false"
                  :enableResize="false"
                  :viewport="{ width: 700, height: 300}"
                >
              </vue-croppie>
              </div>
              <div style="height:400px;" 
                v-show="typeUpload=='avatar'"
              >
                <vue-croppie 
                  ref="croppieRef2" 
                  :enableOrientation="true"
                  :mouseWheelZoom="false"
                  :enableResize="false"
                  :viewport="{ width: 300, height: 300}"
                >
              </vue-croppie>
              </div>
              <b-btn style="margin-top:50px;" @click="uploadImgCrop" variant="outline-success" block>Xong</b-btn>
      </b-modal>
  </div>
</div>
</template>

<style scoped>
@-webkit-keyframes spin {
  0% {
    -webkit-transform: rotate(0deg) translate(-50%);
    transform: rotate(0deg) translate(-50%);
  }
  100% {
    -webkit-transform: rotate(360deg) translate(-50%);
    transform: rotate(360deg) translate(-50%);
  }
}
@keyframes spin {
  0% {
    -webkit-transform: rotate(0deg) translate(-50%);
    transform: rotate(0deg) translate(-50%);
  }
  100% {
    -webkit-transform: rotate(360deg) translate(-50%);
    transform: rotate(360deg) translate(-50%);
  }
}
.add-item-info {
  color: #4dbd74;
}

.hidden-item-info {
  color: #f86c6b;
}
.info-item-user {
  padding: 5px;
  display: flex;
  justify-content: space-between;
  flex-direction: row;
  border-bottom: 1px solid rgba(0, 0, 0, 0.15);
}

.item-name-user {
  background: #f5f6f7;
  padding: 7px;
  display: flex;
  justify-content: space-between;
  flex-direction: row;
}
.content-user-info {
  max-width: 700px;
  margin: auto;
  background-color: #fff;
}
.list-info-user {
  color: #4b4f56 !important;
}
.item-info-user h6 {
  text-align: left;
}
.item-info-user {
  border-top: 1px solid rgba(0, 0, 0, 0.15);
  margin: 12px 0;
}

.cover-img {
  height: 300px;
  max-width: 700px;
  margin: auto;
  position: relative;
}
.loader {
  position: absolute;
  left: 50%;
  top: 50%;
  -webkit-transform: translateY(-50%) translateX(-50%);
  transform: translateY(-50%) translateX(-50%);
  -webkit-animation: spin 0.35s infinite linear;
  animation: spin 0.35s infinite linear;
  border: 2px solid #707070;
  border-radius: 50%;
  border-top-color: white;
  height: 25px;
  -webkit-transform-origin: left;
  transform-origin: left;
  top: 45%;
  width: 25px;
}
.hidden-input {
  left: -999px;
  position: absolute;
}
.container-user-info {
  background-color: #e9ebee;
}
.profile {
  *zoom: 1;
  background-color: #e9ebee;
  border-radius: 2px;
  display: block;
  float: none;
  margin: 5px auto;
  overflow: hidden;
  padding-bottom: 20px;
}
.profile:before,
.profile:after {
  content: '';
  display: table;
}
.profile:after {
  clear: both;
}
.about {
  font-family: Helvetica, 'Helvetica Neue', 'Tahoma';
  font-size: 12px;
  color: #adadad;
  line-height: 17px;
}
.image-wrapper {
  background: rgba(0, 0, 0, 0.2);
  bottom: -50px;
  height: 50px;
  left: 0;
  position: absolute;
  transition: bottom 0.15s linear;
  width: 100%;
}
.edit {
  position: relative;
  left: 50%;
  -webkit-transform: translateX(-50%);
  transform: translateX(-50%);
  color: white;
  cursor: pointer;
  font-size: 22px;
  top: 10px;
}
.cover {
  height: 300px;
  overflow: hidden;
  position: relative;
  width: 100%;
}
.cover img {
  position: absolute;
  left: 50%;
  -webkit-transform: translateX(-50%);
  transform: translateX(-50%);
  height: 300px;
}
.cover .image-wrapper {
  bottom: auto;
  height: 45px;
  left: auto;
  position: absolute;
  right: 0;
  top: 0;
  width: 45px;
}
.name {
  font-family: Helvetica, 'Helvetica Neue', 'Tahoma';
  font-size: 18px;
}
.profile-pic {
  position: absolute;
  left: 50%;
  -webkit-transform: translateX(-50%);
  transform: translateX(-50%);
  border-radius: 50%;
  border: 4px solid white;
  height: 210px;
  overflow: hidden;
  -webkit-transform: translateX(-50%) translateY(-50%);
  transform: translateX(-50%) translateY(-50%);
  width: 210px;
  top: 0;
}
.profile-pic img {
  box-sizing: border-box;
  height: 100%;
  left: 50%;
  max-height: 100%;
  position: absolute;
  -webkit-transform: translateX(-50%);
  transform: translateX(-50%);
  transition: all 0.15s ease-out;
  width: auto;
}
.profile-pic:hover .image-wrapper {
  bottom: 0;
}
.username {
  margin-top: 122px;
  text-align: center;
}
.user-info {
  *zoom: 1;
  position: relative;
}
.user-info:before,
.user-info:after {
  content: '';
  display: table;
}
.user-info:after {
  clear: both;
}
.container-user-info {
  flex-grow: 999;
  overflow-y: auto;
}
.layer {
  background-color: rgba(0, 0, 0, 0.25);
  display: none;
  height: 100%;
  left: 0;
  position: absolute;
  top: 0;
  width: 100%;
}
.layer.visible {
  display: block;
}
</style>


<script>
export default {
  props: {
    participants: {
      type: Array,
      required: true
    },
    me: {
      type: String,
      required: true
    },
    conversation_info: {
      type: Object,
      required: true
    },
    user_info: {
      type: Object,
      required: true
    },
    currentUser: {
      type: String,
      default: ''
    }
  },
  data: () => {
    return {
      variants: [
        'primary',
        'secondary',
        'success',
        'warning',
        'danger',
        'info',
        'light',
        'dark'
      ],
      headerBgVariant: 'success',
      headerTextVariant: 'dark',
      bodyBgVariant: 'light',
      bodyTextVariant: 'dark',
      showModal: false,
      form: {
        search: null,
        type: null
      },
      coverImg: null,
      avatarImg: null,
      showModalResizeImage:false,
      cropped:null,
      typeUpload:null,
      loadAvatarUpload:false,
      loadCoverUpload:false,
      type:''
    }
  },
  computed: {
    searchMark() {
      if (!this.form.search) return
      const mark = this.user_info.bangdiem
      const query = this.form.search.toLowerCase()
      const result = []
      for (let i in mark) {
        const namhoc = mark[i]
        for (let k of namhoc) {
          const msmh = k[1].toLowerCase()
          const tenmh = k[2].toLowerCase()
          const xeploai = k[16].toLowerCase()
          
          if(this.getMark.find(item=>item.msmh === k[1])){
            continue;
          }

          if (query == xeploai) {
            result.push({
              tenmh: k[2],
              xeploai: k[16],
              msmh: k[1]
            })
          } else if (tenmh.indexOf(query) >= 0 && query.length >= 3) {
            result.push({
              tenmh: k[2],
              xeploai: k[16],
              msmh: k[1]
            })
          } else if (msmh.indexOf(query) >= 0 && query.length >= 3) {
            result.push({
              tenmh: k[2],
              xeploai: k[16],
              msmh: k[1]
            })
          }
        }
      }
      return result
    },
    getBasicInfo() {
      if (!this.currentUser) return []
      const user = this.participants.find(item=>item.id===this.currentUser).basicinfo
      if (!user) return []
      const result = []
      for (let key in user) {
        result.push({
          key: key,
          value: user[key]
        })
      }
      return result
    },
    getMark() {
      if (!this.currentUser) return []
      const user = this.participants.find(item=>item.id===this.currentUser).bangdiem
      if (!user) return []
      const result = []
      for (let key in user) {
        result.push({
          key: user[key][2],
          value: user[key][16],
          msmh: user[key][1]
        })
      }
      return result
    },
    getName() {
      return this.participants.find(item=>item.id===this.currentUser).name;
    },
    getAvatar() {
      return this.participants.find(item=>item.id===this.currentUser).avatar;
    },
    getCover() {
      return this.participants.find(item=>item.id===this.currentUser).cover;
    }
  },
  methods: {
    async uploadImgCrop(){

      this.showModalResizeImage = false;
      
      let options = {
          format: 'jpeg',
          type:'blob'
      }
      const seft = this;

      if(this.typeUpload == 'avatar'){
        this.loadAvatarUpload = true;
        this.$refs.croppieRef2.result(options, async (output) => {
          
          const url = await seft.uploadImg(this.currentUser+"/avatar.jpeg",output);
          await seft.$store.state.db.ref("public/"+this.currentUser).update({
            avatar:url
          })
          seft.loadAvatarUpload = false;

        })
      }else if(this.typeUpload == 'cover'){
        this.loadCoverUpload = true;
        this.$refs.croppieRef1.result(options,async (output) => {
          const url = await seft.uploadImg(this.currentUser+"/cover.jpeg",output)
          await seft.$store.state.db.ref("public/"+this.currentUser).update({
            cover:url
          })
          seft.loadCoverUpload = false;

        })
      }
    },
    async uploadImg(filePath, file) {
      return this.$store.state.firebase
        .storage()
        .ref(filePath)
        .put(file)
        .then(function(fileSnapshot) {
          return fileSnapshot.ref.getDownloadURL().then(url => {
            return url
          })
        })
    },
    async _handleChangeCover(e) {

      const file = e.target.files[0]
      document.querySelectorAll('.form-input-img').forEach(e=>e.reset());


      if (file.type.indexOf('image/') < 0) {
        return this.$root.showError('Sai định dạng file')
      }

      const reader = new FileReader();
      const seft = this;

      reader.onload = function (e) {
          seft.showModalResizeImage = true;
          seft.typeUpload = 'cover'
          seft.$refs.croppieRef1.bind({
              url: e.target.result,
          });
      }

      reader.readAsDataURL(file);
    },
    _handleChangeAvatar(e) {
      

      const file = e.target.files[0]
      document.querySelectorAll('.form-input-img').forEach(e=>e.reset());


      if (file.type.indexOf('image/') < 0) {
        return this.$root.showError('Sai định dạng file')
      }

      const reader = new FileReader();
      const seft = this;

      reader.onload = function (e) {
          debugger
          seft.showModalResizeImage = true;
          seft.typeUpload = 'avatar'
          seft.$refs.croppieRef2.bind({
              url: e.target.result,
          });
      }

      reader.readAsDataURL(file);

    },
    checkAtt(att) {
      if (!this.currentUser) return true
      const user = this.participants.find(item=>item.id===this.currentUser).basicinfo
      for (let key in user) {
        if (key === att) {
          return false
        }
      }
      return true
    },
    addBasicInfoShowModal: function() {
      this.showModal = true
      this.type = 'INFO'
    },
    addMarksShowModal: function() {
      this.showModal = true
      this.type = 'MARK'
    },
    addBasicInfo: async function(att) {
      const remote = this.$store.state.functions.httpsCallable('remote')
      try {
        const r = await remote({
          type: 'addBasicInfo',
          att: att,
          conversation_id: this.conversation_info.id
        })
        if (r.data.error) {
          throw r.data.error
        }
        this.$root.showSuccess('Đã thêm thông tin vào cuộc trò chuyện')
      } catch (error) {
        this.$root.showError(error.toString())
      }
    },
    addMark: async function(msmh) {
      const remote = this.$store.state.functions.httpsCallable('remote')
      try {
        const r = await remote({
          type: 'addMark',
          msmh: msmh,
          conversation_id: this.conversation_info.id
        })
        if (r.data.error) {
          throw r.data.error
        }
        this.$root.showSuccess('Đã thêm thông tin vào cuộc trò chuyện')
      } catch (error) {
        this.$root.showError(error.toString())
      }
    },
    async removeBasicInfo(att) {
      const remote = this.$store.state.functions.httpsCallable('remote')
      try {
        const r = await remote({
          type: 'removeBasicInfo',
          att: att,
          conversation_id: this.conversation_info.id
        })
        if (r.data.error) {
          throw r.data.error
        }
        this.$root.showSuccess('Đã ẩn thông tin')
      } catch (error) {
        this.$root.showError(error.toString())
      }
    },
    async removeMark(msmh) {
      const remote = this.$store.state.functions.httpsCallable('remote')
      try {
        const r = await remote({
          type: 'removeMark',
          msmh: msmh,
          conversation_id: this.conversation_info.id
        })
        if (r.data.error) {
          throw r.data.error
        }
        this.$root.showSuccess('Đã ẩn thông tin')
      } catch (error) {
        this.$root.showError(error.toString())
      }
    }
  }
}
</script>