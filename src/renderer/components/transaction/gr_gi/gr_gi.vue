<template src='./gr_gi.html'>
</template>
<script>
import { queryExecSample, mysql } from '@/components/database/dbConnection'
const storage = require('electron-json-storage')
export default {
  data () {
    return {
      company: '',
      type: '',
      companycode: '',
      period: '',
      gr: {
        data: {}
      },
      gi: {
        data: {}
      },
      rows: []
    }
  },
  watch: {
    'type': 'goods'
  },
  created: function () {
    var self = this
    console.log(storage.get('login', (err, data) => {
      if (err) throw err
      console.log(data)
      if (data.company === '01') {
        self.companycode = '01'
        self.company = 'RETORT PHARMACEUTICAL'
      } else {
        self.companycode = '02'
        self.company = 'RETORT LABORATORIES'
      }
      self.period = data.period
    }))
  },
  methods: {
    goods: function () {
      console.log(this.type)
      if (this.type === 'GI') {
        let query = 'SELECT * FROM `stkdetl` a INNER JOIN `imas` b ON a.CCode = b.CCode and a.ItemCd = b.SHCD and a.Qty > 0'
        queryExecSample(query)
          .then(data => {
            console.log(data)
          })
        let query1 = 'SELECT `TRNo` FROM `grgitran` WHERE `period` = ' + mysql.escape(this.period) + ' AND `Ccode` = ' + mysql.escape(this.companycode) + ' AND `Type`="GI" ORDER BY `TRNo` DESC LIMIT 1'
        console.log(query1)
      } else {
        let query = 'SELECT * FROM `stkdetl` a INNER JOIN `imas` b ON a.CCode = b.CCode and a.ItemCd = b.SHCD'
        queryExecSample(query)
          .then(data => {
            console.log(data)
          })
        let query1 = 'SELECT `TRNo` FROM `grgitran` WHERE `period` = ' + mysql.escape(this.period) + ' AND `Ccode` = ' + mysql.escape(this.companycode) + ' AND Type="GR" ORDER BY TRNo DESC LIMIT 1'
        console.log(query1)
      }
    },
    hit: function (data) {
      var current = this
      console.log(current.rows)
    },
    addRow: function () {
      var current = this
      current.rows.push({
        'hsn': '',
        'batch': '',
        'mfgdt': '',
        'expdt': '',
        'qty': '',
        'unit': '',
        'rate': ''
      })
    },
    removeElement: function (index) {
      this.rows.splice(index, 1)
    }
  }
}
</script>
<style src='./gr_gi.css'>
</style>
