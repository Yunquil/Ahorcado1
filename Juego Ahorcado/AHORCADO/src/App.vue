<template>
  <div class="container">
    <div class="header-container" v-if="juegoAhorcado === 1">
      <header>
        <h2>Juego de Ahorcado</h2>
        <div class="image-container">
          <img
            src="https://st4.depositphotos.com/10614052/25198/i/450/depositphotos_251983410-stock-photo-silhouette-of-male-suicider-going.jpg"
            alt="Imagen del juego">
        </div>
        <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Quod laudantium doloribus enim, deleniti facere ad
          sunt, recusandae praesentium earum cumque iure natus deserunt ducimus qui nobis itaque sed, dicta laboriosam!
        </p>
      </header>
      <button class="btn1" @click="juegoAhorcado = 2">Jugar</button>
    </div>
    <div class="container_dificultad" v-else-if="juegoAhorcado === 2">
      <h3>Seleccione la Dificultad</h3>
      <div class="container_boton">
        <button class="btn0" @click="seleccionarDificultad('Fácil')">Facil</button>
        <button class="btn0" @click="seleccionarDificultad('NORMAL')">Normal</button>
        <button class="btn0" @click="seleccionarDificultad('Difícil')">Difícil</button>
      </div>
      <button class="btn2" @click="juegoAhorcado = 3">Siguiente</button>
    </div>
    <div class="container1" v-else-if="juegoAhorcado === 3">
      <h1>Seleccione la categoría que desea</h1>
      <div class="categorias">
        <button class="btn3" @click="seleccionarCategoria('ANIMALES')">ANIMALES</button>
        <button class="btn3" @click="seleccionarCategoria('PAISES')">PAISES</button>
        <button class="btn3" @click="seleccionarCategoria('NOMBRES')">NOMBRES</button>
        <button class="btn3" @click="seleccionarCategoria('COLORES')">COLORES</button>
        <button class="btn3" @click="seleccionarCategoria('COSAS')">COSAS</button>
      </div>
      <button class="btn4" @click="juegoAhorcado = 4">comenzar Juego</button>
    </div>
    <div class="container2" v-else-if="juegoAhorcado === 4">
      <h1>AHORCADO</h1>
      <div class="info">
        <h3>Nivel: {{ dificultadSelec }}</h3>
        <h3>Categoría: {{ categoriaSelec }}</h3>
      </div>
      <img class="img2" :src="imagenError" alt="">
      <div v-if="juegoGanado">
        <p class="mensaje-ganado">¡Ganaste el juego!</p>
      </div>
      <p v-if="juegoPerdido" class="mensaje-perdido">Has perdido el juego</p>
      <h2 class="palabra_oculta">{{ mostrarPalabraOculta() }}</h2>
      <div class="container2_1">
        <button class="btn_letras" v-for="letter in alfabeto" :key="letter" @click="clickLetra(letter)"
          :disabled="usoLetras.includes(letter) || !puedeSeleccionarLetra(letter) || juegoGanado">{{ letter }}
        </button>
        <button class="btn5" @click="reiniciarJuego">R</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import {  ref } from 'vue';

const juegoAhorcado = ref(1);
const usoLetras = ref([]);
const alfabeto = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split('');
const dificultadSelec = ref('');
const categoriaSelec = ref('');
const dificultadError = ref(0);
const imagenError = ref('');

let errorCount = ref(0);
let palabraSecreta = ref('');
let juegoPerdido = ref(false);
let juegoGanado = ref(false);
let letrasRestantes = ref(0);

const nombreCategoria = {
  ANIMALES: ["PERRO", "GATO", "IGUANA", "LOBO", "SAPOPERRO","PATO","COCODRILO","ELEFANTE","TIGRE","ANACONDA","RANA","DINOSAURIO","SERPIENTE","RINOCERONTE","ARMADILLO"],
  PAISES: ["BRASIL", "MEXICO", "COLOMBIA", "JAPON", "AUSTRALIA","RUSIA","ARGENTINA","PERU","AFRICA","CHILE","ESTADOSUNIDOS","ALEMANIA","FRANCIA","CANADA","ESPAÑA"],
  NOMBRES: ["AMALIA", "LUIS", "MARIA", "CARLOS", "EDWIN","JOSE","KEVIN","ARTURO","JAIDER","DAMIAN","ALBEIRO","CAMILO","SERGIO","FELIPE","ANDRES","JULIANA","KATHERIN"],
  COLORES: ["ROJO", "AZUL", "VERDE", "AMARILLO", "MORADO","VIOLETA","NARANJA","DORADO","FUCCIA","MARRON","CAFE","PIEL","NEGRO","BLANCO","GRIS","VINOTINTO"],
  COSAS: ["AUTO", "LIBRO", "BICICLETA", "COMPUTADORA", "GLOBO","CELULAR","MOTOCIERRA","PISTOLA","GRANADA","TELEVISOR","LLANTA","SILLA","BOTELLA","CERVESA","CUCHILLO"],
};

const imagenesErrores = ref({
  'Fácil': [
    'https://i.postimg.cc/WpZxWbM2/img10.png',
    'https://i.postimg.cc/L6WMZZTV/img2.png',
    'https://i.postimg.cc/wBXKP4dW/img3.png',
    'https://i.postimg.cc/hjKFq8DK/img4.png',
    'https://i.postimg.cc/9Mb6X40Z/img5.png',
    'https://i.postimg.cc/3NwPkK06/img6.png',
    'https://i.postimg.cc/7hQj7pgL/img7.png',
    'https://i.postimg.cc/qMzP0vT6/img8.png',
    'https://i.postimg.cc/DyHtz3vj/img9.png',
    'https://i.postimg.cc/QMwh7YrS/img11.png'
  ],

  'NORMAL': [
    'https://i.postimg.cc/L6WMZZTV/img2.png',
    'https://i.postimg.cc/wBXKP4dW/img3.png',
    'https://i.postimg.cc/hjKFq8DK/img4.png',
    'https://i.postimg.cc/9Mb6X40Z/img5.png',
    'https://i.postimg.cc/7hQj7pgL/img7.png',
    'https://i.postimg.cc/DyHtz3vj/img9.png',
    'https://i.postimg.cc/QMwh7YrS/img11.png',
  ],
  'Difícil': [
    'https://i.postimg.cc/wBXKP4dW/img3.png',
    'https://i.postimg.cc/9Mb6X40Z/img5.png',
    'https://i.postimg.cc/7hQj7pgL/img7.png',
    'https://i.postimg.cc/DyHtz3vj/img9.png',
    'https://i.postimg.cc/QMwh7YrS/img11.png',
  ],
});

function clickLetra(letter) {
  if (!usoLetras.value.includes(letter)) {
    usoLetras.value.push(letter);
    if (palabraSecreta.value.includes(letter)) {
      if (letrasAdivinadas()) {
        juegoGanado = true;
      }
    } else {
      letrasRestantes.value--;
      errorCount.value++;
      if (errorCount.value < dificultadError.value) {
        mostrarImagenError();
      } else {
        juegoPerdido.value = true;
      }
    }
  }
}

function letrasAdivinadas() {
  for (const letra of palabraSecreta.value) {
    if (!usoLetras.value.includes(letra)) {
      return false;
    }
  }
  return true;
}

function seleccionarDificultad(dificultad) {
  dificultadSelec.value = dificultad;
  if (dificultad === 'Fácil') {
    letrasRestantes.value = 10;
    dificultadError.value = 11;
  } else if (dificultad === 'NORMAL') {
    letrasRestantes.value = 7;
    dificultadError.value = 8;
  } else if (dificultad === 'Difícil') {
    letrasRestantes.value = 5;
    dificultadError.value = 6;
  }
}

function puedeSeleccionarLetra(letter) {
  return letrasRestantes.value > 0 && !usoLetras.value.includes(letter);
}

function seleccionarCategoria(categoria) {
  categoriaSelec.value = categoria;
  palabraSecreta = ref(generarPalabraAleatoria(nombreCategoria[categoria]));
  palabraAdivinada.value = generarCadenaConGuiones(palabraSecreta.value);
}

function generarPalabraAleatoria(palabras) {
  const randomIndex = Math.floor(Math.random() * palabras.length);
  return palabras[randomIndex].toUpperCase();
}

function reiniciarJuego() {
  usoLetras.value = [];
  juegoPerdido.value = false;
  juegoGanado = false;
  letrasRestantes.value = (dificultadSelec.value === 'Fácil') ? 10 : (dificultadSelec.value === 'NORMAL') ? 7 : 5;
  palabraSecreta.value = generarPalabraAleatoria(nombreCategoria[categoriaSelec.value]);
  reiniciarImagenes();
}


function reiniciarImagenes() {
  errorCount.value = 0;
  imagenError.value = '';
}

function mostrarPalabraOculta() {
  let palabraMostrada = '';
  for (const letra of palabraSecreta.value) {
    if (usoLetras.value.includes(letra)) {
      palabraMostrada += letra;
    } else {
      palabraMostrada += '_';
    }
    palabraMostrada += ' ';
  }
  return palabraMostrada.trim();
}

function mostrarImagenError() {
  const errorIndex = errorCount.value - 1;
  if (errorIndex >= 0 && errorIndex < imagenesErrores.value[dificultadSelec.value].length) {
    imagenError.value = imagenesErrores.value[dificultadSelec.value][errorIndex];
  }
}
</script>


<style scoped>
.container {
  width: 100%;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

.header-container,
.container_dificultad,
.container1 {
  width: 100%;
  max-width: 800px;
  padding: 20px;
  text-align: center;
}

.mensaje-ganado,
.mensaje-perdido {
  font-size: 25px;
}

.image-container {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
}

.container_boton {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-top: 20px;
}

.btn1,
.btn2,
.btn4 {
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  margin-top: 20px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.btn5 {
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: 1px solid black;
  cursor: pointer;
}

.btn3 {
  padding: 10px 20px;
  background-color: #500404;
  color: #fff;
  margin: 20px;
  border-radius: 10px;
  border: none;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.img2 {
  width: 400px;
  max-height: 300px;
  border-radius: 15px;
  border: 1px solid black;
}

.container2 {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
}


.container2_1 {
  display: grid;
  grid-template-columns: repeat(9, 1fr);
  gap: 10px;
  margin-bottom: 20px;
}

</style>
