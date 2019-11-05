<template>
  <!-- 页面的内容 -->
  <div>
    <v-card-title>
      <v-btn color="primary" @click ="addBrand" >新增品牌</v-btn>
      <!--空间隔离组件-->
      <v-spacer />
      <!--搜索框，与search属性关联-->
      <v-text-field label="输入关键字搜索" append-icon="search" v-model.lazy="search" hide-details/>
    </v-card-title>
    <v-data-table
      :headers="headers"
      :items="brands"
      :pagination.sync="pagination"
      :total-items="totalBrands"
      :loading="loading"
      class="elevation-1"
    >
      <template slot="items" slot-scope="props">
        <td class="text-xs-center">{{ props.item.id }}</td>
        <td class="text-xs-center">{{ props.item.name }}</td>
        <td class="text-xs-center"><img v-if="props.item.image" :src="props.item.image" width="130" height="40"/></td>
        <td class="text-xs-center">{{ props.item.letter }}</td>

        <td class="justify-center layout">
          <v-btn color="info" @click="editItem(props.item)">编辑</v-btn>
          <v-btn color="warning" @click="deleteItem(props.item)">删除</v-btn>
        </td>
      </template>
    </v-data-table>
    <!--弹出的对话框-->
    <v-dialog max-width="500" v-model="show" persistent>
      <v-card>
        <!--对话框的标题-->
        <v-toolbar dense dark color="primary">
          <v-toolbar-title>新增品牌</v-toolbar-title>
          <!--隔离线-->
          <v-spacer/>
          <!--关闭窗口的按钮-->
          <v-btn icon @click="closeWindow"><v-icon>close</v-icon></v-btn>
        </v-toolbar>
        <!--对话框的内容，表单-->
        <v-card-text class="px-5">
          <MyBrandForm></MyBrandForm>
        </v-card-text>
      </v-card>
    </v-dialog>
  </div>
</template>
<script>
//  import VBtn from "vuetify/src/components/VBtn/VBtn";
import MyBrandForm from './MyBrandForm.vue'
  /* 页面的js */
  export default {
    components: {MyBrandForm},
    name: "myBrand",
    data (){
      return{
        totalBrands: 0, // 总条数
        brands: [], // 当前页品牌数据
        loading: true, // 是否在加载中
        pagination: {}, // 分页信息
        search: "", // 查询关键字
        headers: [ // 头信息
          {text: 'id', align: 'center', value: 'id'},
          {text: '名称', align: 'center', value: 'name', sortable: false},
          {text: 'LOGO', align: 'center', value: 'image', sortable: false},
          {text: '首字母', align: 'center', value: 'letter'},
          {text: '操作', align: 'center', value: 'letter'},

        ],
        show:false
      }
    },
    watch :{
      pagination:{
        deep: true, // 深度监视
        handler(){
          this.getDataFromServer();
        }
      },
      search:{
        handler(){
          this.getDataFromServer();
        }
      },
    },

    methods: {
      getDataFromServer(){
        this.loading = true;
        this.$http.get("/item/brand/page",{
            params:{

                page: this.pagination.page, // 当前页
                rows: this.pagination.rowsPerPage, // 每页条数
                sortBy: this.pagination.sortBy, // 排序字段
                desc: this.pagination.descending, // 是否降序
                key: this.search // 查询字段

            }}).then(resp =>{
          this.totalBrands = resp.data.total; // 总条数
          this.brands = resp.data.items; // 品牌数据
          this.loading = false; // 加载完成

        }).catch(err=>{
            console.log(err)
        })
        },
      editItem(data){
          console.log(data)
      },
      deleteItem(data){
        console.log(data)
        this.$http.delete("/item/brand/delete?id="+data.id) .then(function(resp){
          // 成功回调函数

        })
          .catch(function(){

          })

      },
      addBrand(){
          this.show=true

      },
      closeWindow(){

        this.show=false
      }


      // 从服务端加载数据的函数
//      getDataFromServer(){
//
//        // 伪造演示数据
//        const brands = [
//          {
//            "id": 2032,
//            "name": "OPPO",
//            "image": "http://img10.360buyimg.com/popshop/jfs/t2119/133/2264148064/4303/b8ab3755/56b2f385N8e4eb051.jpg",
//            "letter": "O",
//            "categories": null
//          },
//          {
//            "id": 2033,
//            "name": "飞利浦（PHILIPS）",
//            "image": "http://img12.360buyimg.com/popshop/jfs/t18361/122/1318410299/1870/36fe70c9/5ac43a4dNa44a0ce0.jpg",
//            "letter": "F",
//            "categories": null
//          },
//          {
//            "id": 2034,
//            "name": "华为（HUAWEI）",
//            "image": "http://img10.360buyimg.com/popshop/jfs/t5662/36/8888655583/7806/1c629c01/598033b4Nd6055897.jpg",
//            "letter": "H",
//            "categories": null
//          },
//          {
//            "id": 2036,
//            "name": "酷派（Coolpad）",
//            "image": "http://img10.360buyimg.com/popshop/jfs/t2521/347/883897149/3732/91c917ec/5670cf96Ncffa2ae6.jpg",
//            "letter": "K",
//            "categories": null
//          },
//          {
//            "id": 2037,
//            "name": "魅族（MEIZU）",
//            "image": "http://img13.360buyimg.com/popshop/jfs/t3511/131/31887105/4943/48f83fa9/57fdf4b8N6e95624d.jpg",
//            "letter": "M",
//            "categories": null
//          }
//        ];
//        // 延迟一段时间，模拟数据请求时间
//        setTimeout(()=>{
//          this.brands = brands; // 赋值给品牌数组
//          this.totalBrands = brands.length; // 赋值数据总条数
//          this.loading = false; // 数据加载完成
//        }, 1000);
//      }
    },
    // 渲染后执行
    mounted(){
      this.getDataFromServer() // 调用数据初始化函数
    }
  }


</script>
<style scoped>
  /* 页面的样式
注意:scoped的作用是style中所有的样式只对本页的html起作用
*/
</style>
