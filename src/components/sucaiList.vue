<!--
 * @Author: your name
 * @Date: 2020-07-23 14:51:28
 * @LastEditTime: 2020-08-04 09:43:22
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \sucai-modal\src\components\sucaiList.vue
--> 
<template>
  <div>
    
    <Row :gutter='20'>
      <i-col span='5'>
        <Tree :data="foldersMenu" :load-data="getFolders" @on-select-change='chooseFolder'></Tree>
      </i-col>
      <i-col span='18'>
        <div class="chooseTips">
          已选{{chooseNum}} 张
        </div>
        <Row :gutter='20' style="height: 400px">
          <i-col span='6' v-for="(item, index) of materialList" :key="index" class="materialItem">
            <div class="materialItemBox" @click="chooseItemCheck(index)">
              <i class="materialItemThumb" :style="getThumb(item)"></i>
              <img src="../assets/choosed.png" class="choosed_logo" v-if="item.choosed">
              <img src="../assets/noChoosed.png" class="choosed_logo" v-else>
            </div>
            <div class="materialItemInfo">
              <div class="materialItemTitle">{{item.name}}</div>
              <div class="materialItemMore">
                <span>{{item.width}}*{{item.height}}</span>
                <span>{{getSize(item.size)}}</span>
              </div>
            </div>
          </i-col>
        </Row>
      </i-col>
    </Row>
  </div>
</template>

<script>
import { getFolders } from '@/api/data'
import {renderSize} from '@/libs/util.js';
import Bus from '../libs/bus'
  export default {
    name: 'sucaiList',
    props: {
      list: {
        type: Array,
        default: () => {
          return []
        }
      },
      maxNum: {
        type: Number,
        default: 1
      }
    },
    watch: {
      list() {
        this.materialList = this.list
      },
      
    },
    data() {
      return {
        materialList: [],
        foldersMenu: [
          {
            id: 0,
            title: '根',
            loading: false,
            children: [],
            selected: true
          }
        ],
        choosedMaterials: []
      }
    },
    computed: {
      chooseNum(){
        return this.choosedMaterials.length
      }
    },
    methods: {
      chooseItemCheck(index ) {
        let url, compress;
        let item = this.materialList[index]
        let _this = this;
        let choosed = _this.materialList[index].choosed? true: false
        if(!choosed) {
          if(_this.chooseNum >  _this.maxNum-1){
            _this.$Message.error(`已选素材已超过${this.maxNum}张！`)
          } else {
            _this.$set(this.materialList[index], 'choosed', true)
            _this.choosedMaterials.push(item)
            Bus.$emit('doMaterials', _this.choosedMaterials)
          }
        } else {
          _this.$set(this.materialList[index], 'choosed', false)
          let order = _this.choosedMaterials.findIndex((choosedItem) => (choosedItem.id == item.id));
          if(order || order === 0) {
            _this.choosedMaterials.splice(order, 1)
            Bus.$emit('doMaterials', _this.choosedMaterials)
          } else {
            console.error("逻辑错误", order)
          }
        }
      },
      getFolders(item, callback) {
        getFolders('image', item.id) .then(res => {
          let foldersMenu = res.data.data.map(folder => {
            let folderItem = {
                title: folder.file_name,
                loading: false,
                children: [],
                id: folder.id
            }
            return folderItem
          })
          callback(foldersMenu);
        }).catch(err => {
          console.log(err)
        })
      },
      getThumb(item) {
        return 'backgroundImage:url(' + item.thumb + ')'
      },
      getSize: item => renderSize(item),
      chooseFolder(array, attr) {
        this.$emit('chooseFolder', attr.id)
      }
    },
  }
</script>

<style lang="less" scoped>
.chooseTips{
  margin: 2px 10px 10px 0;
}
.materialItem{
  .materialItemBox{
    background-color: #f1f3f5;
    height: 130px;
    position: relative;
    .choosed_logo{
      width: 20px;
      height: 20px;
      position: absolute;
      right: 10px;
      top: 10px;
    }
    .materialItemThumb{
      display: block;
      height: 0;
      padding-bottom: 80%;
      background-size: contain;
      background-position: center center;
      background-repeat: no-repeat;
      border-radius: 3px;
      overflow: hidden;
    }
  }
  .materialItemInfo{
    width: 100%;
    margin-bottom: 15px;
    .materialItemTitle{
      width: 100%;
      overflow: hidden;
      text-overflow: ellipsis;
      word-break: none;
      font-size: 16px;
      margin-top: 5px;
    }
    .materialItemMore{
      color: #999;
      font-size: 14px;
      span{
        margin-right: 10px;
      }
    }
  }
  
  
}
</style>