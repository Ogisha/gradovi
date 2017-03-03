<template>
    <div id="app2" class="text-left">
        <div class="container">
            <h2 class="text-center">Rezultati</h2>
            <p style="width:80%;margin-left:10%;" id="proba1"> Ukupno pogodjenih: {{ pogodjeno }}</p>
            <p style="width:80%;margin-left:10%;">Vreme: {{ vreme }} sec </p>
            <h2 id="procenat">{{ vratiProcenat() }}%</h2>
            <div class="iznad">
                <div class="scorebar" v-bind:class="klasa">
                 <span id="spanId" class="h2"> {{ vratiProcenat() }}%</span>
                </div>
            </div>
            
            <div id="pokreniPonovo">
                 <span>Ako zelite da probate jos jednom, kliknite na dugme pored</span>
                 <button class="btn btn-info optionBtn2" type="button" @click="natrag">Opet?</button>
            </div>
       </div>
    </div>
</template>

<script>
    
    import { rezultatiBus} from '../main';
   
    export default {

        data: function() {
            return {
                pogodjeno: 0,
                vreme: 0,
                procenti: 0,
                klasa: ""
            };
        },
        
        methods: {
            natrag() {
                window.open("/index.html","_self");
            },
            
            vratiProcenat() {
                if (this.pogodjeno === 0) {
                    this.klasa = "pogodak0";
                    return "0";
                }
                
                if (this.pogodjeno === 1) {
                    this.klasa = "pogodak1";

                    return "25";
                }
                
                if (this.pogodjeno === 2) {
                    this.klasa = "pogodak2";
                    return "50";
                }
                if (this.pogodjeno === 3) {
                    this.klasa = "pogodak3";
                    return "75";
                }
                if (this.pogodjeno === 4) {
                    this.klasa = "pogodak4";
                    return "100";
                }
            }
        },
        
        created() {
            console.log("'Rezultat' Created");
            
            rezultatiBus.$on("finalnoVreme", (data) => {
                this.vreme = data;
            });
            
            rezultatiBus.$on("gotovaIgra", (data) => {
                this.pogodjeno = data;
            });
        },
  
  destroyed() {
    console.log("'Rezultat' Destroyed");
  }
    };
</script>

<style scoped>
    .iznad {
        width: 80%;
        height: 50px;
        margin-left:10%;
        border: 2px solid black;
        box-shadow: 2px 0 grey;
        border-radius: 8px;
        background: white;
    }
    
    .pogoci {
        background: green;
    }
    
    .promasaji {
        background: red;
    }
    
    #procenat {
        text-align: center;
        font-size: 2.05em;
    }
    
    #pokreniPonovo {
        width: 80%;
        margin-left: 10%;
        margin-top: 2%;
        color: white;
        font-size: 1.6em;
        letter-spacing: .1em;
        word-spacing: .1em;
    }
    
    button {
        float: right;
        padding-left: 3%;
        padding-right: 3%;
    }
    
    .pogodak0 {
        width: 0%;
        background: green;
        height: 46px;
    }
    .pogodak1 {
        width: 25%;
        background: green;
        height: 46px;
    }
    
    .pogodak2 {
        width: 50%;
        background: green;
        height:46px;
    }
    
    .pogodak3 {
        width: 75%;
        background: green;
        height: 46px;
    }
    .pogodak4 {
        width: 100%;
        background: green;
        height: 46px;
    }
    
    #spanId {
        color: white;
        float: right;
        margin: auto 0;
    }
</style>