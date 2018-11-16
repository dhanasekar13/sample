<template>
 <div>
 <div class="container" align=center>
    <h1>Retort Sample application SOP </h1>
    <p>
      menus
        1 - Master => create , read , update and delete the item , customer ,area and regional details
        2 - Transcation => gate pass , invoice ,good return and issue , validation and credit note details using crud operation
        3 - Report => Report for master and transcation using CRUD operation
    </p>
    </div>
 </div>
</template>
<script>
import { ipcRenderer } from 'electron'
export default {

  methods: {
    btn () {
      ipcRenderer.send('create', 1)
    }
  },
  created () {
    const storage = require('electron-json-storage')
    this.btn()
    console.log(storage.get('login', function (error, data) {
      if (error) throw error
      console.log(data)
    }))
  }
}
</script>
<style>
body{
background:lightgrey
}
.container {
background:white;
float:center;
width:800px;
margin:0 auto;
}
</style>
