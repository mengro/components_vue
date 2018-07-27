<template>
  <div class="table-tree">
    <ul class="tree-header">
      <li :style="{width: columnWidth}" v-for="item in columns" :key="item.prop">{{item.label}}</li>
    </ul>
    <el-tree
      :data="c_dataSource"
      :lazy="sync"
      node-key="id"
      :expand-on-click-node="true"
      :render-content="renderContent"
      :load="loadContent"
      :indent="8"
      class="table-tree-body"
      :props="{
        children: c_childrenKey
      }"
    >
    </el-tree>
  </div>
</template>
<script>
  export default {
    name: 'TableTree',
    props: {
      dataSource: {
        type: Array,
      },
      columns: {
        type: Array,
      },
      nameSpace: {
        type: String,
      },
      sync: {
        required: false,
        default: null,
      },
      isLeaf: {
        required: true,
        type: Function,
      },
      // childrenKey可以指定子节点的数据存储的字段名
      childrenKey: {
        required: false,
        default: 'children',
      },
    },
    computed: {
      columnWidth(){
        return `${100 / this.columns.length}%`;
      },
      c_dataSource(){
        if (this.childrenKey.indexOf('.') > 0) {
          const keys = this.childrenKey.split('.');
          if (keys.length > 2) {
            console.error('数据嵌套太深，请修改接口!');
          }
          const [ key1, key2 ] = keys;
          const f = data => {
            if (data[key1] && data[key1][key2]) {
              if (data[key2]) {
                console.error('子节点存储的字段不能与父节点的字段重复');
              }
              data[key2] = data[key1][key2];
              data[key2].forEach(item => f(item));
              return data;
            }
          }
          const dataSourceFormat = this.dataSource.map(item => {
            return f(item);
          })
          return dataSourceFormat;
        }
        return this.dataSource
      },
      c_childrenKey(){
        if (this.childrenKey.indexOf('.') > 0) {
          return this.childrenKey.split('.')[1];
        }
        return this.childrenKey;
      },
    },
    methods:{
      /**
       * 异步加载时使用的回调，真正的操作在对应vuex的actions中
       */
      loadContent(node, resolve){
        if (this.sync) {
          this.$store.dispatch(`${this.nameSpace}/table_loadContent`, { node, resolve });
        }
      },
      /**
       * 使用jsx渲染树节点
       */
      renderContent(h, { node, data, store }) {
        const { columns } = this;
        // 判断节点是否是叶子节点
        node.isLeaf = this.isLeaf(data)
        return (
          <div isLeaf={this.isLeaf(data)} class="custom-tree-node">
            {
              columns && columns.map(item => {
                // 如果当前列配置了render函数，使用render函数渲染单元格
                if (item.render) {
                  return <span style={{width: this.columnWidth}}>{item.render(data[item.prop])}</span>
                }
                // operator类型的单元格
                if (item.type === 'operator') {
                  const operatorElement = (
                    <span style={{width: this.columnWidth}}>
                      {
                        item.buttons && item.buttons.map(button => {
                          // 判断按钮是否隐藏，未配置isHidden默认显示
                          if (button.isHidden && button.isHidden(data)) {
                            return
                          }
                          const buttonElement = (
                            <el-button
                              size="mini"
                              type="text"
                              on-click={ (e) => {
                                e.stopPropagation();
                                // 按钮回调在对应的vuex文件里
                                this.$store.dispatch(`${this.nameSpace}/${button.handle}`, { node, data });
                              }}
                            >
                              {button.label}
                            </el-button>
                          )
                          return buttonElement;
                        })
                      }
                    </span>
                  )
                  return operatorElement;
                }
                // 普通渲染
                return <span style={{width: this.columnWidth}}>{data[item.prop]}</span>
              })
            }
          </div>
        );
      }
    }
  }
</script>
<style>
.table-tree .table-tree-body{
  border-left: 1px solid #ebeef5;
  border-right: 1px solid #ebeef5;
}
.table-tree .table-tree-body .el-tree-node__content{
  height: auto;
}
.table-tree .el-tree-node{
  border-bottom: 1px solid #ebeef5;
}
.table-tree .table-tree-body .custom-tree-node{
  align-items: center;
  font-size: 14px;
  width: 100%;
  display: flex;
}
.table-tree-body .custom-tree-node > span{
  box-sizing: border-box;
  display: inline-block;
  font-size: 14px;
  color: #909399;
  box-sizing: border-box;
  padding: 5px 0;
  text-indent: 10px;
  flex: 1;
}
.table-tree-body .custom-tree-node span:first-child{
  text-indent: 24px;
}
.table-tree-body .el-tree-node__expand-icon{
  margin-right: -24px;
}
</style>
<style scoped>
.tree-header{
  font-size: 0;
  height: 44px;
  line-height: 44px;
  width: 100%;
  border: 1px solid #ebeef5;
  margin-top: 20px;
  box-sizing: border-box;
}
.tree-header li{
  box-sizing: border-box;
  display: inline-block;
  font-size: 14px;
  color: #909399;
  padding: 0 10px;
}
.tree-header li+li{
  border-left: 1px solid #ebeef5;
}
</style>
