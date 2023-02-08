<script setup lang="ts">
</script>

<template>
  <div>
    <MainScreen
      v-if="statusMatch === 'default'"
      @onStart="handleBeforeStart($event)"
    ></MainScreen>
    <InteractScreen
      v-if="statusMatch === 'match'"
      :cardsContext="settings.cardsContext"
      @onFinish="onGetResult()"
    ></InteractScreen>
    <ResultScreen
      :timer="timer"
      v-if="statusMatch === 'result'"
      @onStartAgain="statusMatch = 'default'"
    ></ResultScreen>
    <CoppyRight></CoppyRight>
  </div>
</template>


<script lang="ts">
import MainScreen from "./components/MainScreen.vue";
import InteractScreen from "./components/InteractScreen.vue";
import ResultScreen from "./components/ResultScreen.vue";
import CoppyRight from "./components/CoppyRight.vue";
import shuffled from "./utils/array";
interface Config {
  totalBlocks: number;
}

export default {
  name: "App",
  components: {
    MainScreen,
    InteractScreen,
    ResultScreen,
    CoppyRight,
  },
  data() {
    return {
      settings: {
        total: 0,
        cardsContext: [] as Array<number>,
        startedAt: 0 as number,
      },
      statusMatch: "default",
      timer: 0,
    };
  },

  methods: {
    handleBeforeStart(config: Config) {
      const { totalBlocks } = config;
      this.settings.total = totalBlocks;

      const firstCards: Array<number> = Array.from(
        { length: this.settings.total / 2 },
        (_, i) => i + 1
      );
      const secondCards: Array<number> = [...firstCards];
      const cards = [...firstCards, ...secondCards];
      this.settings.cardsContext = shuffled(
        shuffled(shuffled(shuffled(cards)))
      );
      this.settings.startedAt = new Date().getTime();
      this.statusMatch = "match";
    },
    onGetResult() {
      this.timer = new Date().getTime() - this.settings.startedAt;
      this.statusMatch = "result";
    },
  },
};
</script>

