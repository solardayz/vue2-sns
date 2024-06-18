<template>
  <v-container>
    <h2 class="display-1 text-center mb-5">복싱 운동 기록</h2>

    <v-tabs v-model="activeTab">
      <v-tab>운동 기록 추가</v-tab>
      <v-tab>운동 기록 보기</v-tab>
      <v-tab>운동 기록 통계</v-tab>
      <v-tab>운동 기록 그래프</v-tab>

      <v-tab-item>
        <v-container>
          <v-row>
            <v-col cols="12" md="6">
              <v-card class="mb-4">
                <v-card-title>새로운 운동 기록 추가</v-card-title>
                <v-card>
                  <v-card-text>
                    <v-form @submit.prevent="addExerciseRecord">
                      <v-row>
                        <v-col cols="12" md="6">
                          <v-text-field
                            v-model="selectedDate"
                            label="날짜"
                            type="date"
                            required
                            outlined
                            class="mb-3"
                          ></v-text-field>
                        </v-col>
                        <v-col cols="12" md="6">
                          <v-select
                            v-model="selectedExerciseType"
                            :items="exerciseTypes"
                            label="운동 종류"
                            required
                            outlined
                            class="mb-3"
                          ></v-select>
                        </v-col>
                        <v-col cols="12">
                          <v-text-field
                            v-model.number="rounds"
                            label="라운드 수"
                            type="number"
                            required
                            outlined
                            class="mb-3"
                          ></v-text-field>
                        </v-col>
                      </v-row>
                      <v-row justify="center">
                        <v-btn type="submit" color="primary" large icon>
                          <v-icon>mdi-plus</v-icon>
                        </v-btn>
                      </v-row>
                    </v-form>
                  </v-card-text>
                </v-card>
              </v-card>
            </v-col>
          </v-row>

          <!-- 오늘 추가된 운동 기록 리스트 -->
          <v-row>
            <v-col
              v-for="(record, index) in sortedExerciseRecords"
              :key="index"
              cols="12"
            >
              <v-card v-if="record.date === today" class="mb-4">
                <v-card-title>{{ record.date }}</v-card-title>
                <v-list>
                  <v-list-item
                    v-for="(exercise, idx) in record.exercises"
                    :key="idx"
                  >
                    <v-list-item-content>
                      <v-list-item-title class="headline mb-1">{{
                        exercise.type
                      }}</v-list-item-title>
                      <v-list-item-subtitle
                        >라운드 수: {{ exercise.rounds }}</v-list-item-subtitle
                      >
                    </v-list-item-content>
                  </v-list-item>
                </v-list>
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </v-tab-item>

      <v-tab-item>
        <v-container>
          <v-row>
            <v-col
              v-for="(record, index) in sortedExerciseRecords"
              :key="index"
              cols="12"
            >
              <v-card class="mb-4">
                <v-card-title>{{ record.date }}</v-card-title>
                <v-list>
                  <v-list-item
                    v-for="(exercise, idx) in record.exercises"
                    :key="idx"
                  >
                    <v-list-item-content>
                      <v-list-item-title class="headline mb-1">{{
                        exercise.type
                      }}</v-list-item-title>
                      <v-list-item-subtitle
                        >라운드 수: {{ exercise.rounds }}</v-list-item-subtitle
                      >
                    </v-list-item-content>
                    <v-list-item-action>
                      <v-btn icon @click="deleteExercise(record.date, idx)">
                        <v-icon color="red">mdi-delete</v-icon>
                      </v-btn>
                    </v-list-item-action>
                  </v-list-item>
                </v-list>
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </v-tab-item>

      <v-tab-item>
        <v-container>
          <v-row>
            <v-col cols="12">
              <v-card class="mb-4">
                <v-card-title>운동 기록 통계</v-card-title>
                <v-card-text>
                  <v-simple-table>
                    <thead>
                      <tr>
                        <th class="text-left">날짜</th>
                        <th class="text-left">총 라운드 수</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="(data, index) in chartData" :key="index">
                        <td>{{ data.date }}</td>
                        <td>
                          <v-progress-linear
                            :value="data.totalRounds"
                            :color="getColor(data.totalRounds)"
                            height="20"
                          >
                            <strong>{{ data.totalRounds }}</strong>
                          </v-progress-linear>
                        </td>
                      </tr>
                    </tbody>
                  </v-simple-table>
                </v-card-text>
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </v-tab-item>

      <v-tab-item>
        <v-container>
          <v-row>
            <v-col cols="12">
              <v-card class="mb-4">
                <v-card-title>운동 기록 그래프</v-card-title>
                <v-card-text>
                  <v-sparkline
                    :value="value"
                    :gradient="gradient"
                    :smooth="radius || false"
                    :padding="padding"
                    :line-width="width"
                    :stroke-linecap="lineCap"
                    :gradient-direction="gradientDirection"
                    auto-draw
                  ></v-sparkline>
                </v-card-text>
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </v-tab-item>
    </v-tabs>

    <!-- 추가되었습니다 토스트 메시지 -->
    <v-snackbar v-model="snackbar" :color="snackbarColor" top>
      {{ snackbarMessage }}
      <template v-slot:action="{ attrs }">
        <v-btn color="white" text v-bind="attrs" @click="snackbar = false"
          >닫기</v-btn
        >
      </template>
    </v-snackbar>
  </v-container>
</template>

<script>
const gradients = [
  ["#222"],
  ["#42b3f4"],
  ["red", "orange", "yellow"],
  ["purple", "violet"],
  ["#00c6ff", "#F0F", "#FF0"],
  ["#f72047", "#ffd200", "#1feaea"],
];
export default {
  data() {
    return {
      width: 2,
      radius: 10,
      padding: 8,
      lineCap: "round",
      gradient: gradients[5],
      value: [0, 2, 5, 9, 5, 10, 3, 5, 0, 0, 1, 8, 2, 9, 0],
      gradientDirection: "top",
      gradients,
      activeTab: 0,
      exerciseTypes: [
        "스파링",
        "쉐도우",
        "미트트레이닝",
        "원투 연습",
        "잽 연습",
        "위빙 연습",
        "스텝 연습",
      ],
      exerciseRecords: [],
      selectedExerciseType: "",
      selectedDate: "",
      rounds: 1,
      chartLabels: [],
      chartDatasets: [
        {
          label: "운동 기록",
          backgroundColor: "#42A5F5",
          borderColor: "#42A5F5",
          data: [],
          fill: false,
        },
      ],
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false,
      },
      snackbar: false,
      snackbarMessage: "",
      snackbarColor: "success",
    };
  },
  computed: {
    sortedExerciseRecords() {
      return this.exerciseRecords.sort(
        (a, b) => new Date(b.date) - new Date(a.date)
      );
    },
    chartData() {
      return this.sortedExerciseRecords.map((record) => ({
        date: record.date,
        totalRounds: record.exercises.reduce(
          (sum, exercise) => sum + exercise.rounds,
          0
        ),
      }));
    },
    today() {
      return new Date().toLocaleDateString();
    },
  },
  created() {
    this.selectedDate = new Date().toISOString().substr(0, 10);
    this.selectedExerciseType = this.exerciseTypes[0];
    this.generateDummyData(10);
  },
  methods: {
    generateDummyData(count) {
      const today = new Date();
      this.exerciseRecords = Array.from({ length: count }, () => ({
        date: this.randomDate(today),
        exercises: this.generateRandomExercises(),
      }));
      this.updateChart();
    },
    randomDate(today) {
      const start = new Date(today.getTime() - 30 * 24 * 60 * 60 * 1000);
      const randomDate = new Date(
        start.getTime() + Math.random() * (today.getTime() - start.getTime())
      );
      return randomDate.toISOString().split("T")[0];
    },
    generateRandomExercises() {
      return Array.from({ length: Math.floor(Math.random() * 3) + 5 }, () => ({
        type: this.exerciseTypes[
          Math.floor(Math.random() * this.exerciseTypes.length)
        ],
        rounds: Math.floor(Math.random() * 10) + 1,
      }));
    },
    addExerciseRecord() {
      const date = this.selectedDate || new Date().toISOString().split("T")[0];
      let record = this.exerciseRecords.find((rec) => rec.date === date);

      if (!record) {
        record = { date, exercises: [] };
        this.exerciseRecords.push(record);
      }

      record.exercises.push({
        type: this.selectedExerciseType,
        rounds: this.rounds,
      });

      this.exerciseRecords.sort((a, b) => new Date(b.date) - new Date(a.date));
      this.updateChart();
    },
    deleteExercise(date, index) {
      const record = this.exerciseRecords.find((rec) => rec.date === date);
      if (record) {
        record.exercises.splice(index, 1);
        if (record.exercises.length === 0) {
          this.exerciseRecords = this.exerciseRecords.filter(
            (rec) => rec.date !== date
          );
        }
        this.updateChart();
      }
    },
    updateChart() {
      this.chartLabels = this.sortedExerciseRecords.map(
        (record) => record.date
      );
      this.chartDatasets[0].data = this.sortedExerciseRecords.map((record) =>
        record.exercises.reduce(
          (totalRounds, exercise) => totalRounds + exercise.rounds,
          0
        )
      );
    },
    getColor(value) {
      if (value > 20) return "success";
      if (value > 10) return "warning";
      return "error";
    },
  },
};
</script>

<style>
/* Add your custom styles here */
</style>
