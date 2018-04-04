<template>
    <div class="list-container">
        <h3 class="slotStyle">
            <slot name="title">
                <input 
                    :checked="dataList.length > 0 && checkedList.length === dataList.length" 
                    type="checkbox" 
                    name="checkall"
                    @change="e => checkAllChangeHandle(e)"
                > 全选
            </slot>
        </h3>
        <ul :style="{height: 'calc(100% - 40px)'}">
            <li 
                @click="itemClickHandle(dataItem.value,dataItem)" 
                :class="`ellipsis clearfix ${activeId === dataItem.value ? 'active' : ''}`" 
                v-for="(dataItem, index) in dataList" 
                :title="dataItem.name" 
                :key="index"
            >
                <input
                    :checked="checkedList.findIndex(item => item.value === dataItem.value) > -1" 
                    v-if="checkable"
                    type="checkbox"
                    @change="e => changeHandle(e,dataItem)"
                >
                <span>{{dataItem.name}}</span>
                <span class="selectedIcon icon" v-if="_selectedIds.indexOf(dataItem.value) > -1">√</span>
                <span class="deleteBtn icon" v-if="editable" @click="e => $emit('delete', dataItem.value)">x</span>
            </li>
        </ul>
    </div>
</template>
<script>
export default{
    data(){
        return {
            checkedList: [],
        }
    },
    props: ['dataList', 'itemClickHandle', 'activeId', 'checkable', 'values', 'editable', 'selectedIds'],
    computed: {
        _selectedIds(){
            return this.selectedIds || []
        }
    },
    watch: {
        values(newValues){
            this.checkedList = newValues;
        }
    },
    methods: {
        changeHandle(e, value){
            const { checked } = e.target;
            if (checked) {
                this.checkedList.push(value);
            }else{
                this.checkedList.splice(this.checkedList.indexOf(value),1)
            }
            this.$emit('change', this.checkedList);
        },
        checkAllChangeHandle(e, value){
            const { checked } = e.target;
            if (checked) {
                this.checkedList = this.dataList.map(item => item);
            }else{
                this.checkedList = [];
            }
            this.$emit('change', this.checkedList);
        }
    },
    unmounted(){
        this.checkedList = [];
    }
}
</script>
<style scoped lang="less">
@borderColor: #d4dae2;
@activeColor: rgb(226, 238, 246);
.list-container{
    height: 100%;
    text-indent: 10px;
    border: 1px solid @borderColor;
    ul{
        overflow: auto;
        li{
            cursor: pointer;
            height: 40px;
            line-height: 40px;
            border-bottom: 1px solid @borderColor;
            &:hover{
                background: @activeColor;
            }
            &.active,{
                background: @activeColor;
            }
            .icon{
                margin-right: 10px;
                font-size: 14px;
                font-weight: 600;
            }
            .selectedIcon{
                float: right;
                color: #3598DB;
            }
            .deleteBtn{
                float: right;
                &:hover{
                    color: #000;
                }
            }
        }
    }
    .slotStyle{
        height: 40px;
        line-height: 40px;
        background: #eee;
        border-bottom: 1px solid @borderColor;
        box-sizing: border-box;
    }
}
</style>

