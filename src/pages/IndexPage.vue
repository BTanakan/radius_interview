<template>
  <div class="">
    <div class="flex w-full">
      <q-input class="w-1/2" outlined v-model="nameSpace" label="Search" />
      <div @click="onSearch">Search</div>
    </div>
    <div class="flex w-full mt-5 px-28">
      <div class="bg-blue-500 text-white px-20 py-2" @click="onTapChange(1)">
        All
      </div>
      <div class="px-20 py-2 border" @click="onTapChange(2)">Launched</div>
      <div class="px-20 py-2 border" @click="onTapChange(3)">Upcoming</div>
    </div>
    <div class="w-full">
      <div class="w-full px-28">
        <div v-if="searchLaunch">
          <div v-for="(launce, index) in searchLaunch" :key="index">
            <div
              @click="onModalOpen(launce)"
              class="flex w-full justify-between border mt-4 px-2 py-2"
            >
              <q-img
                :src="launce.links.patch.small"
                spinner-color="white"
                style="height: 20px; max-width: 20px"
              />
              <div class="">{{ launce.name }}</div>

              <div class="flex gap-10">
                <div
                  v-if="launce.crew.length > 0"
                  class="bg-blue-300 px-2 py-2 text-white"
                >
                  {{ launce.crew.length }}
                </div>
                <div class="">{{ dateConvert(launce.date_local) }}</div>

                <div class="text-purple-500 font-bold" v-if="launce.upcoming">
                  Upcoming
                </div>
              </div>
            </div>
          </div>
        </div>
        <div>
          <div v-if="tap == 3">
            <div v-for="(launce, index) in upComing" :key="index">
              <div
                @click="onModalOpen(launce)"
                class="flex w-full justify-between border mt-4 px-2 py-2"
              >
                <q-img
                  :src="launce.links.patch.small"
                  spinner-color="white"
                  style="height: 20px; max-width: 20px"
                />
                <div class="">{{ launce.name }}</div>

                <div class="flex gap-10">
                  <div
                    v-if="launce.crew.length > 0"
                    class="bg-blue-300 px-2 py-2 text-white"
                  >
                    {{ launce.crew.length }}
                  </div>
                  <div class="">{{ dateConvert(launce.date_local) }}</div>

                  <div class="text-purple-500 font-bold" v-if="launce.upcoming">
                    Upcoming
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div v-if="tap == 1">
            <div v-for="(launce, index) in launces" :key="index">
              <div
                @click="onModalOpen(launce)"
                class="flex w-full justify-between border mt-4 px-2 py-2"
              >
                <q-img
                  :src="launce.links.patch.small"
                  spinner-color="white"
                  style="height: 20px; max-width: 20px"
                />
                <div class="">{{ launce.name }}</div>

                <div class="flex gap-10">
                  <div
                    v-if="launce.crew.length > 0"
                    class="bg-blue-300 px-2 py-2 text-white"
                  >
                    {{ launce.crew.length }}
                  </div>
                  <div class="">{{ dateConvert(launce.date_local) }}</div>

                  <div class="text-purple-500 font-bold" v-if="launce.upcoming">
                    Upcoming
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <q-dialog v-model="cardModal">
      <q-card class="my-card px-5 py-5 w-full">
        <p class="text-xl text-center">{{ launceModal?.name }}</p>
        <p class="text-center" v-if="launceModal?.date_local">
          {{ dateConvert(launceModal?.date_local) }}
        </p>
        <div>
          <q-img
            :src="launceModal?.links.patch.large"
            spinner-color="white"
            style="height: 500px; max-width: 500px"
          />
        </div>
        <div class="w-full flex justify-center mt-2">
          <div class="bg-purple-500 text-white text-center w-fit px-2 py-2">
            {{ launceModal?.launchpad ? 'Launchpad' : '' }}
          </div>
        </div>

        <div
          v-if="launceModal?.details"
          class="w-full flex justify-center mt-2"
        >
          <div class="bg-purple-500 text-white text-center w-fit px-2 py-2">
            {{ launceModal?.details ? 'Details' : '' }}
          </div>
        </div>

        <p class="text-center text-gray-500 mt-2">{{ launceModal?.details }}</p>
      </q-card>
    </q-dialog>
  </div>
</template>

<script setup lang="ts">
import { Todo, Meta } from 'components/models';
import { ref, onMounted, Ref, computed } from 'vue';
import { Launches } from 'src/models/launches';
const launces: Ref<Launches[]> = ref([]);
const tap = ref(1);
const cardModal = ref(false);
const launceModal: Ref<Launches | undefined> = ref(undefined);
const searchLaunch: Ref<Launches[]> = ref([]);
const nameSpace = ref('');
onMounted(() => {
  fetchSpace();
});

const onSearch = () => {
  searchLaunch.value = launces.value.filter(
    (launch) => launch.name === nameSpace.value
  );
};
const onModalOpen = (l: Launches) => {
  cardModal.value = true;
  launceModal.value = l;
};

const onTapChange = (tapI: number) => {
  console.log('🚀 ~ onTapChange ~ tapI:', tapI);
  tap.value = tapI;
};
const upComing = computed(() => {
  return launces.value.filter((launch) => launch.upcoming === true);
});

const dateConvert = (date: string) => {
  const originalDate = new Date(date);
  originalDate.setHours(originalDate.getHours() - 12);
  return originalDate.toDateString();
};

const fetchSpace = async () => {
  try {
    const response = await fetch('https://api.spacexdata.com/v5/launches');
    if (response.status !== 200) return;

    const _data = (await response.json()) as Launches[];
    launces.value = _data;
  } catch (error) {}
};
</script>
