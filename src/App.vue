<script setup>
import { ref } from 'vue';

const players = ref([]);
const playerInput = ref('');
const error = ref(false);
const errorMessage = ref('');
const matches2v2 = ref([]);
const matches1v1 = ref([]);
const checked = ref(0);
const typeMatch = ref('1v1');

const alert = (message) => {
     error.value = true;
     errorMessage.value = message;
     setTimeout(() => {
          error.value = false;
     }, 2000);
};

const addPlayer = () => {
     if (!players.value.includes(playerInput.value) && playerInput.value !== '') {
          players.value.push(playerInput.value);
          playerInput.value = '';
     } else if (playerInput.value === '') {
          alert("Please fill the Player Name's input!");
          playerInput.value = '';
     } else if (players.value.includes(playerInput.value)) {
          alert(`${playerInput.value} already exist!`);
          playerInput.value = '';
     }
};

const deletePlayer = (index) => {
     players.value.splice(index, 1);
};

const generateMatch1v1 = () => {
     matches1v1.value = [];
     for (let i = 0; i < players.value.length; i++) {
          for (let j = i + 1; j < players.value.length; j++) {
               matches1v1.value.push([players.value[i], players.value[j]]);
               matches1v1.value[i] = shuffle(matches1v1.value[i]);
          }
     }
};

const generateMatch2v2 = () => {
     const team = [];
     matches2v2.value = [];
     for (let i = 0; i < players.value.length; i++) {
          for (let j = i + 1; j < players.value.length; j++) {
               team.push([players.value[i], players.value[j]]);
          }
     }
     for (let i = 0; i < team.length; i++) {
          for (let j = i + 1; j < team.length; j++) {
               if (!team[j].includes(team[i][0]) && !team[j].includes(team[i][1])) {
                    team[i] = shuffle(team[i]);
                    team[j] = shuffle(team[j]);
                    matches2v2.value.push([team[i], team[j]]);
               }
          }
     }
     matches2v2.value = shuffle(matches2v2.value);
};

const generateMatch = () => {
     typeMatch.value === '1v1' ? generateMatch1v1() : generateMatch2v2();
};

const shuffleMatches1v1 = () => {
     matches1v1.value = shuffle(matches1v1.value);
};

const shuffleMatches2v2 = () => {
     matches2v2.value = shuffle(matches2v2.value);
};

const shuffle = (array) =>
     array
          .map((a) => ({ sort: Math.random(), value: a }))
          .sort((a, b) => a.sort - b.sort)
          .map((a) => a.value);
</script>

<template>
     <main class="py-10 px-5 md:w-[750px] xl:w-[1000px] min-h-screen">
          <h1 class="text-5xl font-extrabold dark:text-white mb-2">Match Generator</h1>
          <p class="text-lg font-normal text-gray-500 lg:text-xl dark:text-gray-400 mb-10">Generate random combination matches with your friends.</p>
          <form @submit.prevent="addPlayer" class="w-full flex items-end gap-5">
               <div class="w-full">
                    <input
                         v-model="playerInput"
                         type="text"
                         id="default-input"
                         :maxlength="10"
                         placeholder="Player Name"
                         autocomplete="off"
                         class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-[#232323] dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
                    />
               </div>
               <div>
                    <button
                         type="submit"
                         class="focus:outline-none text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-900"
                    >
                         Add
                    </button>
               </div>
          </form>

          <div class="w-full flex gap-4 mt-5">
               <label for="1v1" :class="{ 'border-2 border-blue-400': typeMatch === '1v1' }" class="px-5 py-3 rounded-lg dark:text-white bg-[#222222] cursor-pointer">
                    1 vs 1
                    <input type="radio" class="hidden" id="1v1" name="typeMatch" value="1v1" v-model="typeMatch" />
               </label>
               <label for="2v2" :class="{ 'border-2 border-blue-400': typeMatch === '2v2' }" class="px-5 py-3 rounded-lg dark:text-white bg-[#222222] cursor-pointer">
                    2 vs 2
                    <input type="radio" class="hidden" id="2v2" name="typeMatch" value="2v2" v-model="typeMatch" />
               </label>
          </div>
          <div class="w-full rounded-lg border border-stone-800 bg-[#222222] py-5 px-8 mt-10">
               <h2 class="text-2xl font-bold dark:text-white mb-8">
                    Players <span class="font-normal text-base">({{ players.length }})</span>
               </h2>
               <div class="w-full flex gap-5 flex-wrap">
                    <transition-group enter-from-class="scale-0 -translate-y-4" leave-to-class="scale-0 -translate-y-4" enter-active-class="transition-all duration-200" leave-active-class="transition-all duration-200">
                         <div v-for="(player, index) in players" :key="player" class="px-5 py-2 rounded-full flex items-center justify-center gap-3 bg-gray-500 dark:text-white font-bold">
                              <span>{{ player }}</span
                              ><button @click="deletePlayer(index)">
                                   <svg class="w-3 h-3 text-gray-800 dark:text-white" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 14 14">
                                        <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="m1 1 6 6m0 0 6 6M7 7l6-6M7 7l-6 6" />
                                   </svg>
                              </button>
                         </div>
                    </transition-group>
               </div>
          </div>
          <div class="w-full flex justify-end">
               <button
                    type="submit"
                    v-if="(typeMatch === '2v2' && players.length > 3) || (typeMatch === '1v1' && players.length > 1)"
                    @click="generateMatch"
                    class="focus:outline-none mt-5 text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-900"
               >
                    Generate
               </button>
          </div>

          <div v-if="typeMatch === '1v1' && matches1v1.length !== 0" class="w-full flex justify-between items-end">
               <h3 class="text-2xl font-bold dark:text-white mb-3 mt-8">
                    Matches <span class="font-normal text-base">({{ matches1v1.length }})</span>
               </h3>
               <button class="dark:text-gray-400 py-3 dark:hover:text-gray-50" @click="shuffleMatches1v1">🔄 Shuffle</button>
          </div>
          <div v-if="typeMatch === '2v2' && matches2v2.length !== 0" class="w-full flex justify-between items-end">
               <h3 class="text-2xl font-bold dark:text-white mb-3 mt-8">
                    Matches <span class="font-normal text-base">({{ matches2v2.length }})</span>
               </h3>
               <button class="dark:text-gray-400 py-3 dark:hover:text-gray-50" @click="shuffleMatches2v2">🔄 Shuffle</button>
          </div>

          <div class="flex w-full flex-col gap-5 mt">
               <transition-group enter-from-class="opacity-0 -translate-y-4" leave-to-class="opacity-0 -translate-y-4" enter-active-class="transition-all duration-200" leave-active-class="transition-all duration-200">
                    <label
                         v-for="(match, index) in matches2v2"
                         v-if="typeMatch === '2v2'"
                         :key="index"
                         :for="index"
                         :class="{ 'border-2 border-blue-400': checked == index }"
                         class="w-full cursor-pointer px-5 py-2 rounded-lg dark:bg-[#222222] flex flex-col gap-3 items-center dark:text-white"
                    >
                         <div class="pt-2 text-gray-400 font-semibold border-[#454545] w-full text-center">Round {{ index + 1 }}</div>
                         <div class="w-full py-4 grid grid-cols-3 text-center">
                              <div class="w-full">
                                   <strong class="text-xl">{{ match[0][0] }}</strong> <span class="mx-1 text-gray-400">&</span> <strong class="text-xl">{{ match[0][1] }}</strong>
                              </div>
                              <div class="w-full">VS</div>
                              <div class="w-full">
                                   <strong class="text-xl">{{ match[1][0] }}</strong> <span class="mx-1 text-gray-400">&</span> <strong class="text-xl">{{ match[1][1] }}</strong>
                              </div>
                         </div>
                         <input class="hidden" type="radio" :id="index" name="match" :value="index" v-model="checked" />
                    </label>

                    <label
                         v-for="(match, index) in matches1v1"
                         v-if="typeMatch === '1v1'"
                         :key="index"
                         :for="index"
                         :class="{ 'border-2 border-blue-400': checked == index }"
                         class="w-full cursor-pointer px-5 py-2 rounded-lg dark:bg-[#222222] flex flex-col gap-3 items-center dark:text-white"
                    >
                         <div class="pt-2 text-gray-400 font-semibold border-[#454545] w-full text-center">Round {{ index + 1 }}</div>
                         <div class="w-full py-4 grid grid-cols-3 text-center">
                              <div class="w-full">
                                   <strong class="text-xl">{{ match[0] }}</strong>
                              </div>
                              <div class="w-full">VS</div>
                              <div class="w-full">
                                   <strong class="text-xl">{{ match[1] }}</strong>
                              </div>
                         </div>
                         <input class="hidden" type="radio" :id="index" name="match" :value="index" v-model="checked" />
                    </label>
               </transition-group>
          </div>
     </main>
     <transition
          enter-active-class="transition-all ease-in-out duration-300"
          leave-active-class="transition-all ease-in-out duration-300"
          enter-from-class="transform  translate-y-24 opacity-0"
          leave-to-class="transform  translate-y-24 opacity-0"
     >
          <div
               style="transform: translate(-50%, -50%)"
               v-if="error"
               class="fixed dark:text-white font-semibold text-center whitespace-nowrap text-ellipsis overflow-hidden max-w-full top-16 z-50 left-1/2 rounded-full px-5 py-3 bg-black border border-red-400 bg-opacity-40 backdrop-blur-lg"
               role="alert"
          >
               {{ errorMessage }}
          </div>
     </transition>
</template>
