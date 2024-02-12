<!-- MADE BY @sriverogalan  -->
<template>
  <q-page class="flex flex-center row">
    <div class="col-12">
      <div>
        <h1 class="text-h2 col-12 text-grey-4 text-center text-weight-bold">
          ¿A que hora terminarias?
        </h1>
        <h2 class="text-h5 col-12 text-grey-4 text-center text-weight-medium">
          Selecciona una hora y suma el tiempo que desees
        </h2>
      </div>

      <div class="row mt-20px justify-center items-center">
        <div class="col-6 row justify-around">
          <q-input
            filled
            v-model="hour"
            :rules="['time']"
            mask="time"
            label="Hora de inicio"
          >
            <template v-slot:append>
              <q-icon name="access_time" class="cursor-pointer">
                <q-popup-proxy cover transition-show="scale" transition-hide="scale">
                  <q-time v-model="hour" color="purple-9">
                    <div class="row items-center justify-end">
                      <q-btn v-close-popup label="Close" color="purple" flat />
                    </div>
                  </q-time>
                </q-popup-proxy>
              </q-icon>
            </template>
          </q-input>

          <q-input
            filled
            v-model="hourToPlus"
            mask="time"
            :rules="['time']"
            label="+ Horas que deseas sumar"
          >
            <template v-slot:append>
              <q-icon name="access_time" class="cursor-pointer">
                <q-popup-proxy cover transition-show="scale" transition-hide="scale">
                  <q-time v-model="hourToPlus" color="purple-9">
                    <div class="row items-center justify-end">
                      <q-btn v-close-popup label="Close" color="purple" flat />
                    </div>
                  </q-time>
                </q-popup-proxy>
              </q-icon>
            </template>
          </q-input>
        </div>

        <div class="col-12 row justify-center">
          <div
            v-for="(time, index) in substractHours"
            class="col-12 row justify-center items-center"
          >
            <h3 class="text-h4 col-12 text-center mt-20px">
              {{ index + 1 }} - Horas de pausa
            </h3>
            <div class="col-6 row justify-around items-center">
              <q-input
                filled
                v-model="time.start"
                :key="index"
                mask="time"
                :rules="['time']"
                :label="`Inicio de la hora de pausa`"
              >
                <template v-slot:append>
                  <q-icon name="access_time" class="cursor-pointer">
                    <q-popup-proxy cover transition-show="scale" transition-hide="scale">
                      <q-time v-model="time.start">
                        <div class="row items-center justify-end">
                          <q-btn v-close-popup label="Close" color="purple-9" flat />
                        </div>
                      </q-time>
                    </q-popup-proxy>
                  </q-icon>
                </template>
              </q-input>

              <q-input
                filled
                v-model="time.end"
                :key="index"
                mask="time"
                :rules="['time']"
                :label="`Fin de la hora de pausa`"
              >
                <template v-slot:append>
                  <q-icon name="access_time" class="cursor-pointer">
                    <q-popup-proxy cover transition-show="scale" transition-hide="scale">
                      <q-time v-model="time.end">
                        <div class="row items-center justify-end">
                          <q-btn v-close-popup label="Close" color="purple-9" flat />
                        </div>
                      </q-time>
                    </q-popup-proxy>
                  </q-icon>
                </template>
              </q-input>

              <q-btn
                @click="substractHours.splice(index, 1)"
                color="red-9"
                label="Quitar"
                class="q-mt-lg mb-50px"
              />
            </div>
          </div>
        </div>
      </div>

      <div class="col-6 mt-20px row justify-around">
        <h3
          v-if="substractHoursResult"
          :class="{
            'text-red-8':
              substractHoursResult.includes('tiempo de inicio no puede') ||
              substractHoursResult.includes('Revisa que todo este correctamente') ||
              substractHoursResult.includes('Completa todos los campos'),
            'text-green-8':
              !substractHoursResult.includes('tiempo de inicio no puede') &&
              !substractHoursResult.includes('Revisa que todo este correctamente') &&
              !substractHoursResult.includes('Completa todos los campos'),
          }"
          class="col-12 text-center"
        >
          {{ substractHoursResult }}
        </h3>
        <div class="col-6 row justify-around">
          <q-btn
            @click="addComponent"
            color="orange-10"
            label="Restar horas"
            class="q-mt-lg mb-50px"
            v-if="substractHours.length < 3"
          />
          <q-btn
            @click="clearAllComponents"
            color="red-9"
            label="Quitar todas"
            v-if="substractHours.length > 1"
            class="q-mt-lg mb-50px"
          />
          <q-btn
            color="blue-9"
            label="Calcular"
            @click="
              substractHoursResult = calculateHours(hour, hourToPlus, substractHours)
            "
            class="q-mt-lg mb-50px"
          />
        </div>
      </div>
    </div>
  </q-page>
</template>

<script setup>
import { reactive, ref } from "vue";

// Default values
const hour = ref("07:30:00");
const hourToPlus = ref("08:30:00");
// Variables
const substractHoursResult = ref();
const substractHours = ref([]);
// Regex hour
const regexHour = new RegExp(/^(0[0-9]|1[0-9]|2[0-3]):[0-5][0-9]$/);

// Functions
const addComponent = () => {
  if (substractHours.value.length < 3) {
    substractHours.value.push(reactive({ start: null, end: null }));
  }
};

const calculateHours = (firstHour, secondHour, substractHours) => {
  if (!firstHour || !secondHour) return "Completa todos los campos";

  const first = new Date(`1970-01-01T${firstHour}`);
  const second = new Date(`1970-01-01T${secondHour}`);

  // Convertir las horas a milisegundos
  const firstInMilliseconds =
    first.getHours() * 60 * 60 * 1000 + first.getMinutes() * 60 * 1000;
  const secondInMilliseconds =
    second.getHours() * 60 * 60 * 1000 + second.getMinutes() * 60 * 1000;

  // Sumarle el total de horas del second al first
  let resultInMilliseconds = firstInMilliseconds + secondInMilliseconds;

  for (let i = 0; i < substractHours.length; i++) {
    const time = substractHours[i];
    if (!time.start || !time.end) return "Completa todos los campos";
    const start = new Date(`1970-01-01T${time.start}`);
    const end = new Date(`1970-01-01T${time.end}`);

    // Convertir las horas a milisegundos
    const startInMilliseconds =
      start.getHours() * 60 * 60 * 1000 + start.getMinutes() * 60 * 1000;
    const endInMilliseconds =
      end.getHours() * 60 * 60 * 1000 + end.getMinutes() * 60 * 1000;

    // Si el tiempo de inicio es mayor que el tiempo de fin, enviar un mensaje
    if (startInMilliseconds > endInMilliseconds) {
      return "El tiempo de inicio no puede ser mayor que el tiempo de fin";
    }

    // Restar el tiempo de inicio y de fin y sumarlo al resultado anterior
    resultInMilliseconds += endInMilliseconds - startInMilliseconds;
  }

  // Convertir el resultado a horas y minutos
  const resultHours = Math.floor(resultInMilliseconds / (60 * 60 * 1000));
  const resultMinutes = Math.floor(
    (resultInMilliseconds % (60 * 60 * 1000)) / (60 * 1000)
  );

  return resultHours < 0
    ? "Revisa que todo este correctamente"
    : `Terminarias a las ${resultHours
        .toString()
        .padStart(2, "0")}:${resultMinutes.toString().padStart(2, "0")}h `;
};
const clearAllComponents = () => {
  substractHours.value = [];
};
</script>

<style scoped>
h2,
h3 {
  margin: 0;
}
h5 {
  margin: 20px;
}
.q-page {
  font-family: "Roboto", sans-serif;
}
.mt-20px {
  margin-top: 20px;
}
.mt-50px {
  margin-top: 50px;
}
.mb-50px {
  margin-bottom: 50px;
}
</style>
