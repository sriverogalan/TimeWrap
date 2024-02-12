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
              {{ index + 1 }} - Restar las horas
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
        <h3 v-if="substractHoursResult" class="col-12 text-center text-green-8">
          {{
            substractHoursResult != null
              ? "Terminarias a las " + substractHoursResult
              : ""
          }}
        </h3>
        <div class="col-6 row justify-around">
          <q-btn
            @click="addComponent"
            color="orange-10"
            label="Restar horas"
            class="q-mt-lg mb-50px"
          />
          <q-btn
            @click="clearAllComponents"
            color="red-9"
            label="Quitar todos"
            class="q-mt-lg mb-50px"
          />
          <q-btn
            color="blue-9"
            label="Calcular"
            @click="
              substractHoursResult = regexHour.test(
                calculateHours(hour, hourToPlus, substractHours)
              )
                ? calculateHours(hour, hourToPlus, substractHours)
                : `00:00:00`
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
const regexHour = new RegExp(/^(0[0-9]|1[0-9]|2[0-3]):[0-5][0-9]:[0-5][0-9]$/);

// Functions
const addComponent = () => {
  if (substractHours.value.length < 3) {
    substractHours.value.push(reactive({ start: null, end: null }));
  }
};

const calculateHours = (firstHour, secondHour, substractHours) => {
  if (!firstHour || !secondHour) return;
  const first = new Date(`1970-01-01T${firstHour}Z`);
  const second = new Date(`1970-01-01T${secondHour}Z`);
  let isBiggerThanSecond = false;
  let result = new Date(
    first.getUTCHours() * 60 * 60 * 1000 + second.getUTCHours() * 60 * 60 * 1000
  );

  substractHours.forEach((time) => {
    if (!time.start || !time.end) return;
    const start = new Date(`1970-01-01T${time.start}Z`);
    const end = new Date(`1970-01-01T${time.end}Z`);

    if (start.getTime() > end.getTime()) {
      isBiggerThanSecond = true;
    }

    result = new Date(result.getTime() - (start.getTime() - end.getTime()));
  });

  let hours = result.getUTCHours().toString().padStart(2, "0");
  let minutes = result.getUTCMinutes().toString().padStart(2, "0");
  let seconds = result.getUTCSeconds().toString().padStart(2, "0");

  return isBiggerThanSecond === true
    ? "Revisa que todo este correctamente"
    : `${hours}:${minutes}:${seconds}`;
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
