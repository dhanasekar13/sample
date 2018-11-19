<template src='./invoice.html'>
=
</template>
<script>
import { rl, rp, kc } from '@/components/database/company'
const prompt = require('electron-prompt')
const storage = require('electron-json-storage')
export default {
  data () {
    return {
      invoice: {
        company: '',
        period: '',
        companydetails: {},
        invoiceno: ''
      }
    }
  },
  props: ['name'],
  mounted: function () {
    console.log(this.$route.params.name)
    console.log(this.invoice)
  },
  methods: {
    invoicefnt: function () {
      console.log(this.invoice)
    },
    prompted: function () {
      prompt({
        title: 'Enter the Invoice Number',
        label: 'INVOICE NUMBER',
        type: 'input'
      })
        .then(value => {
          console.log(value)
          this.invoice.invoiceno = value
          this.invoicefnt()
        })
    }
  },
  created: function () {
    var self = this
    console.log(storage.get('login', (err, data) => {
      if (err) throw err
      console.log(data)
      self.invoice.company = data.company
      self.invoice.period = data.period
      if (self.invoice.company === '01') {
        self.invoice.companydetails = rp
      } else if (self.invoice.company === '02') {
        self.invoice.companydetails = rl
      } else {
        self.invoice.companydetails = kc
      }
      console.log(self.invoice.companydetails)
      console.log(self.invoice)
    }))
    this.prompted()
  }
}
</script>
