<template>
    <div id="orders">
      <div id="orderList">
        <div v-for="(order, key) in orders" v-bind:key="'order'+key" class="differentOrders">
          <strong>#{{ key }}: <em>{{ order.orderItems }}</em></strong> <br>
              <em style="margin-left: 20px;"> Full name: {{ order.fullName}}{{ ';' }}</em>
              <p style="margin-left: 50px;" id="text-color"> 
               - Gender: {{ order.gender }} <br>
               - Email:  {{ order.email }}  <br>
               - Payment Method: {{ order.paymentMethod }} <br> </p>
              
        </div>
        <button v-on:click="clearQueue" class="clearButton"> Clear Queue </button>
      </div>
      <div id="dots">
          <div v-for="(order, key) in orders" v-bind:style="{ left: order.details.x + 'px', top: order.details.y + 'px'}" v-bind:key="'dots' + key">
            {{ key }}
          </div>
      </div>
    </div>
</template>
  <script>
  import io from 'socket.io-client'
  const socket = io("localhost:3000");
  
  export default {
    name: 'DispatcherView',
    data: function () {
      return {
        orders: null,
      }
    },
    created: function () {
      socket.on('currentQueue', data =>
        this.orders = data.orders);
    },
    methods: {
      clearQueue: function () {
        socket.emit('clearQueue');
      },
      changeStatus: function(orderId) {
        socket.emit('changeStatus', {orderId: orderId, status: "Annan status"});

      }
    }
  }
  </script>
  <style>
  #orderList {
    top:1em;
    left:1em;
    position: absolute;
    z-index: 2;
    color:black;
    background: rgba(255,255,255, 0.5);
    padding: 1em;

  .differentOrders {
    border-bottom: 1px solid #4e4c4c;
    padding: 5px;
  }

  .clearButton {
    margin-top: 20px;
  }

  .clearButton:hover {
    background-color: red;
    cursor: pointer;
  }
  #text-color {
    color: black;
  }
}

  #dots {
    position: relative;
    margin: 0;
    padding: 0;
    background-repeat: no-repeat;
    width:1920px;
    height: 1078px;
    cursor: crosshair;
    background-image: url('/img/polacks.jpg');
  }
  
  #dots div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }
  </style>
  
