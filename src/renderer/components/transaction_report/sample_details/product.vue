<template src='./product.html'>
</template>
<script>
import { rl, rp } from '@/components/database/company'
import { mysql, queryExecSample } from '@/components/database/dbConnection'
var groupArray = require('group-array')
const storage = require('electron-json-storage')
export default {
  data () {
    return {
      product: {
        companydetails: {},
        productdetails: {},
        selectproduct: {},
        productslt: {},
        sdate: '',
        edate: '',
        data: {}
      },
      choose: false,
      choosedproduct: {},
      grouparray: [],
      grouparray1: []
    }
  },
  watch: {
    'product.selectproduct': 'selected',
    'product.data': 'view'
  },
  methods: {
    view: function () {
      console.log(this.product.data)
    },
    exec: function () {
      let query = 'SELECT * FROM `invcontrol` a INNER JOIN `fsmpsr` b ON a.`Pcode` = b.`Code` INNER JOIN `invdetails` c ON c.`CCode` = a.`CCode` AND c.`InvNo` = a.`InvNo` AND c.`InvDt` = a.`InvDt` INNER JOIN `imas` d ON c.`ItemCode` = d.`SHCD` WHERE  a.`InvDt` BETWEEN ' + mysql.escape(this.product.sdate) + ' AND ' + mysql.escape(this.product.edate) + ' AND c.`ItemCode` = ' + mysql.escape(this.product.productslt.SHCD) + ' ORDER BY c.ItemCode'
      queryExecSample(query)
        .then(value => {
          console.log(value)
          this.grouparray1 = groupArray(value, 'Name')
          console.log(this.grouparray1)
        })
    },
    selected: function () {
      console.log(this.product.selectproduct)
    },
    productget: function () {
      let query = 'SELECT * FROM `imas` WHERE 1'
      queryExecSample(query)
        .then(value => {
          console.log(value)
          this.choose = true
          this.choosedproduct = value
          console.log(this.choosedproduct)
          this.grouparray = groupArray(this.choosedproduct, 'DESCR')
          console.log(this.grouparray)
        })
    }
  },
  created: function () {
    var self = this
    console.log(storage.get('login', (err, data) => {
      if (err) throw err
      console.log(data)
      if (data.company === '01') {
        self.product.companydetails = rp
      } else {
        self.product.companydetails = rl
      }
    }))
    self.productget()
  }
}
</script>
