<template>
  <div class="hello">
    <a-row>
      <a-col :span="12">col-12</a-col>
      <a-col :span="12">col-12</a-col>
    </a-row>
  </div>
</template>

<script>
export default {
  name: 'hello',
  data () {
    return {
      msg: '我们一起薅羊毛'
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

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
