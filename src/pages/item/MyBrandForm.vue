<template>
  <v-form v-model="valid">
    <v-text-field v-model="brand.name" label="请输入品牌名称" required></v-text-field>
    <v-text-field v-model="brand.letter" label="请输入品牌首字母" required></v-text-field>
    <v-cascader
      url="item/category/list"
      multiple
      required
      v-model="brand.categories"
      label="请选择商品分类"
    ></v-cascader>
  </v-form>
</template>

<script>
    export default {
        name: "MyBrandForm",
        data() {
          return {
            valid:false, //表单校验结果标记
            brand:{
              name:'',//商品名称
              letter:'',//商品首字母
              image:'',//商品logo
              categories:[],//品牌所属的商品分类数组
            }
          }
        }
    }
</script>

<style scoped>

</style>
