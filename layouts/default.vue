<template>
  <v-app>
    <v-app-bar app absolute>
      <v-icon>mdi-flower</v-icon>
      <div class="mx-2" />
      <v-toolbar-title>Malleus CTF Pwn Challenge</v-toolbar-title>
    </v-app-bar>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
    <v-footer app absolute>
      <div>
        <a href="https://twitter.com/kusano_k" target="_blank">@kusano_k</a>
      </div>
      <v-spacer />
      <div>
        <v-btn x-small>
          <v-icon color="error" @click="dialogRemove=true">mdi-eraser</v-icon>
        </v-btn>
      </div>
    </v-footer>

    <v-dialog v-model="dialogRemove" max-width="480">
      <v-card outlined>
        <v-card-title>解答履歴削除</v-card-title>
        <v-card-text>解答履歴を削除して、全ての問題を未解答の状態に戻しますか？</v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn @click="removeHistory()" color="error">削除</v-btn>
          <v-btn @click="dialogRemove=false">キャンセル</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

  </v-app>
</template>

<script>
  export default {
    data () {
      return {
        dialogRemove: false,
      }
    },
    methods: {
      removeHistory() {
        fetch("/files/problem.json")
          .then(res => res.json())
          .then(data => {
            localStorage.removeItem(data.id);
            location.reload();
          });
      }
    }
  }
</script>
