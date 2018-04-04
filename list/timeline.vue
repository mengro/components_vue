<template>
    <ul>
        <li class="clearfix" v-for="(item, index) in data" :key="index">
            <div class="point"></div>
            <div class="text">
                <span v-for="(field, index) in fieldList" :key="index">
                    <span v-if="field.formatter">{{field.formatter(item[field.name])}}</span>
                    <span else>{{item[field]}}</span>
                </span>
            </div>
        </li>
    </ul>
</template>
<script>
export default{
    props: ['data', 'fieldList']
}
</script>
<style lang="less" scoped>
@import '../../static/css/static.less';
@lightColor: #3598DB;
ul{
    padding: 30px;
    border: 1px solid @borderColor;
    overflow: auto;
    max-height: 300px;
    li{
        position: relative;
        overflow: hidden;
        .point{
            width: 0;
            height: 0;
        }
        .point::before{
            content: '';
            position: absolute;
            top: 20px;
            width: 10px;
            height: 10px;
            display: block;
            margin-left: 6px; 
            background: @lightColor;
            border-radius: 5px;
        }
        .point::after{
            content: '';
            border-left: 2px solid @lightColor;
            padding-bottom: 2000px;
            margin-bottom: -2000px;
            padding-top: 2000px;
            margin-top: -2000px;
            margin-left: 10px;
        }
        .text{
            width: 90%;
            float: right;
            text-align: left;
            overflow: hidden;
            padding: 16px 0;
        }
    }
}
</style>

