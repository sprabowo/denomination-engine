<template>
  <b-container class="bv-example-row">
    <b-row class="mt-5">
      <b-col offset-md="2" md="8">
        <b-jumbotron bg-variant="light">
          <template slot="header">
            <h1 class="text-center">Denomination Engine</h1>
          </template>
          <template slot="lead">
            <p class="text-center">this application can help you to denomination your currency in IDR</p>
          </template>
        </b-jumbotron>
      </b-col>
      <b-col offset-md="2" md="8">
        <b-form-group id="exampleInputGroup1"
                    label="Input Currency:"
                    label-for="exampleInput1"
                    description="example: 120000, 18.215, 180.231, Rp17500, Rp 500, Rp17.500,00, Rp 120.325, 005.000, 001000">
        <b-form-input id="exampleInput1"
                    @keyup.enter.native="checkRegexInput(currency)"
                    type="text"
                    v-model="currency"
                    required
                    placeholder="Enter your money here">
        </b-form-input>
      </b-form-group>
      </b-col>
      <b-col offset-md="2" md="8">
        <b-alert :show="dismissCountDown" v-if="invalid"
          @dismissed="dismissCountdown=0"
          @dismiss-count-down="countDownChanged"
          variant="danger" dismissible>
          Invalid Input <br><br>
          <b-button size="sm" @click="init">Reset</b-button>
        </b-alert>
        <b-alert show v-if="status"
          variant="success" dismissible>
          <strong>{{ currencyAsk }} = </strong></br>
          {{ result }} <br><br>
          <b-button size="sm" @click="init">Reset</b-button>
        </b-alert>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>

export default {
  data() {
    return {
      currency: null,
      currencyAsk: null,
      show: false,
      status: false,
      invalid: false,
      result: null,
      arrP: [100000, 50000, 20000, 10000, 5000, 2000, 1000, 500, 100, 50],
      dismissSecs: 3,
      dismissCountDown: 0
    }
  },
  methods: {
    init() {
      this.currency = null
      this.currencyAsk = null
      this.show = false
      this.status = false
      this.invalid = false
      this.result = null
    },
    distribusiPecahan(n) {
      n = Number(n)
      let sisa
      let bagi
      let stringHasil = ''
      let arrPecahan = this.arrP
      let lastArr = arrPecahan[arrPecahan.length - 1]
      let hasil = arrPecahan.map(function(item, index, array){
        bagi = parseInt(n/item)
        console.log(bagi)
        if (bagi !== 0) {
          sisa = n % item
          n = sisa
          stringHasil += `${bagi} x Rp${item}, ` 
        }
        if(item === lastArr) {
          stringHasil += (n) ? `left Rp${n} (no available fraction)`: ''
          stringHasil = stringHasil.replace(/, $/, '')
        }
        return bagi
      })
      return stringHasil
    },
    countDownChanged(dismissCountDown) {
      this.dismissCountDown = dismissCountDown
    },
    checkRegexInput(t) {
      let patternInvalid = new RegExp(/[1-9]\d{0,}(,|\s)\d{3}((,|\s)\d{0,})*|(\sRp)$/)
      let patternValid = new RegExp(/[1-9]\d{0,}\.?\d{3}(\.?\d{0,})*|[1-9]([0-9]{0,})*$/)
      let checkInvalid = patternInvalid.test(t)
      let checkValid = patternValid.test(t)
      if (checkInvalid) {
        this.invalid = true
        this.dismissCountDown = this.dismissSecs
      } else if(checkValid) {
        let hasilValid = patternValid.exec(t)[0]
        if (hasilValid === null) {
          this.status = false
          this.invalid = true
        }
        // console.log(hasilValid)
        let normalisasiHasil = hasilValid.replace(/\./g, '')
        this.currencyAsk = normalisasiHasil
        this.result = this.distribusiPecahan(normalisasiHasil)
        this.status = true
      } else {
        this.invalid = true
        this.dismissCountDown = this.dismissSecs
      }
    },
    panggil(c) {
      alert(typeof (c))
    }
  }
}
</script>
