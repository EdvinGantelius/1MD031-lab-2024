<template>

<div class="burger-element">
  <h4>{{ burger.name }}</h4>
  <img :src="getImage(burger.name)" width="200" height="200"/>
  <ul>
    <li v-if="burger.containsLactose && burger.containsGluten" class="importantInfo"> Contains both <em><b>Lactose and Gluten</b></em></li>
    <li v-else-if="burger.containsLactose"> Contains <em><b> Lactose </b></em></li>
    <li v-else-if="burger.containsGluten"> Contains <em><b> Gluten </b></em></li>
    <li v-else> No Lactose or Gluten </li>
    <li v-for="(ingredients, index) in burger.ingredients" :key="index">
      {{ ingredients }}
    </li>
    <li><strong>kCal:</strong> {{ burger.kCal }}</li>
  </ul>

  <button @click="decreaseQuantity">-</button> <span> {{ amountOrdered }} </span> <button @click="increaseQuantity">+</button>
</div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: {
      type: Object,
      required: true
    }
  },

  data: function () {
    return {
      amountOrdered: 0,
    }
  },

  methods: {
    getImage(burgerName) {
      switch (burgerName) {
        case 'The Breakfast Burger':
          return "https://static.vecteezy.com/ti/gratis-foton/p2/47572652-frukost-skjutreglage-terar-mini-agg-smorgasar-med-bacon-och-cheddarost-ost-pa-reglaget-bullar-fotona.jpg";
        case 'The Lunch Burger':
          return 'https://eu-central-1.linodeobjects.com/tasteline/2015/09/ginas_burgare_med_pulled_chicken_och_coleslaw.jpg';
        case 'The Dinner Burger':
          return 'https://dynamic-media-cdn.tripadvisor.com/media/photo-o/25/18/0e/8a/original-smash-burger.jpg?w=800&h=-1&s=1';
        default:
          return '';
      }
    },
    increaseQuantity() {
      this.amountOrdered++;
      this.$emit('updated-order', {name: this.burger.name, number: this.amountOrdered});
    },

    decreaseQuantity() {
      if (this.amountOrdered > 0){
        this.amountOrdered--;
        this.$emit('updated-order', {name: this.burger.name, number: this.amountOrdered});
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.burger-image{
  width: 100%;
  height: auto;
  object-fit: cover;
  border-radius: 8px;
}

button {
  margin: 5px;
  padding: 10px;
  font-size:12px;
  cursor: pointer;
}

button:hover {
  background-color: lightgrey;
}
</style>