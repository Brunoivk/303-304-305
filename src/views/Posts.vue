<template>
  <div v-if="store.authenticated">
    <button @click="newImage()" type="Novi post" class="btn btn-primary ml-2">Post new image</button>
    <div @click="gotoDetails(card)" :key="card.id" v-for="card in cards">
      <InstagramCard :info="card"/>
    </div>
  </div>
</template>

<script>
import _ from 'lodash'
import InstagramCard from "@/components/InstagramCard.vue";
import store from "@/store.js";

export default {
  data() {
    return {
      store,
      cards: [],
    }
  },

 // 304 
   /*  // ovo ako imamo vise servisa(ruta) UGNJEZĐIVANJE SERVISA
  fetch("https://api.exchangeratesapi.io/latest?symbols=HRK")  // salje pro mise nakon sto se obecanje izvrsi ide then

  .then((response) => {   // response nije json vec do jasona treba doci
    return response.json()
  })
  .then((json) => {  // tada onaj gore json response moze u konzolu ispisati rez
    let kuna = json.rates.HRK
    console.log("1 euro je : ",kuna, "kn")
    

    
    return fetch("https://api.coindesk.com/v1/bpi/currentprice.json")
        .then((response) => {   // response nije json vec do jasona treba doci
          return response.json()
   })
        .then((json) =>{
          let bc = json.bpi.EUR.rate_float
     
     console.log("vrijednost Bit coina u eurima je",bc, "EUR")
     let rez = bc*kuna;
     console.log("Vrijednost u kunama je ",rez)
   }) 
   
  }) */

async mounted(){ 
// sa asinc i await  305 
  let r = await fetch("https://api.exchangeratesapi.io/latest?symbols=HRK") 
  let kun = await r.json()
  let kuna = kun.rates.HRK
  console.log("1 euro je : ",kuna, "kn")

  let r2 = await fetch("https://api.coindesk.com/v1/bpi/currentprice.json")
let eur = await r2.json()
let euro = eur.bpi.EUR.rate_float
console.log("vrijednost Bit coina u eurima je",euro, "EUR")
let rez = kuna*euro
 console.log("Vrijednost u kunama je ",rez, "kn")

  
  
   // 303 dolje ispod komentara 

  
 


   /* Promise.all([
    fetch("https://api.exchangeratesapi.io/latest?symbols=HRK") ,
    fetch("https://api.coindesk.com/v1/bpi/currentprice.json")
   ])
     .then((response) => {   // response nije json vec do jasona treba doci
    return response.json()
  })
  .then((json) => {  // tada onaj gore json response moze u konzolu ispisati rez
    let kuna = json.rates

    Promise.all([kuna[0].json(), rezultati[1].json()])
        .then((response) => {   // response nije json vec do jasona treba doci
    return response.json()
   })
   .then((rez) =>{
     let eur = rez.bpi.EUR.rate_float
     
     console.log("vrijednost u eurima",eur, "EUR")
   })  */
  
},




/* mounted(){   // ovo ako imamo vise servisa(ruta) UGNJEZĐIVANJE SERVISA
  fetch("http://localhost:3000/power?a=4&b=3")  // salje promise nakon sto se obecanje izvrsi ide then

  .then((response) => {   // response nije json vec do jasona treba doci
    return response.json()
  })
  .then((json) => {  // tada onaj gore json response moze u konzolu ispisati rez
    let rezultat_mnozenja = json.rezultat

    return fetch(`http://localhost:3000/add?a=${rezultat_mnozenja}&b=10`)
  })

  .then((response) => {   // response nije json vec do jasona treba doci
    return response.json()
   })
   .then((krajnji) =>{
     console.log("Krajnji", krajnji.rezultat)
   }) 
}, */

/* mounted(){
  fetch("http://localhost:3000/power?a=4&b=3")  // salje promise nakon sto se obecanje izvrsi ide then
  .then((response) => {   // response nije json vec do jasona treba doci
    return response.json()
  })
  .then((json) => {  // tada onaj gore json response moze u konzolu ispisati rez
    console.log(json)
  })
}, */

 // ovo je isto kao i iznad 
/* mounted(){
 let p1 = fetch("http://localhost:3000/power?a=4&b=3")
  let p2 = p1.then((response) => {   // response nije json vec do jasona treba doci
    return response.json()
  })
  p2.then((json) => {  // tada onaj gore json response moze u konzolu ispisati rez
    console.log(json)
  })
}, */

  watch: {
    "store.searchTerm": _.debounce(function(val) {this.fetchPosts(val)}, 500)
    //"store.searchTerm": function(val) {this.fetchPosts(val)}
  },
  created() {
    this.fetchPosts()
  },
  name: "posts",
  methods: {
     // 303
    // paralelno pozivanje na backendu s vise upita 
    async fetchPosts(term) {
      term = term || store.searchTerm

      let pretraga = async (term) =>{
      let response = await fetch(`http://localhost:3000/posts?_any=${term}`)
      let data = await response.json()
      return data // promise(array svi postova koji udovoljavaju term)
    } 

    let terms = term.split(" ")
    console.log("terms", terms)

    let promises = terms.map(e => pretraga(e)) //  za svaki termin pretrage vratili promise
    console.log("promises", promises)

    let results = await Promise.all(promises) // promise sacekali s promise all i await
    console.log("results", results)

    let rezultat = results.reduce( (final, e) => final.concat(e), []) // pomocu reducea promjenili u jednu listu
    console.log("rezultat", rezultat)

    let data = rezultat

    // redupliciranje 

    let finalni = {}  
    data.forEach(post => finalni[post.id]= post)
    console.log("finalni",finalni)

    data = Object.values(finalni)





    

          console.log("Podaci s backenda", data)
          this.cards = data.map(doc => {
            return {id: doc.id, url: doc.source, email: doc.createdBy, title: doc.title, posted_at: Number(doc.postedAt)}
          })
        
    },
    gotoDetails(card) {
      this.$router.push({path: `post/${card.id}`})
    },
    newImage() {
      this.$router.push({name: 'newpost'}).catch(err => console.log(err))
    }
  },
  components: {
    InstagramCard
  },
}
</script>

<style scoped>
  button {
    margin-bottom: 20px
  }
</style>