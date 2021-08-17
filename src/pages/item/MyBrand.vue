<template>
  <v-card>
    <v-card-title>
      <v-btn color="primary">新增</v-btn>
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
          <v-btn color="info">编辑</v-btn>
          <v-btn color="warning">删除</v-btn>
        </td>
      </template>
    </v-data-table>
  </v-card>
</template>

<script>
    export default {
        name: "MyBrand",
      data() {
        return {
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
          ]
        }
      },
      mounted(){ // 渲染后执行
        // 查询数据
        this.getDataFromServer();
      },
      methods: {
        getDataFromServer() { // 从服务的加载数据的方法。
          // 伪造假数据
          this.brands = [
            {id: 1, name: '三星', image: '123', letter: 'S'},
            {id: 1, name: '三星', image: '123', letter: 'S'},
            {id: 1, name: '三星', image: '321', letter: 'S'},
          ];
        }
      }
    }
</script>

<style scoped>

</style>
