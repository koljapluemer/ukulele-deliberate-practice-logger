<script setup>
import { ref, watch, computed } from "vue";

const nrOfSessionToday = ref(0);
let sessions = [];
let session = ref({});
// see if sessions in localStorage
if (localStorage.getItem("sessions")) {
  sessions = JSON.parse(localStorage.getItem("sessions"));
}

const createNewSession = () => {
  session.value = {
    topic: "",
    unit: "",
    goal: "",
    startedAt: null,
    stoppedAt: null,
    questionData: {},
    comment: "",
    state: "creating",
  };
};

createNewSession();

const startSession = () => {
  session.value.state = "running";
  session.value.startedAt = new Date();
};

const stopSession = () => {
  session.value.state = "stopped";
  session.value.stoppedAt = new Date();
  nrOfSessionToday.value++;
};

const saveSession = () => {
  session.value.state = "saved";
  sessions.push(session.value);
  localStorage.setItem("sessions", JSON.stringify(sessions));
  createNewSession();
};


const likertQuestions = [
  "I reached my goal successfully.",
  "I was able to focus on the task at hand.",
  "The unit was fun.",
  "I feel like I made progress towards the larger goal.",
  "The unit was easy.",
  "The unit was exhausting.",
];


</script>

<template>
  <div class="drawer">
    <input id="my-drawer" type="checkbox" class="drawer-toggle" />
    <div class="drawer-content">
      <main
        class="flex flex-col justify-around items-center p-4"
        v-if="session"
      >
        <div class="" v-if="session.state === 'creating'">
          <h1 class="font-bold text-2xl">Hi. Let's practice some Uke.</h1>

          <div class="card w-96 bg-base-100 shadow-xl">
            <figure>
              <img src="@/assets/uke.png" alt="Ukulele" />
            </figure>
            <div class="card-body">
              <h2 class="card-title">New Uke Session Unit</h2>

              <div class="form-control w-full max-w-xs my-2">
                <label class="label">
                  <span class="label-text">Session Topic</span>
                </label>
                <input
                  type="text"
                  placeholder="Type here"
                  class="input input-bordered w-full max-w-xs my-2"
                  v-model="session.topic"
                />
              </div>

              <div class="form-control w-full max-w-xs my-2">
                <label class="label">
                  <span class="label-text">Unit Topic</span>
                </label>
                <input
                  type="text"
                  placeholder="Type here"
                  class="input input-bordered w-full max-w-xs my-2"
                  v-model="session.unit"
                />
              </div>

              <div class="form-control w-full max-w-xs my-2">
                <label class="label">
                  <span class="label-text">What *exactly* are you trying?</span>
                </label>
                <textarea
                  class="textarea textarea-bordered h-24"
                  placeholder="..."
                  v-model="session.goal"
                ></textarea>
              </div>

              <div class="card-actions justify-end">
                <button class="btn btn-primary" @click="startSession">
                  Start Session
                </button>
              </div>
            </div>
          </div>
        </div>

        <div
          class="card w-96 bg-base-100 shadow-xl"
          v-if="session.state == 'running'"
        >
          <div class="card-body">
            <h1 class="font-bold text-2xl">{{ session.topic }}</h1>
            <h2 class="font-bold text-xl">{{ session.unit }}</h2>
            <p class="text-lg">{{ session.goal }}</p>
            <p class="text-lg">
              Started at: {{ session.startedAt.toLocaleString() }}
            </p>

            <div class="card-actions justify-end">
              <button class="btn btn-primary" @click="stopSession">
                End Session
              </button>
            </div>
          </div>
        </div>

        <div
          class="card bg-base-100 shadow-xl"
          v-if="session.state == 'stopped'"
        >
          <div class="card-body">
            <!-- loop Likert Questions, allow radio buttons from strongly disagree to strongly agree, hook every question to a corresponding data point in session.questionData:  -->

            <div class="overflow-x-auto">
              <h1 class="font-bold text-2xl">{{ session.topic }}</h1>
              <h2 class="font-bold text-xl">{{ session.unit }}</h2>
              <p class="text-lg">{{ session.goal }}</p>
              <p class="text-lg">
                Started at: {{ session.startedAt.toLocaleString() }}
              </p>
              <p class="text-lg">
                Stopped at: {{ session.stoppedAt.toLocaleString() }}
              </p>
              <table class="table mt-4">
                <!-- head -->
                <thead>
                  <tr>
                    <th></th>
                    <th class="w-32">Strongly Disagree</th>
                    <th class="w-32">Disagree</th>
                    <th class="w-32">Neutral</th>
                    <th class="w-32">Agree</th>
                    <th class="w-32">Strongly Agree</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="question in likertQuestions" :key="question">
                    <td>{{ question }}</td>
                    <td>
                      <input
                        type="radio"
                        :name="question"
                        value="1"
                        @click="session.questionData[question] = 1"
                      />
                    </td>
                    <td>
                      <input
                        type="radio"
                        :name="question"
                        value="2"
                        @click="session.questionData[question] = 2"
                      />
                    </td>
                    <td>
                      <input
                        type="radio"
                        :name="question"
                        value="3"
                        @click="session.questionData[question] = 3"
                      />
                    </td>
                    <td>
                      <input
                        type="radio"
                        :name="question"
                        value="4"
                        @click="session.questionData[question] = 4"
                      />
                    </td>
                    <td>
                      <input
                        type="radio"
                        :name="question"
                        value="5"
                        @click="session.questionData[question] = 5"
                      />
                    </td>
                  </tr>
                </tbody>
              </table>

              <div class="form-control w-full max-w-xs my-2">
                <label class="label">
                  <span class="label-text"
                    >Comment (what was hard? what was interesting?)</span
                  >
                </label>
                <textarea
                  class="textarea textarea-bordered h-24"
                  placeholder="..."
                  v-model="session.comment"
                ></textarea>
              </div>
              <div class="card-actions justify-end">
                <button class="btn btn-primary" @click="saveSession">
                  Save Session
                </button>
              </div>
            </div>
          </div>
        </div>
      </main>
      <label for="my-drawer" class="btn drawer-button">Show Stats</label>
    </div>
    <div class="drawer-side">
      <label for="my-drawer" class="drawer-overlay"></label>
      <ul class="menu p-4 w-80 min-h-full bg-base-200 text-base-content">
        <li>Sessions Today: {{ nrOfSessionToday }}</li>
      </ul>
    </div>
  </div>
</template>

<style scoped></style>
