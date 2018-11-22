<template src='./bo.html'>
</template>
<script>
import { rl, rp } from '@/components/database/company'
import { mysql, queryExecSample } from '@/components/database/dbConnection'
var groupArray = require('group-array')
const storage = require('electron-json-storage')
export default {
  data () {
    return {
      bo: {
        companydetails: {},
        sdate: '',
        edate: '',
        bodetails: {},
        selectbo: {}
      },
      choose: false,
      choosedbo: {},
      grouparray: [],
      search: ''
    }
  },
  watch: {
    'bo.edate': 'changed'
  },
  methods: {
    changed: function () {
      let query = 'SELECT * FROM `invcontrol` a INNER JOIN `fsmpsr` b ON a.`Pcode` = b.`Code` INNER JOIN `invdetails` c ON c.`CCode` = a.`CCode` AND c.`InvNo` = a.`InvNo` AND c.`InvDt` = a.`InvDt` INNER JOIN `imas` d ON c.`ItemCode` = d.`SHCD` WHERE b.Code = ' + mysql.escape(this.bo.selectbo.Code) + ' AND a.`InvDt` BETWEEN ' + mysql.escape(this.bo.sdate) + ' AND ' + mysql.escape(this.bo.edate) + ' ORDER BY c.ItemCode'
      queryExecSample(query)
        .then(value => {
          console.log(value)
          this.choose = true
          this.choosedbo = value
          console.log(this.choosedbo)
          this.grouparray = groupArray(this.choosedbo, 'DESCR')
          console.log(this.grouparray)
        })
    },
    boget: function () {
      let query = 'SELECT * FROM `fsmpsr` WHERE 1'
      queryExecSample(query)
        .then(data => {
          console.log(data)
          this.bo.bodetails = data
        })
    }
  },
  created: function () {
    var self = this
    console.log(storage.get('login', (err, data) => {
      if (err) throw err
      console.log(data)
      if (data.company === '01') {
        self.bo.companydetails = rp
      } else {
        self.bo.companydetails = rl
      }
    }))
    self.boget()
  },
  computed: {
    sorted: function () {
      var self = this
      return self.search
    }
  }
}
</script>
