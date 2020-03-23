<template>
  <div class="hello">
    You're in with this address: {{ address }}, this is your full wallet:
    <br><br>
    <pre style="width:550px; text-align:left; border:1px solid #ccc; border-radius:5px; display:inline-block;">{{wallet}}</pre>
  </div>
</template>


<script>
  let ScryptaCore = require('@scrypta/core')
  
  export default {
    name: 'HelloWorld',
    data(){ 
      return {
        scrypta: new ScryptaCore(true),
        address: '',
        wallet: ''
      }
    },
    async mounted() {
      const app = this
      app.wallet = await app.scrypta.returnDefaultIdentity()
      let SIDS = app.wallet.split(':')
      app.address = SIDS[0]
      let identity = await app.scrypta.returnIdentity(app.address)
      if(identity.rsa === undefined){
        app.needsRSA = true
      }
      app.wallet = identity
      app.isLogging = false
    }
  }
</script>