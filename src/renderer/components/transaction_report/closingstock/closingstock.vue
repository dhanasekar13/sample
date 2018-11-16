<template src="./closing.html">
</template>
<script>
import { queryExecSample } from '@/components/database/dbConnection'
import { rl, rp } from '@/components/database/company'
export default {
  data () {
    return {
      gridColumns: ['ItemCd', 'DESCR', 'BatchNo', 'Qty', 'RATE', 'MRP', 'MfgDt', 'ExpDt'],
      company: '',
      customerRec: [],
      companydetails: '',
      currentSort: 'DESCR',
      currentSortDir: 'asc',
      pageSize: 50,
      currentPage: 1,
      searchQuery: '',
      sortKey: '',
      sortOrders: function () {
        this.customerRec.forEach(function (key) {
          this.sortOrders[key] = 1
        })
      }
    }
  },
  methods: {
    nextPage: function () {
      if ((this.currentPage * this.pageSize) < this.customerRec.length) this.currentPage++
    },
    prevPage: function () {
      if (this.currentPage > 1) this.currentPage--
    },
    onload: function () {
      var current = this
      var query = 'SELECT a.CCode, a.SHCD,a.DESCR,a.PCAPCTY,a.UMN, b.ItemCd,b.BatchNo,b.RecDate,b.Qty,b.RATE,b.MRP,b.MfgDt,b.ExpDt FROM imas a, stkdetl b WHERE a.CCode = b.CCode and a.SHCD=b.ItemCd and b.CCode= ' + current.company + ' and b.Qty>0 ORDER BY `b`.`RecDate` DESC'
      var value = queryExecSample(query)
      value.then(function (data) {
        for (var i = 0; i < data.length; i++) {
          current.customerRec.push(data[i])
        }
        console.log(current.customerRec)
      })
      if (current.company === '01') {
        current.companydetails = rp
      } else if (current.company === '02') {
        current.companydetails = rl
      }
      console.log(current.companydetails)
    },
    sort: function (s) {
      if (s === this.currentSort) {
        this.currentSortDir = this.currentSortDir === 'asc' ? 'desc' : 'asc'
      }
      this.currentSort = s
    }
  },
  computed: {
    sortedCats: function () {
      return this.customerRec.filter((post) => {
        return post.DESCR.toString().toLowerCase().includes(this.searchQuery)
      }).sort((a, b) => {
        let modifier = 1
        if (this.currentSortDir === 'desc') modifier = -1
        if (a[this.currentSort] < b[this.currentSort]) return -1 * modifier
        if (a[this.currentSort] > b[this.currentSort]) return -1 * modifier
        return 0
      }).filter((row, index) => {
        let start = (this.currentPage - 1) * this.pageSize
        let end = this.currentPage * this.pageSize
        if (index >= start && index < end) return true
      })
    }
  },
  beforeMount () {
    var current = this
    const storage = require('electron-json-storage')
    storage.get('login', function (error, data) {
      if (error) throw error
      console.log(data)
      current.company = data.company
      console.log(current.company)
      current.onload()
    })
  },
  mounted () {

  }
}
</script>
<style src="./closing.css">
</style>
