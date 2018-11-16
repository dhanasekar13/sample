<template>
  <div>
    <p> this is the gate pass transcation report</p>
  </div>
</template>
<script>
export default {
  props: ['name'],
  data () {
    return {
      gpnos: '',
      value: this.name
    }
  },
  methods: {
    prompt: function () {
      return new Promise(function (resolve, reject) {
        const prompt = require('electron-prompt')
        prompt({
          title: 'Enter the GP NO',
          type: 'input',
          label: 'ENTER THE GATE PASS NO..'
        })
          .then((r) => {
            resolve(r)
          })
      })
    },
    gpno: function () {
      var current = this
      console.log(current.gpnos)
    }
  },
  created: function () {
    var current = this
    var gpno = this.prompt()
    gpno.then(function (r) {
      current.gpnos = r
    })
  },
  beforeMount: function () {
    console.log(this.value)
    console.log(this)
    this.gpno()
    this.$router.push({ name: 'transaction_report_invoiceno', params: { name: 'dhanasekar' } })
  }
}
</script>
