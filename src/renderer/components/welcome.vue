<template>
 <div>
 <div class="container" align=center>
    <h3>Retort Sample Application SOP </h3>
    <div class='row'>
        <div class="card col-sm-6" style="width:18rem">
            <div class="card-body">
                <h6 class="card-title">Master Menu</h6>
                <p class="card-text">
                create , read , update and delete the item , customer , fsmpsr ,area and regional details  </p>
                <button class="btn btn-info">ITEM </button>
                <button class="btn btn-info">CUSTOMER</button>
                <button class="btn btn-info">FSMPSR</button>
            </div>
        </div>
        <div class="card col-sm-6" style="width:18rem">
            <div class="card-body">
                <h6 class="card-title">Transcation Menu</h6>
                <p class="card-text">
                gate pass , invoice ,good return and issue , validation and credit note details using crud operation</p>
                <button class="btn btn-info">GATE PASS</button>
                <button class="btn btn-info">INVOICE</button>
                <button class="btn btn-info">GR / GI</button>
            </div>
        </div>
    </div>
            <br>
    <div class='row'>
        <div class="card col-sm-6" style="width:18rem">
            <div class="card-body">
                <h6 class="card-title">Master Report Menu</h6>
                <p class="card-text">
                create , read , update and delete the item , customer , fsmpsr ,area and regional details    </p>
                <button class="btn btn-info">ITEM</button>
                <button class="btn btn-info">PSR / FSM</button>
                <button class="btn btn-info">CUSTOMER</button>
            </div>
        </div>
        <div class="card col-sm-6" style="width:18rem">
            <div class="card-body">
                  <h6 class="card-title">Transcation Menu</h6>
                  <p class="card-text">
                  gate pass , invoice ,good return and issue , validation and credit note details using crud operation</p>
                  <button class="btn btn-info">INVOICE</button>
                  <button class="btn btn-info">CLOSING STOCK</button>
                  <button class="btn btn-info">SAMPLE DETAILS</button>
            </div>
        </div>
    </div>
</div>


 </div>
</template>
<script>
export default {
  methods: {
  },
  created () {
    const storage = require('electron-json-storage')
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
