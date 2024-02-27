<template>
  <div>
    <form action="http://localhost:9999/sb/all" method="get">
      <input type="submit" value="1111">
    </form>
    <table bgcolor="#7fffd4">
      <tr>{{ sb }}</tr>
      <tr>--------------------------</tr>
      <tr v-for="name in sb">
        <td v-for="name1 in name">{{ name1 }}
        </td>
      </tr>
    </table>




  </div>
</template>

<script>
export default {
  name: "login",
  data() {
    return {
      sb: '',
    }
  },
  methods: {
    find() {
      this.$http.get("http://localhost:9999/sb/all").then((res) => {
        this.sb = res.data.results;
        console.log(this.sb);
      })
    }
  },
  created() {
    this.find();
  }
}
</script>

<style scoped>

</style>
