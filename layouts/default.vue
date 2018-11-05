<template>
  <v-app>
    <v-content class="content">
      <v-container>
        <nuxt />
      </v-container>
    </v-content>
    <v-footer
      :fixed="fixed"
      app
    >
      <span class="footer">&copy; 2018</span>
    </v-footer>
  </v-app>
</template>

<script>
  export default {
    data() {
      return {
        fixed: false,
      }
    }
  }
</script>

<style>
  .content {
    background-image: 
      linear-gradient(-46deg, #5971AF 0%, #6399B1 100%), 
      url('https://images.pexels.com/photos/1117403/pexels-photo-1117403.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=650&w=940');
    height: 100%; 
    background-position: center;
    background-repeat: no-repeat;
    background-size: cover;
  }

  .footer {
    padding-left: 15px;
  }
</style>
