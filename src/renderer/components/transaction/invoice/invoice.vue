<template src='./invoice.html'>

</template>
<script>
import { tmpinvdetl } from '@/components/database/table'
import { queryExecSample, mysql, insertRecord } from '@/components/database/dbConnection'
const storage = require('electron-json-storage')
export default {
  data () {
    return {
      full: {
        company: '',
        rows: [],
        period: '',
        itemmas: '',
        a: false,
        invno: '',
        selectval: '',
        invDt: '',
        Name: ''
      }
    }
  },
  watch: {
    'full.selectval': 'changefield',
    'row.hsn': 'individual '
  },
  created: function () {
    var current = this
    console.log(storage.get('login', function (error, data) {
      if (error) throw error
      console.log(data)
      current.full.company = data.company
      current.full.period = data.period
      console.log(current.company)
      var query = 'SELECT * FROM `stkdetl` a, imas b WHERE a.Qty > 0 and a.CCode = ' + mysql.escape(current.full.company) + ' and a.CCode = b.CCode and a.ItemCd = b.SHCD ORDER BY `a`.`Qty` ASC '
      var value = queryExecSample(query)
      value.then(function (data) {
        if (data.length > 0) {
          console.log(data)
          current.full.itemmas = data
          // var dummy = 'SELECT * FROM `invcontrol` a, `invdetails` b, `imas` c, `fsmpsr` d where a.CCode = '01' and a.CCode = b.CCode and a.period = b.period and a.period = '18-19' and a.InvNo = b.InvNo and b.ItemCode = c.SHCD and d.Code = a.Pcode'
          //  var query1 = 'SELECT * FROM `invdetails` a, imas m WHERE a.`CCode`=' + mysql.escape(current.full.company) + ' and a.`period`= ' + mysql.escape(current.full.period) + ' and a.ItemCode = m.SHCD'
          var query1 = 'SELECT * FROM `invcontrol` a, `invdetails` b, `imas` c, `fsmpsr` d where a.CCode = ' + mysql.escape(current.full.company) + ' and a.CCode = b.CCode and a.period = b.period and a.period = ' + mysql.escape(current.full.period) + ' and a.InvNo = b.InvNo and b.ItemCode = c.SHCD and d.Code = a.Pcode'
          var value1 = queryExecSample(query1)
          value1.then(function (data) {
            if (data.length > 0) {
              console.log(data)
              current.full.invno = data
            } else {
              current.full.invo = ''
            }
          })
        }
      })
    }))
  },
  methods: {
    deletetempinvoice: function () {
      let query = 'DELETE FROM `tmpinvdetl` WHERE 1'
      return new Promise((resolve, reject) => {
        queryExecSample(query)
          .then((data) => {
            resolve(data)
          })
      })
    },
    formatDate: function (date) {
      var dateString = new Date(date.getTime() - (date.getTimezoneOffset() * 60000)).toISOString().split('T')[0]
      console.log(dateString)
      return dateString
    },
    inserttempinvoice: function () {
      // var self = this
      return new Promise((resolve, reject) => {
        let conforms = confirm('Are you sure wnat to create a invoice')
        if (conforms) {
          console.log(this.full)
          for (var i = 0; i < this.full.rows.length; i++) {
            tmpinvdetl.Type = 'SAMPLE'
            tmpinvdetl.CCode = this.full.company
            tmpinvdetl.period = this.full.period
            tmpinvdetl.InvNo = this.full.invno
            tmpinvdetl.InvDt = this.full.invDt
            tmpinvdetl.SLNo = i
            tmpinvdetl.ItemCode = this.full.rows[i].hsn.ItemCd
            tmpinvdetl.Pack = this.full.rows[i].PCAPCTY
            tmpinvdetl.BatchNo = this.full.rows[i].batch
            tmpinvdetl.ExpDt = this.formatDate(this.full.rows[i].expdt)
            tmpinvdetl.Qty = this.full.rows[i].qty
            tmpinvdetl.TValue = this.full.rows[i].amount
            tmpinvdetl.MRP = this.full.rows[i].rate
            tmpinvdetl.InvFlag = 'N'
            tmpinvdetl.EdFlag = 'N'
            tmpinvdetl.Stkexpflag = 'Y'
            console.log(tmpinvdetl)
            var tablename = 'tmpinvdetl'
            insertRecord(tablename, tmpinvdetl).then(function () {
              alert('Inserted Successfully')
              resolve(1)
            }, function () {
              alert('sorry record is not inserted  try again')
            })
          }
        }
      })
    },
    updateStkdetl: function (data) {
      console.log('Update Stock Details')
      console.log(data)
      //  data.rows[index].hsn.ItemCd
      // data.batch
      //  data.qty
      //   on particular item on this batch reduce the value
      for (var i = 0; i < data.rows.length; i++) {
        let query = 'Update `stkdetl` set `qty` = `qty` - ' + mysql.escape(data.qty) + 'where `itemcd` =  ' + mysql.escape(data.rows[i].hsn.ItemCd) + ' and batchno = ' + mysql.escape(data.rows[i].hsn.batchno) + ' and ccode = ' + mysql.escape(data.company)
        console.log(query)
      }
    },
    save: function () {
      console.log('save the data')
      this.deletetempinvoice()
        .then((data) => {
          console.log(data)
          this.inserttempinvoice()
            .then((val) => {
              if (val) {
                var query1 = 'Insert into `invdetails` select * from `tmpinvdetl` where ccode = ' + mysql.escape(this.full.company)
                queryExecSample(query1)
                  .then((val) => {
                    alert('invoice is inserted successfully')
                    this.updateStkdetl(this.full)
                  })
              }
            })
        })
    },
    report: function () {
      console.log('reload')
      alert('hey this is where you enter the invoice no to print')
    },
    changefield: function () {
      var current = this
      console.log(current.full.selectval.LRDt)
      current.full.rows.push({
        'hsn': current.full.selectval.SHCD,
        'batch': current.full.selectval.BatchNo,
        'expdt': new Date(current.full.selectval.ExpDt).toLocaleDateString(),
        'qty': current.full.selectval.Qty,
        'rate': current.full.selectval.Rate,
        'amount': current.full.selectval.MRP
      })
    },
    create: function () {
      console.log('create')
      var current = this
      current.full.selectval = ''
      var query1 = 'SELECT MAX(InvNo) FROM `invdetails` WHERE `CCode`=' + mysql.escape(current.full.company) + ' and `period`= ' + mysql.escape(current.full.period)
      var value1 = queryExecSample(query1)
      value1.then(function (data) {
        if (data.length > 0) {
          current.a = true
          console.log(Object.values(data[0]))
          var chk = Object.values(data[0])
          if (chk[0] === null) {
            current.full.invno = 1
          } else {
            current.full.invno = parseInt(Object.values(data[0])) + 1
          }
          var query2 = 'SELECT * from `fsmpsr` where 1'
          var values2 = queryExecSample(query2)
          values2.then(function (data) {
            current.full.Name = data
          })
        } else {
          current.full.invno = parseInt('1')
        }
      })
    },
    hit: function (data) {
      var current = this
      console.log(current.rows)
    },
    addRow: function () {
      var current = this
      current.full.rows.push({
        'hsn': '',
        'batch': '',
        'expdt': '',
        'qty': '',
        'rate': '',
        'amount': ''
      })
    },
    removeElement: function (index) {
      this.rows.splice(index, 1)
    }
  }
}
</script>
<style src='./invoice.css'>
</style>
