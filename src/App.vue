<template>
  <div class="main-table">
    <DataTable :headers="headers" :items="items">
      <template #active="{ active }">
        <div
          :class="{
            activeItem: active,
            inactiveItem: !active,
          }"
        >
          {{ active }}
        </div>
      </template>
    </DataTable>
  </div>
</template>

<script setup>
import DataTable from './components/DataTable.vue';

const headers = [
  { text: 'Вроде имя', value: 'name' },
  { text: 'Вроде рост', value: 'height', sortable: true },
  { text: 'Жирность', value: 'weight', sortable: true },
  { text: 'Старость', value: 'age', sortable: true },
  { text: 'Активность', value: 'active', sortable: true },
];

const items = [
  {
    name: 'Ильмир1',
    height: 178,
    weight: 77,
    age: 20,
    active: false,
  },
  { name: 'Динар2', height: 180, weight: 75, age: 21, active: true },
  { name: 'Саня3', height: 181, weight: 73, age: 22, active: false },
  { name: 'Ильмир4', height: 178, weight: 77, age: 20, active: true },
  { name: 'Динар5', height: 180, weight: 75, age: 21, active: false },
  { name: 'Саня6', height: 181, weight: 73, age: 22, active: true },
  { name: 'Ильмир7', height: 178, weight: 77, age: 20, active: false },
  { name: 'Динар8', height: 180, weight: 75, age: 21, active: true },
  { name: 'Саня9', height: 181, weight: 73, age: 22, active: false },
  { name: 'Ильмир10', height: 178, weight: 77, age: 20, active: true },
  { name: 'Динар11', height: 180, weight: 75, age: 21, active: false },
  { name: 'Саня12', height: 181, weight: 73, age: 22, active: true },
  { name: 'Ильмир13', height: 178, weight: 77, age: 20, active: false },
  { name: 'Динар14', height: 180, weight: 75, age: 21, active: true },
  { name: 'Саня15', height: 181, weight: 73, age: 22, active: false },
  { name: 'Ильмир16', height: 178, weight: 77, age: 20, active: true },
  { name: 'Динар17', height: 180, weight: 75, age: 21, active: false },
  { name: 'Саня18', height: 181, weight: 73, age: 22, active: true },
  { name: 'Ильмир19', height: 178, weight: 77, age: 20, active: false },
  { name: 'Динар20', height: 180, weight: 75, age: 21, active: true },
  { name: 'Саня21', height: 181, weight: 73, age: 22, active: false },
];
</script>

<style lang="scss">
.activeItem {
  font-size: 0;
  width: 15px;
  height: 15px;
  background: green;
  border-radius: 50%;
}

.inactiveItem {
  font-size: 0;
  width: 15px;
  height: 15px;
  background: red;
  border-radius: 50%;
}
</style>
