<template>
  <div class="container mx-auto p-4 pb-32">
    <h1 class="text-2xl font-bold mb-4">Fetch API Pokemon</h1>
    <table
      v-if="pokemon?.results"
      class="w-full text-left table-auto text-slate-800 shadow-lg !rounded-lg p-4">
      <tr class="font-bold bg-slate-800 text-white !rounded-lg">
        <th>No.</th>
        <th>Nama Pokemon</th>
        <th>Link Detail</th>
      </tr>
      <tr
        v-for="(p, i) in pokemon?.results"
        :key="i"
        class="hover:bg-slate-50"
        :class="i % 2 === 0 ? 'bg-slate-50' : ''">
        <td>{{ i + 1 }}</td>
        <td>{{ p.name }}</td>
        <td>
          <a :href="p.url">{{ p.url }}</a>
        </td>
      </tr>
    </table>

    <hr />

    <h2 class="font-bold text-2xl text-gray-800 mt-12 mb-4">Battle Armor Effect</h2>
    <ul v-if="battle_armor?.effect_entries" class="list-disc pl-4">
      <li v-for="(effect, index) in battle_armor?.effect_entries" :key="index">
        <span>
          <strong>Effect: </strong>
          <p>{{ effect?.effect }}</p>
        </span>
      </li>
    </ul>
  </div>
</template>

<script lang="ts" setup>
useHead({
  title: "Fetch API | MuatMuat Test",
});

const { data: pokemon }: any = useFetch("https://pokeapi.co/api/v2/pokemon");

const { data: battle_armor }: any = useFetch(
  "https://pokeapi.co/api/v2/ability/battle-armor"
);
</script>

<style scoped>
th,
td {
  padding: 8px;
}

a {
  @apply text-blue-600 hover:underline;
}
</style>
