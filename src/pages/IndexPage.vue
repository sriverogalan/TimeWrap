<!-- MADE BY @sriverogalan ;) -->
<template>
  <q-page>
    <div v-if="result">
      <h1>Acabarias a las {{ finalText }}</h1>
    </div>

    <Section v-else>
      <h5 class="text-center col-12">¿A que hora terminarias hoy?</h5>
      <q-input
        rounded
        v-model="ope_dest"
        label="Operari desti (pon tu numero de operario)"
        class="col-6"
      />

      <q-input
        rounded
        v-model="cookie"
        label="Cookie (SESSIONID=token de la cookie de la session de vtiger)"
        class="col-6"
        aria-placeholder="aqui"
      />

      <q-input filled v-model="formTime" mask="fulltime" :rules="['fulltime']">
        <template v-slot:append>
          <q-icon name="access_time" class="cursor-pointer">
            <q-popup-proxy cover transition-show="scale" transition-hide="scale">
              <q-time v-model="formTime" with-seconds format24h>
                <div class="row items-center justify-end">
                  <q-btn v-close-popup label="Cerrar" color="primary" flat />
                </div>
              </q-time>
            </q-popup-proxy>
          </q-icon>
        </template>
      </q-input>

      <q-btn
        rounded
        color="white"
        text-color="black"
        label="Enviar"
        class="col-6"
        :loading="loading"
        @click="submit()"
      />

      <q-dialog v-model="icon">
        <q-card>
          <q-card-section class="row items-center q-pb-none">
            <div class="text-h6">Cerrar</div>
            <q-space />
            <q-btn icon="close" flat round dense v-close-popup @click="loading = false" />
          </q-card-section>

          <q-card-section>
            No se ha podido obtener la hora, comprueba que la cookie sea correcta
          </q-card-section>
        </q-card>
      </q-dialog>
    </Section>
  </q-page>
</template>

<script setup>
import { ref } from "vue";
import Section from "../components/Section.vue";

const loading = ref(false);
const ope_dest = ref("");
const cookie = ref("");
const formTime = ref("08:00:00");
const icon = ref(false);
const result = ref(false);
const finalText = ref("");

async function submit() {
  loading.value = true;
  try {
    const responsePartes = await fetch("http://vtiger/abreOF/partes/resumpartes", {
      method: "POST",
      headers: {
        "Content-Type": "x-www-form-urlencoded",
        Cookie: cookie.value,
      },
      body: JSON.stringify({
        data: new Date().toLocaleDateString("es-ES", {
          day: "2-digit",
          month: "2-digit",
          year: "numeric",
        }),
        ope_dest: "000" + ope_dest.value,
        avi_num: null,
      }),
    });

    const responseText = await responsePartes.text();
    const regex = /(\d{2}:\d{2}:\d{2})/;
    const match = responseText.match(regex);
    const hora = match[1]; // Aquí tienes la hora con segundos en números

    const horaActual = new Date();
    const horaActualString = `${horaActual.getHours()}:${horaActual.getMinutes()}:${horaActual.getSeconds()}`;
    const horaActualDate = new Date(`01/01/2000 ${horaActualString}`);
    const horaServidorDate = new Date(`01/01/2000 ${hora}`);
    const horaFormDate = new Date(`01/01/2000 ${formTime.value}`);
    const horaFinalDate = new Date(
      horaActualDate.getTime() - horaServidorDate.getTime() + horaFormDate.getTime()
    );

    finalText.value = `${horaFinalDate.getHours()}:${horaFinalDate.getMinutes()}:${horaFinalDate.getSeconds()}`;
    result.value = true;
  } catch (error) {
    icon.value = true;
  } finally {
    loading.value = false;
  }
}
</script>
