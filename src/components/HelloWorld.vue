<template>
  <div class="hello">
    You're in with this address: {{ address }}, this is your full wallet:
    <br><br>
    <pre style="width:550px; text-align:left; border:1px solid #ccc; border-radius:5px; display:inline-block;">{{wallet}}</pre>
    <br><hr><br>
    <b-field label="Write data inside the blockchain">
      <b-input v-model="dataToWrite"></b-input>
    </b-field>
    <b-button v-on:click="writeData" type="is-primary" v-if="!isWriting" expanded size="is-large">WRITE DATA!</b-button>
    <div v-if="isWriting" style="text-align:center; padding:20px">Writing data please wait...</div>
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
        isWriting: false,
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
    },
    methods: {
      async writeData(){
        const app = this
        if(app.dataToWrite !== ''){
           app.$buefy.dialog.prompt({
            message: `Insert wallet password`,
            inputAttrs: {
              type: "password"
            },
            trapFocus: true,
            onConfirm: async password => {
              let key = await app.scrypta.readKey(password, app.wallet.wallet)
              if (key !== false) {
                app.isWriting = true
                let balance = await app.scrypta.get('/balance/' + app.wallet.address)
                if(balance.balance >= 0.001){
                  let written = await app.scrypta.write(app.wallet.wallet, password, app.dataToWrite, '', '', '')
                  if (written.txs.length >= 1 && written.txs[0] !== null) {
                    app.$buefy.toast.open({
                      message: "Data written correctly!",
                      type: "is-success"
                    })
                    app.dataToWrite = ''
                    app.isWriting = false
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
                  app.isWriting = false
                }
              }else{
                app.$buefy.toast.open({
                  message: "Wrong password!",
                  type: "is-danger"
                })
              }
            }
          })
        }else{
          app.$buefy.toast.open({
            message: "Please write something first.",
            type: "is-danger"
          })
        }
      }
    }
  }
</script>