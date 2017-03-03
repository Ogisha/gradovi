<template>
    <div class="container-fluid">
        <h1  @mouseleave="izbrisi">Gradovi Srbije - <a v-bind:href="googlePretraga" target="_blank" style="text-decoration:none" @mouseenter="opisi">
        <small><strong> {{ vratiGrad() }}</strong></small></a></h1>
        <span class="opisi"></span>
        <div class="row">
          <div class="col-sm-6 col-md-8">
            <div class="row">
              
              <div class="col-sm-6">
                <p class="text-left info">OBLAST: <span id="oblastIspis"></span></p>
              </div>
              
              <div class="col-sm-6">
                <p class="text-right info">VREME: <span id="secIspis"></span></p>
              </div>
            </div>
            
            <input type="text"
                   v-bind:placeholder="preostalo"
                   id="acomplete"
                   @keyup="kucanje"
                   v-model="grad"
                   disabled><br>
                   
            <div class="row">
              
              <div class="col-xs-6 col-md-7 autocompletePodaci">
                <span id="ispisi"></span>
              </div>
              
              <div class="col-xs-6 col-md-5">
                <span v-if="uToku">
                  <button
                          type="button"
                          id="dodGr"
                          class="btn btn-info optionBtn"
                          @click="addFn(grad)"
                          style="float:right;">DODAJ U LISTU</button>
                </span>
                <span v-else>
                  <button
                          type="button"
                          class="btn btn-info optionBtn"
                          @click="zapocni"
                          style="float:right;">POKRENI IGRU!</button>
                </span>
              </div>
            </div>
          </div>
          
          <div class="col-sm-6 col-md-4">
            
            <div id="ubaceniGradovi">
              <ul class="list-group ubGr">
                <li class="list-group-item" v-for="gr in gradovi">{{ gr }}
                <button
                        type="delete"
                        id="delBtn"
                        @click="funk(gr)">X</button>
                </li>
            </ul><hr>
              <p class="preostaloGr">Preostalo gradova: {{ gradoviMax }}</p>
            </div>
          </div>
          
        </div>
        <div id="bottomBtn" class="row">
          
          <div class="col-sm-6 col-md-3">
            <button
                    type="button"
                    id="optionBtn1"
                    class="optionBtn btn btn-primary"
                    data-toggle="modal" 
                    data-target="#arhivaAkcija">ARHIVA AKCIJA</button>
          </div>
          
          <div class="col-sm-6 col-md-3">
            <button
                    type="button"
                    id="optionBtn2"
                    class="optionBtn btn btn-primary"
                    @click="novaPoz">NOVA POZADINA</button>
          </div>
          
          <div class="col-sm-6 col-md-3">
            <button
                    type="button"
                    id="optionBtn3"
                    class="optionBtn btn btn-warning"
                    @click="resetFn">RESETUJ SVE</button>
          </div>
          
          <div class="col-sm-6 col-md-3">
            <button
                    type="button"
                    id="optionBtn4"
                    class="optionBtn btn btn-success"
                    @click="zavrsiIgru(gradovi)">ZAVRSI IGRU</button>
          </div>
        </div>
        
        <div id="history">

          <div id="arhivaAkcija" class="modal fade" role="dialog">
            <div class="modal-dialog">
  
              <div class="modal-content">
                <div class="modal-header">
                  <button type="button" class="close" data-dismiss="modal">&times;</button>
                  <h4 class="modal-title ">Arhiva akcija:</h4>
                </div>
                
                <div class="modal-body">
                    <ul class="list-group">
                      <li class="list-group-item text-left" v-for="i in history"><em>{{ i.datum}}</em> - {{i.zapis}}</li>
                    </ul>
  
                  <div class="modal-footer">
                    <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
</template>

<script>
    
import { rezultatiBus } from '../main'

export default {
  name: 'app',
  data () {
    return {
      grad: null,
      gradovi: [],
      gradoviMax: 4,
      history: [],
      uToku: false,
      oblast: "",
      vreme: 0,
      pogodaka: 0,
      zavrseno: false,
      preostalo: "",
      pozadinaNo : 1,
      googlePretraga: "https://www.google.rs/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=beograd,+srbija&*",
      gameOver: false
    };
  },
  methods: {
    
    vratiGrad() {
        if (this.pozadinaNo === 1) {
            this.googlePretraga = "https://www.google.rs/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=beograd,+srbija&*";
            return "Beograd";
        }
        if (this.pozadinaNo === 2) {
            this.googlePretraga = "https://www.google.rs/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=sombor,+srbija&*";
            return "Sombor";
        }

        if (this.pozadinaNo === 3) {
            this.googlePretraga = "https://www.google.rs/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=kikinda,+srbija&*";
            return "Kikinda";
        }
        if (this.pozadinaNo === 4) {
            this.googlePretraga = "https://www.google.rs/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=nis,+srbija&*";
            return "Nis";
        }
        if (this.pozadinaNo === 5) {
            this.googlePretraga = "https://www.google.rs/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=subotica,+srbija&*";
            return "Subotica";
        }
    },
    
    zapocni() {
        
      this.uToku = true;
      $("#acomplete").removeAttr("disabled");
      this.snimi("Upravo je zapocela nova igra");
      this.preostalo = "Preostalo je jos " + this.gradoviMax + " gradova";
      
      $.getJSON("podaci.json", function(data) {
        this.oblast = data.oblast;
        this.vreme = data.vreme;
        
        $("#oblastIspis").text(this.oblast);
        $("#secIspis").text(this.vreme);
 
        var broj = this.vreme;
        
        var x = setInterval(function() {
            
            broj--;
            $("#secIspis").text(broj);
            if (broj === 0)
             clearInterval(x);
            
            $("#optionBtn3, #optionBtn4").click(function() {
                clearInterval(x);
            });
        }, 1000);
      });
    },
    
    funk(gradovi) {
      this.snimi("Izbrisan grad '" + this.gradovi[this.gradovi.indexOf(gradovi)] + "'");
      this.gradovi.splice(this.gradovi.indexOf(gradovi), 1);
      this.gradoviMax++;
      
      if ((this.gradoviMax !== 0) && (this.gradoviMax !== 1))
        this.preostalo = "Preostala su jos " + this.gradoviMax + " grada";
                 
      else {
        if (this.gradoviMax === 0)
            this.preostalo = "Uneli ste dozvoljeni broj gradova.";
        if (this.gradoviMax == 1)
            this.preostalo = "Preostao je jos " + this.gradoviMax + " grad";
      }
    },
    
    kucanje() {
        
      var pretraga = $("#acomplete").val();
      var myRegExp = new RegExp(pretraga, "i");
      
      $.getJSON("podaci.json", function(data) {
        var output = "<ul class='list-group ispisLista'>";
    
        $.each(data, function (key, val) {
            for (var i = 0; i < val.length -4; i++) {
              if ((val[i].search(myRegExp) != -1))
                output += "<li class='list-group-item searchLiItems'>" + val[i] + "</li>";
            }
            
            output += "</ul>";
            
        $("#ispisi").html(output);
        });
        
        $(".searchLiItems").on("mouseenter", function() {
            $("#acomplete").val($(this).text());
            $(this).css({
                "background": "cornflowerBlue",
                "color": "white",
                "text-shadow": "1px 0 black",
                "cursor": "pointer"
                });
        }).on("mouseleave", function() {
            $(this).css({
                "background": "white",
                "color": "black",
                "text-shadow": "none"
                });
        });
        
        $(".searchLiItems").on("click", function() {
            this.grad = $(this).text();
            $("#acomplete").val($(this).text());
            $("#ispisi").text("");
        });
      });
    },

    snimi(poruka) {
      var dat = new Date();
      var dan = dat.getDate();
      var mesec = dat.getMonth();
      mesec++;
      var godina = dat.getFullYear();
      var sat = dat.getHours();
      var minute = dat.getMinutes();
      if (minute < 10)
        minute = "0" + minute;
      var datum = dan + "/" + mesec + "/" + godina + "  " + sat + ":" + minute;
      
      var novi = {
        datum: datum,
        zapis: poruka
      };

      this.history.push(novi);
    },
    
    addFn(grad) {
        
        if (this.gradoviMax <= 0) {
            alert("Unijeli ste dozvoljen broj gradova, kliknite na 'ZAVRSI IGRU' da vidite rezultat, ili editujte listu");
            this.grad = "";
        }
            
        else {
            if (($("#acomplete").val() === "") || (this.gradovi.indexOf($("#acomplete").val()) != -1)) {
                if ($("#acomplete").val() === "")
                    alert("Neispravan unos!");
                if (this.gradovi.indexOf($("#acomplete").val()) != -1)
                    alert("Grad vec postoji!");
            }
                
            else {
                this.gradovi.push($("#acomplete").val());
                this.gradoviMax--;
                this.snimi("Dodan grad '" + $("#acomplete").val() + "'");
                this.grad = "";
                
                if ((this.gradoviMax !== 0) && (this.gradoviMax !== 1))
                 this.preostalo = "Preostala su jos " + this.gradoviMax + " grada";
                 
                 else {
                    if (this.gradoviMax === 0)
                        this.preostalo = "Uneli ste dozvoljeni broj gradova.";
                    if (this.gradoviMax == 1)
                        this.preostalo = "Preostao je jos " + this.gradoviMax + " grad";
                 }
            }
        }
    },
    
    resetFn() {
      if (this.gradovi.length === 0)
        alert("Niste uneli nijedan grad!");

      $("#optionBtn3").addClass("provera");
      this.gradovi = [];
      this.gradoviMax = 4;
      this.snimi("Igra je resetovana!");
      this.uToku = false;
      $("#oblastIspis").text("");
      $("#secIspis").empty();
      $("#acomplete").val();
      this.grad = "";
      $("#acomplete").attr("disabled", "disabled");
      this.preostalo = "";
    },
    
    pogodjeno(pg) {
      this.pogodak = pg;
      alert(this.pogodak);
    },
    
    zavrsiIgru(gradovi) {
      var time = 100 - $("#secIspis").text();
      var pogodak = 0;
      
      $.ajaxSetup({
        async: false
      });
      
      $.getJSON("podaci.json", function(data) {
        for (var i = 0; i < data.tacno.length; i++) {
          for (var j = 0; j < gradovi.length; j++) {
            if (data.tacno[i] == gradovi[j])
              pogodak++;
          }
        }
        
      });
      
      this.vreme = time;
      rezultatiBus.$emit("finalnoVreme", this.vreme);
      this.pogodaka = pogodak;
      rezultatiBus.$emit("gotovaIgra", this.pogodaka);
      
      $.ajaxSetup({
        async: true
      });
      
      this.gameOver = true;
      this.$emit("GameOver", this.gameOver);
  
    /* window.open("/rezultat.html","_self");*/
    },
    
    novaPoz() {
        
        var temp = "";
        
        if (this.pozadinaNo >= 5) {
            this.pozadinaNo = 1;
            temp = "url(/src/assets/" + this.pozadinaNo + ".jpg)";

            $("body").css({
                "background-image": temp,
                "background-size": "cover"
            });
        }
        
        else {
            this.pozadinaNo++;
            console.log(this.pozadinaNo);
        
            temp = "url(/src/assets/" + this.pozadinaNo + ".jpg)";
            $("body").css({
                "background-image": temp,
                "background-size": "cover"
            });
        }
    },
    
    opisi() {
        
        var br = 0;
        var x = setInterval(function() {
            var tekst =" Pogledaj vise na netu";
        var str = tekst.split("");
        
        $(".opisi").append(str[br]);
        br++;
        if(br == str.length)
            clearInterval(x);
        $("h1").on("mouseleave", function() {
            clearInterval(x);
        });
        }, 25);
    },
    
    izbrisi() {
         $(".opisi").text("");
        
    }
  },
  
  created() {
    console.log("'Main' Created")
    this.snimi("Dobrodosli u igru! Zabavite se :-)");
  },
  
  destroyed() {
    console.log("'Main' Destroyed");
  }
};
</script>

<style>

  body {
    background-image: url(/src/assets/1.jpg);
    background-size: cover;
  }

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0 5%;
  padding: 0 2%;
}

.col-md-8 {
  border: 2px solid white;
  border-radius:8px;
  box-shadow: 1px 2px black;
  padding: 4% 2%;
  background-color:rgba(135,206,250,.2);
  height: 400px;
}

h1, h2 {
  font-weight: normal;
  letter-spacing: .05em;
  font-size: 4em;
  text-shadow: 1px 2px white;
  margin-top: .5%;
  margin-bottom: 4%;
  font-family: Geneva;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 5px 10px;
}

p {
  font-size: 1.6em;
  letter-spacing: .01em;
  color: white;
  text-shadow: 1px 1px black;
}

a {
  color: #42b983;
}

#ubaceniGradovi {
  background-color:rgba(135,206,250,.2);
  border: 2px solid white;
  border-radius:8px;
  box-shadow: 1px 2px black;
  height: 400px;
  overflow: auto;
  overflow-x: hidden;
}

#acomplete {
  width: 100%;
  padding: 1%;
  margin: 2% 0;
}

.dugmad {
  padding: 2% 4%;
  font-size: 1.2em;
  letter-spacing: .1em;
  float: right;
}

#delBtn {
  float:right;
  border: none;
  background: white;
  font-size: 1.2em;
  font-weight: 700;
}

.preostaloGr {
  border: 1px solid black;
  position: relative;
  margin-bottom: 0;
}

hr {
  width: 60%;
}

.optionBtn {
  width: 85%;
  /*padding: 4%;*/
  font-weight: bolder;
  letter-spacing: .05em;
}

#bottomBtn {
  margin-top: 4%;
}

#optionBtn3 {
  background-color:rgba(255,160,122,.7);
}

#optionBtn3:hover {
  background-color: #f0ad4e;
}

#optionBtn4 {
  background-color:rgba(102,205,170,.7);
}

#optionBtn4:hover {
  background-color: #5cb85c;
}

#optionBtn1, #optionBtn2 {
  float: left;
  margin-left: -15px;
  background-color:rgba(135,206,250,.7);
}

#optionBtn1:hover, #optionBtn2:hover {
  background-color: #428bca;
}

#optionBtn4, #optionBtn3{
  float: right;
}

.searchLiItems {
  width: 50%;
  font-size: .65em;
  padding: .5%;
}

.autocompletePodaci {
  position: relative;
  height: 180px;
  overflow: auto;
}

#arhivaAkcija {
    background: url(/src/assets/ns.jpg);
    background-size: cover;
}

#history {
    font-family: Geneva;
    font-size: 1.1em;
}

.opisi {
    position: absolute;
    top: 3%;
    right:2%;
    font-family: Monospace;
    font-size: 1.6em;
}

@media screen and (max-width: 1200px) {
    .opisi {
        font-size: 1em;
    }
    
    h1 {
        margin-top: 2%;
        font-size: 2.75em;
    }
}

@media screen and (max-width: 995px) {
    #optionBtn1, #optionBtn2, #optionBtn3, #optionBtn4 {
        float: none;
        margin-left: 0;
    }
    
    .optionBtn {
        font-weight: 400;
        font-size: .9em;
    }
 
    .opisi {
        font-size: .8em;
    }
    
    h1 {
        font-size: 2.55em;
    }
}

@media screen and (max-width: 792px) {
    
    p {
        font-size: 1.2em;
    }

    .col-md-8 {
        margin-bottom: -3%;
        height: 280px;
    }

    #ubaceniGradovi {
        width:105%;
        height: 280px;
        margin-left: -2.5%;
    }
}

@media screen and (max-width: 700px) {
    .opisi {
        font-size: .65em;
    }
    
    h1 {
        font-size: 2.55em;
    }
}

@media screen and (max-width: 700px) {
    .opisi {
        font-size: .5em;
    }
    
    h1 {
        font-size: 2.25em;
    }
}
</style>
