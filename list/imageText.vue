<template>
  <ul class="goodList-container">
    <li :style="liStyle" v-for="item in data" :key="item[prop.key]">
      <a target="_blank" :href="handleLink(item)">
        <div class="content">
          <div class="img-container">
            <img :src="item[prop.imageUrl]" :alt="item[prop.title]" />
          </div>
          <h3 :title="item[prop.title]">{{item[prop.title]}}</h3>
        </div>
      </a>
    </li>
  </ul>
</template>
<script>
  /**
   * 此组件由标题、主图、link组成，可以继续扩展
   * 调用时需要传入prop，用于声明title、link、imageUrl等数据的对应取值字段或maker函数：
   *    prop: {
   *      data="data"
   *      prop="{
   *        key: 'id',
   *        title: 'text',
   *        link: {
   *          key: 'id',
   *          maker: fragment => {
   *            return `http://xxx?id=${fragment}`;
   *          }
   *        },
   *        imageUrl: 'imageUrl',
   *      }"
   *      column="3"
   *    }
   *    或者
   *    prop: {
   *      data="data"
   *      prop="{
   *        key: 'id',
   *        title: 'title',
   *        link: 'url',
   *        imageUrl: 'imageUrl',
   *      }"
   *      column="3"
   *    }
   */
  export default {
    name: 'ImageTextList',
    props: {
      prop: {
        required: false,
        default: {
          key: 'id',
          title: 'title',
          link: '_blank',
          imageUrl: 'imageUrl',
        }
      },
      data: {
        required: true,
      },
      column: {
        required: false,
        default: 5,
      }
    },
    computed: {
      liStyle(){
        return {
          width: `${100 / this.column}%`,
          paddingTop: `${100 / this.column}%`,
        }
      }
    },
    methods: {
      handleLink(item) {
        const { link } = this.prop;
        if (typeof link === 'string') {
          return item[link];
        }else if (link.maker && link.key) {
          return link.maker(item[link.key]);
        }
      },
    },
  }
</script>
<style scoped>
.goodList-container li{
  display: inline-block;
  position: relative;
  margin-bottom: 30px;
}
.goodList-container li a{
  display: block;
  position: absolute;
  top: 0;
  height: 100%;
  width: 100%;
}
.content{
  padding: 10px;
  height: 100%;
  width: 100%;
  box-sizing: border-box;
  position: relative;
}
.content h3{
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  position: absolute;
  top: 100%;
  left: 0;
  height: 30px;
  line-height: 30px;
  width: 100%;
  padding: 0 10px;
  box-sizing: border-box;
}
.img-container{
  width: 100%;
  height: 100%;
  border: 1px solid #e4e4e4;
  position: relative;
}
.img-container:hover{
  border-color: #ffcfb3;
}
.img-container img{
  width: 100%;
  position: absolute;
  top: 50%;
  left: 0;
  transform: translateY(-50%);
}
</style>

