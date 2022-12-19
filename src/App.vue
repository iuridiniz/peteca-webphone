<script setup lang="ts">

import { ref, computed, watch } from 'vue';

type AppState = 'online:idle' | 'call:incoming-ringing' | 'call:outgoing-active' | 'call:onhold' | 'offline';

const displayContent = ref<string>("");
const AppState = ref<AppState>('offline');
const inCall = computed<boolean>(() => AppState.value.startsWith('call:'));
const displayHasContent = computed<boolean>(() => displayContent.value !== null && displayContent.value.length > 0);

const validateKeys = (e: KeyboardEvent) => {
  const key = e.key;
  const validKeys = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9", "*", "#", "+", "-", "(", ")", " ", "Backspace", "Enter"];
  if (!validKeys.includes(key)) {
    e.preventDefault();
  }
  /* skip duplicate space, start space */
  const lastChar = displayContent.value[displayContent.value.length - 1];
  if (key == ' ' && (lastChar == ' ' || displayContent.value.length == 0)) {
    e.preventDefault();
  }

  /* apply effect in respective button */
  const keyTransformed = e.key == "*" ? "asterisk" : e.key == "#" ? "pound" : (e.key > '0' && e.key < '9') ? e.key : 'unknown';
  const button = document.querySelector(`.pw-keypad-button-${keyTransformed}`);
  if (button) {
    button.classList.add("pw-keypad-button-flash");
    setTimeout(() => {
      button.classList.remove("pw-keypad-button-flash");
    }, 100);
  }
};

const keypadButtonClicked = (e: MouseEvent) => {
  const button = e.currentTarget as HTMLButtonElement;
  const value = button.dataset.value;
  const input = document.querySelector(".pw-display-text") as HTMLInputElement;
  input.focus();
  displayContent.value += value;
}

const callStartButtonClicked = () => {
  AppState.value = 'call:outgoing-active';
}

const callEndButtonClicked = () => {
  AppState.value = 'online:idle';
}

const clearButtonClicked = () => {
  const input = document.querySelector(".pw-display-text") as HTMLInputElement;
  input.focus();
  displayContent.value = "";
}

watch(
  [displayContent],
  () => {
    const input = document.querySelector(".pw-display-text") as HTMLInputElement;

    if (!input.classList.contains("pw-display-text-small")) {
      if (input.clientWidth < input.scrollWidth) {
        input.classList.add("pw-display-text-small");
        input.dataset.overflowOnLenght = input.value.length.toString();
        return;
      }
    } else {
      if (input.classList.contains("pw-display-text-small") && input.dataset.overflowOnLenght) {
        const overflowOnLenght = parseInt(input.dataset.overflowOnLenght as string);
        if (input.value.length < overflowOnLenght) {
          input.classList.remove("pw-display-text-small");
          input.dataset.overflowOnLenght = "";
        }
      }
    }
  },
  {
    flush: 'post'
  }
);
</script>

<template>
  <div class="pw pw-root">
    <main class="pw-main">
      <div class="pw-app-bar">
        <div class="pw-app-bar-title">
          Peteca WebPhone
        </div>
        <div class="pw-app-bar-status">•</div>
        <div class="pw-app-bar-actions">
          <button class="pw-app-bar-action-button">
            <svg xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMidYMid meet" viewBox="0 0 24 24" width="1em">
              <path fill="currentColor" d="M6 21v-2h12v2Z" />
            </svg>
          </button>
          <button class="pw-app-bar-action-button">
            <svg class="pw-icon-tape-line" xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMidYMid meet"
              viewBox="0 0 24 24" width="1em">
              <path fill="currentColor"
                d="m9.25 22l-.4-3.2q-.325-.125-.612-.3q-.288-.175-.563-.375L4.7 19.375l-2.75-4.75l2.575-1.95Q4.5 12.5 4.5 12.337v-.675q0-.162.025-.337L1.95 9.375l2.75-4.75l2.975 1.25q.275-.2.575-.375q.3-.175.6-.3l.4-3.2h5.5l.4 3.2q.325.125.613.3q.287.175.562.375l2.975-1.25l2.75 4.75l-2.575 1.95q.025.175.025.337v.675q0 .163-.05.338l2.575 1.95l-2.75 4.75l-2.95-1.25q-.275.2-.575.375q-.3.175-.6.3l-.4 3.2Zm2.8-6.5q1.45 0 2.475-1.025Q15.55 13.45 15.55 12q0-1.45-1.025-2.475Q13.5 8.5 12.05 8.5q-1.475 0-2.488 1.025Q8.55 10.55 8.55 12q0 1.45 1.012 2.475Q10.575 15.5 12.05 15.5Z" />
            </svg>
          </button>
        </div>
      </div>
      <div class="pw-display">
        <input @keypress="validateKeys" v-model="displayContent" type="text" class="pw-display-text" />
      </div>
      <div class="pw-keypad">
        <button @click="keypadButtonClicked" data-value="1" class="pw-keypad-button pw-keypad-button-1">
          <div class="pw-keypad-button-upper">1</div>
          <svg class="pw-keypad-button-lower pw-icon-tape-line" xmlns="http://www.w3.org/2000/svg"
            preserveAspectRatio="xMidYMid meet" viewBox="0 0 24 24" width="1em">
            <path fill="currentColor"
              d="M10.83 13h2.34A3 3 0 1 1 16 15H8a3 3 0 1 1 2.83-2zM4 5v14h16V5H4zM3 3h18a1 1 0 0 1 1 1v16a1 1 0 0 1-1 1H3a1 1 0 0 1-1-1V4a1 1 0 0 1 1-1zm5 10a1 1 0 1 0 0-2a1 1 0 0 0 0 2zm8 0a1 1 0 1 0 0-2a1 1 0 0 0 0 2z" />
          </svg>
        </button>
        <button @click="keypadButtonClicked" data-value="2" class="pw-keypad-button pw-keypad-button-2">
          <div class="pw-keypad-button-upper">2</div>
          <small class="pw-keypad-button-lower">ABC</small>
        </button>
        <button @click="keypadButtonClicked" data-value="3" class="pw-keypad-button pw-keypad-button-3">
          <div class="pw-keypad-button-upper">3</div>
          <small class="pw-keypad-button-lower">DEF</small>
        </button>
        <button @click="keypadButtonClicked" data-value="4" class="pw-keypad-button pw-keypad-button-4">
          <div class="pw-keypad-button-upper">4</div>
          <small class="pw-keypad-button-lower">GHI</small>
        </button>
        <button @click="keypadButtonClicked" data-value="5" class="pw-keypad-button pw-keypad-button-5">
          <div class="pw-keypad-button-upper">5</div>
          <small class="pw-keypad-button-lower">JKL</small>
        </button>
        <button @click="keypadButtonClicked" data-value="6" class="pw-keypad-button pw-keypad-button-6">
          <div class="pw-keypad-button-upper">6</div>
          <small class="pw-keypad-button-lower">MNO</small>
        </button>
        <button @click="keypadButtonClicked" data-value="7" class="pw-keypad-button pw-keypad-button-7">
          <div class="pw-keypad-button-upper">7</div>
          <small class="pw-keypad-button-lower">PQRS</small>
        </button>
        <button @click="keypadButtonClicked" data-value="8" class="pw-keypad-button pw-keypad-button-8">
          <div class="pw-keypad-button-upper">8</div>
          <small class="pw-keypad-button-lower">TUV</small>
        </button>
        <button @click="keypadButtonClicked" data-value="9" class="pw-keypad-button pw-keypad-button-9">
          <div class="pw-keypad-button-upper">9</div>
          <small class="pw-keypad-button-lower">WXYZ</small>
        </button>
        <button @click="keypadButtonClicked" data-value="*" class="pw-keypad-button pw-keypad-button-asterisk">
          <div class="pw-keypad-button-upper">*</div>
        </button>
        <button @click="keypadButtonClicked" data-value="0" class="pw-keypad-button pw-keypad-button-0">
          <div class="pw-keypad-button-upper">0</div>
        </button>
        <button @click="keypadButtonClicked" data-value="#" class="pw-keypad-button pw-keypad-button-pound">
          <div class="pw-keypad-button-upper">#</div>
        </button>
      </div>
      <div class="pw-actions">
        <button class="pw-action-button pw-action-button-call-start" :disabled="inCall" @click="callStartButtonClicked">
          <svg class="pw-icon-call-start" xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMidYMid meet"
            viewBox="0 0 24 24" width="1em">
            <path fill="currentColor"
              d="M19.95 21q-3.225 0-6.287-1.425q-3.063-1.425-5.425-3.8q-2.363-2.375-3.8-5.438Q3 7.275 3 4.05v-.525Q3 3.25 3.05 3H8.9l.925 5.025l-2.85 2.875q1.05 1.8 2.638 3.375Q11.2 15.85 13.1 17l2.9-2.9l5 1v5.85q-.25.025-.525.038Q20.2 21 19.95 21Z" />
          </svg>
        </button>
        <button class="pw-action-button pw-action-button-call-end" :disabled="!inCall" @click="callEndButtonClicked">
          <svg class="pw-icon-call-end" version="1.1" viewBox="0 0 24 24" width="1em"
            xmlns="http://www.w3.org/2000/svg">
            <g transform="matrix(3.728 0 0 3.7281 -154.43 -424.88)">
              <path fill="currentColor"
                d="m46.709 114.77-4.4809 4.4814 0.34675 0.34675c0.51692-0.51388 0.90871-0.90391 1.2501-1.2438 0.37318 0.34007 0.79221 0.61846 1.2568 0.83457 0.5401 0.25136 1.0946 0.37672 1.6635 0.37672 0.0441 0 0.0905-1e-3 0.13901-3e-3 0.0485-2e-3 0.09491-5e-3 0.13901-0.0103v-1.5482l-1.3229-0.26458-0.7674 0.76739c-0.25782-0.15605-0.49927-0.33395-0.7245-0.53433 1.4652-1.4608 1.2486-1.2558 2.8479-2.8551zm-4.4354 0.0331c-0.0088 0.0441-0.01292 0.0905-0.01292 0.13901v0.13901c0 0.56886 0.12669 1.1234 0.38034 1.6635 0.14066 0.29981 0.30669 0.58091 0.49764 0.84336l0.37827-0.37879c-0.0736-0.10296-0.14192-0.20824-0.20464-0.31575l0.75396-0.76067-0.24495-1.3296z"
                stroke-width=".26458" />
            </g>
          </svg>
        </button>
        <button class="pw-action-button pw-action-button-clear" :disabled="!displayHasContent"
          @click="clearButtonClicked">
          <svg class="pw-icon-clear" xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMidYMid meet"
            viewBox="0 0 24 24" width="1em">
            <path fill="currentColor"
              d="m11.4 16l2.6-2.6l2.6 2.6l1.4-1.4l-2.6-2.6L18 9.4L16.6 8L14 10.6L11.4 8L10 9.4l2.6 2.6l-2.6 2.6Zm-3.45 3L3 12l4.95-7H21v14ZM9 17h10V7H9l-3.55 5Zm10 0V7Z" />
          </svg>
        </button>
      </div>
    </main>

  </div>
</template>

<style lang="scss">
.pw {
  .pw-main {
    @apply pw-bg-gray-100;
    @apply pw-flex-col;
    @apply pw-flex;
    @apply pw-justify-center;
    @apply pw-items-stretch;
    @apply pw-max-w-sm;
    @apply pw-max-h-screen;
  }

  .pw-app-bar {
    @apply pw-p-0;
    @apply pw-m-0;
    @apply pw-flex;
    @apply pw-justify-between;
    @apply pw-items-center;
  }

  .pw-display {
    @apply pw-bg-gray-600;
    @apply pw-py-8;
    @apply pw-px-4;
    @apply pw-max-w-sm;
    @apply pw-rounded-xl;
    @apply pw-shadow-lg;
    @apply pw-m-2;
    @apply pw-h-24;
    @apply pw-flex;
  }

  .pw-display-text {
    font-family: "Peteca", ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
    ;
    @apply pw-text-center;
    @apply pw-text-green-300;
    @apply pw-bg-gray-600;
    @apply pw-w-full;
    @apply pw-text-4xl;
  }

  .pw-display-text.pw-display-text-small {
    @apply pw-text-base;
  }

  .pw-keypad {
    @apply pw-bg-gray-300;
    @apply pw-text-gray-700;
    @apply pw-grid;
    @apply pw-grid-cols-3;
    @apply pw-gap-2;
    @apply pw-p-4;
    @apply pw-max-w-sm;
    @apply pw-rounded-xl;
    @apply pw-shadow-lg;
    @apply pw-m-2;
  }

  .pw-keypad-button {
    @apply pw-col-span-1;
    @apply pw-rounded-lg;
    @apply pw-p-1;
    @apply pw-bg-gray-200;
    @apply pw-w-auto;
    @apply pw-h-16;
    @apply pw-font-bold;
    @apply pw-text-2xl;
    @apply pw-font-mono;
  }

  .pw-keypad-button-upper {
    @apply pw-text-center;
  }

  .pw-keypad-button-lower {
    @apply pw-text-center;
    @apply pw-font-normal;
    @apply pw-text-gray-400;

  }

  .pw-keypad-button-flash {
    @apply pw-bg-gray-700;
    @apply pw-shadow-2xl;
    opacity: 0.8;
  }

  .pw-keypad-button:active {
    @apply pw-bg-gray-700;
    @apply pw-shadow-2xl;
    opacity: 0.8;
    transform: translateY(4px);
  }

  .pw-actions {
    @apply pw-bg-gray-300;
    @apply pw-text-gray-700;
    @apply pw-grid;
    @apply pw-grid-cols-3;
    @apply pw-gap-4;
    @apply pw-p-2;
    @apply pw-rounded-xl;
    @apply pw-shadow-lg;
    @apply pw-m-2;
    @apply pw-mx-auto;
  }

  .pw-action-button {
    @apply pw-bg-gray-600;
    @apply pw-text-gray-200;
    @apply pw-p-0;
    @apply pw-mx-1;
    @apply pw-w-16;
    @apply pw-h-16;
    @apply pw-rounded-full;
    @apply pw-shadow-lg;

    .pw-icon-call-start {
      @apply pw-text-green-400;
    }

    .pw-icon-call-end {
      @apply pw-text-red-400;
    }
  }

  .pw-action-button:disabled {
    @apply pw-bg-gray-400;
    @apply pw-text-gray-200;
    @apply pw-p-0;
    @apply pw-mx-1;

    @apply pw-rounded-full;
    @apply pw-shadow-lg;

    .pw-icon-call-start {
      @apply pw-text-green-100;
    }

    .pw-icon-call-end {
      @apply pw-text-red-100;
    }
  }

  .pw-icon-tape-line {
    @apply pw-w-full;
    @apply pw-h-6;
  }

  .pw-icon-call-start,
  .pw-icon-call-end,
  .pw-icon-clear {
    @apply pw-w-full;
    @apply pw-h-12;
  }

  .pw-icon-clear {
    @apply pw-pr-1;
  }
}
</style>
