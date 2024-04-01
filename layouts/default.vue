<template>
  <v-app>
    <slot />
    <v-bottom-navigation color="primary">
      <v-btn value="recent" to="/dice">
        <v-icon>mdi-dice-3</v-icon>

        <span>Dice</span>
      </v-btn>

      <v-btn value="favorites" to="/card">
        <v-icon>mdi-cards-playing-heart-multiple</v-icon>

        <span>Card</span>
      </v-btn>

      <v-btn value="nearby" to="/coin">
        <v-icon>mdi-hand-coin</v-icon>

        <span>Coin</span>
      </v-btn>
    </v-bottom-navigation>
  </v-app>
</template>

<script>

</script>
