<template>
<div>
    <h1>Welcome to My the Page</h1>
    <button @click="game1">Game 1</button>
    <button @click="game2">Game 2</button>
    <!-- <nuxt-link to="/game/gameLatThe">Game 1</nuxt-link> -->
  </div>
</template>

<script>
export default {
  name: "Index",
  data () {
    return {
    }
  },
  methods: {
    game1 () {
      this.$router.push({ name: 'game1' })
    },
    game2 () {
      this.$router.push({ name: 'game2' })
    }
  }
}
</script>

<style scoped lang="scss">

</style>
