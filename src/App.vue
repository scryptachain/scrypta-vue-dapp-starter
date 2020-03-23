<template>
  <div id="app">
    <router-view v-if="!isLogging && !needsRSA && wallet" />
    <div v-if="needsRSA && wallet">Need to create RSA keys!</div>
    <div v-if="!wallet">
      <section class="hero">
        <div class="hero-body">
          <div class="container">
            <h1 class="title">
              Scrypta dApp Starter
            </h1>
            <h2 class="subtitle">
              Use this starter to build your own Scrypta dApp!.<br><br>
              <b-button size="is-medium" v-on:click="showCreate">Create an Identity Now!</b-button>
            </h2>
          </div>
        </div>
      </section>
    </div>
    <b-loading :is-full-page="true" :active.sync="isLogging" :can-cancel="false"></b-loading>
    <b-modal :active.sync="showCreateModal"
              has-modal-card
              trap-focus
              aria-role="dialog"
              aria-modal>
              <form action="">
                <div class="modal-card" style="width: auto">
                    <header class="modal-card-head">
                        <p class="modal-card-title">Create new Identity</p>
                    </header>
                    <section class="modal-card-body">

                        <b-field label="Create a Password">
                            <b-input
                                type="password"
                                v-model="password"
                                password-reveal
                                placeholder="This will be your main password"
                                required>
                            </b-input>
                        </b-field>

                        <b-field label="Repeat password">
                            <b-input
                                type="password"
                                v-model="passwordrepeat"
                                password-reveal
                                placeholder="Repeat password"
                                required>
                            </b-input>
                        </b-field>
                    </section>
                    <footer v-if="!isCreating" class="modal-card-foot">
                        <button class="button is-primary" style="width:100%" v-on:click="createUser">CREATE</button>
                    </footer>
                    <footer v-if="isCreating" class="modal-card-foot">
                        <div style="text-align:center">Creating identity, please wait...</div>
                    </footer>
                </div>
            </form>
    </b-modal>
  </div>
</template>

<script>
  let ScryptaCore = require('@scrypta/core')
  
  export default {
    data(){ 
      return {
        scrypta: new ScryptaCore(true),
        address: '',
        wallet: '',
        isLogging: true,
        needsRSA: false,
        isCreating: false,
        showCreateModal: false,
        password: '',
        passwordrepeat: ''
      }
    },
    async mounted() {
      const app = this
      app.wallet = await app.scrypta.returnDefaultIdentity()
      if(app.wallet.length > 0){
        let SIDS = app.wallet.split(':')
        app.address = SIDS[0]
        let identity = await app.scrypta.returnIdentity(app.address)
        if(identity.rsa === undefined){
          app.needsRSA = true
        }
        app.wallet = identity
        app.isLogging = false
      }else{
        app.isLogging = false
      }
    },
    methods: {
      showCreate(){
        const app = this
        app.showCreateModal = true
      },
      async createUser(){
        const app = this
        if(app.password !== ''){
          if(app.passwordrepeat === app.password){
            app.isCreating = true
            setTimeout(async function(){
              let id = await app.scrypta.createAddress(app.password, true)
              await app.scrypta.createRSAKeys(id.pub, app.password)
              let identity = await app.scrypta.returnIdentity(id.pub)
              app.needsRSA = false
              app.address = id.pub
              app.wallet = identity
              localStorage.setItem('SID', id.walletstore)
              app.showCreateModal = false
              app.password = ''
              app.passwordrepeat = ''
              app.isCreating = false
            }, 500)
          }else{
            app.$buefy.toast.open({
                message: 'Passwords doesn\'t matches.',
                type: 'is-danger'
            })
          }
        }else{
          app.$buefy.toast.open({
              message: 'Write a password first!',
              type: 'is-danger'
          })
        }
      }
    }
  }
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
