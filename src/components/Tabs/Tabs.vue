<script>
import Context from './Context'
export default {
    name:'Tabs',
    render() {
        return(
            <div>
                <ul class="tabs-header">
                    {this.$slots.default}
                </ul>
                <Context contentData={this.contentData}/>
            </div>
        )
    },
    data(){
        return{
            contentData:{}
        }
    },
    props:{
        currentIndex:{//这里是为了可以让tabpane组件（是子组件）读取用作判断
            type:[String,Number],
            default:"1"
        }
    },
    methods: {
        changeCurIndexHandler(index){
            this.$emit("changeTab", index);//vue里是不推荐修改传过来的props的值的，所以这里需要再往上传到给父级，在父级里修改currentIndex
        }
    },
}
</script>
<style scoped>
    .tabs-header {
        display: flex;
        list-style: none;
        margin: 0;
        padding: 0;
        border-bottom: 2px solid #ededed;
        cursor: pointer;
    }
</style>