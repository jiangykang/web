<template>
  <v-card>
      <v-flex xs12 sm10>
        <v-tree url="/item/category/list"
                :isEdit="isEdit"
                @handleAdd="handleAdd"
                @handleEdit="handleEdit"
                @handleDelete="handleDelete"
                @handleClick="handleClick"
        />
      </v-flex>
  </v-card>
</template>

<script>
  import {treeData} from "../../mockDB";

  export default {
    name: "category",
    data() {
      return {
        isEdit:true,
        treeData:treeData
      }
    },
    methods: {
      handleAdd(node) {
        console.log("add .... ");
        console.log(node);
        //todo 写入后台数据
        this.$http.post("/item/category/add", node).then(resp => {
          if (resp.data==1) {
            console.log("新增节点后台处理成功");
          }
          else
            console.log("新增节点后台处理失败！");

        });
      },
      handleEdit(id, name) {
        console.log("edit... id: " + id + ", name: " + name)
      },
      handleDelete(id,parentId) {
        //提交后台
        let node = {
          id:id,
          parentId:parentId
        }
        this.$http.post("/item/category/delete",node).then(resp => {
          if (resp.data==1) {
            console.log("删除节点后台处理成功");
          }
          else
            console.log("删除节点后台处理失败！");
        }).catch(()=>{
          console.log("删除出现错误！！！！！！！！！！！！！！！！！！！！！！！！")
        });
        console.log("delete ... " + id)
      },
      handleClick(node) {
        console.log(node);
      }
    }
  };
</script>

<style scoped>

</style>
