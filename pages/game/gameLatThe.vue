<template>
  <div class="page-content">
    <button class="returnMenu" @click="returnMenu">{{ text.return }}</button>
    <img src="https://bigone.vn/uploads/images/hinh-nen-pikachu-dep-chuan-full-hd-cho-may-tinh%20(42).jpg" alt="Pokemon Logo" class="logo-pokemon" />
    <h1 class="page-title">{{ text.game.title }}</h1>
    <form class="game-options">
      <label
        v-for="(option, index) in options"
        :key="index"
        :for="'gameOption' + (index + 1)"
        class="game-options__label"
      >
          <input
            :id="'gameOption' + (index + 1)"
            type="radio" name="gameOption"
            :value="option"
            class="game-options__input"
            @click="selectOption(option)"
          />

        <svg width="20px" height="20px" viewBox="0 0 20 20">
          <circle cx="10" cy="10" r="9"></circle>
          <path d="M10,7 C8.34314575,7 7,8.34314575 7,10 C7,11.6568542 8.34314575,13 10,13 C11.6568542,13 13,11.6568542 13,10 C13,8.34314575 11.6568542,7 10,7 Z" class="inner"></path>
          <path d="M10,1 L10,1 L10,1 C14.9705627,1 19,5.02943725 19,10 L19,10 L19,10 C19,14.9705627 14.9705627,19 10,19 L10,19 L10,19 C5.02943725,19 1,14.9705627 1,10 L1,10 L1,10 C1,5.02943725 5.02943725,1 10,1 L10,1 Z" class="outer"></path>
        </svg>

        <span class="game-options__text">
          {{ option }}
        </span>
      </label>

    </form>

    <div class="game-data">
      <p class="game-data__item game-data__fails">
        {{ text.game.badSelect }} : {{ gameData }}
      </p>
    </div>

    <ul
      :class="[
        'game-list-cards' ,
        currentOption === 8 ? 'game-list-cards--8' : '',
        currentOption === 10 ? 'game-list-cards--10' : ''
        ]"
    >
      <li
        v-for="(card, index) in selectCards"
        :key="index"
        :class="[
          'game-card',
          card.isUncovered ? 'is-uncovered' : 'is-covered',
          ]"
        @click="toggleCard(card , index)"
      >
        <div class="game-card__face game-card-front">
          <h3 class="game-card-front__name">{{ card.name }}</h3>
          <img :src="card.images.png.front" :alt="card.name" class="game-card-front__image" />
        </div>
        <div class="game-card__face game-card-back">
          <div class="game-card-back__inner">?</div>
        </div>
      </li>
    </ul>

    <button
      class="button button--game"
      @click="resetGame(option)"
    >
      {{ text.game.reset }}
    </button>
    <div
      class="game-result game-result--win"
      v-if="checkWin"
    >
      {{ text.game.win }}
    </div>
    <div
      class="game-result game-result--lose"
      v-if="checkLose"
    >
      {{ text.game.lose }}
    </div>

  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'GamePage',
  data() {
    return {
      data: [],
      text : {
        game : {
          title : "Pokemon",
          win : "Congratulations! You Win!",
          lose: "You lose! Good Luck!",
          reset: "Reset Game",
          badSelect : "Bad Select Number"
        },
        return : "Return to Menu"

      },
      options: [4, 6, 8, 10],
      gameData : 10,
      isUncovered : false,
      selectCards: [],

      firstCardIndex: null,
      isLocked: false,

      currentOption: null,
      checkWin: false,
      checkLose: false
    }
  },
  mounted() {
    this.fetchData()
  },
  computed : {
    isGameWon() {
      return this.selectCards.every(card => card.isUncovered)
    },
    isGameLost() {
      return this.gameData <= 0 && !this.isGameWon
    }
  },
  watch: {
    // isGameWon(newVal) {
    //   if (newVal) {
    //     setTimeout(() => { this.checkWin = true }, 500)
    //   } else {
    //     this.checkWin = false
    //   }
    //}
    isGameWon(newVal) {
      // Khi người chơi chiến thắng
      if (newVal) {
        setTimeout(() => { this.checkWin = true }, 500)
      }
    },
    isGameLost(newVal) {
      // Khi người chơi thua cuộc
      if (newVal) {
        setTimeout(() => {
          this.checkLose = true
          // Lật tất cả các thẻ khi thua cuộc
          this.selectCards.forEach(card => card.isUncovered = true)
          this.isLocked = true  // Khóa trò chơi
        }, 500)
      }
    },
    currentOption(newOption, oldOption) {
      if (newOption !== oldOption) {
        this.resetGame()
      }
    }
  },
  methods: {
    async fetchData() {
      try {
        const response = await axios.get('/dataLatBai.json')
        // const response = await this.$axios.$get('/api/dataLatBai')
        console.log(1111111 ,response);

        this.data = response.data.map((item)=> ({
          ...item,
          isUncovered : false
        }))

        // Giá trị mặc định
        this.selectOption(4)

      } catch (error) {
        console.error("Failed to fetch data:", error)
        this.data = []
      }
    },
    selectOption(option) {
      this.currentOption = option
      this.selectCards = this.prepareCardDeck(option)
    },
    prepareCardDeck(option) {
      // Tạo và trộn bộ bài dựa trên lựa chọn.
      const randomData = this.data.sort(() => 0.5 - Math.random())
      const selectedCards = randomData.slice(0, option)
      const cardDeck = [...selectedCards, ...selectedCards].sort(() => Math.random() - 0.5)
      return cardDeck.map(item => ({
        ...item,
        isUncovered: false
      }))

    },
    toggleCard(card , index) {
      // Kiểm tra nếu trò chơi đang khóa hoặc thẻ đã lật.
      if (this.isLocked || card.isUncovered) return

      if (this.firstCardIndex === null) {
            // Lưu chỉ số của lá bài đầu tiên và lật lá bài này
            this.firstCardIndex = index
            this.selectCards[index].isUncovered = true
        } else {
            // Lật lá bài thứ hai
            this.selectCards[index].isUncovered = true

            // So sánh tên của hai lá bài
            if (this.selectCards[this.firstCardIndex].name === this.selectCards[index].name) {
                this.firstCardIndex = null
            } else {
                this.isLocked = true
                console.log(this.gameData)
                this.gameData -= 1

                setTimeout(() => {
                    this.selectCards[this.firstCardIndex].isUncovered = false
                    this.selectCards[index].isUncovered = false
                    this.firstCardIndex = null

                    this.isLocked = false
                }, 500)
            }
      }
    },
    resetGame() {
      this.selectOption(this.currentOption)

      this.selectCards.forEach(card => {
        card.isUncovered = false
      })

      this.firstCardIndex = null
      this.isLocked = false
      this.checkWin = false
      this.checkLose = false
      this.gameData = 10
    },
    returnMenu () {
      this.$router.push('/')
    }
  }
}
</script>

<style scoped>
@import '@/assets/css/gameLatThe.css';
</style>
