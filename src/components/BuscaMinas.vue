<template>
  <div id="Tablero" >


        <h4 v-if="partidaGanada()">¡¡GANASTE LA PARTIDA!!</h4>
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
                  <td class="ladrillo" v-on:click="mostrarCasilla(indexX,indexJ)"  v-on:contextmenu.prevent="marcarMina(indexX,indexJ)" v-for="(item ,indexJ) in nivelActual.columnas" v-bind:key="indexJ">
                    <div class="sinMina"  v-if="cuadros[indexX][indexJ].visible && cuadros[indexX][indexJ].valor==0"> </div>
                    <div v-else-if="cuadros[indexX][indexJ].visible">{{cuadros[indexX][indexJ].valor}}</div>
                    <div v-else-if="cuadros[indexX][indexJ].marcado">🏁</div>
                    <div v-else> </div>
                  </td>
                </tr>
        </table>
    </div>
</template>

<script>
const PARTIDA_EN_CURSO=0;
const PARTIDA_FINALIZADA=1;
const PARTIDA_FINALIZADA_GANASTE=3;


export default {
      data:function(){
        return{
        cuadros: [],
        nivelPrincipiante:{
            nivel: 1,
            filas: 5,
            columnas:5,
            minas:1
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
        casillasMarcadas:0,
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
            this.casillasMarcadas=0;
            this.estadoJuego=PARTIDA_EN_CURSO;
            //Valores vacions de los cuadros
            for( let i = 0; i< filas; i++){
                let ArFilas=[];
                for(let j=0;j<columnas;j++){
                    let cuadro = {
                        valor:0,
                        visible:false,
                        marcado:false,
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
        },
        mostrarCasilla(x,y){

          if(this.estadoJuego==PARTIDA_EN_CURSO){
            //Varifica casilla no bomba y que no sea visible para sumar puntos.
            if(this.cuadros[x][y].valor!='💣' && this.cuadros[x][y].visible==false){
                this.puntos+=this.cuadros[x][y].valor;
                this.mostrarCeros(x,y);
                if(this.partidaGanada()){
                    this.estadoJuego=PARTIDA_FINALIZADA_GANASTE;
                    this.mostrarMinas();
                }
            }
            //Varifica casilla bomba 
            if(this.cuadros[x][y].valor=='💣' && this.cuadros[x][y].visible==false){
                this.cuadros[x][y].valor='💥';
                //Termina la partida
                this.estadoJuego=PARTIDA_FINALIZADA;

                this.mostrarMinas();


                
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
        },
        marcarMina(x,y){
            if(this.cuadros[x][y].marcado)
                this.cuadros[x][y].marcado=false;
            else
                this.cuadros[x][y].marcado=true;
        },
        mostrarCeros(x,y){
            if( x<0 || y<0 || y>=this.nivelActual.columnas || x>=this.nivelActual.filas){
                return ;
            }
            if(this.cuadros[x][y].visible==false && this.cuadros[x][y].marcado==false){
                if(this.cuadros[x][y].valor=='💣'){
                    return ;
                }else if(this.cuadros[x][y].valor>0){
                    this.casillasMarcadas++;
                    this.cuadros[x][y].visible=true;
                    return ;
                }
                this.casillasMarcadas++;
                this.cuadros[x][y].visible=true;
                this.mostrarCeros(x+1,y);
                this.mostrarCeros(x,y+1);
                this.mostrarCeros(x-1,y);
                this.mostrarCeros(x,y-1);
                this.mostrarCeros(x+1,y+1);
                this.mostrarCeros(x-1,y-1);
                this.mostrarCeros(x-1,y+1);
                this.mostrarCeros(x+1,y-1);
            }
            

        },
        mostrarMinas(){
            //Muestra todas las bombas;
            for(let i=0;i<this.minas.length;i++){
                let mina=this.minas[i];
                this.cuadros[mina.fila][mina.columna].visible=true;
                if(this.cuadros[mina.fila][mina.columna].marcado){
                    this.cuadros[mina.fila][mina.columna].valor='❌';
                }
            }
        },
        partidaGanada(){
            let casillasSinMinas=this.nivelActual.filas*this.nivelActual.columnas-this.nivelActual.minas
            if(this.casillasMarcadas==casillasSinMinas){
                return true;
            }else{
                return false;
            }
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
    padding:0px
}
.sinMina{
    background-color: #9F9F9F;
    width: 100%;
    height:100%;
    border-color: #9F9F9F;
}
</style>
