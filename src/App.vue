<script setup lang="ts">
import { Ref, ref } from 'vue';
import data from "@/assets/data.json";

type Site = {
  id: string,
  name: string,
  url: string,
  orderNumber: number
};
type Category = {
  id: string,
  name: string,
  isDefaultChecked: boolean,
  orderNumber: number,
  sites: Site[]
};

const searchWord = ref("");

const selectedCategoryIds:Ref<string[]> = ref([]);
const selectedSiteIds:Ref<string[]> = ref([]);

// カテゴリソート
const categories:Category[] = data.categories.sort((a, b) => a.orderNumber - b.orderNumber);
for(const category of categories){
  // サイトソート
  category.sites.sort((a, b) => a.orderNumber - b.orderNumber);

  // デフォルトチェック
  if(category.isDefaultChecked){
    selectedCategoryIds.value.push(category.id);
    for(const site of category.sites){
      selectedSiteIds.value.push(site.id);
    }
  }
}

const onChangeCategory = (categoryId:string) => {
  const category = categories.find(category => category.id === categoryId);
  if(!category){return;}

  const isChecked = selectedCategoryIds.value.includes(categoryId);
  if(isChecked){
    category.sites.forEach(site => selectedSiteIds.value.push(site.id));
  }else{
    category.sites.forEach(site => {
      // サイトあったら削除
      const siteIdIndex = selectedSiteIds.value.indexOf(site.id);
      if(siteIdIndex > -1){
        selectedSiteIds.value.splice(siteIdIndex, 1);
      }
    })
  }
};

const onChangeSite = (category:Category) => {
  const siteCount = category.sites.length;
  const checkedSiteCount = category.sites.filter(site => selectedSiteIds.value.includes(site.id)).length;
  if(siteCount === checkedSiteCount){
    if(!selectedCategoryIds.value.includes(category.id)){
      selectedCategoryIds.value.push(category.id);
    }
  }else{
    const categoryIdIndex = selectedCategoryIds.value.indexOf(category.id);
    if(categoryIdIndex > -1){
      selectedCategoryIds.value.splice(categoryIdIndex, 1);
    }
  }
};

const multiSearch = () => {
  console.log("multi search")
  if(!searchWord.value){return;}

  const replacedStr = "@SEARCH_WORD@";
  const replaceSearchWord = encodeURI(searchWord.value);
  const regex = new RegExp(replacedStr, "g");
  categories.map(category => category.sites) 
            .reduce((previous, current) => previous.concat(current)) //全サイト抽出
            .filter(site => selectedSiteIds.value.includes(site.id)) //選択されているサイトのみにする
            .map(site => site.url.replace(regex, replaceSearchWord)) //置換
            .forEach(url => window.open(url, "_blank"));
};

</script>

<template>
  <q-layout>

    <q-header reveal :reveal-offset="Infinity" class="bg-info text-white">
      <div class="row">
        <q-toolbar class="col-auto">
          <q-toolbar-title>
            マルチサーチ
          </q-toolbar-title>

        </q-toolbar>
        <q-toolbar class="col-xs-7 col-sm-6 col-md-5 col-lg-4 col-xl-3">

          <q-input
            v-model="searchWord"
            label-color="white"
            color="white"
            label="検索ワード"
            class="full-width"
            @keydown.enter="multiSearch"
          >
            <template v-slot:append>
              <q-btn @click="multiSearch" text-color="white" icon="search">検索</q-btn>
            </template>
          </q-input>
        </q-toolbar>
      </div>
    </q-header>

    <q-page-container>
      <div class="row">
        <div v-for="category in categories" class="q-pa-md col-xs-12 col-sm-6 col-md-3">
          <q-card  class="">
            <q-card-section>
              <q-checkbox
                v-model="selectedCategoryIds"
                :label="category.name"
                :val="category.id"
                @update:model-value="onChangeCategory(category.id)"
              ></q-checkbox>
            </q-card-section>
            <q-separator inset />
            <q-card-section>
              <div v-for="site in category.sites" class="q-ml-md">
                <q-checkbox
                  v-model="selectedSiteIds"
                  :label="site.name"
                  :val="site.id"
                  @update:model-value="onChangeSite(category)"
                ></q-checkbox>
              </div>
            </q-card-section>
          </q-card>
        </div>
      </div>
    </q-page-container>

  </q-layout>
</template>

<style scoped>
</style>
