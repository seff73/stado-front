<!-- src/App.vue -->
<template>
  <div id="app">
    <div class="table-container">
      <h1>Team Eleva: Horas de trabajo y disponibilidad</h1>
      <br/>
      <table border="1">
        <thead>
          <tr>
            <th>Nombre</th>
            <th>Email</th>
            <th>Horas Asignadas</th>
            <th>Estado</th>
            <th>Última Actualización</th>
            <th>Acciones</th>
          </tr>
        </thead>
        <tbody>
        <tr v-for="(row, index) in rows" :key="index">
          <td>{{ row.NOMBRE }}</td>
          <td>{{ row.EMAIL }}</td>
          <td><input v-model="row.HORAS" /></td>
          <td>
            <!-- Círculo de color -->
            <button class="dropdown-icon" @click="toggleDropdown(index)">
              <div class="status-circle" :style="{ backgroundColor: getStatusColor(row.ESTADO) }"></div>
              ▼
            </button>
            <!-- Select para cambiar el estado -->
            <select
            v-if="activeDropdown === index"
            v-model="row.ESTADO"
              @change="updateRow(index)"
              class="hidden-select"
            >
              <option value="Verde">Verde</option>
              <option value="Amarillo">Amarillo</option>
              <option value="Rojo">Rojo</option>
            </select>
          </td>
          <td>{{ row.FECHA }}</td>
          <td><button @click="updateRow(index)">Guardar</button></td>
        </tr>
      </tbody>
    </table>
  </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      rows: [],
      activeDropdown: null, // Índice del select activo
    };
  },
  created() {
    this.fetchData();
  },
  methods: {
    async fetchData() {
      const baseUrl = process.env.BACK_URL || 'localhost';
      const port = process.env.PORT || 5000;
      const response = await axios.get(`http://${baseUrl}:${port}/api/data`);
      this.rows = response.data;
    },
    async updateRow(index) {
      const updatedRow = this.rows[index];
      this.activeDropdown = null; // Ocultar el select después de guardar
      await axios.post(`http://${baseUrl}:${port}/api/data/${index}`, { updatedRow });
      this.fetchData();
      alert('Datos guardados correctamente');
    },
    toggleDropdown(index) {
      // Alternar la visibilidad del select
      this.activeDropdown = this.activeDropdown === index ? null : index;
    },
    // Función para asignar colores según el estado
    getStatusColor(status) {
      switch (status) {
        case 'Verde':
          return '#4CAF50'; // Verde
        case 'Amarillo':
          return '#FFC107'; // Amarillo
        case 'Rojo':
          return '#F44336'; // Rojo
        default:
          return '#CCCCCC'; // Gris (por defecto)
      }
    },
  },
};
</script>

<style>
#app {
  font-family: Arial, sans-serif;
  margin: 20px;
  text-align: center;
  align-items: center;
}
.table-container {
  display: inline-block; /* Hace que la tabla se ajuste al contenido */
  width: 100%;
}
table {
  width: 100%;
  border-collapse: collapse;
  margin: 0 auto; /* Centra la tabla dentro del contenedor */
  text-align: center; /* Centra el texto dentro de las celdas */
  
}
th, td {
  padding: 8px;
  text-align: center;
  width: fit-content;
  
}
input {
  width: 100%;
}
.status-circle {
  display: inline-block;
  width: 15px;
  height: 15px;
  border-radius: 50%;
  margin-right: 10px;
  vertical-align: middle;
}
.dropdown-icon {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 12px;
  vertical-align: middle;
  display: flex;
}
.hidden-select {
  position: absolute;
  z-index: 10;
  margin-top: 5px;
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: white;
}
</style>