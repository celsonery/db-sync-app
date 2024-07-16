<template>
  <q-page class="flex">
    <div class="window-width window-height">
      <div class="q-pa-lg q-gutter-md flex column">
        <q-select
          filled
          v-model="database"
          :options="dbList"
          label="Selecione um Banco de dados"
          :disable="loading"
        />

        <q-btn
          class="self-end"
          color="primary"
          label="Sincronizar base de dados"
          @click="sendToSync"
          :disable="!database || loading"
          :loading="loading"
        />
      </div>

      <div class="q-pa-md q-gutter-sm" v-show="closeMsg">
        <q-banner rounded class="bg-orange-5 text-black">

          ATENÇÂO: O processo pode ser demorado de acordo com o tamanho da Base de Dados!

          <template v-slot:action>
            <q-btn flat color="black" label="OK" @click="closeMsg = false" />
          </template>
        </q-banner>
      </div>
    </div>
  </q-page>
</template>

<script>
import axios from 'axios'
import { useQuasar } from 'quasar'

export default {
  $q: useQuasar(),
  name: 'IndexPage',
  data () {
    return {
      database: '',
      dbList: [],
      loading: false,
      closeMsg: false
    }
  },

  methods: {
    sendToSync () {
      this.loading = true
      this.closeMsg = true
      axios.post('http://127.0.0.1:8000/api/databases', {
        database: this.database
      })
        .then(response => {
          this.$q.notify({
            type: 'positive',
            message: response.data.message
          })
        })
        .catch(error => {
          this.$q.notify({
            type: 'negative',
            message: error.response.data.message
          })
        })
        .finally(() => {
          this.loading = false
          this.closeMsg = false
        })
    }
  },

  mounted () {
    axios.get('http://127.0.0.1:8000/api/databases')
      .then(response => {
        this.dbList = response.data.databases
      })
  }
}
</script>
