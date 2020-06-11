<template>
  <div class="hello">
    You're in with this address: {{ address }}, this is your full wallet:
    <br><br>
    <pre style="width:550px; text-align:left; border:1px solid #ccc; border-radius:5px; display:inline-block;">{{wallet}}</pre>
    <br><hr><br>
    If you want to write a data into the blockchain use this field and push the button:
    <b-field label="Data to write">
      <b-input v-model="dataToWrite"></b-input>
    </b-field>
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
        wallet: '',
        dataToWrite: ''
      }
    },
    async mounted() {
      const app = this
      app.wallet = await app.scrypta.returnDefaultIdentity()
      let SIDS = app.wallet.split(':')
      app.address = SIDS[0]
      let identity = await app.scrypta.returnIdentity(app.address)
      app.wallet = identity
      app.isLogging = false
    },
    methods: {
      async writeData(key, password){
        const app = this
          let balance = await app.scrypta.get('/balance/' + app.wallet.address)
          if(balance.balance >= 0.001){
            let written = await app.scrypta.write(app.wallet.wallet, password, app.dataToWrite, '', '', '')
            if (written.txs.length >= 1 && written.txs[0] !== null) {
              app.$buefy.toast.open({
                message: "Data written correctly!",
                type: "is-success"
              })
            }else{
              app.$buefy.toast.open({
                message: "There's something wrong with the write operation, please retry.",
                type: "is-danger"
              })
            }
          }else{
            app.$buefy.toast.open({
              message: "Not enough balance, please fund your address.",
              type: "is-danger"
            })
          }
      }
    }
  }
</script>