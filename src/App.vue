<template>
  <v-app>
    <!--    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
          transition="scale-transition"
          width="40"
        />
      </div>

      <v-spacer></v-spacer>
    </v-app-bar>-->

    <v-main>
      <v-container>
        <div class="wrapper mt-2 text-center rounded-lg elevation-15">
          <v-row>
            <v-col cols="4">
              <strong>Початкове значення пресу</strong>
            </v-col>
            <v-col cols="8">
              <strong>Розхід</strong>
            </v-col>
            <!--            <v-col cols="3">
              <strong>Випередження таймеру</strong>
            </v-col>-->
          </v-row>

          <v-row>
            <v-col cols="4">
              <span>cm<sup>3</sup></span>
            </v-col>
            <v-col cols="4">
              <span>сек / cm<sup>3</sup></span>
            </v-col>
            <v-col cols="4">
              <strong>cm<sup>3</sup> / сек</strong>
            </v-col>
            <!--            <v-col cols="3">
              <span>сек</span>
            </v-col>-->
          </v-row>

          <v-row>
            <v-col cols="4">
              <v-text-field
                v-model.number="startValue"
                type="number"
                label="Ввести"
                color="orange darken-2"
                class="ml-2"
              ></v-text-field>
            </v-col>
            <v-col cols="4">
              <v-text-field
                v-model.number="consumption"
                type="number"
                label="Ввести"
                color="orange darken-2"
                class="ml-2"
              ></v-text-field>
            </v-col>
            <v-col cols="4">
              <v-text-field v-model.number="computedConsumption" class="ml-2" readonly></v-text-field>
            </v-col>
            <!--            <v-col cols="3">
              <v-text-field label="Ввести значення" color="orange darken-2" class="ml-2"></v-text-field>
            </v-col>-->
          </v-row>
        </div>

        <div class="mt-10 overflow-hidden">
          <v-row>
            <v-col cols="12">
              <div class="d-flex justify-center">
                <div class="result-box mx-3 rounded-lg elevation-3">
                  <span class="text-h5 font-weight-bold">{{ convertedSeconds }}</span>
                </div>
                <div class="result-box mx-3 rounded-lg elevation-3">
                  <span class="text-h5 font-weight-bold">{{ result }}</span>
                  <strong>cm<sup>3</sup></strong>
                </div>
              </div>
            </v-col>
          </v-row>
        </div>

        <div>
          <v-slider
            v-model="slider"
            :min="min"
            :max="max"
            :thumb-size="18"
            color="error"
            prepend-icon="mdi-alarm"
            thumb-label="always"
            hide-details
            class="mt-8"
          ></v-slider>
        </div>

        <div class="mt-5 overflow-hidden">
          <v-row>
            <v-col cols="7">
              <div v-if="checkPoints.length">
                <div
                  v-for="(checkPoint, index) in checkPoints"
                  :key="index"
                  class="result-item d-flex align-baseline rounded-lg mb-4"
                >
                  <small class="mr-1">{{ index + 1 }}.</small>
                  <v-btn @click="disableAlarm(checkPoint)" :disabled="!checkPoint.isActive" color="error" fab dark small>
                    <v-icon>mdi-bell-off-outline</v-icon>
                  </v-btn>
                  <v-text-field
                    v-model.number="checkPoint.value"
                    pattern="\d*"
                    :disabled="checkPoint.isActive"
                    label="Контроль"
                    color="error"
                    class="ml-1"
                    suffix="cm3"
                    dense
                    hide-details
                  ></v-text-field>
                </div>
              </div>
            </v-col>
            <v-col cols="5">
              <div class="mb-10">
                <v-btn @click="addCheckPoint" color="blue-grey" class="white--text" height="40" block x-small>Додати замiр</v-btn>
              </div>
              <div class="actions">
                <v-btn
                  @click="start"
                  :disabled="!startValue || !consumption"
                  color="success"
                  elevation="2"
                  class="d-block"
                  block
                  small
                >
                  старт
                </v-btn>
                <v-btn @click="stop" color="error" elevation="2" class="mt-5 d-block" block small>стоп</v-btn>
                <v-btn @click="proceedCount" color="primary" elevation="2" class="mt-5 d-block" block small> далi </v-btn>
                <v-btn @click="reset" color="secondary" elevation="3" class="mt-5 d-block" block small> скинути </v-btn>
              </div>
            </v-col>
          </v-row>
        </div>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import mp3 from '@/assets/alarm.mp3'

export default {
  name: 'App',

  components: {},

  data: () => ({
    startValue: null,
    consumption: null,
    currentConsumption: 0,
    interval: null,
    secondsCounter: 0,
    audio: null,
    slider: 30,
    min: 0,
    max: 120,
    checkPoints: [
      {
        value: null,
        isActive: false,
        checked: false,
      },
    ],
  }),
  mounted() {
    this.configAudio()
  },
  computed: {
    computedConsumption() {
      return 1 / this.consumption || null
    },
    convertedSeconds() {
      const date = new Date(null)
      date.setSeconds(this.secondsCounter)
      // TODO: check substr() on mobile
      let time = date.toISOString().substr(11, 8)
      return time
    },
    result() {
      return (this.startValue - this.currentConsumption).toFixed(1) || null
    },
  },
  methods: {
    addCheckPoint() {
      const newCheckPoint = {
        value: null,
        isActive: false,
        checked: false,
      }
      this.checkPoints.push(newCheckPoint)
    },
    start() {
      this.interval = setInterval(() => {
        this.secondsCounter++
        this.currentConsumption = this.secondsCounter * this.computedConsumption
        this.checkPoints.forEach((checkPoint) => {
          if (checkPoint.value.toFixed(2) >= this.result && !checkPoint.checked) {
            checkPoint.checked = true
            checkPoint.isActive = true
            this.playAudio()
          }
        })
      }, 1000)
    },
    stop() {
      clearInterval(this.interval)
      // this.secondsCounter = 0
    },
    proceedCount() {},
    reset() {
      this.secondsCounter = 0
      this.startValue = null
      this.consumption = null
      this.currentConsumption = 0
      this.checkPoints = [
        {
          value: null,
          isActive: false,
          checked: false,
        },
      ]
    },
    disableAlarm(checkPoint) {
      checkPoint.isActive = false
      this.audio.pause()
    },
    configAudio() {
      this.audio = new Audio(mp3)
      this.audio.loop = true
    },
    playAudio() {
      this.audio.play()
    },
  },
}
</script>

<style lang="scss" scoped>
.wrapper {
  background-color: #90caf9;
  border: 2px solid #546e7a;
  overflow: hidden;
}

.col {
  border: 1px solid #78909c;
}

.result {
  &-box {
    position: relative;
    width: 20%;
    min-width: 110px;
    height: 50px;
    background-color: #90caf9;
    border: 2px solid #546e7a;
    padding: 0 4px;
    line-height: 50px;
    text-align: center;
    strong {
      position: absolute;
      right: -40px;
      top: -15px;
    }
  }
  &-item {
    //padding: 10px;
  }
}
</style>
