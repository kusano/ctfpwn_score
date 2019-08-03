<template>
  <div v-if="problems.length==0">
    <v-progress-circular
      color="primary"
      size="128"
      indeterminate
    />
  </div>
  <div v-else>
    <v-layout wrap>
      <v-flex v-for="(problem, index) in problems" xs12 sm6 md4 lg3 xl2>
        <v-card class="mx-2 my-4">
          <v-card-title>
            {{problem.title}}
            <div v-for="g in problem.genre">
              <span class="mx-1" />
              <v-chip small>{{g}}</v-chip>
            </div>
          </v-card-title>
          <v-img :src="problem.image" style="filter:grayscale(90%)">
          </v-img>
          <v-card-text v-html="problem.description" />
          <v-divider />
          <v-card-actions>
            <v-text-field label="Flag" placeholder="FLAG{????}">
              <template v-slot:append-outer>
                <v-btn>
                  <v-icon left>mdi-send</v-icon>
                  Submit
                </v-btn>
              </template>
            </v-text-field>
          </v-card-actions>
        </v-card>
      </v-flex>
    </v-layout>
  </div>
</template>

<script>
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
        this.problems = data.problems;
      });
  }
}
</script>
