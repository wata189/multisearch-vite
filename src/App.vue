<script setup lang="ts">
import { ref } from 'vue';

const searchWord = ref("");
</script>

<template>
  <q-layout>

    <q-header elevated class="bg-info text-white">
      <div class="row">
        <q-toolbar class="col-auto">
          <q-toolbar-title>
            マルチサーチ
          </q-toolbar-title>

        </q-toolbar>
        <q-toolbar class="col-xs-8 col-sm-7 col-md-6 col-lg-5 col-xl-4">
          <q-input
            v-model="searchWord"
            label-color="white"
            label="検索ワード"
            class="full-width"
          >
            <template v-slot:append>
              <q-btn text-color="white" icon="search">検索</q-btn>
            </template>
          </q-input>
        </q-toolbar>
      </div>
    </q-header>

    <q-page-container>
    </q-page-container>

  </q-layout>
</template>

<style scoped>
</style>
