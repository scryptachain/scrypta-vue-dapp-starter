<template>
  <div class="home">
    <img style="margin: 20px 0" src="../assets/logo.png"><br>
    <h1>Scrypta dApp Starter</h1>
    <p>
      This is a VueJS starter for Scrypta. You will have all methods under app.scrypta.<br>
      The starter will connect automatically to one of our random nodes.<br>
      You will see it in the bottom right corner.
    </p>
    <div class="node-badge" v-if="connected">{{ connected }}</div>
  </div>
</template>

<style>
  #app{
    text-align: center;
    font-family: 'karmillaregular';
  }
</style>

<script>

export default {
  name: 'home',
  mounted : function(){
    this.checkIdaNodes()
  },
  methods: {
      checkIdaNodes(){
        var checknodes = this.scrypta.returnNodes()
        const app = this
        for(var i = 0; i < checknodes.length; i++){
          this.axios.get('https://' + checknodes[i] + '/check')
          .then(function (response) {
             app.nodes.push(response.data.name)
             if(i == checknodes.length){
               app.connectToNode()
             }
          });
        }
      },
      connectToNode(){
        var app = this
        if(app.connected == ''){
          app.connected = app.nodes[Math.floor(Math.random()*app.nodes.length)];
          app.checkBalance()
        }
      }
  },
  data () {
    return {
      scrypta: window.ScryptaCore,
      axios: window.axios,
      nodes: [],
      connected: ''
    }
  }
}
</script>

<style>
  .node-badge{
    position:fixed; bottom:-3px; font-size:10px; padding:8px; right:10px; z-index:9999;
  }
</style>