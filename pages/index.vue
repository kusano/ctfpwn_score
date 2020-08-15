<template>
  <div v-if="problems.length==0">
    <v-progress-circular
      color="primary"
      size="128"
      indeterminate
    />
  </div>
  <div v-else>
    <v-layout v-if="cleared" justify-center>
      <v-img :src="congrats" max-width="640" class="my-4" style="border: solid #444 1px" />
    </v-layout>
    <v-layout v-if="cleared" justify-center>
      <div class="text-right">
        <a class="twitter-share-button"
          :href="getTweetURL('Solved all problems!!!')"
          data-size="large"
        >Tweet</a>
      </div>
    </v-layout>
    <v-layout wrap>
      <v-flex v-for="(problem, index) in problems" :key="index" xs12 sm6 md6 lg4 xl3>
        <v-card class="mx-2 my-4" outlined>
          <v-card-title>
            <v-icon v-if="problem.solved" color="green">mdi-check-box-outline</v-icon>
            <span v-if="problem.solved" class="mx-2" />
            {{problem.title}}
            <div v-for="g in problem.genre" :key="g">
              <span class="mx-1" />
              <v-chip x-small>{{g}}</v-chip>
            </div>
          </v-card-title>
          <v-img :src="problem.image" :style="{filter: problem.solved ? '' : 'grayscale(90%)'}">
          </v-img>
          <v-card-text>
            <div v-if="problem.solved" class="text-right">
              <a class="twitter-share-button"
                :href="getTweetURL('Solved ' + problem.title + '!')"
              >Tweet</a>
            </div>
            <div v-html="problem.description" />
          </v-card-text>
          <v-divider />
          <v-card-actions>
            <v-form @submit.prevent="submit(problem)" style="width: 100%">
              <v-text-field
                v-model.trim="problem.input"
                label="Flag"
                placeholder="FLAG{????}"
                :disabled="problem.submitting"
                :success-messages="problem.inputSuccess"
                :error-messages="problem.inputError"
              >
                <template v-slot:append-outer>
                  <v-btn
                    type="submit"
                    :loading="problem.submitting"
                  >
                    <v-icon left style="font-size: 18px">mdi-send</v-icon>
                    Submit
                  </v-btn>
                </template>
              </v-text-field>
            </v-form>
          </v-card-actions>
        </v-card>
      </v-flex>
    </v-layout>
  </div>
</template>

<style>
.v-btn {
  text-transform: none;
}
</style>

<script>
import sha256 from 'crypto-js/sha256';

// :-(
// https://developer.twitter.com/en/docs/twitter-for-websites/javascript-api/guides/set-up-twitter-for-websites
function reloadTwitter() {
  const id = "twitter-wjs";
  const e = document.getElementById("id");
  if (e)
    e.remove();

  const s = document.createElement("script");
  s.id = id;
  s.src = "https://platform.twitter.com/widgets.js";
  document.body.appendChild(s);

  const t = window.twttr || {};
  t._e = [];
  t.ready = f => {t._e.push(f);}
  window.twttr = t;
}

export default {
  data() {
    return {
      problems: []
    }
  },
  created() {
    fetch("/files/problem.json")
      .then(res => res.json())
      .then(data => {
        this.id = data.id;
        this.congrats = data.congrats;

        let storage = JSON.parse(localStorage[this.id] || "{}");

        for (let problem of data.problems) {
          problem.input = "";
          problem.submitting = false;
          problem.solved = false;
          problem.inputSuccess = "";
          problem.inputError = "";

          let flag = storage[problem.title];
          if (flag && problem.flag==sha256(flag))
              problem.solved = true;
        }
        this.problems = data.problems;

        reloadTwitter();
      });
  },
  methods: {
    submit(problem) {
      if (problem.processing)
        return;
      problem.processing = true;

      problem.submitting = true;
      setTimeout(() => {
        problem.submitting = false;
        if (problem.flag==sha256(problem.input)) {
          problem.solved = true;
          problem.inputSuccess = "correct";

          let storage = JSON.parse(localStorage[this.id] || "{}");
          storage[problem.title] = problem.input;
          localStorage[this.id] = JSON.stringify(storage);

          reloadTwitter();
        } else {
          problem.inputError = "wrong";
        }
        setTimeout(() => {
          problem.input = "";
          problem.inputSuccess = "";
          problem.inputError = "";
          problem.processing = false;
        }, 3000);
      }, 1000);
    },
    getTweetURL(text) {
      return "https://twitter.com/intent/tweet" +
        "?text=" + encodeURIComponent(text) +
        "&url=" + encodeURIComponent("https://sanya.sweetduet.info/ctfpwn/") +
        "&hashtags=" + encodeURIComponent("MalleusCTFPwn");
    },
  },
  computed: {
    cleared() {
      return this.problems.every(problem => problem.solved);
    },
  },
}
</script>
