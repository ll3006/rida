<script setup lang="ts">
const settings = useSettingsStore();
const sources = useSourcesStore();
const progress = useProgressStore();
const currentPage = defineModel<number>();

const pages = computed(
  () => sources.current?.chapters[progress.chapter]?.pages
);

const pageCount = computed(() => {
  return pages.value?.length ?? 0;
});

const loadedIds = computed(() => {
  let loadedIds = [] as number[];
  if (pages.value) {
    let len = pages.value.length;
    loadedIds = pages.value.reduce(
      (acc, el, i) => (sources.loadedUrls.has(el) ? [...acc, len - i] : acc),
      [] as number[]
    );
  }
  return loadedIds;
});
</script>

<template>
  <div
    class="absolute bottom-0 flex w-full flex-row gap-0.5 overflow-hidden bg-gradient-to-t from-black transition-opacity"
    :class="[settings.pinPageSelector ? '' : tw`opacity-0 hover:opacity-100`]"
  >
    <span
      class="pointer-events-none absolute right-1/2 z-10 self-center align-middle text-sm font-bold text-white"
      >{{ currentPage ?? 1 }}/{{ pageCount }}</span
    >
    <template v-if="pageCount > 0" v-for="n in pageCount">
      <div
        @click="
          () => {
            currentPage = pageCount - n + 1;
          }
        "
        class="relative flex h-8 grow items-end hover:bottom-1"
        :class="[
          {
            [tw`bg-gradient-to-t from-white`]: pageCount - n + 1 == currentPage,
          },
        ]"
      >
        <div
          class="h-[0.3rem] w-full bg-neutral-600"
          :class="[
            {
              [tw`border-t-[0.1rem] border-rose-600`]: loadedIds.includes(n),
            },
          ]"
        ></div>
      </div>
    </template>
  </div>
</template>
