<template>
  <div class="container bubble bubble-bottom-left">
    <div class="row mt-5 epoch animate__animated animate__fadeInUp">
      <div class="col-12 justify-content-center text-center">
        <h3 class="mt-3">
          Epoch {{ epochs.epoch }}
        </h3>
        <p>{{ selisih }}</p>
      </div>
    </div>
    <div class="row justify-content-center">
      <div v-for="(dat, id) in dats" :key="id">
        <div v-if="dat.d == 'Trade'" class="col btn">
          <label class="text-center">
            {{ dat.d }}
          </label>
          <p>{{ ConvertLovelace(dat.response.controlled_amount) }} ₳</p>
        </div>
        <div v-if="dat.d == 'Hodl'" class="col btn">
          <label class="text-center">
            {{ dat.d }}
          </label>
          <p>{{ ConvertLovelace(dat.response.controlled_amount) }} ₳</p>
        </div>
        <div v-if="dat.d == 'Airdrop'" class="col btn">
          <label class="text-center">
            {{ dat.d }}
          </label>
          <p>{{ ConvertLovelace(dat.response.controlled_amount) }} ₳</p>
        </div>
        <div v-if="dat.d == 'Likuiditas'" class="col btn isi">
          <label class="text-center">
            {{ dat.d }}
          </label>
          <p>{{ ConvertLovelace(dat.response.controlled_amount) }} ₳</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import 'animate.css'

export default {
  name: 'IndexPage',
  data () {
    return {
      dats: [],
      epochs: [],
      now: Math.trunc((new Date()).getTime() / 1000),
      selisih: ''
    }
  },
  created () {
    const ad = 'https://cardano-mainnet.blockfrost.io/api/v0/epochs/latest'

    this.$axios.$get(ad, { headers: { project_id: 'mainnetl82qjEr1AsW7meAAnUE7cTEtNCEuUI69' } })
      .then((response) => {
        this.epochs = response
      })
      .catch((error) => {
        console.log(error)
      })

    const addr = {
      Trade: 'addr1qxvf4w9m4gj65ff27we6azsdatx9nqsp7ldjutxc4mn638aka8ef6vh20z2xny02dncy2nnleay7nwrpvfpxlt8cmdfqsumnku',
      Hodl: 'addr1q9qtu84sl6p7q9jmzyd2gkhvys3dspcdh0u7qsrjlu6ldf295tp2alkuvn8ys2s08wk257esasvuhp0mjvs9d8vw05zs3wjgl7',
      Airdrop: 'addr1qx7kg8f25h77sh8x46xjx4wlj85d9pnsf4n8hmt6untwm4c09msglh7tgzgxtt8fp9paunkehhuy0ast2fygmddyw80qyy5uvx',
      Likuiditas: 'addr1q888trzjg8kc5usphpjlmskw0vtzp95e6ggzuhaswkyjv2vpjdsvpgjj8g92ez0ek5yux3scvagmz5lkv426umg3hveqhgudaf'
    }

    this.dats = []
    for (const d in addr) {
      const UrlAddress = 'https://cardano-mainnet.blockfrost.io/api/v0/addresses/' + addr[d] + '/extended'

      this.$axios.$get(UrlAddress, { headers: { project_id: 'mainnetl82qjEr1AsW7meAAnUE7cTEtNCEuUI69' } })
        .then((response) => {
          const stake = response.stake_address
          const UrlAkun = 'https://cardano-mainnet.blockfrost.io/api/v0/accounts/' + stake

          this.$axios.$get(UrlAkun, { headers: { project_id: 'mainnetl82qjEr1AsW7meAAnUE7cTEtNCEuUI69' } })
            .then((response) => {
              this.dats.push({ d, response })
            })
            .catch((error) => {
              console.log(error)
            })
        })
        .catch((error) => {
          console.log(error)
        })
    }
  },
  mounted () {
    window.setInterval(() => {
      this.now = this.ConvertEpoch(Math.trunc((new Date()).getTime() / 1000), this.epochs.end_time)
    }, 1000)
  },
  methods: {
    ConvertLovelace (data) {
      return (data / 1000000).toFixed(2)
    },
    ConvertEpoch (now, end) {
      const DateNow = new Date(now * 1000)
      const DateEnd = new Date(end * 1000)
      const distance = DateEnd - DateNow

      const _second = 1000
      const _minute = _second * 60
      const _hour = _minute * 60
      const _day = _hour * 24

      const days = Math.floor(distance / _day)
      const hours = Math.floor((distance % _day) / _hour)
      const minutes = Math.floor((distance % _hour) / _minute)

      this.selisih = days + ' Hari : ' + hours + ' Jam : ' + minutes + ' Menit '
    }
  }
}
</script>

<style>
  body{
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    margin: 0;
    height: 100%;
    background-color: #404040;
    overflow: hidden;
  }

  .epoch{
    color: #404040;
  }

  .container{
    border-radius: 20px;
    background-color: #efeedc;
    color: #404040;
  }

  .btn{
    color: #404040;
  }

  .bubble {
    position: relative;
    font-family: sans-serif;
    font-size: 18px;
    line-height: 24px;
    text-align: center;
    color: #404040;
  }

  .bubble-bottom-left:before {
    content: "";
    width: 0px;
    height: 0px;
    position: absolute;
    border-left: 24px solid #efeedc;
    border-right: 12px solid transparent;
    border-top: 12px solid #efeedc;
    border-bottom: 20px solid transparent;
    left: 210px;
    bottom: -24px;
  }

</style>
