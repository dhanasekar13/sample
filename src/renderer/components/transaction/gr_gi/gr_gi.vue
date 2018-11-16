<template src='./gr_gi.html'>

</template>
<script>
export default {
  data () {
    return {
      company: 'Retort laboratories',
      rows: []
    }
  },
  methods: {
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
