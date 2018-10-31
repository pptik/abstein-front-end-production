<template>
  <footer align="center">
    <p class="text-center grey-text">{{copyright}}</p>
  </footer>
</template>

<script>
    export default {
        /*props: {
          blogCategories: {
            type: String,
            required: true
          }
        },*/
        data(){
          return{
            copyright: "© 2018 All Right Reserved"
          }
        }
    }
</script>

<style scoped>

</style>
