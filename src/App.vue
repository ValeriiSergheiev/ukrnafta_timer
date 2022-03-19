<template>
  <v-app>
    <v-app-bar app color="primary" dark>
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
    </v-app-bar>

    <v-main>
      <v-container>
        <div class="wrapper mt-5 text-center rounded-lg elevation-15">
          <v-row>
            <v-col cols="3">
              <strong>Початкове значення пресу</strong>
            </v-col>
            <v-col cols="6">
              <strong>Розхід</strong>
            </v-col>
            <v-col cols="3">
              <strong>Випередження таймеру</strong>
            </v-col>
          </v-row>

          <v-row>
            <v-col cols="3">
              <span>cm<sup>3</sup></span>
            </v-col>
            <v-col cols="3">
              <span>сек / cm<sup>3</sup></span>
            </v-col>
            <v-col cols="3">
              <strong>cm<sup>3</sup> / сек</strong>
            </v-col>
            <v-col cols="3">
              <span>сек</span>
            </v-col>
          </v-row>

          <v-row>
            <v-col cols="3">
              <v-text-field
                label="Ввести значення"
                color="orange darken-2"
                class="ml-2"
              ></v-text-field>
            </v-col>
            <v-col cols="3">
              <v-text-field
                label="Ввести значення"
                color="orange darken-2"
                class="ml-2"
              ></v-text-field>
            </v-col>
            <v-col cols="3">
              <v-text-field v-model="test" class="ml-2" readonly></v-text-field>
            </v-col>
            <v-col cols="3">
              <v-text-field
                label="Ввести значення"
                color="orange darken-2"
                class="ml-2"
              ></v-text-field>
            </v-col>
          </v-row>
        </div>

        <div class="mt-10 overflow-hidden">
          <v-row>
            <v-col cols="12">
              <div class="d-flex justify-center">
                <div class="result-box mx-3 rounded-lg elevation-3">
                  <span></span>
                </div>
                <div class="result-box mx-3 rounded-lg elevation-3">
                  <span></span>
                  <strong>cm<sup>3</sup></strong>
                </div>
              </div>
            </v-col>
          </v-row>
        </div>

        <div class="mt-5 overflow-hidden">
          <v-row>
            <v-col cols="6">
              <div v-if="checkPoints.length">
                <div
                  v-for="(checkPoint, index) in checkPoints"
                  :key="index"
                  class="result-item d-flex align-baseline rounded-lg mb-2"
                >
                  <strong class="mr-2">{{ index + 1 }}.</strong>
                  <v-btn
                    :disabled="checkPoint.isActive"
                    color="error"
                    fab
                    dark
                    small
                  >
                    <v-icon>mdi-bell-off-outline</v-icon>
                  </v-btn>
                  <v-text-field
                    v-model="checkPoint.value"
                    :disabled="checkPoint.isActive"
                    label="Контроль"
                    color="error"
                    class="ml-5"
                    suffix="cm3"
                    dense
                    hide-details
                  ></v-text-field>
                </div>
              </div>
            </v-col>
            <v-col cols="6">
              <div class="mb-10">
                <v-btn
                  @click="addServicePoint"
                  color="blue-grey"
                  class="white--text"
                >
                  Додати замiр
                  <v-icon dark> mdi-plus </v-icon>
                </v-btn>
              </div>
              <div class="actions">
                <v-btn
                  color="success"
                  elevation="2"
                  width="150"
                  class="d-block"
                >
                  старт
                </v-btn>
                <v-btn
                  color="error"
                  elevation="2"
                  class="mt-5 d-block"
                  width="150"
                >
                  стоп
                </v-btn>
                <v-btn
                  color="primary"
                  elevation="2"
                  class="mt-5 d-block"
                  width="150"
                >
                  далi
                </v-btn>
                <v-btn
                  color="secondary"
                  elevation="3"
                  class="mt-5 d-block"
                  width="150"
                >
                  скинути
                </v-btn>
              </div>
            </v-col>
          </v-row>
        </div>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: "App",

  components: {},

  data: () => ({
    test: 45789,
    checkPoints: [
      {
        value: null,
        isActive: true,
      },
    ],
  }),
  methods: {
    addServicePoint() {
      const newServicePoint = {
        value: null,
        isActive: false,
      };
      this.checkPoints.push(newServicePoint);
    },
  },
};
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
    padding: 10px;
  }
}
</style>
