<script>
export default {
    name:'TabPane',
    render() {
        return(
            //思路：在tabs组件里设置一个当前选中的状态值，这个状态值是跟tabpane组件设定的index作为对比的，如果他们两相等则选中状态
            <li onClick={this.changeIndexHandler.bind(this)} class={this.index==this.$parent.currentIndex?'tab active':'tab'}>{this.title}</li>
        )
    },
    methods: {
        //当点击li标签时候触发这个方法，这个方法就会执行父级的方法改变currentIndex
        changeIndexHandler(){
            this.$parent.changeCurIndexHandler(this.index);//这里调用父级的方法，把index传过去，在这里不能用emit，因为用emit的话TabPane组件没地方触发事件
            this.$parent.contentData = this.$slots.default;//点击li时触发把对应点击的插槽内容赋值给父级的内容显示组件
        }
    },
    mounted() {
       if(this.index==this.$parent.currentIndex){
           this.$parent.contentData = this.$slots.default;
       }
    },
    props:{
        title:{
            type:String,
            default:""
        },
        index:{
            type:[String,Number],
            default:"1"
        }
    }
}
</script>
<style scoped>
.tab {
    flex:1;
    list-style: none;
    line-height: 40px;
    /* margin-right: 30px; */
    position: relative;
    text-align:center;
}

.tab.active {
  border-bottom: 2px solid blue;
}

</style>