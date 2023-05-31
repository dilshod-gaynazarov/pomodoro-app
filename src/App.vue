<template>
  <div :class="!work
    ? 'wrapper w-full min-h-screen bg-[#FFF2F2]'
    : 'wrapper w-full min-h-screen bg-[#F2FFF5]'
    ">
    <div class="container grid place-items-center h-screen mx-auto">
      <button @click="toggleModal" :class="work
        ? 'absolute right-14 top-12 text-[30px] text-[#14401d]'
        : 'absolute right-14 top-12 text-[30px] text-[#471515]'
        ">
        <i class="bx bx-cog"></i>
      </button>
      <div id="dropdown" :class="modal
        ? 'absolute top-9 right-10 z-10 rounded-[24px] shadow-lg w-[350px]'
        : 'hidden'
        ">
        <div class="flex items-start justify-between p-4">
          <h3 class="text-xl font-semibold text-gray-900">Settings</h3>
          <button @click="toggleModal" type="button" :class="work
            ? 'text-[#14401d] bg-[#F2FFF5] rounded-lg text-sm p-1.5 ml-auto inline-flex items-center'
            : 'text-[#471515] bg-[#FFF2F2] rounded-lg text-sm p-1.5 ml-auto inline-flex items-center'
            " data-modal-hide="defaultModal">
            <svg aria-hidden="true" class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20"
              xmlns="http://www.w3.org/2000/svg">
              <path fill-rule="evenodd"
                d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z"
                clip-rule="evenodd"></path>
            </svg>
            <span class="sr-only">Close modal</span>
          </button>
        </div>
        <div class="p-4 flex items-center justify-between">
          Dark mode
          <el-switch v-model="dark" />
        </div>
        <div class="p-4 flex items-center justify-between">
          Focus length
          <input type="number" class="w-20 rounded-lg" value="25" />
        </div>
        <div class="p-4 flex items-center justify-between">
          Short break length
          <input type="number" class="w-20 rounded-lg" value="5" />
        </div>
        <div class="p-4 flex items-center justify-between">
          Long break length
          <input type="number" class="w-20 rounded-lg" value="15" />
        </div>
        <div class="p-4 flex items-center justify-between">
          Auto resume timer
          <el-switch v-model="autoResume" />
        </div>
        <div class="p-4 flex items-center justify-between">
          Sound
          <el-switch v-model="sound" />
        </div>
      </div>

      <div class="timer">
        <div :class="work
          ? 'status border-2 border-[#14401d] bg-[rgba(77,218,110,0.15)]'
          : 'status border-2 border-[#471515] bg-[rgba(255,76,76,0.15)]'
          ">
          <img :src="!work ? focus : breakStop" alt="" />{{ status }}
        </div>
        <div class="numbers">
          <h1 id="min" :class="work ? 'text-[#14401d]' : 'text-[#471515]'">25</h1>
          <h1 id="seconds" :class="work ? 'text-[#14401d]' : 'text-[#471515]'">00</h1>
        </div>
        <div class="controls flex gap-5 text-4xl">
          <button @click="refresh" :class="work
            ? 'p-6 bg-[rgba(77,218,110,0.15)] rounded-[24px] text-[#14401D] hover:bg-[#86e299] focus:bg-[rgba(77,218,110,0.62)]'
            : 'p-6 bg-[rgba(255,76,76,0.15)] rounded-[24px] text-[#471515] hover:bg-[#ee8989] focus:bg-[rgba(255,76,76,0.71)]'
            ">
            <i class="bx bx-refresh"></i>
          </button>
          <button @click="btn" id="play" :class="work
            ? 'p-6 bg-[rgba(77,218,110,0.15)] rounded-[24px] text-[#14401D] hover:bg-[#86e299] focus:bg-[rgba(77,218,110,0.62)]'
            : 'p-6 bg-[rgba(255,76,76,0.15)] rounded-[24px] text-[#471515] hover:bg-[#ee8989] focus:bg-[rgba(255,76,76,0.71)]'
            ">
            <i :class="!lamp ? 'bx bx-play' : 'bx bx-pause'"></i>
          </button>
          <button @click="next" :class="work
            ? 'p-6 bg-[rgba(77,218,110,0.15)] rounded-[24px] text-[#14401D] hover:bg-[#86e299] focus:bg-[rgba(77,218,110,0.62)]'
            : 'p-6 bg-[rgba(255,76,76,0.15)] rounded-[24px] text-[#471515] hover:bg-[#ee8989] focus:bg-[rgba(255,76,76,0.71)]'
            ">
            <i class="bx bx-skip-next"></i>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import focus from "./assets/focus.svg";
import breakStop from "./assets/break.svg";

const modal = ref(false);
const toggleModal = () => (modal.value = !modal.value);

const status = ref("FOCUS");
const lamp = ref(false);
const minute = ref(25);
const end = ref(60);
const setTimeOutResult = ref(null);
const work = ref(false);
const sch = ref(0);


let refresh = () => {
  clearInterval(setTimeOutResult.value);
  if (work.value) {
    if (status.value == "LONG BREAK") {
      minute.value = 15;
      min.textContent = minute.value;
      end.value = 60;
      seconds.textContent = "00";
      status.value = "LONG BREAK";
    } else {
      minute.value = 5;
      min.textContent = `0${minute.value}`;
      end.value = 60;
      seconds.textContent = "00";
      status.value = "SHORT BREAK";
    }
  } else {
    minute.value = 25;
    min.textContent = minute.value;
    end.value = 60;
    seconds.textContent = "00";
    status.value = "FOCUS";
  }
  lamp.value = false;
};

let next = () => {
  clearInterval(setTimeOutResult.value);
  if (status.value == "FOCUS") {
    if (sch.value == 3) {
      minute.value = 15;
      min.textContent = minute.value;
      end.value = 60;
      seconds.textContent = "00";
      status.value = "LONG BREAK";
      lamp.value = false;
      sch.value = 0;
    } else {
      minute.value = 5;
      min.textContent = `0${minute.value}`;
      end.value = 60;
      seconds.textContent = "00";
      status.value = "SHORT BREAK";
      lamp.value = false;
      sch.value = sch.value + 1;
    }
  } else {
    minute.value = 25;
    min.textContent = minute.value;
    end.value = 60;
    seconds.textContent = "00";
    status.value = "FOCUS";
    lamp.value = false;
  }
  work.value = !work.value;
};

let btn = () => {
  lamp.value = !lamp.value;

  if (lamp.value) {
    setTimeOutResult.value = setInterval(() => {
      end.value = end.value - 1;
      seconds.textContent = end.value;

      if (end.value < 10) {
        seconds.textContent = `0${end.value}`;
      }
      if (end.value == 0 && minute.value !== -1) {
        end.value = 60;
        minute.value = minute.value - 1;

        if (minute.value < 10) {
          min.textContent = `0${minute.value}`;
        } else {
          min.textContent = minute.value;
        }
      }

      if (minute.value == -1) {
        clearInterval(setTimeOutResult.value);
        work.value = !work.value;
        if (work.value) {
          if (sch.value == 3) {
            minute.value = 15;
            min.textContent = minute.value;
            sch.value = 0;
            status.value = "LONG BREAK";
          } else {
            minute.value = 5;
            min.textContent = `0${minute.value}`;
            sch.value = sch.value + 1;
            status.value = "SHORT BREAK";
          }
        } else {
          minute.value = 25;
          min.textContent = minute.value;
          status.value = "FOCUS";
        }
        end.value = 60;
        lamp.value = false;
      }
    }, 1000);
  }

  if (!lamp.value) {
    clearInterval(setTimeOutResult.value);
  }
};

onMounted(() => { });
</script>

<style lang="scss" scoped>
.timer {
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}

.numbers {
  margin: 32px 0;
}

.numbers h1 {
  font-size: 256px;
  line-height: 217px;
  font-weight: 800;
}

.status {
  width: 250px;
  height: 48px;
  border-radius: 48px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 20px;
}

.controls {
  height: 100px;
  display: flex;
  align-items: center;
}

.controls button {
  transition: all 0.3s ease;
}

.controls button:focus {
  padding: 32px 48px;
}
</style>
