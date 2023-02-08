<template>
  <div class="screen">
    <div
      class="screen__inner"
      :style="{
        width: `${
          ((((920 - 16 * 4) / Math.sqrt(cardsContext.length) - 16) * 3) / 4 +
            16) *
          Math.sqrt(cardsContext.length)
        }px`,
      }"
    >
      <CardItem
        :imgBackFaceUrl="`imgs/${card}.png`"
        v-for="(card, index) in cardsContext"
        :key="index"
        :ref="`card-${index}`"
        :card="{ index, value: card }"
        :cardsContext="cardsContext"
        @onFlip="checkRule($event)"
      ></CardItem>
    </div>
  </div>
</template>
<style lang="css" scoped>
.screen {
  width: 100%;
  height: 100vh;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 2;
  background-color: var(--dark);
  color: var(--light);
}
.screen__inner {
  display: flex;
  flex-wrap: wrap;
  margin: 2rem auto;
  /* align-items: center;
  justify-content: center; */
}
</style>


<script lang="ts">
import CardItem from "./CardItem.vue";
interface CardItemInterface {
  index: Number;
  value: Number;
}

export default {
  props: {
    cardsContext: {
      type: Array,
      default: function () {
        return [];
      },
    },
  },
  components: {
    CardItem,
  },
  data() {
    return {
      rules: [] as Array<CardItemInterface>,
    };
  },
  methods: {
    checkRule(card: CardItemInterface) {
      if (this.rules.length === 2) return false;
      this.rules.push(card);
      if (
        this.rules.length === 2 &&
        this.rules[0].value === this.rules[1].value
      ) {
        const cardFirst = this.$refs[`card-${this.rules[0].index}`] as any;
        cardFirst[0].onDisabledCard();
        const cardSecond = this.$refs[`card-${this.rules[1].index}`] as any;
        cardSecond[0].onDisabledCard();
        this.rules = [];

        const disabledElements = document.querySelectorAll(
          ".screen .card.disabled"
        );
        if (
          disabledElements &&
          disabledElements.length === this.cardsContext.length - 2
        ) {
          setTimeout(() => {
            this.$emit("onFinish");
          }, 920);
        }
      } else if (
        this.rules.length === 2 &&
        this.rules[0].value !== this.rules[1].value
      ) {
        setTimeout(() => {
          const cardFirst = this.$refs[`card-${this.rules[0].index}`] as any;
          cardFirst[0].onFlipBackCard();
          const cardSecond = this.$refs[`card-${this.rules[1].index}`] as any;
          cardSecond[0].onFlipBackCard();
          this.rules = [];
        }, 800);
      } else return false;
    },
  },
};
</script>