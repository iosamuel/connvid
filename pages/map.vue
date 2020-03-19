<template>
  <v-card>
    <v-card-title>
      COVID-19 Cases
      <v-spacer />
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
      />
    </v-card-title>
    <v-data-table
      :headers="covidCasesHeaders"
      :items="covidCases"
      :search="search"
    />
  </v-card>
</template>

<script>
export default {
  async asyncData({ $axios }) {
    const covidCases = [];
    const covidCasesHeaders = [];
    try {
      const response = await $axios.$get('https://e.infogram.com/api/live/flex/a2e70c7d-0e70-46ca-a79a-f7e5a243828a/dfee1a5c-5cc8-4e90-8efb-d5bdf2803bf6');
      const headers = response.data[0].shift();
      headers.forEach((header) => {
        const value = header.split(' ').join('').toLowerCase();
        covidCasesHeaders.push({ text: header, value });
      });
      response.data[0].forEach((row, i) => {
        const data = {};
        row.forEach((col, j) => {
          data[covidCasesHeaders[j].value] = col;
        });
        covidCases.push(data);
      });
    } catch (e) {
      // eslint-disable-next-line no-console
      console.error('Error', e);
    }
    return { covidCases, covidCasesHeaders };
  },
  data() {
    return {
      search: ''
    };
  }
};
</script>
