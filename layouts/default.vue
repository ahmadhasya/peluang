<template>
  <v-app>
    <Title>Vue JS Peluang | {{ menu }}</Title>
    <slot />
    <v-bottom-navigation v-model="menu" color="primary">
      <v-btn value="Dadu" to="/dice">
        <v-icon>mdi-dice-3</v-icon>

        <span>Dadu</span>
      </v-btn>

      <v-btn value="Kartu" to="/card">
        <v-icon>mdi-cards-playing-heart-multiple</v-icon>

        <span>Kartu</span>
      </v-btn>

      <v-btn value="Koin" to="/coin">
        <v-icon>mdi-hand-coin</v-icon>

        <span>Koin</span>
      </v-btn>
      <v-btn value="Bantuan" to="/help">
        <v-icon>mdi-help</v-icon>

        <span>Bantuan</span>
      </v-btn>
    </v-bottom-navigation>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      menu: null,
    };
  },
};
</script>
