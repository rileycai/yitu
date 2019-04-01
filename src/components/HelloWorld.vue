<template>
  <div class="hello">
    <img src="./../assets/icon.png">
    <h1>{{ msg }}</h1>
    <h2>作者：{{ author }}</h2>
    <h2>电话：{{ tel }}</h2>
    <h2>邮箱：{{ email }}</h2>
    <h3>
      <router-link to="/one">题目一</router-link>
      <router-link to="/two">题目二</router-link>
    </h3>
   
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: '依图科技前端笔试题',
      author:'蔡真真',
      tel:'17600691591',
      email:'caizhenzhen94@outlook.com'
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello{
  margin-top: 60px;
}
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
