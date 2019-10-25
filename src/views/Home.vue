<template>
  <div class="home">
    <img style="margin: 20px 0" src="../assets/logo.png"><br>
    <h1>Scrypta dApp Starter</h1>
    <p>
      This is a VueJS starter for Scrypta dApps.<br><br>
      You will have all <a href="https://wiki.scryptachain.org/developers/scrypta-core/introduction" target="_blank">ScryptaCore</a> methods under <b>app.scrypta</b><br>
      The starter will connect automatically to one of our IdaNodes.<br>
      You will see it in the bottom right corner.<br><br>
      <a href="https://wiki.scryptachain.org/developers/ida-nodes" target="_blank">Read the docs</a> in our wiki website.
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
      async checkIdaNodes(){
        var checknodes = this.scrypta.returnNodes()
        const app = this
        for(var i = 0; i < checknodes.length; i++){
          let check = await this.axios.get(checknodes[i] + '/wallet/getinfo')
          if(check.data.blocks !== undefined){
             app.nodes.push(checknodes[i])
          }
        }
        app.connectToNode()
      },
      connectToNode(){
        var app = this
        if(app.connected == ''){
          app.connected = app.nodes[Math.floor(Math.random()*app.nodes.length)];
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