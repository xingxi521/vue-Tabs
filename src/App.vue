<template>
  <div id="app">
    <img src="./assets/logo.png">
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
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {
  },
  data(){
    return{
      currentIndex:1
    }
  },
  methods:{
    changeTabHandler(index){
      this.currentIndex = index;
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
