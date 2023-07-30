<!-- MADE BY @sriverogalan ;) -->
<template>
  <q-page class="q-pa-md text-center">
    <h2 class="col-12 text-center text-grey-4 text-weight-medium">
      ¿A que hora terminarias?
    </h2>
    <h3 v-if="substractHours" class="text-light-green-12 text-weight-medium">
      A las {{ substractHours }} 😉
    </h3>
    <h5>Selecciona la fecha a la que empieza</h5>
    <q-time v-model="hour" color="purple-9" with-seconds format24h bordered>
      <q-input v-model="hourToPlus" filled type="time" label="Tiempo que deseas sumar a esa fecha" />
      <q-btn
        color="purple-9"
        label="Calcular"
        @click="substractHours = plusHours(hour, addSeconds(hourToPlus))"
        class="q-mt-lg"
      />
    </q-time>
  </q-page>
</template>

<script setup>
import { ref } from "vue";

const substractHours = ref();

const hourToPlus = ref("08:30");
const hour = ref("07:30:00");

const addSeconds = (hour) => {
  return hour + ":00";
};
const plusHours = (firstHour, secondHour) => {
  const dateFirstHour = new Date(`1970-01-01T${firstHour}Z`);
  const dateSecondHour = new Date(`1970-01-01T${secondHour}Z`);

  // Sumar las dos horas en milisegundos y convertir a Date
  const datePlus = new Date(dateFirstHour.getTime() + dateSecondHour.getTime());

  // Devolver la hora en formato ISO 8601 (HH:mm:ss)
  return datePlus.toISOString().substr(11, 8);
};
</script>
