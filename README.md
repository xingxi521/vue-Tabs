整体思路：
1.参考别人的tab组件都是以这种形式在vue里渲染的
<Tabs :currentIndex="currentIndex" @changeTab="changeTabHandler">
      <TabPane title="Tab1" index="1">
        <div>内容1</div>
        <h3>标题3</h3>
      </TabPane>
      <TabPane title="Tab2" index="2">
        <div>内容2</div>
      </TabPane>
      <TabPane title="Tab3" index="3">
        <div>内容3</div>
      </TabPane>
</Tabs>

下面这个是渲染出来后的结果
<ul>
    <li>tab1</li>
    <li>tab2</li>
    <li>tab3</li>
</ul>
<div>文本1</div>
<div>文本2</div>
<div>文本3</div>
2.那么就可以先定义好一个组件名为：Tabs ，然后就开始先把头部写出来，
那么上面的TabPane很明显就相当于最终渲染出来的li标签了，那么我们就可以再创建一个名字为TabPane的组件（他最终渲染出来是一个li标签）
然后在Tabs组件里通过this.$slots.default(读取插槽) 把TabPane加载进来。

3.创建完成后先实现标题的显示，这个不难，就是组件传值，在使用组件时候加一个title <TabPane title="Tab1"></TabPane> 那么组件内直接在props里接收一下，然后
在TabPane组件里的渲染模板上渲染出来即可：
<li>{this.title}</li>

4.然后开始把选中状态的效果实现出来，在上面看人家一般的Tab组件判断选中就是父级的一个属性跟子级的属性作对比，一致就是选中状态了，
那么整体实现方法就是：在父级组件（Tabs组件）定义一个 currentIndex的属性，然后每个TabPane组件定义一个index，当currentIndex跟TabPane组件的index值一样，那么就是选中状态，
因为选中效果都是在TabPane组件上的（也就是li标签），所以得到这组件里处理了，根据分析的思路实现代码如下：

<li class={this.index==this.$parent.currentIndex?'tab active':'tab'}>{this.title}</li>

5.然后就是实现点击li标签（也就是点击TabPane组件）切换选中状态的效果了，思路就是改变currentIndex，那么就是点击li标签时候，把组件传的index值赋值给Tabs组件的currentIndex
，那么我们就得一级一级往上把index传到调用Tabs的组件里了：
TabPane组件的逻辑：
<li onClick={this.changeIndexHandler.bind(this)} class={this.index==this.$parent.currentIndex?'tab active':'tab'}>{this.title}</li>
methods: {
    //当点击li标签时候触发这个方法，这个方法就会执行父级的方法改变currentIndex
    changeIndexHandler(){
        this.$parent.changeCurIndexHandler(this.index);//这里调用父级的方法，把index传过去，在这里不能用emit，因为用emit的话TabPane组件没地方触发事件
    }
}
Tabs组件的逻辑：
methods: {
    changeCurIndexHandler(index){
        this.$emit("changeTab", index);//vue里是不推荐修改传过来的props的值的，所以这里需要再往上传到给父级，在父级里修改currentIndex
    }
}
props:{
    currentIndex:{//这里是为了可以让tabpane组件（是子组件）读取用作判断
        type:[String,Number],
        default:"1"
    }
}
最终调用Tabs组件的逻辑：
@changeTab是刚才Tabs组件里的自定义事件，用来改变currentIndex的值，因为currentIndex是在这里开始往下传的
<Tabs :currentIndex="currentIndex" @changeTab="changeTabHandler">
      <TabPane title="Tab1" index="1">
        <div>内容1</div>
        <h3>标题3</h3>
      </TabPane>
      <TabPane title="Tab2" index="2">
        <div>内容2</div>
      </TabPane>
      <TabPane title="Tab3" index="3">
        <div>内容3</div>
      </TabPane>
</Tabs>

data(){
    return{
      currentIndex:1
    }
  },
methods:{
    changeTabHandler(index){
      this.currentIndex = index;//这里的index是经过一级一级往上传才传到这里进行改变currentIndex的
    }
}

6.最后要实现的就是对应的选中状态显示对应的内容，通过别人最终渲染的结果来看啊，TabPane组件（li标签）跟内容应该是属于同级的，那么就去创建一个Context组件
用来显示内容的，那么实现选中状态对应内容的思路：分析来看每个内容都是属于每一个TabPane组件的插槽内容，那么就好办了，我们只要在点击li标签(TabPane组件)时触发的方法里把
当前点击的TabPane组件的插槽内容赋值给Tabs组件的一个变量，然后再把变量传给Context组件进行渲染就行了
代码：
Tabs组件：定义一个变量，接收显示内容的数据
render() {
    return(
        <div>
            <ul class="tabs-header">
                {this.$slots.default}
            </ul>
            //显示内容的组件
            <Context contentData={this.contentData}/>
        </div>
    )
}
data(){
    return{
        contentData:{}
    }
}

TabPane组件：
把相应的插槽内容赋值给父级（Tabs组件）的contentData，就能达到对应显示内容的效果了
methods: {
    //当点击li标签时候触发这个方法，这个方法就会执行父级的方法改变currentIndex
    changeIndexHandler(){
        this.$parent.changeCurIndexHandler(this.index);//这里调用父级的方法，把index传过去，在这里不能用emit，因为用emit的话TabPane组件没地方触发事件
        this.$parent.contentData = this.$slots.default;//点击li时触发把对应点击的插槽内容赋值给父级的内容显示组件
    }
}