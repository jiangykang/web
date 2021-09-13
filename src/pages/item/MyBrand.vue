<template>
  <v-card>
    <v-card-title>
      <v-btn color="primary" @click="addBrand">新增品牌</v-btn>
      <!--      空间隔离组件-->
      <v-spacer />
      <v-text-field label="输入关键字搜索" v-model="search" append-icon="search" hide-details>
      </v-text-field>

    </v-card-title>
    <!--分割线-->
    <v-divider />

    <v-data-table
      :headers="headers"
      :items="brands"
      :pagination.sync="pagination"
      :total-items="totalBrands"
      :loading="loading"
      class="elevation-1"
    >
      <template slot="items" slot-scope="props">
        <td>{{ props.item.id }}</td>
        <td class="text-xs-center">{{ props.item.name }}</td>
        <td class="text-xs-center">
          <img v-if="props.item.image" :src="props.item.image" width="130" height="40">
          <span v-else>无</span>
        </td>
        <td class="text-xs-center">{{ props.item.letter }}</td>
        <td class="justify-center layout">
          <v-btn color="info" @click="editBrand(props.item)">编辑</v-btn>
          <v-btn color="warning">删除</v-btn>
        </td>
      </template>
    </v-data-table>

    <!--  弹出的对话框-->
    <v-dialog max-width="500" v-model="show" persistent>
      <v-card>
        <!--      对话框的标题-->
        <v-toolbar dense dark color="primary">
          <v-toolbar-title>{{title}}</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-btn icon @click="closeWindow"><v-icon>close</v-icon></v-btn>
        </v-toolbar>
        <!--      对话框的内容，表单-->
        <v-card-text class="px-5">
          <MyBrandForm ref="newForm" @close="closeWindow" :oldBrand="oldBrand" :isEdit="isEdit"></MyBrandForm>
        </v-card-text>
      </v-card>
    </v-dialog>

  </v-card>


</template>

<script>
  import MyBrandForm from "./MyBrandForm";
    export default {
        name: "MyBrand",
      data() {
        return {
          title: "",
          search: '', // 搜索过滤字段
          totalBrands: 0, // 总条数
          brands: [], // 当前页品牌数据
          loading: true, // 是否在加载中
          pagination: {}, // 分页信息
          headers: [ // 头信息
            {text: 'id', align: 'center', value: 'id'},
            {text: '名称', align: 'center', sortable: false, value: 'name'},
            {text: 'LOGO', align: 'center', sortable: false, value: 'image'},
            {text: '首字母', align: 'center', value: 'letter', sortable: true,},
            {text: '操作', align: 'center', value: 'id', sortable: false}
          ],
          show: false, //控制对话框的显示
          oldBrand: {}, //即将被编辑的品牌数据
          isEdit:false //是否是编辑状态
        }
      },
      components: {
        MyBrandForm
      },
      mounted(){ // 渲染后执行
        // 查询数据
        this.getDataFromServer();
      },
      methods: {
        getDataFromServer() { // 从服务的加载数据的方法。
          // 伪造假数据
          // this.brands = [
          //   {id: 1, name: '三星', image: '123', letter: 'S'},
          //   {id: 1, name: '三星', image: '123', letter: 'S'},
          //   {id: 1, name: '三星', image: '321', letter: 'S'},
          // ];

          //发起请求
          this.$http.get("item/brand/page",{
            params:{
              key: this.search, // 搜索条件
              page: this.pagination.page,// 当前页
              rows: this.pagination.rowsPerPage,// 每页大小
              sortBy: this.pagination.sortBy,// 排序字段
              desc: this.pagination.descending// 是否降序
            }
          })
          .then(resp => {
            // 将得到的数据赋值给本地属性
            this.brands = resp.data.items;
            this.totalBrands = resp.data.total;
            // 完成赋值后，把加载状态赋值为false
            this.loading = false;
          })

        },
        addBrand() {
          this.title="新增品牌" ;
          this.isEdit=false;
          //控制弹窗可见
          this.show=true;
          //清空数据
          this.oldBrand = null;
          // 清空子组件数据
          this.$refs.newForm.$refs.myBrandForm.reset();
        },
        editBrand(oldBrand) {
          this.title="编辑品牌" ;
          this.isEdit=true;
          //根据品牌信息查询商品分类
          this.$http.get("item/category/bid/" + oldBrand.id)
          .then(({data}) => {
            //控制弹窗可见
            this.show=true;
            //获取要编辑的brand
            this.oldBrand=oldBrand;
            //回显商品分类
            this.oldBrand.categories = data;
          })

        },
        closeWindow(){
          //关闭窗口
          this.show=false;
          //重新加载数据
          this.getDataFromServer();
          console.log(this.$refs);
          // 清空子组件数据
          this.$refs.newForm.$refs.myBrandForm.reset();

        }
      },
      watch: {
        pagination: { // 监视pagination属性的变化
          deep: true, // deep为true，会监视pagination的属性及属性中的对象属性变化
          handler() {
            // 变化后的回调函数，这里我们再次调用getDataFromServer即可
            this.getDataFromServer();
          }
        },
        search: { // 监视搜索字段
          handler() {
            this.getDataFromServer();
          }
        }
      }
    }
</script>

<style scoped>

</style>
