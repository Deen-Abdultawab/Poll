<template>
  <section class="w-[90%] max-w-[400px] mx-auto">
    <div class="p-[1.2rem] border border-[#ccc] bg-[#f9f9f9] mt-[5rem]">
      <h1 class="text-center mb-[1.2rem] font-bold">{{ question }}</h1>
      <div class="results">
        <div
          v-for="(option, index) in options"
          :key="index"
          class="option transitionEffect relative result-bar hover:bg-[#e0e0e0]"
          @click="vote(index)"
        >
          <div
            class="overlay absolute w-0 h-full top-0 left-0 rounded-[0.4rem] bg-[#7bd4ac] z-[1] transition-width duration-1000 ease" 
            :style="{ width: hasVoted ? calculatePercentage(option.votes) + '%' : '0%' }"
            :class="hasVoted && calculatePercentage(option.votes) !== '100.00'? '!rounded-r-[0] p-[0.5rem]' : ''"
          ></div>
          <div 
          class="z-[22] relative"
          >
            <span>
              {{ option.text }}
            </span>
            <span v-if="hasVoted">
              ({{ calculatePercentage(option.votes) }}%)
            </span>
          </div>
        </div>
      </div>
    </div>
    <div v-if="error" class="error-message mt-[0.7rem] p-[0.6rem] border border-[#ff6b6b] rounded-[0.5rem] bg-[#ffebee] text-[#ff6b6b] text-center">
      {{ error }}
    </div>
    <button v-if="hasVoted" @click="resetVote" class="border border-black p-[0.5rem] rounded-[0.5rem] mt-4 cursor-pointer">Revote</button>
  </section>
</template>

<script setup lang="ts">
// I received the Test mail on the 23rd february 2024 @ 7:25pm
import { ref } from 'vue';

interface PollOption {
  text: string;
  votes: number;
}

const hasVoted = ref<boolean>(false);
const error = ref<string | null>(null);
const question = ref<string>("What is your favorite programming language?");

const options = ref<PollOption[]>([
  { text: "JavaScript", votes: 0 },
  { text: "Python", votes: 0 },
  { text: "Java", votes: 0 },
  { text: "C++", votes: 0 },
]);

const resetVote = (): void => {
    hasVoted.value = false;
    error.value = null;
    options.value?.forEach((option)=>option.votes = 0)
}

const vote = (index: number): void => {
  try {
    if (hasVoted.value) {
      throw new Error("You have already voted.");
    }

    if (index < 0 || index >= options.value.length) {
      throw new Error("Invalid option selected.");
    }

    options.value[index].votes++;
    hasVoted.value = true;
    error.value = null;
  } catch (err) {
    error.value = err instanceof Error ? err.message : "An unexpected error occurred.";
  }
};

const calculatePercentage = (votes: number): string => {
  try {
    const totalVotes = options.value.reduce((sum, option) => sum + option.votes, 0);

    if (totalVotes === 0) {
      return "0.00";
    }

    const percentage = (votes / totalVotes) * 100;
    return percentage.toFixed(2);
  } catch (err) {
    error.value = "Failed to calculate percentage.";
    return "0.00";
  }
};

</script>

<style scoped>

</style>