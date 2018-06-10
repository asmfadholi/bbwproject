<template lang="jade">
  .container
    //tampilan login
    .main-div#login_div.row
      h3.col-12.text-center LOGIN USER
      .row
        span.offset-1.col-5 Email:
        input.col-5#email_field(type='email' placeholder='Email..' v-model="logine.email")
        br
      .row
        span.offset-1.col-5 Password:
        input#password_field.col-5(type='password' placeholder='Password..' v-model="logine.pass")
        br
      .row
        button.offset-2.col-8(@click='logins') LOGIN

    //tampilan user
    .loggedin-div#user_div
      .row
        h3.col-12.text-center.hello Welcome
        button.offset-2.col-8(@click='logouts') LOGOUT
      hr
      form.row(@submit.prevent="addBook")
        h2.col-12.text-center TRANSACTION
        .row
          span.offset-1.col-5 Phone Number: 
          input.col-5(v-model="phone")
          br
        .row  
          span.offset-1.col-5 Operator: 
          select.col-5(v-model="trans.operator" autofocus)
            option(value="" selected) Choose operator...
            option(v-for="oper in operator") {{oper.nama}}    
          br
        .row  
          span.offset-1.col-5 Voucher:  
          select.col-5(v-model="trans.total" autofocus)
            option(value="" selected) Choose voucher...
            option(v-if="trans.operator" v-for="voc in voucher" v-bind:value="voc.total") {{voc.harga}}    
          br
        .row
          span.offset-1.col-5  Price:
          span.col-5.price Rp.{{trans.total}}
          br
          input.offset-2.col-8.trans(type='submit' value='SAVE TRANSACTION')

</template>

<script>
import axios from 'axios'
import firebase from 'firebase'

let config = {apiKey: "AIzaSyAErZlStIMLGmMxS9KYYvkUsL4EZgSAUY4",
  authDomain: "vuejs-firebase-02-98e6a.firebaseapp.com",
  databaseURL: "https://vuejs-firebase-02-98e6a.firebaseio.com",
  projectId: "vuejs-firebase-02-98e6a",
  storageBucket: "vuejs-firebase-02-98e6a.appspot.com",
  messagingSenderId: "256211555134"}

let app = firebase.initializeApp(config)
let db = app.database()

//mengambil data dari database yang berupa format json 
let operator = db.ref('operator')
let voucher = db.ref('voucher')
let transaksi = db.ref('transaksi')

//autentifikasi user apakah sudah login/belum
firebase.auth().onAuthStateChanged(function(user) {
  if (user) {
    //jika sudah login maka masuk ke dalam tampilan user
    $('#user_div').css('display', 'block')
    $('#login_div').css('display', 'none')
    var user = firebase.auth().currentUser

    if(user != null){

      var email_id = user.email
      $('.hello').html("Welcome " + email_id)

    }
  } else {
    //jika belum login maka masuk ke dalam login
    $('#login_div').css('display', 'block')
    $('#user_div').css('display', 'none')
  }
});

export default {
  name: 'OperatorApp',
  data () {
    return {
      logine: {
        email: '',
        pass: ''
      },
      phone: '',
      harga: '',    
      trans: {
        operator: '',
        total: ''     
      }
    }
  },
  //data yang diambil dari database disimpan disini
  firebase: {
    operator: operator,
    voucher: voucher,
    transaksi: transaksi
  },
  methods: {
    //autentifikasi proses transaksi dimana data harus sudah diisi dan no. handphone harus berupa angka
    addBook: function() {
      if(this.trans.operator == '' || this.trans.total == '' || isNaN(this.phone) || this.phone == ''){
        alert('Transaksi Gagal')  
        
      //jika lolos autentifikasi, data transaksi masuk ke dalam database transaksi  
      } else {
        transaksi.push(this.trans)
        this.phone = ''
        this.trans.operator = ''
        this.trans.total = ''
        alert('Transaksi berhasil')  
      }      
    },
    // trigger login
    logins: function(){      
      var userEmail = this.logine.email
      var userPassword = this.logine.pass
      // console.log(userEmail + ' ' + userPassword);
      firebase.auth().signInWithEmailAndPassword(userEmail, userPassword).catch(function(error) {
      // Handle Errors here.
      var errorCode = error.code;
      var errorMessage = error.message;
      alert(error)
      });
    },
    //trigger logout
    logouts: function(){
      firebase.auth().signOut().then(function() {
        alert('Logout berhasil')
      }).catch(function(error) {
        alert('Logout gagal')
      });
    }    
  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped>
.container {
  display: flex;
  align-items: center;
  justify-content: center;
}

/* tampilan login */
.main-div{
  max-width: 500px;
  border: 2px solid black;
  padding: 10px;
  position: relative;
  &:after {
    content: '';
    position: absolute;
    height: 100%;
    width: 10%;
    background: black;
    top: 0;
    right: -11%;
  }
  .col-12 {
    margin: 20px 0;
  }
  .row{
    margin: 20px 0; 
    input {
      width: 400px;
      border: 2px solid black;
    }
    button {      
      background: white;
      border: 2px solid black;
      box-shadow: 5px 5px;
      &:hover {
        background: rgba(0,0,0,0.2);
      }
    }
  }  
}

// tampilan user
.loggedin-div{
  width: 500px;
  border: 2px solid black;
  padding: 10px;
  position: relative;
  display: none;
  .col-12 {
    margin: 10px 0 20px 0;
  }
  .row{
    margin: 10px 0; 
    input, select {
      width: 400px;
      border: 2px solid black;
      height: 30px;      
    }    
    button {      
      background: white;
      border: 2px solid black;
      box-shadow: 5px 5px;
      &:hover {
        background: rgba(0,0,0,0.2);
      }
    }
    .trans {
      box-shadow: 5px 5px;
      margin-top: 30px;
      background: none;
      &:hover {
        background: rgba(0,0,0,0.2);
      }
    }
    .price {
      margin-left: -15px;
    }
  }  
}
</style>
