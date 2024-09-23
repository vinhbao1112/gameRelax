<template>
  <div class="container">
    <nuxt-link to="/">
        <button class="back cursor-pointer">Back</button>
    </nuxt-link>
    <section id="game">
      <div class="game">
        <h1>Súc Sắc :  {{ result1 }} , {{ result2 }} , {{ result3 }} </h1>
        <h2>Tổng : {{ resultSucSac }}</h2>
        <div class="text-2">Kết quả : <b><u>{{ render() }}</u></b></div>
        <button
          class="btn cursor-pointer"
          @click="random"
        >
          Random
        </button>
      </div>
      <!-- súc sắc -->
      <div class="vien">
          <button
              class="vien-item"
              @click="randomItem(1)"
              :disabled="!checkRandom"
              :class="{ 'cursor-pointer': checkRandom }"
              :style="{ 'background-color': currentItem === 1 ? 'brown' : 'rgb(47, 204, 212)' ,
                        'color': currentItem === 1 ? 'white' : 'black' }"
            >
              {{ this.result1 }}
          </button>
          <button
              class="vien-item"
              @click="randomItem(2)"
              :disabled="!checkRandom"
              :class="{ 'cursor-pointer': checkRandom}"
              :style="{ 'background-color': currentItem === 2 ? 'brown' : 'rgb(136, 103, 218)',
                        'color': currentItem === 2 ? 'white' : 'black' }"
            >
              {{ this.result2 }}
          </button>
          <button
              class="vien-item"
              @click="randomItem(3)"
              :disabled="!checkRandom"
              :class="{ 'cursor-pointer': checkRandom }"
              :style="{ 'background-color': currentItem === 3 ? 'brown' : 'rgb(5, 219, 15)',
                        'color': currentItem === 3 ? 'white' : 'black'}"
            >
              {{ this.result3 }}
          </button>
      </div>
    </section>

  </div>
</template>

<script>
export default {
  name: "game1",
  data() {
    return {
      time: 500,
      result1 : "Viên 1",
      result2 : "Viên 2",
      result3 : "Viên 3",
      checkRandom : false,
      currentItem : null
    };
  },
  mounted() {
    // this.random()
  },
  computed: {
    resultSucSac() {
      const firstRandom = this.result1
      const secondRandom = this.result2
      const thirdRandom = this.result3
      if (firstRandom == "Viên 1") {
        return "..."
      }
      return firstRandom + secondRandom + thirdRandom
    },
    getTime() {
      return this.time
    }
  },
  methods: {
    render() {
      console.log(this.result1 , this.result2 , this.result3 , this.resultSucSac);
      if (this.resultSucSac === "...") {
        return "..."
      } else if (this.resultSucSac < 12) {
        return "Xỉu";
      } else {
        return "Tài";
      }
    },
    random() {
      this.checkRandom = true
      this.result1 = Math.floor(Math.random() * 6) + 1
      this.result2 = Math.floor(Math.random() * 6) + 1
      this.result3 = Math.floor(Math.random() * 6) + 1
    },
    randomItem(itemNumber) {
      if (this.checkRandom) {
        this.currentItem = itemNumber
        this[`result${itemNumber}`] = Math.floor(Math.random() * 6) + 1
        setTimeout(()=> {
          this.currentItem = null
        }, this.time)
      }
    }
  }
};
</script>

<style scoped lang="scss">
@import "@/assets/scss/gameXucSac.scss";
</style>
