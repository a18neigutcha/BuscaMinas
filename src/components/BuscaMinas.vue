<template>
  <div id="Tablero" >
        <div class="niveles">
            <span>Nivel:</span>
            <div class="Nivel" v-on:click="cambiarNivel(1)">Nivel 1</div>
            <div class="Nivel" v-on:click="cambiarNivel(2)">Nivel 2</div>
            <div class="Nivel" v-on:click="cambiarNivel(3)">Nivel 3</div>
        </div>

        <div class="panel">
            <div class="Marcador_Minas">{{minas.length}}</div>
            <div class="Carita">
                <span v-on:click="iniciarNivel()">😀</span>
            </div>
            <div class="Marcador_Puntos">{{puntos}}</div>
        </div>
        <table class="cuadro">
                <tr v-for="(item ,indexX) in nivelActual.filas" v-bind:key="indexX">
                  <td class="ladrillo" v-on:click="mostrarCasilla(indexX,indexJ)" v-for="(item ,indexJ) in nivelActual.columnas" v-bind:key="indexJ">
                    <div v-if="cuadros[indexX][indexJ].visible">{{cuadros[indexX][indexJ].valor}}</div>
                    <div v-else> </div>
                  </td>
                </tr>
        </table>
    </div>
</template>

<script>
const PARTIDA_EN_CURSO=0;
const PARTIDA_FINALIZADA=1;


export default {
      data:function(){
        return{
        cuadros: [],
        nivelPrincipiante:{
            nivel: 1,
            filas: 5,
            columnas:5,
            minas:5
        },
        nivelIntermedio:{
            nivel: 2,
            filas: 16,
            columnas:16,
            minas:40
        },
        nivelExperto:{
            nivel: 3,
            filas: 16,
            columnas:30,
            minas:99
        },
        nivelActual:null,
        minas:[],
        puntos:0,
        estadoJuego:PARTIDA_EN_CURSO
        }
    },
    created() {
        this.nivelActual = this.nivelPrincipiante
        this.iniciarNivel();
    },
    methods: {
        iniciarNivel(){
            

            let filas = this.nivelActual.filas;
            let columnas= this.nivelActual.columnas;
          //  let totalCuadros = filas * columnas;
            this.puntos=0;
            this.minas=[];
            this.cuadros = [];
            this.estadoJuego=PARTIDA_EN_CURSO;
            //Valores vacions de los cuadros
            for( let i = 0; i< filas; i++){
                let ArFilas=[];
                for(let j=0;j<columnas;j++){
                    let cuadro = {
                        valor:0,
                        visible:false,
                        fila:i,
                        columna:j
                    }
                    ArFilas.push(cuadro);
                }
                this.cuadros.push(ArFilas);
            }

            //Posicion aleatoria minas
            for(let i=0;i<this.nivelActual.minas;i++){
                do{
                    var repetido=false;
                    var posicionX  = Math.floor(Math.random()*(this.nivelActual.filas-1));
                    var posicionY  = Math.floor(Math.random()*(this.nivelActual.columnas-1));
                    if(this.cuadros[posicionX][posicionY].valor == '💣'){
                        repetido=true;
                    }
                }while(repetido==true);

                this.cuadros[posicionX][posicionY].valor = '💣';
                this.minas.push(this.cuadros[posicionX][posicionY]);
            }
            //Valores casillas
            for(let i=0;i<filas;i++){
                for(let j=0;j<columnas;j++){
                    if(this.cuadros[i][j].valor!='💣'){
                        //console.log("No hay bomba "+i+":"+j);
                        let cuadro=this.cuadros[i][j];
                        for(let k=0;k<this.minas.length;k++){
                            let mina=this.minas[k];
                            if(Math.abs(cuadro.fila-mina.fila)==1 && Math.abs(cuadro.columna-mina.columna)==1 ){
                                this.cuadros[i][j].valor++;
                            }else if(Math.abs(cuadro.fila-mina.fila)==1 && Math.abs(cuadro.columna-mina.columna)==0 ){
                                this.cuadros[i][j].valor++;
                            }else if(Math.abs(cuadro.fila-mina.fila)==0 && Math.abs(cuadro.columna-mina.columna)==1 ){
                                this.cuadros[i][j].valor++;
                            }
                        }
                    }

                }
            }
            console.log(this.minas);
        },
        mostrarCasilla(x,y){

          if(this.estadoJuego==PARTIDA_EN_CURSO){
            //Varifica casilla no bomba y que no sea visible para sumar puntos.
            if(this.cuadros[x][y].valor!='💣' && this.cuadros[x][y].visible==false){
                this.puntos+=this.cuadros[x][y].valor;
            }
            //Varifica casilla bomba 
            if(this.cuadros[x][y].valor=='💣' && this.cuadros[x][y].visible==false){
                //Termina la partida
                this.estadoJuego=PARTIDA_FINALIZADA;


                //Muestra todas las bombas;
                for(let i=0;i<this.minas.length;i++){
                  let mina=this.minas[i];
                  this.cuadros[mina.fila][mina.columna].visible=true;
                }
            }

            this.cuadros[x][y].visible=true;
          }else{
            console.log("La partida termino, no puedes marcar mas casillas");
          }
        
            
            
        },
        cambiarNivel(nivel){
            if(nivel==this.nivelPrincipiante.nivel){
                this.nivelActual=this.nivelPrincipiante;
            }else if(nivel==this.nivelIntermedio.nivel){
                this.nivelActual=this.nivelIntermedio;
            }else if(nivel==this.nivelExperto.nivel){
                this.nivelActual=this.nivelExperto;
            }
            this.iniciarNivel();
        }

      }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#Tablero{
    background-color: gray;
}

.niveles{
    display: flex;
    justify-content: center;
    align-items: center;
}

.panel{
    display: flex;
    justify-content: center;
    align-items: center;
}

td{
    width: 40px;
    height: 40px;
    justify-content: center;
    align-items: center;
    background-color: #c0c0c0;
    font-size: 1.5em;
    border: 1px solid #808080;
}
</style>
