<template>
  <v-container>
    <h2 class="display-1 text-center mb-5">복싱 운동 기록</h2>

    <v-row>
      <v-col v-for="(record, index) in exerciseRecords" :key="index" cols="12">
        <v-card class="mb-4">
          <v-card-title>{{ record.date }}</v-card-title>
          <v-list>
            <v-list-item v-for="(exercise, idx) in record.exercises" :key="idx">
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

    <v-row>
      <v-col cols="12" md="6">
        <v-card class="mb-4">
          <v-card-title>새로운 운동 기록 추가</v-card-title>
          <v-form @submit.prevent="addExerciseRecord">
            <v-select
              v-model="selectedExerciseType"
              :items="exerciseTypes"
              label="운동 종류"
              required
            ></v-select>
            <v-text-field
              v-model.number="rounds"
              label="라운드 수"
              type="number"
              required
            ></v-text-field>
            <v-btn type="submit" color="primary">추가</v-btn>
          </v-form>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
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
    };
  },
  created() {
    this.generateDummyData(10); // 10개의 더미 데이터 생성
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
    },
    randomDate(today) {
      const start = new Date(today.getTime() - 30 * 24 * 60 * 60 * 1000); // 30일 이내의 날짜에서 랜덤 선택
      const end = today;
      const randomDate = new Date(
        start.getTime() + Math.random() * (end.getTime() - start.getTime())
      );
      return randomDate.toLocaleDateString(); // 날짜 포맷을 맞추기 위해 toLocaleDateString 사용
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
      // 새로운 운동 기록을 추가하는 메서드 (이 예시에서는 더미 데이터에만 추가)
      const today = new Date().toISOString().slice(0, 10); // 현재 날짜(ISO 8601 포맷)를 가져옴
      let record = this.exerciseRecords.find((rec) => rec.date === today);

      if (!record) {
        // 오늘 날짜에 해당하는 기록이 없으면 새로운 객체 생성
        record = { date: today, exercises: [] };
        this.exerciseRecords.push(record);
      }

      // 선택한 운동 종류와 라운드 수를 기록에 추가
      record.exercises.push({
        type: this.selectedExerciseType,
        rounds: this.rounds,
      });

      // 폼 초기화
      this.selectedExerciseType = "";
      this.rounds = 1;
    },
  },
};
</script>

<style scoped>
/* 추가적인 스타일링이 필요한 경우 여기에 추가 */
</style>
