<script>
import { computed } from 'vue';
export default {
  props: {
    matched: {
      type: Boolean,
      default: false,
    },
    position: {
      type: Number,
      required: true,
    },
    value: {
      type: String,
      required: true,
    },
    visible: {
      type: Boolean,
      default: false,
    },
  },
  setup(props, context) {
    const flipStyles = computed(() => {
      if (props.visible) {
        return 'is-flipped';
      }
    });

    const selectCard = () => {
      context.emit('select-card', {
        position: props.position,
        faceValue: props.value,
      });
    };

    return {
      selectCard,
      flipStyles,
    };
  },
};
</script>

<template>
  <div class="card" :class="flipStyles" @click="selectCard">
    <div class="card-face is-front">
      {{ value }}
      <div v-if="matched" class="correct">correct!</div>
    </div>
    <div class="card-face is-back"></div>
  </div>
</template>

<style>
.card {
  position: relative;
  transition: 0.5s transform ease-in;
  transform-style: preserve-3d;
}

.card.is-flipped {
  transform: rotateY(180deg);
}

.card-face {
  width: 100%;
  height: 100%;
  position: absolute;
  border-radius: 7px;
  cursor: pointer;
  backface-visibility: hidden;
}

.card-face.is-front {
  background-color: purple;
  color: white;
  font-size: 20px;
  transform: rotateY(180deg);
}

.card-face.is-back {
  background-color: orange;
  color: white;
}

.correct {
  position: absolute;
  bottom: 5px;
  right: 5px;
  color: lightgreen;
}
</style>
