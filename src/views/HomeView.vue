<template>


<!-- RUBRIK 1 -->
    <section>
        <header class="header">
            <div>
                <h1>Welcome to BurgerOnline</h1>
                <img class="image" src="https://cdn.getyourguide.com/img/location/5a1d4877b80c7.jpeg/99.jpg"
                alt="bakgrundsbild">
                <!--<p>Best burgers, best bite, best decision!</p>-->
           </div>
        </header>
    </section>

<!-- UNDERRUBRIKER -->
    <main>
        <section id="burger" class="grid-container">

            <div id="burgerinfo">
                <h3>Choose your favourite burger!</h3>
                <p>Take a look at our lovely menu for real enjoyment</p>
            
                    <h1>Burgers</h1>
                    <div class="burger-grid">
                        <Burger v-for="burger in burgers"
                                v-bind:burger="burger" 
                                v-bind:key="burger.name"
                                v-on:updated-order="updatedBurgerOrder"
                        />
                    </div>
                </div>
        </section>


    <!-- KUNDINFORMATION -->
    <section id="customerinfo">
        <form>
        <h3>Customer Information</h3>
            <p><em>To handle your order in the best way we need this information from you:</em><br />
                <p>
                    <label for="Fullname">Full name:</label><br>
                    <input type="text" id="Fullname" v-model="userFullname" required="required" placeholder="Full name">
                </p>
                <p>
                    <label for="E-mail">E-mail:</label><br>
                    <input type="email" id="E-mail" v-model="email" required="required" placeholder="E-mail">
                </p>
            </p>

        <!-- BETALNING -->
            <p>
                <label for="Paymentmethod">Payment Method:</label><br>
                <select id="Paymentmethod" v-model="paymentMethod">
                    <option>Debitcard</option>
                    <option>Swish</option>
                    <option>Klarna</option>
                    <option>Apple Pay</option>
                    <option>Bitcoin</option>
                </select>
            </p>

        <!-- KÖN -->
            <p>
                <legend>Gender:</legend>

                <p>
                    <input type="radio" id="male" v-model="gender" value="male" checked="checked"/>
                    <label for="male">Male</label>
                </p>
                <p>
                    <input type="radio" id="female" v-model="gender" value="female"/>
                    <label for="female">Female</label>
                </p>
                <p>
                    <input type="radio" id="non-binary" v-model="gender" value="nonbinary"/>
                    <label for="non-binary">Non-binary</label>
                </p>

            </p>
        </form>

        <div id="map-container">
            <div id="map" v-on:click="setLocation" style="position: relative;">
                <div
                        v-if="location.x && location.y"
                        :style="{
                            position: 'absolute',
                            top: location.y + 'px',
                            left: location.x + 'px',
                            fontSize: '15px',
                            fontWeight: 'bold',
                            /*backgroundColor: 'red',*/
                            background: 'black',
                            color: 'white',
                            width:'20px',
                            height:'20px',
                            borderRadius: '50%',
                            display: 'flex',
                            justifyContent: 'center',
                            alignItems: 'center',

                        }">
                        T
                    </div>
                    <b>Pick your delivery adress</b>
                </div>
        </div>

        <button type="submit" v-on:click="addOrder">
            <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSMoFGpn3xJ96QyjR4LykyZJq4n-oEOIHVRNg&s" height="70px">
            <b>Place Order</b>
        </button>
    </section>

    </main>
    <footer></footer>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

function MenuItem(name, URLimage, kCal, containsLactose, containsGluten) {
  this.name = name; /*this gör att vi kopplar till just det objektet*/
  this.URLimage = URLimage;
  this.kCal = kCal;
  this.containsLactose = containsLactose;
  this.containsGluten = containsGluten;
}

/* const burgers = [
  new MenuItem('The Breakfast Burger', 'https://static.vecteezy.com/ti/gratis-foton/p2/47572652-frukost-skjutreglage-terar-mini-agg-smorgasar-med-bacon-och-cheddarost-ost-pa-reglaget-bullar-fotona.jpg', 250, false, false),
  new MenuItem('The Lunch Burger', 'https://eu-central-1.linodeobjects.com/tasteline/2015/09/ginas_burgare_med_pulled_chicken_och_coleslaw.jpg', 450, true, true),
  new MenuItem('The Dinner Burger', 'https://dynamic-media-cdn.tripadvisor.com/media/photo-o/25/18/0e/8a/original-smash-burger.jpg?w=800&h=-1&s=1', 850, true, true)
];
*/

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      userFullname: '',
      email: '',
      paymentMethod: 'Debitcard',
      gender: 'male',
      orderedBurgers: {},
      location: {x:25, y:25}
    };
  },
  methods: {
    setLocation: function (event) {
        var offset = {x: event.currentTarget.getBoundingClientRect().left,
            y: event.currentTarget.getBoundingClientRect().top};
    
        this.location = {x: event.clientX - 10 - offset.x,
                        y: event.clientY - 10 - offset.y}; 
        },


    getOrderNumber: function () {
      return Math.floor(Math.random()*100);
    },
    addOrder: function (event) {
        if (!this.userFullname || !this.email || !this.paymentMethod || !this.gender ||!this.location.x || !this.location.y){
            alert("Fyll i alla fält och välj leveransadress innan du beställer")
            return;
        } 

      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                fullName: this.userFullname,
                                email: this.email,
                                paymentMethod: this.paymentMethod,
                                gender: this.gender,   
                                details: this.location,
                                orderItems: this.orderedBurgers
                              },
                 );
        
                console.log("Order skickad!");
                /*console.log("Kundinformation:");
                console.log("Full Name:", this.userFullname);
                console.log("Email:", this.email);
                console.log("Payment Method:", this.paymentMethod);
                console.log("Gender:", this.gender);
                console.log("Delivery Location:", this.location);
                console.log("Ordered Items:", this.orderedBurgers);*/
                alert("Din beställning hanteras nu!")

    },
    handleSubmit() {
      console.log('Full name: ' + this.userFullname);
      console.log('Email: ' + this.email);

      console.log('Payment method: ' + this.paymentMethod);
      console.log('Gender: ' + this.gender);
    },

    updatedBurgerOrder(event) {
        if (event.number === 0) {
            delete this.orderedBurgers[event.name];
        } else {
            this.orderedBurgers[event.name] = event.number;
        }
        console.log(this.orderedBurgers);
    }
}
}

    </script>

<style>

  #map-container {
    width: 100%;
    height: 700px;
    overflow: scroll;
    margin-bottom: 100px;

  }  
  #map {
    width: 1920px;
    height: 1078px;
    background: url("/img/polacks.jpg") no-repeat center top;
    background-size: contain;
  }
  
@import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Edu+AU+VIC+WA+NT+Pre:wght@400..700&family=Oswald:wght@200..700&family=Sour+Gummy:ital,wght@0,100..900;1,100..900&display=swap');
body {
    font-family: 'Oswald';
    font-size: 12pt;
}

p {
    color: red;
}

main, header, footer, nav ul {
    max-width: 70rem;
    margin: 0 auto 0 auto;
}
main {
    background-color: white;
}

/* nav ul li {
    display: inline-block;
    background-color: grey;
    padding: 1em;
    margin: 1em;
} */


.header {
    overflow: hidden;
    width: 100%;
    height: auto;
    

}

.image {
    width: 100%;
    height: 200px;
    opacity: 0.75;
}

section {
    margin: 50px 0px;
}

header h1 { 
    font-family: 'Agbalumo';
    font-size: 42pt;
    padding: 65px 10px 20px 230px;
    width:40rem;
    margin: 0 auto;
    text-align: center;
    position: absolute;
    z-index: 2;
}

#burgerinfo {
    background-color: rgb(20, 20, 20);
    color: white;
    border: 5px dashed white;
    width: 100%;
    height: 650px;
    padding: 10px
}

#burgerinfo div {
    padding: 0px 10px 10px 20px;
}

#customerinfo {
    border: 5px dashed red;
    background-color: bisque;
    padding: 0px 20px 0px 20px;
}

button:hover {
    background-color: lightgreen;
    cursor: pointer;
}

button {
    margin: 0px 0px 50px 440px;
}

/*.myburgers{
    display: grid;
    grid-template-columns: 33% 33% 33%;
    grid-gap: 5px;
}
    */

.grid-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 0px;
    margin-right: 10px;
}


.burger-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(255px, 1fr));
    grid-gap: 15px;
    width: 100%;
    max-width: 1000px;
    margin: 0 auto;
    justify-content: center;
}

.burger-element{
    text-align: center;
}

.burger-element img{
    width: 100%;
    max-width: 300px;
}


</style>