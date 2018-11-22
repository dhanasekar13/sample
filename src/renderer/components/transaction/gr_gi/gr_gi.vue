<template src='./gr_gi.html'>
</template>
<script>
import { remote } from 'electron'
import { queryExecSample, mysql } from '@/components/database/dbConnection'
const storage = require('electron-json-storage')
export default {
  data () {
    return {
      company: '',
      type: '',
      companycode: '',
      period: '',
      gno: '',
      gdate: '',
      gname: '',
      gremark: '',
      gds: {},
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
    stockupdate: function () {
      if (this.type === 'GI') {
        for (let i = 0; i < this.rows.length; i++) {
          let query1 = 'Update stkcntl set runqty = runqty - ' + mysql.escape(this.rows[i].qty) + ', IssueQtytot = IssueQtytot + ' + mysql.escape(this.rows[i].qty) + ' where itemcd = ' + mysql.escape(this.rows[i].data1.ItemCd) + ' and ccode = ' + mysql.escape(this.companycode)
          queryExecSample(query1)
            .then(data => {
              console.log(i)
            }).catch(err => console.log(err))
          let query2 = 'Update stkdetl set qty = qty - ' + mysql.escape(this.rows[i].qty) + ' where itemcd = ' + mysql.escape(this.rows[i].data1.ItemCd) + ' and batchno = ' + mysql.escape(this.rows[i].batch) + ' and ccode = ' + mysql.escape(this.companycode)
          queryExecSample(query2)
            .then(data1 => {
              console.log(i)
            }).catch(err => console.log(err))
        }
      } else {
        for (let i = 0; i < this.rows.length; i++) {
          let query3 = 'Update stkcntl set runqty = runqty + ' + mysql.escape(this.rows[i].qty) + ', IssueQtytot = IssueQtytot - ' + mysql.escape(this.rows[i].qty) + ' where itemcd = ' + mysql.escape(this.rows[i].data1.ItemCd) + ' and ccode = ' + mysql.escape(this.companycode)
          queryExecSample(query3)
            .then(data => {
              console.log(i)
            })
          let query4 = 'Update stkdetl set qty = qty + ' + mysql.escape(this.rows[i].qty) + ' where itemcd = ' + mysql.escape(this.rows[i].data1.ItemCd) + ' and batchno = ' + mysql.escape(this.rows[i].batch) + ' and ccode = ' + mysql.escape(this.companycode)
          queryExecSample(query4)
            .then(data => {
              console.log(i)
            })
        }
      }
      alert('successfully stock updated')
    },
    creategoods: function () {
      this.stockupdate()
      let query = 'Update `TmpGRGI` set `period` = ' + mysql.escape(this.period) + ', `TRNo` = ' + mysql.escape(this.gno) + ', `TRDate` = ' + mysql.escape(this.gdate) + ', `ccode` = ' + mysql.escape(this.companycode) + ', `Remarks` = ' + mysql.escape(this.gremark) + ', `Type` = ' + mysql.escape(this.type) + ', `Pname` = ' + mysql.escape(this.gname)
      queryExecSample(query)
        .then(data => {
          console.log(data)
          let query1 = 'Insert into `GRGITran` select * from `TmpGRGI`'
          queryExecSample(query1)
            .then(data1 => {
              let query2 = 'Update `TmpGRGI` set `period` = ' + 0 + ', `TRNo` = ' + 0 + ', `TRDate` = ' + 0 + ', `ccode` = ' + 0 + ', `Type` = ' + 0 + ', `Pname` = ' + 0 + ' '
              queryExecSample(query2)
                .then(data2 => {
                  console.log('ddd')
                  console.log(data2)
                  alert('successfully created')
                  remote.getCurrentWindow().reload()
                }).catch(err => console.log(err))
            }).catch(err => console.log(err))
        }).catch(err => console.log(err))
    },
    goods: function () {
      console.log(this.type)
      if (this.type === 'GI') {
        let query = 'SELECT * FROM `stkdetl` a INNER JOIN `imas` b ON a.CCode = b.CCode and a.ItemCd = b.SHCD and a.Qty > 0 and a.CCode = ' + mysql.escape(this.companycode)
        queryExecSample(query)
          .then(data => {
            this.gi.data = data
            this.gds = this.gi.data
          })
        let query1 = 'SELECT `TRNo` FROM `grgitran` WHERE `period` = ' + mysql.escape(this.period) + ' AND `Ccode` = ' + mysql.escape(this.companycode) + ' AND `Type`="GI" ORDER BY `TRNo` DESC LIMIT 1'
        queryExecSample(query1)
          .then(data1 => {
            this.gno = data1[0].TRNo + 1
          })
      } else {
        let query = 'SELECT * FROM `stkdetl` a INNER JOIN `imas` b ON a.CCode = b.CCode and a.ItemCd = b.SHCD'
        queryExecSample(query)
          .then(data2 => {
            this.gr.data = data2
            this.gds = this.gr.data
          })
        let query1 = 'SELECT `TRNo` FROM `grgitran` WHERE `period` = ' + mysql.escape(this.period) + ' AND `Ccode` = ' + mysql.escape(this.companycode) + ' AND Type="GR" ORDER BY TRNo DESC LIMIT 1'
        queryExecSample(query1)
          .then(data3 => {
            this.gno = data3[0].TRNo + 1
          })
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
        'rate': '',
        'data1': {}
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
