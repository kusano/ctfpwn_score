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
      <v-img :src="congrats" max-width="640" class="my-4" />
    </v-layout>
    <v-layout wrap>
      <v-flex v-for="(problem, index) in problems" :key="index" xs12 sm6 md4 lg3 xl2>
        <v-card class="mx-2 my-4">
          <v-card-title>
            <v-icon v-if="problem.solved" color="green">mdi-check-box-outline</v-icon>
            <span v-if="problem.solved" class="mx-2" />
            {{problem.title}}
            <div v-for="g in problem.genre">
              <span class="mx-1" />
              <v-chip small>{{g}}</v-chip>
            </div>
          </v-card-title>
          <v-img :src="problem.image" :style="{filter: problem.solved ? '' : 'grayscale(90%)'}">
          </v-img>
          <v-card-text v-html="problem.description" />
          <v-divider />
          <v-card-actions>
            <form @submit.prevent="submit(problem)">
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
                    <v-icon left>mdi-send</v-icon>
                    Submit
                  </v-btn>
                </template>
              </v-text-field>
            </form>
          </v-card-actions>
        </v-card>
      </v-flex>
    </v-layout>
  </div>
</template>

<script>
import sha256 from 'crypto-js/sha256';

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
        for (let problem of data.problems) {
          problem.input = "";
          problem.submitting = false;
          problem.solved = false;
          problem.inputSuccess = "";
          problem.inputError = "";
        }
        this.problems = data.problems;
        this.congrats = data.congrats;
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
  },
  computed: {
    cleared() {
      return this.problems.every(problem => problem.solved);
    },
  },
}
</script>
