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
import cheerio from 'cheerio';
import puppeteer from 'puppeteer';

export default {
  asyncData() {
    if (process.server) {
      const covidCases = [];
      const covidCasesHeaders = [];
      return puppeteer
        .launch()
        .then(browser => browser.newPage())
        .then((page) => {
          return page.goto('https://infogram.com/covid-2019-ins-colombia-1hnq41zg9ord63z').then(() => {
            return page.content();
          });
        })
        .then((html) => {
          const $ = cheerio.load(html);
          $('.igc-table-container').last().find('thead .igc-table-header span').each((i, title) => {
            const titleText = $(title).text();
            const value = titleText.split(' ').join('').toLowerCase();
            covidCasesHeaders.push({ text: $(title).text(), value });
          });
          $('.igc-table-container').last().find('tbody tr').each((i, row) => {
            const data = {};
            $(row).find('span').each((j, col) => {
              if (covidCasesHeaders[j]) {
                const dataText = $(col).text();
                data[covidCasesHeaders[j].value] = dataText;
              }
            });
            covidCases.push(data);
          });
          return { covidCases, covidCasesHeaders };
        })
        // eslint-disable-next-line
        .catch(console.error);
    }
  },
  data() {
    return {
      search: ''
    };
  }
};
</script>
