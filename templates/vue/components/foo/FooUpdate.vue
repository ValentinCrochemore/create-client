<template>
  <div class="container mx-auto px-4 max-w-2xl mt-4">
    <div class="flex items-center justify-between">
      <router-link
        :to="{ name: '{{titleUcFirst}}List' }"
        class="text-blue-600 hover:text-blue-800"
      >
        &lt; Back to list
      </router-link>

      <button
        class="px-6 py-2 bg-red-600 text-white text-xs rounded shadow-md hover:bg-red-700"
        @click="deleteItem"
      >
        Delete
      </button>
    </div>

    <h1 class="text-3xl my-4">Edit {{titleUcFirst}} \{{ item?.["@id"] }}</h1>

    <div
      v-if="isLoading || deleteLoading"
      class="bg-blue-100 rounded py-4 px-4 text-blue-700 text-sm"
      role="status"
    >
      Loading...
    </div>

    <div
      v-if="error || deleteError"
      class="bg-red-100 rounded py-4 px-4 my-2 text-red-700 text-sm"
      role="alert"
    >
      \{{ error || deleteError }}
    </div>

    <div
      v-if="created || updated"
      class="bg-green-100 rounded py-4 px-4 my-2 text-green-700 text-sm"
      role="status"
    >
      <template v-if="created">\{{ created["@id"] }} created. </template>
      <template v-else-if="updated">\{{ updated["@id"] }} updated. </template>
    </div>

    <Form v-if="item" :values="item" :errors="violations" @submit="update" />
  </div>
</template>

<script lang="ts" setup>
import { onBeforeUnmount } from "vue";
import { useRoute, useRouter } from "vue-router";
import { storeToRefs } from "pinia";
import { use{{titleUcFirst}}CreateStore } from "@/stores/{{lc}}/create";
import { use{{titleUcFirst}}DeleteStore } from "@/stores/{{lc}}/delete";
import { use{{titleUcFirst}}UpdateStore } from "@/stores/{{lc}}/update";
import type { {{titleUcFirst}} } from "@/types/{{lc}}";
import Form from "@/components/{{lc}}/{{titleUcFirst}}Form.vue";
import { useMercureItem } from "@/composables/mercureItem";

const route = useRoute();
const router = useRouter();

const {{lc}}CreateStore = use{{titleUcFirst}}CreateStore();
const { created } = storeToRefs({{lc}}CreateStore);

const {{lc}}DeleteStore = use{{titleUcFirst}}DeleteStore();
const { error: deleteError, isLoading: deleteLoading } =
  storeToRefs({{lc}}DeleteStore);

const {{lc}}UpdateStore = use{{titleUcFirst}}UpdateStore();
const {
  retrieved: item,
  updated,
  error,
  isLoading,
  violations,
} = storeToRefs({{lc}}UpdateStore);

useMercureItem({
  store: {{lc}}UpdateStore,
  deleteStore: {{lc}}DeleteStore,
  redirectRouteName: "{{titleUcFirst}}List",
});

await {{lc}}UpdateStore.retrieve(decodeURIComponent(route.params.id as string));

function update(item: {{titleUcFirst}}) {
  {{lc}}UpdateStore.update(item);
}

async function deleteItem() {
  if (!item?.value) {
    {{lc}}UpdateStore.setError("No {{lc}} found. Please reload");
    return;
  }

  if (window.confirm("Are you sure you want to delete this {{lc}}?")) {
    await {{lc}}DeleteStore.deleteItem(item.value);

    if ({{lc}}DeleteStore.deleted) {
      router.push({ name: "{{titleUcFirst}}List" });
    }
  }
}

onBeforeUnmount(() => {
  {{lc}}UpdateStore.$reset();
  {{lc}}CreateStore.$reset();
});
</script>
