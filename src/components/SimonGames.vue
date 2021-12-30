<template>
  <div class="simon">
    <h1 class="simon__title">Simon says</h1>
    <div class="simon__inner">
      <div class="simon__circle">
        <div class="sector"
             v-for="sector in sectors"
             :class="[sector.classes, sector.isHighlighted ? 'highlighted' : '']"
             :id="sector.id"
             @click="handleSectorClick(sector.id)"
        ></div>
      </div>
      <div class="simon__table">
        <AppAlert
          v-show="isOpen"
          :round="round"
          :difficulty="difficulty"
          type="lose"
          @close="isOpen = false"
        />

        <div>
          <h2 class="simon__subtitle">Раунд {{ round }}</h2>
          <button class="btn" @click="startTheGame">{{ buttonName }}</button>
        </div>

        <div class="simon__options">
          <h2 class="simon__subtitle">Сложность игры</h2>
          <RadioInput
            v-for="option in optionsRadioInput"
            :id="option.id"
            :label="option.label"
            :value="option.value"
            :checked="difficulty"
            @changeLevel="changeDifficulty"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import {computed, ref} from "vue"
import RadioInput from "./RadioInput"
import AppAlert from "./AppAlert"

import audio1 from '../assets/sounds/1.ogg'
import audio2 from '../assets/sounds/2.ogg'
import audio3 from '../assets/sounds/3.ogg'
import audio4 from '../assets/sounds/4.ogg'


export default {
  setup() {
    const round = ref(0)
    const sectors = ref([
      {
        classes: 'sector-blue',
        id: 0,
        isHighlighted: false
      },
      {
        classes: 'sector-yellow',
        id: 1,
        isHighlighted: false
      },
      {
        classes: 'sector-red',
        id: 2,
        isHighlighted: false
      },
      {
        classes: 'sector-green',
        id: 3,
        isHighlighted: false
      }
    ])
    const difficulty = ref('easy')
    const alert = ref(false)
    const optionsRadioInput = ref([
      {
        id: 'easy',
        value: 'easy',
        label: 'Легко',
      },
      {
        id: 'normal',
        value: 'normal',
        label: 'Нормально',
      },
      {
        id: 'hard',
        value: 'hard',
        label: 'Сложно',
      }
    ])
    const isOpen = ref(false)
    const audios = [
      audio1,
      audio2,
      audio3,
      audio4
    ]
    const timeout = computed(() => {
      switch (difficulty.value) {
        case 'easy':
          return 1.5
        case 'normal':
          return 1
        case 'hard':
          return 0.4
        default:
          return 1.5
      }
    })
    const isGameOn = ref(false)
    const isUserFail = ref(false)
    const seconds = timeout.value * 1000

    const buttonName = computed(() => {
      if (isUserFail.value) {
        return 'Ещё раз'
      } else {
        return 'Старт'
      }
    })

    const userSelectedSectors = computed(() => {
      return []
    })

    const gameSelectedSectors = computed(() => {
      const data = []
      for(let i = 0; i < round.value; i++) {
        data.push(getRandomNumber())
      }
      return data
    })

    const changeDifficulty = (data) => {
      difficulty.value = data
    }

    const playAudio = (index) => {
      const audio = new Audio(audios[index])
      audio.play()
    }

    const setIsGameOn = (value) => {
      isGameOn.value = value
    }

    const handleSectorClick = (sectorId) => {
      playAudio(sectorId)
      if (!isGameOn.value) return
      highlightElement(sectorId)
      setUserSelectedSelector(sectorId)
      setIsUserFail(sectorId)
      if (!isUserFail.value && gameSelectedSectors.value.length === userSelectedSectors.value.length) {
        startNextRound()
      }
    }

    const getRandomNumber = () => {
      return Math.floor(Math.random() * Math.floor(sectors.value.length))
    }

    const highlightElement = (sectorId) => {
      sectors.value[sectorId].isHighlighted = true
      playAudio(sectorId)
      setTimeout(() => {
        sectors.value[sectorId].isHighlighted = false
      }, seconds / 2 )
    }

    const highlightElements = () => {
      gameSelectedSectors.value.forEach((id, index) => {
        setTimeout(() => {
          highlightElement(id)
        }, seconds * (index + 1))
      })
    }

    const setUserSelectedSelector = (value) => {
      userSelectedSectors.value.push(value)
    }

    const clearUserSelectedSelector = () => {
      userSelectedSectors.value.length = 0
    }

    const setIsUserFail = (userClickSectorId) => {
      if (gameSelectedSectors.value[userSelectedSectors.value.length - 1] !== userClickSectorId) {
        return getUserFail()
      }
    }

    const getUserFail = () => {
      isGameOn.value = false
      isUserFail.value = true
      isOpen.value = true
    }

    const startTheGame = () => {
        clearUserSelectedSelector()
        setIsGameOn(true)
        isUserFail.value = false
        isOpen.value = false
        round.value = 1
      highlightElements()
    }

    const startNextRound = () => {
      clearUserSelectedSelector()
      round.value++
      highlightElements()
    }


    return {
      round,
      sectors,
      optionsRadioInput,
      alert,
      isOpen,
      audios,
      buttonName,
      playAudio,
      handleSectorClick,
      difficulty,
      changeDifficulty,
      startTheGame
    }
  },
  components: {
    RadioInput,
    AppAlert
  }
}
</script>

<style>
  .simon {
    margin: 0 auto;
    padding: 10px;

    width: 100%;
    max-width: 1000px;
    height: 100%;
  }

  .simon__title {
    margin-bottom: 40px;
    font-size: 36px;
  }

  .simon__subtitle {
    font-size: 24px;
  }

  .simon__inner {
    display: flex;
    align-items: center;
    flex-wrap: wrap;
  }

  .simon__circle,
  .simon__table {
    height: 500px;
    flex-basis: 50%;
  }

  .simon__circle {
    display: flex;
    flex-direction: column;
    align-items: center;
    flex-wrap: wrap;


    background: #fff;
    border: 5px solid #2c3e50;
  }

  .simon__table {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;

    position: relative;
  }

  .simon__options {
    margin-top: 20px;

    display: flex;
    flex-direction: column;
  }

  .simon__option {
    margin-top: 10px;

    text-align: left;
  }

  .simon__option > label {
    margin-left: 5px;
  }

  .sector {
    width: 50%;
    height: 50%;
    opacity: .6;
    box-shadow: 1px 1px 5px 0 #2c3e50;
  }

  .sector.highlighted {
    opacity: 1;
  }

  .sector-red {
    background: #FF5643;
  }

  .sector-blue {
    background: dodgerblue;
  }

  .sector-green {
    background: #BEDE15;
  }

  .sector-yellow {
    background: #FEEF33;
  }

  .btn {
    margin-top: 10px;
    width: 120px;
    height: 35px;

    font-size: 18px;

    cursor: pointer;
    outline: none;
    color: #aaa2ce;
    background: #fff;
    border: 0;
    border-radius: 5px;
    box-shadow: 0px 1px 5px 0 rgba(0, 0, 0, .25);

    transition: all .2s linear;
  }

  .btn:hover {
    background: #aaa2ce;
    color: #fff;
    font-size: 20px;
  }

  .alert {
    width: 50%;
    height: 50px;

    position: absolute;
    left: 122px;
    top: 0;

    display: flex;
    justify-content: center;
    align-items: center;

    font-size: 18px;
    border-radius: 5px;
  }

  .alert.lose {
    background: #FF5643;
    color: #fff;
  }

</style>
