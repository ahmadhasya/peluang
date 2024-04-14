<template>
  <v-app>
    <Title>Vue JS Peluang | {{ menu }}</Title>
    <slot />
    <v-bottom-navigation v-model="menu" color="primary">
      <v-btn value="Dice" to="/dice">
        <v-icon>mdi-dice-3</v-icon>

        <span>Dice</span>
      </v-btn>

      <v-btn value="Card" to="/card">
        <v-icon>mdi-cards-playing-heart-multiple</v-icon>

        <span>Card</span>
      </v-btn>

      <v-btn value="Coin" to="/coin">
        <v-icon>mdi-hand-coin</v-icon>

        <span>Coin</span>
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
