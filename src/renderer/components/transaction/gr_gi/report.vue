<template src='./report.html'>
</template>
<script>
import { rl, rp, kc } from '@/components/database/company'
import { mysql, queryExecSample } from '@/components/database/dbConnection'
const prompt = require('electron-prompt')
const storage = require('electron-json-storage')
export default {
  data () {
    return {
      goods: {
        companydetails: {},
        sdate: '',
        edate: '',
        value: {}
      }
    }
  },
  created: function () {
    var self = this
    console.log(storage.get('login', (err, data) => {
      if (err) throw err
      console.log(data)
      if (data.company === '01') {
        self.goods.companydetails = rp
      } else if (data.company === '02') {
        self.goods.companydetails = rl
      } else {
        self.goods.companydetails = kc
      }
    }))
    self.prompted()
  },
  methods: {
    run: function () {
      let query = 'SELECT * FROM `grgitran` WHERE `TRDate` between ' + mysql.escape(this.goods.sdate) + ' and ' + mysql.escape(this.goods.edate)
      queryExecSample(query)
        .then(data => {
          this.goods.value = data
          console.log(this.goods.value)
        })
    },
    prompted: function () {
      prompt({
        title: 'Choose STARTING DATE',
        label: 'Starting Date :',
        type: 'input',
        value: (new Date().toISOString().split('T')[0])
      })
        .then(r => {
          this.goods.sdate = r
          this.prompted1()
        })
    },
    prompted1: function () {
      prompt({
        title: 'Choose ENDING DATE',
        label: 'Ending Date :',
        type: 'input',
        value: (new Date().toISOString().split('T')[0])
      })
        .then(r1 => {
          this.goods.edate = r1
          this.run()
        })
    }
  }
}
</script>
