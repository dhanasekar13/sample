<template src='./invoice.html'>
=
</template>
<script>
import { rl, rp, kc } from '@/components/database/company'
import { mysql, queryExecSample } from '@/components/database/dbConnection'
const prompt = require('electron-prompt')
const storage = require('electron-json-storage')
export default {
  data () {
    return {
      invoice: {
        company: '',
        period: '',
        companydetails: {},
        invoiceno: '',
        invoicedata: {}
      }
    }
  },
  props: ['name'],
  mounted: function () {
    console.log(this.$route.params.name)
    console.log(this.invoice)
  },
  methods: {
    formatDate: function (date) {
      var dateString = new Date(date.getTime() - (date.getTimezoneOffset() * 60000)).toISOString().split('T')[0]
      console.log(dateString)
      return dateString
    },
    invoicefnt: function () {
      console.log(this.invoice)
      let query = 'SELECT * FROM `invdetails` a INNER JOIN `invcontrol` b ON a.CCode=b.CCode and a.period=b.period and a.InvNo = b.InvNo and a.InvDt = b.InvDt INNER JOIN `imas` c ON a.ItemCode = c.SHCD INNER JOIN `fsmpsr` d ON b.Pcode = d.Code WHERE a.period=' + mysql.escape(this.invoice.period) + 'and a.InvNo=' + mysql.escape(this.invoice.invoiceno)
      console.log(query)
      queryExecSample(query)
        .then((data) => {
          this.invoice.invoicedata = data
          console.log(this.invoice.invoicedata)
        })
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
