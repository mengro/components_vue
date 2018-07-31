<template>
  <el-card
    :body-style="{ padding: '10px' }"
  >
    <ul>
      <li class="row" v-for="(item, index) in value" :key="item[prop.key] || index">
        <el-input @change="value => handleChange(value, index)" :value="item[prop.value]"></el-input>
        <i @click="e => handleDelete(index)" class="el-icon-ys-close icon"></i>
      </li>
    </ul>
    <el-button size="mini" class="button" @click="handleAdd" plain>{{buttonText}}</el-button>
  </el-card>
</template>
<script>
export default {
  props: {
    value: {
      required: true,
      type: Array,
    },
    prop: {
      required: false,
      default(){
        return {
          key: 'id',
          value: 'name',
        }
      },
    },
    buttonText: {
      default: '添加值',
    },
  },
  methods: {
    handleAdd(){
      const item = {};
      const { key, value } = this.prop;
      item[key] = '';
      item[value] = '';
      const valueCopy = this.value.slice();
      valueCopy.push(item);
      this.$emit('input', valueCopy);
    },
    handleDelete(index){
      const valueCopy = this.value.slice();
      valueCopy.splice(index, 1);
      this.$emit('input', valueCopy);
    },
    handleChange(value, index){
      const valueCopy = this.value.slice();
      valueCopy[index][this.prop.value] = value;
      this.$emit('input', valueCopy);
    },
  },
}
</script>
<style scoped>
.row{
  position: relative;
  padding: 6px 0;
}
.icon{
  position: absolute;
  top: 50%;
  right: 0;
  transform: translate(-50%, -50%) ;
  cursor: pointer;
}
.button{
  width: 100%;
}
</style>
