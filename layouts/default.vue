<template>
  <v-app dark>
    <v-app-bar app absolute>
      <v-icon>mdi-flower</v-icon>
      <div class="mx-2" />
      <v-toolbar-title>Malleus CTF Pwn Challenge</v-toolbar-title>
    </v-app-bar>
    <v-content>
      <v-container>
        <nuxt />
      </v-container>
    </v-content>
    <v-footer app absolute>
      <v-spacer />
      <div>
        <a href="https://twitter.com/kusano_k">@kusano_k</a>
      </div>
    </v-footer>
  </v-app>
</template>
