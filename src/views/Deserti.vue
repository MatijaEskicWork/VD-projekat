<template>
<Header @osveziJezik="promenaJezika()"></Header>
<div v-if="this.jezik == 'srpski'">
    <div class="row">
        <div class="col-12">
            <h3>Pregled {{this.naziv}}</h3>
        </div>
    </div>
    <div class="row">
        <form>
            <input id="search" type="text" placeholder="Pretrazi recepte..." v-model="pretragaTekst"/>
            
            <button @click="pretraga()" type="button">
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-search" viewBox="0 0 16 16">
                    <path d="M11.742 10.344a6.5 6.5 0 1 0-1.397 1.398h-.001c.03.04.062.078.098.115l3.85 3.85a1 1 0 0 0 1.415-1.414l-3.85-3.85a1.007 1.007 0 0 0-.115-.1zM12 6.5a5.5 5.5 0 1 1-11 0 5.5 5.5 0 0 1 11 0z"/>
                </svg>
            </button>
            <div class="select">
                <select class ="sel form-control selcls" v-model="filter" data-trigger="" name="choices-single-default">
                    <option value="none">Sortiraj</option>
                    <option value="tr">Težina rastuće</option>
                    <option value="to">Težina opadajuće</option>
                    <option value="or">Ocena rastuće</option>
                    <option value="oo">Ocena opadajuće</option>
                </select>
            </div>
        </form>
    </div>
    <div v-for="(recept, index) in filterListe" :key=index>
        <recept-kartica @refreshListu="obrisi(recept)" v-if="recept.tip == this.tipRecepta" :id="recept.id" :korisnikDodao="recept.korisnikDodao" :tip="recept.tip" :ime="recept.ime" :tezina ="recept.tezina" :ocena="recept.ocena" :kratakOpis="recept.kratakOpis" :slika="recept.slika" :trajanje="recept.trajanje" :opisJela="recept.opisJela"></recept-kartica>
    </div>
</div>
<div v-else>
    <div class="row">
        <div class="col-12">
            <h3>Overview of appetizers</h3>
        </div>
    </div>
    <div class="row">
        <form>
            <input id="search" type="text" placeholder="Search for recipes..." v-model="pretragaTekst"/>
            
            <button @click="pretraga()" type="button">
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-search" viewBox="0 0 16 16">
                    <path d="M11.742 10.344a6.5 6.5 0 1 0-1.397 1.398h-.001c.03.04.062.078.098.115l3.85 3.85a1 1 0 0 0 1.415-1.414l-3.85-3.85a1.007 1.007 0 0 0-.115-.1zM12 6.5a5.5 5.5 0 1 1-11 0 5.5 5.5 0 0 1 11 0z"/>
                </svg>
            </button>
            <div class="select">
                <select class ="sel form-control selcls" v-model="filter" data-trigger="" name="choices-single-default">
                    <option value="none">Sort</option>
                    <option value="tr">Difficulty ascending</option>
                    <option value="to">Difficulty descending</option>
                    <option value="or">Grade ascending</option>
                    <option value="oo">Grade descending</option>
                </select>
            </div>
        </form>
    </div>
    <div class="kartice">
        <div v-for="(recept, index) in filterListe" :key=index>
            <recept-kartica @refreshListu="obrisi(recept)" v-if="recept.tip == this.tipRecepta" :id="recept.id" :korisnikDodao="recept.korisnikDodao" :tip="recept.tip" :ime="recept.ime" :tezina ="recept.tezina" :ocena="recept.ocena" :kratakOpis="recept.kratakOpis" :slika="recept.slika" :trajanje="recept.trajanje" :opisJela="recept.opisJela"></recept-kartica>
        </div>
    </div>
</div>
    <Footer></Footer>
</template>

<style>
/*v-if="recept.tip == this.tipRecepta"*/

form input[type=text] {
  padding: 10px;
  font-size: 15px;
  border: 1px solid grey;
  width: 20%;
  background: #f1f1f1;
}

form button {
  width: 8%;
  padding: 10px;
  background: #df5b0ea1;
  color: white;
  font-size: 15px;
  border: 1px solid grey;
  border-left: none; 
  cursor: pointer;
  margin-bottom: 2px;
}

form button:hover {
  background: #df5b0ebd;
}

/* Clear floats */
form::after {
  content: "";
  clear: both;
  display: table;
}

</style>

<script>
import ReceptKartica from '@/components/ReceptKartica.vue';
import Header from '@/components/Header.vue';
import Footer from '@/components/Footer.vue';
export default {
  components: { Header,
    Footer,ReceptKartica},
    
     name: 'Deserti',
     data() {
         return {
             link:'',
             naziv:'',
             tipRecepta:'',
             pretragaTekst: '',
             filter:'',
             listaRecepata:[],
             filtriranaLista:[],
             jezik:''
         }
     },
     computed: {
         filterListe() {
             return this.filtriranaLista;
         }
     },
     /*data() {
         return {
         }
             recepti:[]
         }
     },*/
    created(){
        this.ucitajPodatke();
        //this.recepti = JSON.parse(localStorage.getItem('recepti'));
        if (this.jezik == "engleski")
            this.listaRecepata = JSON.parse(localStorage.getItem("engleskiRecepti"));
        else 
            this.listaRecepata = JSON.parse(localStorage.getItem("listaRecepata"));
        this.filtriranaLista = this.listaRecepata;
        this.jezik = localStorage.getItem("jezik");
        document.title = 'Recepti';
    },
    methods: {
        promenaJezika() {
            window.location.reload();
            let jezik = localStorage.getItem("jezik");
        },
        obrisi(recept)
        {
            this.listaRecepata = JSON.parse(localStorage.getItem("listaRecepata"));
            this.filtriranaLista = this.listaRecepata;
        },
        ucitajPodatke() {
            this.naziv = 'deserata';
            this.tipRecepta = 'desert';
        },

        filtriraj() {
            let lst = this.filtriranaLista;
            let tmpPretraga = this.pretragaTekst;
            return lst.filter(function(el){
                return el.ime.toLowerCase().includes(tmpPretraga.toLowerCase());
            });
        },
        sortiraj() {
            let lst = this.filtriranaLista;
            if (this.filter == 'tr') {
                return lst.sort(function(a,b) {
                    return a.tezina - b.tezina;
                });
            }
            else if (this.filter == 'to') {
                return lst.sort(function(a,b) {
                    return b.tezina - a.tezina;
                });
            }
            else if (this.filter == 'or') {
                return lst.sort(function(a,b) {
                    return a.ocena - b.ocena;
                });
            }
            else if (this.filter == 'oo') {
                return lst.sort(function(a,b) {
                    return b.ocena - a.ocena;
                });
            }
        },
        pretraga() {
            this.filtriranaLista = this.listaRecepata;
            if (this.filter != 'none' && this.filter != '') {
                this.filtriranaLista = this.sortiraj();
            }
            if (this.pretragaTekst != '' && this.pretragaTekst != null) {
                this.filtriranaLista = this.filtriraj();
            }
        }
    }
}
</script>
