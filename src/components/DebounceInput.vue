<script lang="ts">
import debounce from 'lodash.debounce';
import {
  defineComponent,
  onMounted,
  ref,
  reactive,
  toRefs,
  computed,
  watch,
} from 'vue';

export default defineComponent({
  setup() {
    const state = reactive({
      question: '',
      answer: 'Questions usually contain a question mark. ;-)',
      async getAnswer() {
        state.answer = 'Thinking...';
        try {
          const res = await fetch('https://yesno.wtf/api');
          state.answer = (await res.json()).answer;
        } catch (error) {
          state.answer = 'Error! Could not reach the API. ' + error;
        }
      },
    });

    const debouncedAnswer = debounce((event) => {
      state.question = event.target.value;
    }, 500);

    return { debouncedAnswer, ...toRefs(state) };
  },
  watch: {
    question(question) {
      if (question.indexOf('?') > -1) {
        this.getAnswer();
      }
    },
  },
});
</script>

<template>
  <p>
    Ask a yes/no question:
    <input :value="question" @input="(event) => debouncedAnswer(event)" />
  </p>
  <p>{{ answer }}</p>
</template>
