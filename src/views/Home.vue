<!--
 * @Author: your name
 * @Date: 2020-07-23 09:48:43
 * @LastEditTime: 2020-08-04 09:25:17
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \sucai-modal\src\views\Home.vue
--> 
<template>
  <div class="home">
    <button @click="openModal('image')">打开图片modal</button>
    <button @click="openModal('video')">打开视频modal</button>
    <button @click="openModal('voice')">打开音频modal</button>
    <h2>已选选项</h2>
    <div class="formItem">
      <div v-for="(item, index) of choosedMaterials" :key="index">{{item}}</div>
    </div>
    <sucai-modal :modalKey = 'modalKey' @handleMaterialModalOk= 'handleModalOk' :fileLimitNum='fileLimitNum' @handleMaterialModalCancle = 'handleModalCancle' :type='type' 
    @chooseVideoOk="chooseVideoOk"
    @chooseCoverOk = "chooseCoverOk"
    ></sucai-modal>
  </div>
</template>

<script>
// @ is an alias to /src
import SucaiModal from '@/components/sucai-modal.vue'
export default {
  name: 'Home',
  components: {
    SucaiModal
  },
  data() {
    return {
      modalKey: false,
      type: 'image',
      choosedMaterials: [],
      fileLimitNum: 1
    }
  },
  methods: {
    openModal(type) {
      if(type == 'image'){
        this.fileLimitNum = 3
      }
      this.type = type
      this.modalKey = true
    },
    handleModalOk(list) {
      this.choosedMaterials = list
      this.modalKey = false
    },
    handleModalCancle() {
      this.modalKey = false
    },
    chooseVideoOk(list) {
      console.log('chooseVideo',list)
    },
    chooseCoverOk(list) {
      console.log('chooseCover', list)
      this.modalKey = false
    }
  },
}
</script>
