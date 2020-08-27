<template>
  <div>
    <v-row>
      <v-col md='3' offset-md='1'>
        <v-card>
          <Register></Register>
        </v-card>
      </v-col>
      <v-col md='3' offset-md='4'>
        <v-card>
          <Login></Login>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
import Login from './Login'
import Register from './Register'

export default {
  components: {
    Login, 
    Register 
  },

}
</script>

<style>
</style>