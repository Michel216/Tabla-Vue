<template>
  <div>
    <h1 class="titulo">Productos</h1>
    <div class="linea"></div>
<br>
    <div class="container my-container">
        <button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#Modal">
            Nuevos Productos
        </button>
    </div>
<div style=" justify-content: center;">
  <table>
    <thead>
        <tr>
            <th>Nombre</th>
            <th>Precio</th>
            <th>Costo</th>
            <th>Cantidad</th>
            <th>Proveedor</th>
            <th></th>
            <th>Ganancias</th>
            <th></th>
        </tr>
    </thead>
    <tbody>
        <tr v-for="(item, i) in data" :key="i">
            <td> {{ item.nombre }}</td>
            <td>{{ item.precio }}</td>
            <td>{{ item.costo }}</td>
            <td>{{ item.cantidad }}</td>
            <td>{{ item.proveedor }}</td>
            <td>
              <button class="custom-button" @click="mas(i)">➕</button>
              <button class="custom-button" @click="menos(i)">➖</button>
            </td>
            <td>{{ item.ganancias }}</td>
            <div class="error-message">{{ errorGanancia }}</div>
        </tr>
    </tbody>
</table>

<div class="modal fade" id="Modal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog modal-xs">
        <div class="modal-content">
            <div class="modal-header">
                <h1 class="modal-title fs-5" id="exampleModalLabel">Nuevos Productos</h1>
                <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <div class="modal-body">
                <div>
                    <input type="text" v-model="nombre" placeholder="Nombre">
                    <div class="error-message">{{ errorNombre }}</div>
                </div>
                <br>
                <div>
                    <input type="number" v-model="precio" placeholder="Precio">
                    <div class="error-message">{{ errorPrecio }}{{error}}</div>
                </div>
                <br>
                <div>
                  <input type="number" v-model="costo" placeholder="Costo">
                    <div class="error-message">{{ errorCosto }}</div>
                </div>
                <br>
                <div style="margin-bottom: 20px;">
                   <input type="text" v-model="proveedor" placeholder="Proveedor">
                    <div class="error-message">{{ errorProveedor }}</div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cerrar</button>
                    <button type="button" class="btn btn-primary" @click="guardar()">Guardar</button>
                </div>
            </div>
        </div>
    </div>
</div>
</div>
<br><br>
<div style="    background-color: #a31fcfdd; color: aliceblue; position: relative; bottom: -25px; width: 100%; padding: 30px 0;">
  <div class="totales">
    <h3>Precio Total del Inventario:</h3>
    <h3 class="precioTotal">{{precioTotal}}</h3>
  </div>

  <div class="totales">
    <h3>Costo Total del Inventario:</h3>
    <h3 class="costoTotal">{{costoTotal}}</h3>
  </div>

  <div class="totales">
    <h2>Ganancia Total:</h2>
    <h2 class="gananciaTotal">{{gananciaTotal}}</h2>
  </div>
</div>
</div>
</template>

<script setup>
import { ref } from "vue";

let nombre = ref("");
let precio = ref("");
let costo = ref("");
let proveedor = ref("");

let data = ref([]);
let costoTotal = ref(0); 
let precioTotal = ref(0); 
let gananciaTotal = ref(0);

const errorNombre = ref("");
const errorPrecio = ref("");
const errorCosto = ref("");
const errorProveedor = ref("");
const error = ref("");
const errorGanancia = ref("");

let cantidad = ref(1);

function validarFormulario() {
  errorNombre.value = !nombre.value ? "Por favor, ingresa el Nombre." : "";
  if (errorNombre.value) {
    setTimeout(() => {
      errorNombre.value = "";
    }, 3000);
  }

  errorPrecio.value = !precio.value || precio.value < 0 ? "Por favor, ingresa el precio correctamente." : "";
  if (errorPrecio.value) {
    setTimeout(() => {
      errorPrecio.value = "";
    }, 3000);
  }

  errorCosto.value = !costo.value || costo.value < 0 ? "Por favor, ingresa el costo correctamente." : "";
  if (errorCosto.value) {
    setTimeout(() => {
      errorCosto.value = "";
    }, 3000);
  }

  errorProveedor.value = !proveedor.value ? "Por favor, ingresa el Proveedor." : "";
  if (errorProveedor.value) {
    setTimeout(() => {
      errorProveedor.value = "";
    }, 3000);
  }
  error.value = precio.value < costo.value ? "Precio no puede ser menor a costo" : "";
  if (error.value) {
    setTimeout(() => {
      error.value = "";
    }, 3000);
  }

  return !(errorNombre.value || errorPrecio.value || errorCosto.value || errorProveedor.value || error.value);
}

function guardar() {
  if (validarFormulario()) {
    let productos = {
      nombre: nombre.value,
      precio: precio.value,
      costo: costo.value,
      cantidad: cantidad.value,
      proveedor: proveedor.value,
    };
    data.value.push(productos);

    if (productos.ganancias < 0) {
      errorGanancia.value = "Ganancia negativa.";
      if (errorGanancia.value) {
        setTimeout(() => {
          errorGanancia.value = "";
        }, 3000);
        return !(errorGanancia.value);
      }
    }
    actualizarTotales();
     nombre: "",
      precio: "",
      costo: "",
      proveedor: "",
  }
}

function actualizarTotales() {
  precioTotal.value = data.value.reduce((total, producto) => total + producto.precio * producto.cantidad , 0);
  costoTotal.value = data.value.reduce((total, producto) => total + producto.costo * producto.cantidad, 0);
  gananciaTotal.value = precioTotal.value - costoTotal.value;
  data.value.forEach(producto => {
    producto.ganancias = (producto.precio - producto.costo) * producto.cantidad;
  });
}

function mas(index) {
  data.value[index].cantidad++;
  actualizarTotales();
}

function menos(index) {
  if (data.value[index].cantidad > 0) {
    data.value[index].cantidad--;
    actualizarTotales();
  }
}
</script>

<style>
  @font-face {
    font-family: "ARCADE V3";
    src: url("https://db.onlinewebfonts.com/t/c4d8c39ba1cbb5a1c79ca02b43389c24.eot");
    src: url("https://db.onlinewebfonts.com/t/c4d8c39ba1cbb5a1c79ca02b43389c24.eot?#iefix")format("embedded-opentype"),
    url("https://db.onlinewebfonts.com/t/c4d8c39ba1cbb5a1c79ca02b43389c24.woff2")format("woff2"),
    url("https://db.onlinewebfonts.com/t/c4d8c39ba1cbb5a1c79ca02b43389c24.woff")format("woff"),
    url("https://db.onlinewebfonts.com/t/c4d8c39ba1cbb5a1c79ca02b43389c24.ttf")format("truetype"),
    url("https://db.onlinewebfonts.com/t/c4d8c39ba1cbb5a1c79ca02b43389c24.svg#ARCADE V3")format("svg");
}
 .titulo{
  font-family: 'ARCADE V3', sans-serif;
    font-size: 50px;
    text-align: center;
    color: #871bab;
    font-style: italic; 
padding: 20px;
}
.linea{
   border: none;
    height: 5px ;
    background-color: #871bab;  
width: 80%;
align-content: center;
margin: auto;
border-radius: 10px;
}
.btn-primary{
  display: flex;
  justify-content: flex-end;
  margin-right: 35px;
  border: none;
 font-size: 17px;
  color: aliceblue;
  background-color: #a31fcf; 
  border-radius: 10px;
}
.my-container {
  display: flex;
  justify-content: flex-end;
}
.custom-button {
    background-color: transparent; 
    color: #fff; 
    border: none; 
    border-radius: 4px; 
    padding: 8px 12px; 
    cursor: pointer; 
}

.custom-button:hover {
    background-color: #a31fcfb4; 
}
input[type="text"],
input[type="number"] {
  text-align: start;
  width: 80%; 
  height: 40px;
  margin: 0 auto; 
  display: block;
}
input.error
{
    border: 2px solid red;
}

.error-message {
  color: red;
}
  table {
    margin: auto;
    margin-top: 30px;
    width: 70%; 
border-radius: 10px;
    border-collapse: collapse; 
    text-align: center;
    box-shadow: 0px 0px 20px #871bab;
  }

  tr {
    border-bottom: 1px solid  #871bab;
    text-align: center; 
    padding: 8px; 
    color: black;
  }
  tr:last-child {
    border-bottom: 0px solid  #9900ff;
    text-align: center; 
    padding: 8px; 
    color: black;
  }
  th{
  font-size: 110%;
  }
  .btn {
    padding: 8px 16px;
    border-radius: 10px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.btn:hover {
    transform: scale(1.05); 
}
.modal-header {
 background-color: #a31fcf;
 color: #fff;
 text-align: center;
}
#Modal {
  margin: auto;
  backdrop-filter: blur(8px);
}
.totales{
  display: flex; 
  gap: 10px;
  text-align: center;
  justify-items: center;
  justify-content: center;
  align-content: center;
  align-items: center;
}
.btn-primary{
  width: auto; 
  height: 40px !important;
}
.btn-primary:hover{
  background-color: #a31fcf;
}

</style>
