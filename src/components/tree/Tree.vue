<template>
  <v-list class="pt-0 pb-0" dense>
    <TreeItem
      class="item" :model="model" v-for="(model, index) in db" :key="index"
      :url="url"
      :isEdit="isEdit"
      :nodes="nodes"
      @handleAdd="handleAdd"
      @handleEdit="handleEdit"
      @handleDelete="handleDelete"
      @handleClick="handleClick"
    />
  </v-list>
</template>

<script>
  import TreeItem from './TreeItem';

  export default {
    name: "vTree",
    props: {
      url: String,
      isEdit: {
        type: Boolean,
        default: false
      },
      treeData:{
        type:Array
      }
    },
    data() {
      return {
        db: [],
        nodes:{
          opened:null,
          selected:{isSelected:false}
        }
      }
    },
    components: {
      TreeItem
    },
    created() {
      if(this.treeData && this.treeData.length > 0){
        this.db = this.treeData;
        return;
      }
      this.getData();
    },
    methods: {
      getData() {
        this.$http.get(this.url, {params: {pid: 0}}).then(resp => {
          this.db = resp.data;
          this.db.forEach(n => n['path'] = [n.name])
        })
      },
      handleAdd(node) {
        this.$emit("handleAdd", this.copyNodeInfo(node));
      },
      handleEdit(id, name) {
        this.$emit("handleEdit", id, name)
      },
      handleDelete(id,parentId) {
        this.countSubNodeById(parentId,this.db);
        this.deleteById(id, this.db);
        console.log("this.db")
        console.log(this.db)
        this.$emit("handleDelete", id,parentId);
      },
      handleClick(node){
        this.$emit("handleClick", this.copyNodeInfo(node))
      },
      // 根据id删除
      deleteById(id, arr) {
        let src = arr || this.db;
        for (let i in src) {
          console.log("deleteById开始执行了")
          let d = src[i];
          if (d.id === id) {
            src.splice(i, 1)
            return;
          }
          if (d.children && d.children.length > 0) {
            this.deleteById(id, d.children)
          }
        }
      },
      // 查找父节点存在几个子节点
      countSubNodeById(id, arr) {
        let src = arr || this.db;
        let count = 0;
        for (let i in src) {
          console.log("countSubNodeById开始执行了")
          let d = src[i];
          if (d.parentId === id ) {
            count++;
            console.log("count是多少呢？");
            console.log(count);
          }
          if (d.children && d.children.length > 0) {
            this.countSubNodeById(id, d.children)
          }
        }
        console.log(count);
        if (count <= 1) {
          for (let i in src) {
            let d = src[i];
            if (d.id === id) {
              d.isParent=false;
              src.splice(i, 1,d);
              return;
            }
          }
        }
      },
      copyNodeInfo(node){
        const o = {};
        for(let i in node){
          o[i] = node[i];
        }
        return o;
      }
    },
    watch: {}
  }
</script>

<style scoped>
  .item {
    cursor: pointer;
  }
</style>
