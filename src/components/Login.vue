<template>
  <v-app>
    <v-main>
      <v-container class="fill-height" fluid>
        <v-row no-gutters justify="center">
          <v-col cols="12" sm="7" md="7" class="hidden-sm-and-down">
            <v-card flat color="#fffcf5">
              <v-flex class="text-center">
                <img src="../assets/loginPic.png" class="mt-5 pr-10" contain height="550">
              </v-flex>
            </v-card>
          </v-col>
          <v-col cols="12" sm="5" md="5">
            <v-card flat color="#fffcf5" max-width="500" class="mt-5 textTable">
              <v-flex class="px-10 pt-16 pb-2 text-center">
                <img src="../assets/logoGesit.png" justify="center" contain height="90">
                <p class="text-center greetings orangeText">Welcome to GESIT, Please Sign In!</p>
              </v-flex>
            
              <v-card-text>
                <v-text-field 
                  @keypress.enter="login()"
                  label="Enter your NPP" 
                  v-model="npp"
                  placeholder="xxxxx"
                  prepend-inner-icon="mdi-account"
                  rounded
                  filled
                  type = "number"
                  single-line
                  prefix="P0 - "
                  color="#26A69A"
                  required>
                </v-text-field>

                <v-text-field 
                  @keypress.enter="login()"
                  label="Enter your password" 
                  v-model="password"
                  prepend-inner-icon="mdi-lock" 
                  :append-icon="show1 ? 'mdi-eye' : 'mdi-eye-off'"
                  @click:append="show1 = !show1"
                  :type="show1 ? 'text' : 'password'"
                  placeholder="xxxx@bni.co.id or another characters"
                  rounded
                  filled
                  single-line
                  color="#26A69A"
                  hide-details
                  required>
                </v-text-field>

                <!--<v-card-actions class="text--secondary pt-0">
                  <v-spacer></v-spacer>
                  <p class="pt-1" @click="to"><a class="linkText">Forgot your password?</a></p>
                </v-card-actions>-->

                <v-btn 
                  class="mt-7"
                  color="#094f59" 
                  x-large 
                  block
                  rounded
                  dark
                  @click="login()">
                  Sign In
                </v-btn>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
      <v-snackbar v-model="alert" :color="color" timeout="3000" bottom>
        {{message}}
      </v-snackbar>
    </v-main>
  </v-app>
</template>

<script>
// import axios from 'axios'
export default {
  name: 'login',
  created () {
    document.title = "Login";
  },
  data (){
    return {
      show1: false,
      error:null,
      alert: false,
      color: '',
      message:null,
      password: '',
      npp: '',
    };
  },
  methods: {
    login() {
      if (this.npp != '' && this.password != '') { 
        var url = null;
        if (this.npp == '2021')
          url = this.$api+'/Authentication?npp=P0' + this.npp + '&password=' + this.password;
        else
          url = this.$api+'/Login/LoginDummy?npp=P0' + this.npp + '&password=' + this.password;
          this.$http.get(url,{
          headers:{
            // 'progo-key':'progo123',
            'Content-Type': 'application/json',
            'Authorization' : 'Bearer ' + localStorage.getItem('token')
          }
          }).then(response => { 
            // console.log(response)
              if(this.npp == '2021'){
                localStorage.setItem('npp', response.data.data.npp);
                localStorage.setItem('name', response.data.data.name);
                localStorage.setItem('role', response.data.data.role);
                localStorage.setItem('token', response.data.data.token);
                this.$router.push('/homeAdmin');
              }else{
                var jabatan = response.data.data.role;
                localStorage.setItem('npp', response.data.data.npp);
                localStorage.setItem('name', response.data.data.name);
                localStorage.setItem('role', response.data.data.role);
                localStorage.setItem('token', response.data.data.token);
                if(jabatan == 'AMGR')
                  this.$router.push('/homeAdmin');
                else if(jabatan == 'MGR' || jabatan == 'AVP')
                   this.$router.push('/monitoringMGR');
                else if(jabatan == 'OS')
                    this.$router.push('/homeOS');
              }
          }).catch(error => {
              this.error = error;
              this.message="Invalid NPP or Password";
              this.color="red"
              this.alert=true;
              localStorage.removeItem('token')
          })
      }
      else {
        this.alert = true;
        this.color = "red"
        this.message = "Please fill your NPP and Password correctly!";
      }
    },
    to() {
      this.$router.push('/forgotPass');
    },
  }
}
</script>

<style lang="css" scoped>

.greetings{
  color:#F15A23;
  font-family: 'Questrial', sans-serif;
}

.linkText{
  color:#005E6A;
}

.greenText{
  color:#005E6A;
  font-family: 'Secular One', sans-serif;
}
</style>