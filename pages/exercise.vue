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
                <v-card-text>
                  <v-form @submit.prevent="addExerciseRecord">
                    <v-text-field
                      v-model="selectedDate"
                      label="날짜"
                      type="date"
                      required
                      class="mb-3"
                    ></v-text-field>
                    <v-select
                      v-model="selectedExerciseType"
                      :items="exerciseTypes"
                      label="운동 종류"
                      required
                      class="mb-3"
                    ></v-select>
                    <v-text-field
                      v-model.number="rounds"
                      label="라운드 수"
                      type="number"
                      required
                      class="mb-3"
                    ></v-text-field>
                    <v-btn type="submit" color="primary" large block
                      >추가</v-btn
                    >
                  </v-form>
                </v-card-text>
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
      // New data properties to be merged
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
      snackbarColor: "success", // success, error, warning 등을 선택할 수 있습니다.
    };
  },
  computed: {
    sortedExerciseRecords() {
      return this.exerciseRecords
        .slice()
        .sort((a, b) => new Date(b.date) - new Date(a.date));
    },
    chartData() {
      return this.sortedExerciseRecords.map((record) => {
        const totalRounds = record.exercises.reduce(
          (sum, exercise) => sum + exercise.rounds,
          0
        );
        return {
          date: record.date,
          totalRounds,
        };
      });
    },
    today() {
      return new Date().toLocaleDateString();
    },
  },
  created() {
    this.generateDummyData(10); // 10개의 더미 데이터 생성
    // 오늘 날짜를 ISO 포맷으로 변환하여 selectedDate에 설정
    this.selectedDate = new Date().toISOString().substr(0, 10);
    this.selectedExerciseType = this.exerciseTypes[0];
  },
  methods: {
    generateDummyData(count) {
      const today = new Date();
      const exerciseRecords = [];

      for (let i = 0; i < count; i++) {
        const randomDate = this.randomDate(today);
        const exercises = this.generateRandomExercises();

        exerciseRecords.push({
          date: randomDate,
          exercises: exercises,
        });
      }

      this.exerciseRecords = exerciseRecords;
      this.updateChart();
    },
    randomDate(today) {
      const start = new Date(today.getTime() - 30 * 24 * 60 * 60 * 1000); // 30일 이내의 날짜에서 랜덤 선택
      const end = today;
      const randomDate = new Date(
        start.getTime() + Math.random() * (end.getTime() - start.getTime())
      );
      return randomDate.toISOString().split("T")[0]; // 날짜 포맷을 맞추기 위해 ISO 8601 포맷 사용
    },
    generateRandomExercises() {
      const numExercises = Math.floor(Math.random() * 3) + 5; // 5~7개의 운동 종류를 랜덤 선택
      const exercises = [];

      for (let i = 0; i < numExercises; i++) {
        const exerciseType =
          this.exerciseTypes[
            Math.floor(Math.random() * this.exerciseTypes.length)
          ];
        const rounds = Math.floor(Math.random() * 10) + 1; // 1~10 라운드 수를 랜덤 선택

        exercises.push({
          type: exerciseType,
          rounds: rounds,
        });
      }

      return exercises;
    },
    addExerciseRecord() {
      const date = this.selectedDate || new Date().toISOString().split("T")[0]; // 입력된 날짜를 사용, 없으면 현재 날짜 사용
      let record = this.exerciseRecords.find((rec) => rec.date === date);

      if (!record) {
        // 해당 날짜에 해당하는 기록이 없으면 새로운 객체 생성
        record = { date, exercises: [] };
        this.exerciseRecords.push(record);
      }

      // 선택한 운동 종류와 라운드 수를 기록에 추가
      record.exercises.push({
        type: this.selectedExerciseType,
        rounds: this.rounds,
      });

      // 운동 기록 내림차순 정렬
      this.exerciseRecords.sort((a, b) => new Date(b.date) - new Date(a.date));

      // 차트 데이터 업데이트
      this.updateChart();
    },

    deleteExercise(date, index) {
      const record = this.exerciseRecords.find((rec) => rec.date === date);
      if (record) {
        record.exercises.splice(index, 1); // 해당 인덱스의 운동 기록 삭제
        if (record.exercises.length === 0) {
          // 만약 기록이 더 이상 없다면 전체 기록에서도 삭제
          const recordIndex = this.exerciseRecords.indexOf(record);
          this.exerciseRecords.splice(recordIndex, 1);
        }
        // 차트 데이터 업데이트
        this.updateChart();
      }
    },
    updateChart() {
      // 날짜 레이블 설정
      this.chartLabels = this.sortedExerciseRecords.map(
        (record) => record.date
      );

      // 데이터셋 설정
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
