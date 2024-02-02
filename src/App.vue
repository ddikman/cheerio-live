<script setup>

import { Codemirror } from 'vue-codemirror'
import { javascript } from '@codemirror/lang-javascript'
import { oneDark } from '@codemirror/theme-one-dark'
import { ref, watch, onMounted } from 'vue';

import * as cheerio from 'cheerio';

const html = ref(localStorage.getItem('html') || '');
const query = ref(localStorage.getItem('query') || '');
const output = ref('');

let $;

const extensions = [javascript(),oneDark];

function load() {
  $ = cheerio.load(html.value);
}

onMounted(() => {
  load();
}),

watch(html, () => {
  load();
  localStorage.setItem('html', html.value);
});

watch(query, () => {
  localStorage.setItem('query', query.value);
  try {
    output.value = eval(`($) => { ${query.value} }`)($);
  } catch (err) {
    output.value = err.message;
  }
});

</script>

<template>
  <main>
    <label>Paste your html here</label>
    <textarea v-model="html"></textarea>

    <label>Write your query here</label>

    <Codemirror
        v-model="query"
        placeholder="Code goes here..."
        :style="{ height: '400px' }"
        :autofocus="true"
        :extensions=extensions
        :indent-with-tab="true"
        :tab-size="2"
      />

    <span>This is the result:</span>
    <div style="text-wrap: pre;">{{ output }}</div>
  </main>
</template>

<style scoped>

main {
  display: flex;
  flex-direction: column;
  gap: 16px;
  width: 100%;
}

textarea {
  width: 100%;
  height: 200px;
}

</style>
