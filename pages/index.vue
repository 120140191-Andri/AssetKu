<template>
  <div class="tes">
    <div v-for="(dats, id) in dat" :key="id" class="f">
      <p>{{ id + ": " + dats }}</p>
    </div>
    <br><hr>
    <div v-for="(dats, id) in data.amount" :key="id">
      <p>{{ id + ": " + dats }}</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  data () {
    return {
      dat: [],
      data: []
    }
  },
  created () {
    const addr = 'addr1qxvf4w9m4gj65ff27we6azsdatx9nqsp7ldjutxc4mn638aka8ef6vh20z2xny02dncy2nnleay7nwrpvfpxlt8cmdfqsumnku'
    const UrlAddress = 'https://cardano-mainnet.blockfrost.io/api/v0/addresses/' + addr + '/extended'

    this.$axios.$get(UrlAddress, { headers: { project_id: 'mainnetl82qjEr1AsW7meAAnUE7cTEtNCEuUI69' } })
      .then((response) => {
        this.data = response
        const stake = response.stake_address
        const UrlAkun = 'https://cardano-mainnet.blockfrost.io/api/v0/accounts/' + stake

        this.$axios.$get(UrlAkun, { headers: { project_id: 'mainnetl82qjEr1AsW7meAAnUE7cTEtNCEuUI69' } })
          .then((response) => {
            this.dat = response
          })
          .catch((error) => {
            console.log(error)
          })
      })
      .catch((error) => {
        console.log(error)
      })
  }
}
</script>
