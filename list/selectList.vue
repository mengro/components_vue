<template>
  <ul class="selectList-container">
    <li class="header">
      <div class="grid selection">
      </div><div :title="column.label" class="ellipsis grid gridValue" v-for="column in columns" :key="column.prop">
        <span>{{column.label}}</span>
      </div>
    </li>
    <el-radio-group v-if="selectType === 'radio'" :style="{ width: '100%' }" v-model="radioGroupValue" @change="handleChange">
      <li class="row" v-for="item in dataSource" :key="item.skuNo">
        <div class="grid selection">
          <el-radio :label="item.skuNo"></el-radio>
        </div><div :title="item[column.prop]" class="ellipsis grid gridValue" v-for="column in columns" :key="column.prop">
          <span>{{item[column.prop]}}</span>
        </div>
      </li>
    </el-radio-group>  
    <el-checkbox-group v-if="selectType === 'checkbox'" :style="{ width: '100%' }" v-model="checkboxValue" @change="handleChange">
      <li class="row" v-for="item in dataSource" :key="item.skuNo">
        <div class="grid selection">
          <el-checkbox :label="item.skuNo"></el-checkbox>
        </div><div :title="item[column.prop]" class="ellipsis grid gridValue" v-for="column in columns" :key="column.prop">
          <span>{{item[column.prop]}}</span>
        </div>
      </li>
    </el-checkbox-group>
  </ul>
</template>
<script>
/**
dataSource: [],
columns: [
  {
    prop: 'name',
    label: '名称',
  }, {
    prop: 'id',
    label: 'ID',
  }, {
    prop: 'count',
    label: '数量',
  },
],
 */
export default {
  name: 'SelectList',
  data() {
    return {
      radioGroupValue: '',
      checkboxValue: [],
    }
  },
  props: {
    dataSource: {
      default: [],
    },
    columns: {
      required: true,
      type: Array,
    },
    selectType: {
      default: '', // checkbox/radio
    },
    value: {
      default: '',
    },
  },
  methods: {
    handleChange(value) {
      if (this.selectType === 'checkbox') {
        this.$emit('input', this.checkboxValue);
      }else if (this.selectType === 'radio') {
        this.$emit('input', this.radioGroupValue);
      }
    },
  },
  watch: {
    selectType(type) {
      if (!type) {
        return;
      }
      if (type === 'checkbox') {
        this.checkboxValue = JSON.parse(JSON.stringify(this.value));;
      }else if (type === 'radio') {
        this.radioGroupValue = this.value;
      };
    },
  },
}
</script>
<style scoped>
.selectList-container {
  padding: 0 30px;
  height: 500px;
  overflow-y: auto;
  overflow-x: hidden;
}
.header {
  height: 40px;
  line-height: 40px;
  font-size: 14px;
  font-weight: 600;
  border-bottom: 1px solid #ddd;
}
.row {
  height: 30px;
  font-size: 12px;
  line-height: 30px;
  border-bottom: 1px solid #ddd;
}
.grid {
  display: inline-block;
}
.selection {
  width: 10%;
  vertical-align: top;
  text-align: center;
}
.gridValue {
  width: 30%;
}
</style>
<style>
.selectList-container .el-radio .el-radio__label {
  display: none!important;
}
.selectList-container .el-checkbox__label {
  display: none!important;
}
</style>
