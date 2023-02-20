<script setup lang="ts">
import { ref, computed } from "vue";
import type { Datos } from "./types";

const datos = ref<Datos>({
  monthPrev: 0,
  monthCurr: 0,
  cargos: [],
});

const cargo = ref(0);
const totalLuz = ref(0);

const addCargo = () => {
  if (cargo.value < 0) return;
  datos.value.cargos.push(cargo.value);
  cargo.value = 0;
};

const totalCargos = computed(() => {
  if (datos.value.cargos.length < 0) return 0;
  return datos.value.cargos.reduce((acc, cur) => acc + cur);
});

const submit = () => {
  const dif = Math.abs(datos.value.monthPrev - datos.value.monthCurr);

  const precioLuz = 0.7265;
  const consumo = dif * precioLuz;

  const numInquilinos = 5;
  const cargoPersona = totalCargos.value / numInquilinos;

  totalLuz.value = consumo + cargoPersona;
};
</script>
<template>
  <form
    class="md:w-96 w-full p-8 rounded-lg md:shadow-lg md:border flex flex-col gap-6"
    @submit.prevent="submit"
  >
    <h2 class="font-semibold text-xl">Pagos Servicios / Luz</h2>

    <div class="flex gap-4">
      <label
        >Mes Anterior:
        <input type="number" step="0.01" v-model="datos.monthPrev" />
      </label>

      <label
        >Mes Actual:
        <input type="number" step="0.01" v-model="datos.monthCurr" />
      </label>
    </div>

    <label
      >Añadir cargos fijos:
      <div class="flex gap-4">
        <input type="number" step="0.01" v-model="cargo" />
        <button type="button" @click="addCargo">añadir</button>
      </div>
    </label>

    <p v-if="datos.cargos.length > 0">
      cargos: {{ datos.cargos.join(" + ") }} = {{ totalCargos }}
    </p>

    <button type="submit">Calcular</button>

    <p class="text-xl" v-if="totalLuz">el total a pagar es: {{ totalLuz }}</p>
  </form>
</template>

<style scoped>
input {
  @apply w-full p-2 border rounded-lg;
}
button {
  @apply py-2 bg-sky-500 text-white rounded-lg hover:bg-sky-600 shadow cursor-pointer px-4;
}
</style>
