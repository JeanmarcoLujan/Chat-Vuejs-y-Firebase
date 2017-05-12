<template>
  <div id="app">
    <div class="navbar navbar-default navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <h4>Vue App</h4>
        </div>
        <div class="navbar-collapse collapse" id="navbar-main">
          <ul class="nav navbar-nav navbar-right">
                <li v-if="!logueado">
                    <a @click="conectar"  href="#">Conectar</a>
                </li>

                <li  class="dropdown" v-if="logueado">
                    <a href="#" class="dropdown-toggle" data-toggle="dropdown">
                        <span class="glyphicon glyphicon-user"></span> 
                        <strong >{{user.displayName}}</strong>
                        <span class="glyphicon glyphicon-chevron-down"></span>
                    </a>

                    <ul class="dropdown-menu">
                        <li>
                            <div class="navbar-login">
                                <div class="row" align="center">
                                    <div class="col-lg-12">
                                        <img  class="avatar" :src="user.photoURL">
                                    </div>
                                </div>
                            </div>
                        </li>
                        <li class="divider"></li>
                        <li>
                            <div class="navbar-login navbar-login-session">
                                <div class="row">
                                    <div class="col-lg-12" align="center">
                                        <p>
                                            <a @click="desconectar()"  href="#" class="btn btn-danger btn-block">
                                                Cerrar Sesion
                                            </a>
                                        </p>
                                    </div>
                                </div>
                            </div>
                        </li>
                    </ul>
                </li>
            </ul>
        </div>
      </div>
    </div>
    <div v-if="logueado" class="container">
      <h1>{{mensaje}}</h1>
      <div class="panel-body chat-box" >
        <ul class="chat list-unstyled">
          <li  class="clearfix" v-for="msm in messages">
            <div class="chat-body" align="left">
              <div class="col-md-2">
                <span >
                  <img  class="img-circle avatar" :src="msm.photoMessage">
                </span>
              </div>
              <div class="col-md-10">
                <br>
                <strong>{{msm.userMessage}}</strong><br>
                <p>{{ msm.message}}</p>
              </div>
            </div>
          </li>
        </ul>
      </div>
      <div class="panel-footer" v-if="logueado">
        <form v-on:submit.prevent="addMessage">
          <div class="input-group" >
            <input  type="hidden" v-model="newMessage.userMessage">
            <input  type="hidden" v-model="newMessage.photoMessage">
            <input class="form-control input-md" type="text" v-model="newMessage.message" placeholder="Escriba mensaje">
            <span class="input-group-btn">
              <input type="submit" class="btn btn-success btn-md" value="Enviar">
            </span>
          </div>
        </form>
        <div>
          {{user.displayName}}
          
        </div>
      </div>

    </div>
  </div>
</template>

<script>

import firebase from 'firebase'


let config ={
    apiKey: "AIzaSyAsk4NHdpVpHCVDyuumd7IF7Kv5TRXI3h4",
    authDomain: "vuefirebaseapp.firebaseapp.com",
    databaseURL: "https://vuefirebaseapp.firebaseio.com",
    projectId: "vuefirebaseapp",
    storageBucket: "vuefirebaseapp.appspot.com",
    messagingSenderId: "1015011733095"
}

let app = firebase.initializeApp(config);

let db = app.database();
let userRef = db.ref('users');
let userRefchat = db.ref('chat');

let provider = new firebase.auth.GoogleAuthProvider();
let auth = app.auth();

export default {
  name: 'app',
  firebase:{
    messages: userRefchat
  },
  data(){
    return{
      mensaje:'Chat',
      newMessage:{
        message:'',
        userMessage:'',
        photoMessage:''
      },
      user:null,
      logueado:false
    }
  },
  mounted(){
    auth.onAuthStateChanged(this.processUser);
    this.processUser(auth.currentUser);


  },
  methods:{
    conectar:function(){
      auth.signInWithPopup(provider).catch(function(error) {
        console.error("Hay un error al conectar");
      });

    }, 
    desconectar: function(){
      auth.signOut().catch(function(error) {
        console.error("Hay un error al desconectar");
      });

    },
    processUser(authed){
      console.log(authed);
      if (authed === null) {
        this.user = null;
        this.logueado=false
        return;
      }
      this.user = authed
      this.newMessage.userMessage = authed.displayName
      this.newMessage.photoMessage = authed.photoURL
      this.logueado=true
    },
    addMessage:function(){
      
      userRefchat.push(this.newMessage);
      this.newMessage.message='';
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

.avatar{
  width: 100px;
  height: 100px;
}

.chat{padding: 0;}
.chat li {margin-bottom: 10px;padding-bottom: 15px;}
.chat li.left .chat-body{margin-left: 70px;}
.chat li.right .chat-body{margin-right: 70px; text-align: right;}
.chat-box {overflow-y: scroll; height: 400px;}
</style>
