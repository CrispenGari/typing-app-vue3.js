<template>
  <form action="" class="form" @submit.prevent="submitHandler">
    <h1>Type the following paragraph.</h1>
    <p class="form__text">
      {{ paragraphText }}
    </p>
    <textarea
      spellcheck="true"
      placeholder="Start Typing..."
      class="form__input"
      v-model.trim="text"
      :disabled="!started || time < 0"
    ></textarea>
    <div class="form__timer__words">
      <div class="form__timer__stats">
        {{ text === "" ? 0 : typedWords }}/ {{ paragraphWords }} words
      </div>
      <div class="form__timer__stats">
        {{ typedCharacters }} / {{ paragraphCharacters }} characters
      </div>
      <div class="form__words">
        <div
          :class="
            `form__timer__timer ${
              time === 0
                ? 'form__time__up'
                : (time / totalTime) * 100 < 25
                ? 'form__time__five'
                : (time / totalTime) * 100 < 50
                ? 'form__time__ten'
                : ''
            }`
          "
        >
          <p v-show="false">{{ timeRequired }}</p>
          {{ `${time > 0 ? time : 0} ` }}
          s
        </div>
      </div>
    </div>

    <div class="form__controls">
      <button type="button" @click="clearHandler" :disabled="time < 0">
        Clear
      </button>
      <button type="button" :disabled="started" @click="startTime()">
        Start
      </button>
      <button type="button" @click="restartHandler">Restart</button>
      <button type="submit">Submit</button>
    </div>
  </form>
  <div class="form__results__stats">
    <h1>
      Typing Summary
      <span
        :class="
          `form__results__barge ${
            alertProps?.percentage > 90
              ? 'best'
              : alertProps?.percentage > 75
              ? 'better'
              : alertProps?.percentage > 50
              ? 'good'
              : 'bad'
          }`
        "
      >
        {{
          alertProps?.percentage > 90
            ? "best"
            : alertProps?.percentage > 75
            ? "better"
            : alertProps?.percentage > 50
            ? "good"
            : "bad"
        }}
      </span>
    </h1>
    <div class="form__results__row">
      <span>Total Words</span>
      <span>{{ paragraphWords }}</span>
    </div>
    <div class="form__results__row">
      <span>Total Words Typed</span>
      <span>{{
        alertProps?.totalWordsTyped ? alertProps?.totalWordsTyped : 0
      }}</span>
    </div>
    <div class="form__results__row">
      <span>Total Correct Words Typed</span>
      <span>{{
        alertProps?.totalCorrectWordsTyped
          ? alertProps?.totalCorrectWordsTyped
          : 0
      }}</span>
    </div>
    <div class="form__results__row">
      <span>Ratting</span>
      <span v-if="alertProps?.totalCorrectWordsTyped >= 1">{{
        `${alertProps?.percentage} %`
      }}</span>
      <span v-else>0</span>
    </div>
  </div>
</template>

<script>
import constants from "../../utils";
export default {
  name: "Form",
  data() {
    return {
      paragraphIndex:
        Math.floor(Math.random() * 100) % constants?.paragraphs?.length,
      // Math.floor(Math.random() * 100) % constants?.paragraphs?.length,
      paragraphs: constants?.paragraphs,
      text: "",
      time: 0,
      alertProps: {},
      started: false,
    };
  },
  mounted() {},
  created() {
    this.time =
      String(this.paragraphs[this.paragraphIndex]?.text).split(" ").length + 30;
  },
  methods: {
    startTime() {
      this.started = true;
      const self = this;

      setInterval(() => {
        self.time--;
      }, 1000);
    },
    submitHandler() {
      let wordsObtained = 0;

      const typedWordsArray = this.text.split(" ");
      const paragraphWords = this.paragraphs[this.paragraphIndex]?.text?.split(
        " "
      );
      console.log(paragraphWords);
      for (let i = 0; i < paragraphWords.length; i++) {
        if (typedWordsArray[i] === paragraphWords[i]) {
          wordsObtained++;
        } else {
          wordsObtained += 0;
        }
      }

      setTimeout(() => {
        this.alert = false;
      }, 10000);
      this.alertProps = {
        totalWords: paragraphWords.length,
        totalWordsTyped:
          typedWordsArray.length === 1 ? 0 : typedWordsArray.length,
        totalCorrectWordsTyped: wordsObtained,
        percentage: Math.ceil((wordsObtained / paragraphWords.length) * 100),
      };
    },
    clearHandler() {
      this.text = "";
    },
    restartHandler() {
      (this.paragraphIndex =
        Math.floor(Math.random() * 100) % constants?.paragraphs?.length),
        (this.paragraphs = constants?.paragraphs),
        (this.text = ""),
        (this.time = this.totalTime);
    },
  },
  computed: {
    paragraphWords() {
      return String(this.paragraphs[this.paragraphIndex]?.text).split(" ")
        .length;
    },
    totalTime() {
      return (
        String(this.paragraphs[this.paragraphIndex]?.text).split(" ").length +
        30
      );
    },
    typedCharacters() {
      return String(this.text).length;
    },
    paragraphCharacters() {
      return String(this.paragraphs[this.paragraphIndex]?.text).length;
    },
    paragraphText() {
      return this.paragraphs[this.paragraphIndex]?.text;
    },
    typedWords() {
      return String(this.text).split(" ").length;
    },
    timeRequired() {
      return this.time;
    },
  },
};
</script>

<style scoped>
.form {
  width: 100%;
  max-width: 500px;
  background: rgba(0, 0, 0, 0.8);
  padding: 10px;
  border-radius: 5px;
  user-select: none;
  color: white;
  overflow: hidden;
}
.form > h1,
.form__results__stats > h1 {
  text-align: center;
  font-weight: 500;
  font-size: 15px;
  text-transform: uppercase;
  letter-spacing: 2px;
}
.form__results__stats > h1 {
  display: flex;
  width: 100%;
  justify-content: center;
}
p.form__text {
  width: 100%;
  user-select: none;
  font-size: 13px;
  margin: 10px 0px;
}
textarea.form__input {
  width: 100%;
  height: 25vh;
  outline: none;
  margin: 2px 0;
  border: none;
  border-radius: 5px;
  resize: none;
  font-size: 14px;
  padding: 10px;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
}
.form__controls,
.form__timer__words {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 0;
}
.form__timer__timer {
  background: black;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  font-size: 12px;
  border: 2px solid lightseagreen;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
.form__timer__stats {
  font-size: 14px;
  color: gray;
}
.form__controls > button {
  background: black;
  outline: none;
  width: 90px;
  border: none;
  padding: 5px;
  color: white;
  border-radius: 5px;
  cursor: pointer;
}
.form__time__ten {
  border: 2px solid orange;
}
.form__time__five {
  border: 2px solid orangered;
}
.form__time__up {
  border: 2px solid red;
}
.form__controls > button:hover {
  background: green;
  transition: 100ms;
}
.form__controls > button:active {
  background: lightseagreen;
  transition: 100ms;
}
.form__results__stats:hover {
  animation: hover-animation 2s linear;
}
.form__results__stats {
  width: 100%;
  max-width: 500px;
  background: rgba(0, 0, 0, 1);
  padding: 10px;
  border-radius: 5px;
  user-select: none;
  color: white;
  user-select: none;
  cursor: pointer;
}
.form__results__row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 5px 0px;
}
.form__results__row > span {
  font-size: 15px;
  color: gray;
}
.form__results__barge {
  font-size: 11px;
  margin-left: 5px;
  background: green;
  border-radius: 999px;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 0 5px;
}
.bad {
  background: orangered;
}
.better {
  background: lightseagreen;
}
.good {
  background: orange;
}
.best {
  background: green;
}
@keyframes hover-animation {
  0% {
    transform: rotate(0deg);
  }
  25% {
    transform: rotate(30deg);
  }
  50% {
    transform: rotate(-30deg);
  }
  75% {
    transform: rotate(30deg);
  }
  100% {
    transform: rotate(0deg);
  }
}
</style>
