<!-- <script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
</script>

<template>
  <header>
    <img alt="Vue logo" class="logo" src="@/assets/logo.svg" width="125" height="125" />

    <div class="wrapper">
      <HelloWorld msg="You did it!" />

      <nav>
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/about">About</RouterLink>
      </nav>
    </div>
  </header>

  <RouterView />
</template>

<style scoped>
header {
  line-height: 1.5;
  max-height: 100vh;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

nav {
  width: 100%;
  font-size: 12px;
  text-align: center;
  margin-top: 2rem;
}

nav a.router-link-exact-active {
  color: var(--color-text);
}

nav a.router-link-exact-active:hover {
  background-color: transparent;
}

nav a {
  display: inline-block;
  padding: 0 1rem;
  border-left: 1px solid var(--color-border);
}

nav a:first-of-type {
  border: 0;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  nav {
    text-align: left;
    margin-left: -1rem;
    font-size: 1rem;

    padding: 1rem 0;
    margin-top: 1rem;
  }
}
</style> -->


<script setup lang="ts">
import { onBeforeMount, ref } from 'vue'
import { useTimeAgo } from '@vueuse/core'
import MyWorker from './my-worker?worker'

import ReloadPrompt from './ReloadPrompt.vue'

const pong = ref(null)
const mode = ref(null)
const worker = new MyWorker()

// replaced dyanmicaly
const date = '__DATE__'
const timeAgo = useTimeAgo(date)

const runWorker = async () => {
  worker.postMessage('ping')
}
const resetMessage = async () => {
  worker.postMessage('clear')
}
const messageFromWorker = async ({ data: { msg, mode: useMode } }) => {
  pong.value = msg
  mode.value = useMode
}

onBeforeMount(() => {
  worker.addEventListener('message', messageFromWorker)
})
</script>

<template>
  <img src="/favicon.svg" alt="PWA Logo" width="60" height="60">
  <br>
  <div>Built at: {{ date }} ({{ timeAgo }})</div>
  <br>
  <router-view />
  <br>
  <br>
  <button @click="runWorker">
    Ping web worker
  </button>
  &#160;&#160;
  <button @click="resetMessage">
    Reset message
  </button>
  <br>
  <br>
  <template v-if="pong">
    Response from web worker: <span> Message: {{ pong }} </span>&#160;&#160;<span> Using ENV mode: {{ mode }}</span>
  </template>
  <ReloadPrompt />
</template>
