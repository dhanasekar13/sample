<template src='./flash.html'>
</template>
<script>
import { rl, rp, kc } from '@/components/database/company'
import { mysql, queryExecSample } from '@/components/database/dbConnection'
const prompt = require('electron-prompt')
const storage = require('electron-json-storage')
export default {
  data () {
    return {
      flash: {
        companydetails: {},
        sdate: '',
        edate: '',
        sample: {}
      }
    }
  },
  methods: {
    run: function () {
      let query = 'SELECT * FROM `invdetails` a INNER JOIN `imas` b ON a.CCode = b.CCode AND a.ItemCode = b.SHCD WHERE a.InvDt BETWEEN ' + mysql.escape(this.flash.sdate) + ' AND ' + mysql.escape(this.flash.edate) + ' AND a.CCode = ' + mysql.escape(this.flash.companydetails.CCODE) + ' ORDER BY ItemCode'
      queryExecSample(query)
        .then(value => {
          console.log(value)
          this.flash.sample = value
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
          this.flash.sdate = r
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
          this.flash.edate = r1
          this.run()
        })
    }
  },
  created: function () {
    var self = this
    console.log(storage.get('login', (err, data) => {
      if (err) throw err
      console.log(data)
      if (data.company === '01') {
        self.flash.companydetails = rp
      } else if (data.company === '02') {
        self.flash.companydetails = rl
      } else {
        self.flash.companydetails = kc
      }
    }))
    self.prompted()
  }
}
</script>
