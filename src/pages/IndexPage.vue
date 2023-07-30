<template>
  <q-page class="row justify-center">
    <h1 class="col-12 text-center">A que hora terminarias de trabajar?</h1>
    <h3 v-if="substractHours" class="purple-9">Terminarias a las {{ substractHours }}</h3>
    <div class="q-pa-md text-center">
      <q-time v-model="hour" color="purple-9" with-seconds format24h>
        <q-input v-model="hourToPlus" filled type="time" hint="Tiempo restar" />
        <q-btn
          color="purple-9"
          label="Calcular"
          @click="substractHours = plusHours(hour, addSeconds(hourToPlus))"
          class="q-mt-lg"
        />
      </q-time>
    </div>
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
const plusHours = (primeraHora, segundaHora) => {
  const datePrimeraHora = new Date(`1970-01-01T${primeraHora}Z`);
  const dateSegundaHora = new Date(`1970-01-01T${segundaHora}Z`);

  // Sumar las dos horas en milisegundos y convertir a Date
  const dateSuma = new Date(datePrimeraHora.getTime() + dateSegundaHora.getTime());

  // Devolver la hora en formato ISO 8601 (HH:mm:ss)
  return dateSuma.toISOString().substr(11, 8);
};
</script>
