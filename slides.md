---
theme: ./theme
title: "Vue Done Right: Composable Best Practices Simplified"
website: lichter.io
handle: TheAlexLichter
favicon: https://lichter.io/img/me@2x.jpg
highlighter: shiki
lineNumbers: true
layout: intro
transition: fade
---

---
layout: intro
preload: false
transition: none
---

<h1 class="mt-12 flex justify-center items-center" v-motion :initial="{ x: -500 }" :enter="{ x: 0, transition: { duration: 500 } }">

<logos-nuxt-icon class="text-10xl"/>

</h1>

---
layout: intro
transition: none
---

<h1 v-motion :initial="{ y: 0 }" :enter="{ y: -500, transition: { duration: 750, delay: 250 } }" class="mt-12 flex justify-center items-center">

<logos-nuxt-icon class="text-10xl"/>

</h1>

---
layout: intro
preload: false
clicks: 1
---

<h1 class="mt-12">

<logos-vue class="text-8xl" :class="$slidev.nav.clicks === 1 && 'transition ease-in-out animate-ping'" :style="$slidev.nav.clicks === 1 && 'animation: ping 2s cubic-bezier(0, 0, 0.2, 1) 3 !important;'" v-motion :initial="{ y: 500 }" :enter="{ y: 0, transition: { duration: 500, delay: 250 } }"/>

</h1>

---
layout: intro
---

# <logos-vue class="text-4xl mb-3" />ue Done <span class="text-[#41b883]">Right</span>

## Composable Best Practices <span class="text-[#41b883] font-semibold">Simplified</span>

### PragVue 2024

<style>

h2 {
  @apply !mt-16;
}

h3 {
  @apply !mt-32 !text-lg;
}
</style>

---
layout: two-cols
heading: About me
---

<template v-slot:default>
<div class="flex flex-col justify-center items-center h-full">
  <img class="w-75 rounded-full" src="https://lichter.io/img/me@2x.webp" />
  <h2 class="mt-4">Alexander Lichter</h2>
</div>
</template>

<template v-slot:right>
<VClicks class="space-y-2 mt-10 text-xl h-full">

* <mdi-account-check class="dark:text-green-100 text-green-700" /> **Web Engineering Consultant**
* <mdi-microphone /> Speaker &bull; Instructor &bull; Podcast Host
* <logos-nuxt-icon /> Nuxt.js Team
* <mdi-twitter class="text-blue-400" /><mdi-youtube class="text-red-500" /><mdi-twitch class="text-purple-700" /> @TheAlexLichter
* <mdi-web /> [https://lichter.io](https://lichter.io)
* <mdi-github /> [manniL](https://github.com/manniL)

</VClicks>
</template>

---
layout: intro
---

# Composition API? Why?

---

# Composition API? Why?

And what's wrong with the Options API?


<VClicks>

* Nothing "wrong" with OAPI, it won't go away, but CAPI is:
* Future-proof (hey Vapor!)
* Better TS Support
* Easier and more predictable to share code
* Separation of Concerns
* Con: More "difficult" if no good patterns/best practices

</VClicks>

---

<LightOrDark>
  <template #dark>
    <img src="https://img.lichter.io/infographics/vue-soc-both.png" class="m-auto h-105 filter invert">
  </template>
  <template #light>
    <img src="https://img.lichter.io/infographics/vue-soc-both.png" class="m-auto h-105">
  </template>
</LightOrDark>

---
layout: intro
---

# What is a composable?

---

# What is a composable?

<VClicks>

> In the context of Vue applications, a "composable" is a function that leverages Vue's Composition API to encapsulate and reuse **stateful** logic

</VClicks>

---
layout: image
preload: false
image: https://media1.giphy.com/media/pPhyAv5t9V8djyRFJH/200w.webp?cid=dda24d505en5ptm0b7vuax4xnm02tifsojx365molkzumjth&ep=v1_internal_gif_by_id&rid=200w.webp&ct=g
---

---

# What is a composable?

> In the context of Vue applications, a "composable" is a function that leverages Vue's Composition API to encapsulate and reuse **stateful** logic

## Composable Checklist (1 is sufficient)

<VClicks>

* Function uses other composable
* Function uses lifecycle hooks
* Function uses the Reactivity API (`ref`, `reactive`, `computed`)
* Function uses Dependency Injection (`provide`/`inject`)

</VClicks>

<style>
  h2 {
    @apply !mt-16 !text-2xl;
  }
  ul {
    @apply !mt-4;
  };
</style>

---

<h1>Composable? (Example 1) <mdi-check-bold class="text-green-500" :class="$clicks != 0 ? 'visible' : 'invisible'" /></h1>

<Code file="composable-or-not-1.ts">

````md magic-move 
```ts
import { onBeforeUnmount, onMounted } from 'vue'

export function intervalFn(cb: () => void, interval = 1000) {
  let timer: ReturnType<typeof setInterval> | null = null

  function clean() {
    if (timer) {
      clearInterval(timer)
      timer = null
    }
  }

  onMounted(() => {
    if (interval <= 0)
      return
    timer = setInterval(cb, interval)
  })
  
  onBeforeUnmount(() => {
    clean()
  })
}
```
```ts
import { onBeforeUnmount, onMounted } from 'vue'

export function useIntervalFn(cb: () => void, interval = 1000) {
  let timer: ReturnType<typeof setInterval> | null = null

  function clean() {
    if (timer) {
      clearInterval(timer)
      timer = null
    }
  }

  onMounted(() => {
    if (interval <= 0)
      return
    timer = setInterval(cb, interval)
  })
  
  onBeforeUnmount(() => {
    clean()
  })
}
// Adapted from https://github.com/vueuse/vueuse/blob/main/packages/shared/useIntervalFn/index.ts
```
````

</Code>

----

<h1>Composable? (Example 2) <mdi-close class="text-red-700" :class="$clicks >= 1 ? 'visible' : 'invisible'" /></h1>

<Code file="composable-or-not-2.ts">

````md magic-move
```ts
export function onUserSpeaksGerman(): Boolean {
  const doesSpeakGerman = navigator.languages.some(lang => lang.startsWith('de'))
  if (!doesSpeakGerman) {
    return false
  }

  const didUserSeeGermanNotificationAlready = localStorage.getItem('some-key-here')
  if (didUserSeeGermanNotificationAlready) {
    return false
  }

  // Do something else here

  return true
}
```
```ts
export function onUserSpeaksGerman(): Boolean {
  const doesSpeakGerman = navigator.languages.some(lang => lang.startsWith('de'))
  if (!doesSpeakGerman) {
    return false
  }

  const didUserSeeGermanNotificationAlready = localStorage.getItem('some-key-here')
  if (didUserSeeGermanNotificationAlready) {
    return false
  }

  // Do something else here

  return true
}
// Adapted from https://github.com/manniL/lichter.io/blob/main/app.vue
```
````

</Code>

---

<h1>Composable? (Example 3) <mdi-check-bold class="text-green-500" :class="$clicks != 0 ? 'visible' : 'invisible'" /></h1>

<Code file="composable-or-not-3.ts">

````md magic-move
```ts
import { ref, computed, readonly } from 'vue'
export function user () {
  const user = ref(null)
  const history = computed(() => user.value?.history ?? {})

  async function fetchUser () {
    user.value = await fetch('/api/v1/user') // some api call
  }

  // More helpers hear optionally

  return {
    user: readonly(user),
    fetchUser,
    history
  }
}
```
```ts
import { ref, computed, readonly } from 'vue'

export function useUser () {
  const user = ref(null)
  const history = computed(() => user.value?.history ?? {})

  async function fetchUser () {
    user.value = await fetch('/api/v1/user') // some api call
  }

  // More helpers here optionally

  return {
    user: readonly(user),
    fetchUser,
    history
  }
}
```
````

</Code>

---

<h1>Composable? (Example 4) <mdi-close class="text-red-700" :class="$clicks >= 1 ? 'visible' : 'invisible'" /></h1>

<Code file="composable-or-not-4.ts">

````md magic-move
```ts
const { format } = Intl.NumberFormat('en-GB', {})

export default format
```
```ts
const { format } = Intl.NumberFormat('en-GB', {})

export default format
```
````

</Code>

---
layout: intro
---

# Organizing Composables

---

# Organizing Composables

* How do you organize your code in vanilla JavaScript or TypeScript?

<VClicks>

* You put code that belongs to the same "concern"/"topic"/"feature" together (colocation)
* You use functions to declare groups of code that belong together (encapsulation)
* You then use these functions in other functions (composition)
* Eventually, you have levels of abstraction

</VClicks>

---
layout: intro
---

# Why don't we do this in our 
# <logos-vue /> code?

<div class="!mt-16" v-click>

This is the [live coding part](https://play.vuejs.org/#eNp9VMtu2zAQ/JWNLpYBVyoQ9JIqQR8I0BZIWyRGe+GFlVYSE4oUSMo2YPjfu6JetmD7IlDc4ezs7JL74HNdR5sGg7sgsakRtQOLrqlBclXcs8BZFjwwJapaGwd7MJivINVV3TjMVrDlLi3hALnRFSyIZ8EUU3EMGXecqVQr60AbUQjF5RNaywuE+5YlXHxDKTX81UZmN4vlABb2pdRb1YNyLi0exZ65ynS1xp27isINGovZLOyFDdoHtOmxk7gBEYZLuH+Yq482XDYY2VoKFy4Wy6gnCJfRqxaq3RqVVJc490wBiBzCSWvHu+xCQLJcY9RcXQdqEYf204POKmTqMJTsm0Q0TPkVJfXmrSBUuP3TpR1FzS3u2KgE7+Kg+2Y6eSJ4lNaCnrgrI+PpqOwE3kcfLsIvpnXmpJYKXakzKiVvVOqEVuB0UUjsbaQ8QxknvhLRzXyPWFvSJO7mnqacfhxWteQO6Q8gEYq6Bpt3lc5Q0m2YOc0CiDvgv8Y50vIplSJ9I+A0xZTWL+karb3SJO7A5w+eVENn+tXpoUxsSJTIp0T+kra+7vfj1B28s0lM6DPHZn6PBOtSWCi5BQ5pyVWK8JG8n3iSeLQoWNHzQJOeiyJ6tVrRG+LNZ0E77kKi+VW3LaIn5G7oOws43frtD7/X9nY17NOIpm9n9l/trt1jwW+DFs2GXBljjpsCXRd+fPlJxRwFqWmNJPSV4DNaLZtWYwf70qiMZB/hvNrv/vUTqljbx51DZYeiuuGkGfZ4FtAD+PVK6ZPc2+i2n/1DcPgP5T7kcQ==)!

</div>

---
layout: intro
---

# Inline Composables 🎉

---

<LightOrDark>
  <template #dark>
    <img src="https://raw.githubusercontent.com/vuejs/docs/main/src/guide/extras/images/composition-api-after.png" class="m-auto h-140 -mt-8 filter invert">
  </template>
  <template #light>
    <img src="https://raw.githubusercontent.com/vuejs/docs/main/src/guide/extras/images/composition-api-after.png" class="m-auto h-140 -mt-8">
  </template>
</LightOrDark>

---
preload: false
---

<LightOrDark>
  <template #dark>
    <img 
    v-motion
    :initial="{ scale: 1, x: 0, y: 0 }"
    :enter="{ x: -1500, scale: 13, transition: { duration: 1500 } }"
    src="https://raw.githubusercontent.com/vuejs/docs/main/src/guide/extras/images/composition-api-after.png" 
    class="m-auto h-140 -mt-8 filter invert">
  </template>
  <template #light>
    <img
    v-motion
    :initial="{ scale: 1, x: 0, y: 0 }"
    :enter="{ x: -1500, scale: 13, transition: { duration: 1500 } }"
    src="https://raw.githubusercontent.com/vuejs/docs/main/src/guide/extras/images/composition-api-after.png"
    class="m-auto h-140 -mt-8">
  </template>
</LightOrDark>

<!--

Open https://gist.github.com/yyx990803/8854f8f6a97631576c14b63c8acd8f2e

-->
---
layout: intro
---

# Maintain Reactivity

---

# Maintain Reactivity


Common pitfalls

<VClicks>

* Who "lost" reactivity once in their application?
* Let's check out some reasons why

</VClicks>

---

# Pitfall 1 - .value can "end" Reactivity <mdi-check-bold class="text-green-500" :class="$clicks ? 'visible' : 'invisible'"  />

<Code>

````md magic-move
```ts
const source = ref('some-icon.png')
const icon = useFavicon(source.value)

source.value = 'my-new-icon.png'
```
```ts
const source = ref('some-icon.png')
const icon = useFavicon(source)

source.value = 'my-new-icon.png'
```
````
</Code>

<VClicks at="1">

* Reactivity will be kept by passing **the `ref`** and not the plain value
* ...if the composable can handle `ref`s (it should!)
* **Always** pass the `ref` if you want to keep reactivity
* Exceptions are using other reactivity constructs, e.g. a `computed` (there, use `.value`)

</VClicks>

---

# Pitfall 2 - `reactive` and destructuring <mdi-check-bold class="text-green-500" :class="$clicks ? 'visible' : 'invisible'"  />

<Code>

````md magic-move
```ts
let { user } = reactive({ user: null })

watchEffect(() => {
  console.log(user)
})

user = { name: 'Alex' }
```
```ts
const state = reactive({ user: null })
watchEffect(() => {
  console.log(state.user)
})

state.user = { name: 'Alex' }
```
```ts
const state = reactive({ user: null })
const { user } = toRefs(state)

watchEffect(() => {
  console.log(user.value)
})

user.value = { name: 'Alex' }
```
````

<VClicks at="1">

* Use the reactive object directly or a constraint like `let user = state.user`
* Or better: Convert it to a `ref` with `toRef`/`toRefs`
* Destructuring is **fine for props** though since 3.5 (or with the experimental flag before)

</VClicks>

</Code>

--- 
layout: intro
---

# Use [VueUse](https://vueuse.org/) when you can

<VClicks>

### Because it has lots of tested composables which can do what you need!

</VClicks>

<style>

h3 {
 @apply mt-16  
}

</style>

---
layout: intro
---

## Ready for more? Then <span class="inline-block bg-gradient-to-r from-blue-600 via-red-500 to-green-400 bg-clip-text text-transparent">follow</span> along!

<div class="flex mx-32 gap-8 my-auto justify-around">

<Qrcode v-click class="max-w-xl" url="https://www.youtube.com/@TheAlexLichter/" note="My YouTube Channel @TheAlexLichter" />
<Qrcode v-click class="max-w-xl" url="https://dejavue.fm/?ref=enterjs" note="The DejaVue Podcast &nbsp; dejavue.fm" />

</div>

---
layout: intro
---

# Thanks a lot to my sponsors!
<img src="https://raw.githubusercontent.com/manniL/static/main/sponsors.svg" class="h-80 mx-auto" alt="My GitHub sponsors">

---
layout: two-cols
heading: Thank you for your attention!
---

<template v-slot:default>
<div class="flex flex-col justify-center items-center h-full">
<img
  class="w-75 rounded-full"
  src="https://lichter.io/img/me@2x.webp"
  />
  <h2 class="mt-4">Alexander Lichter</h2>
</div>
</template>

<template v-slot:right>

* <mdi-account-check class="dark:text-green-100 text-green-700" /> **Web Engineering Consultant**
* <mdi-microphone /> Speaker &bull; Instructor &bull; Podcast Host
* <logos-nuxt-icon /> Nuxt.js Team
* <mdi-twitter class="text-blue-400" /><mdi-youtube class="text-red-500" /><mdi-twitch class="text-purple-700" /> @TheAlexLichter
* <mdi-web /> [https://lichter.io](https://lichter.io)
* <mdi-github /> [manniL](https://github.com/manniL)

</template>

<style>
  ul {
    @apply space-y-2 mt-10 text-xl h-full;
  }
</style>

---
layout: intro
---

## Ready for more? Then <span class="inline-block bg-gradient-to-r from-blue-600 via-red-500 to-green-400 bg-clip-text text-transparent">follow</span> along!

<div class="flex mx-32 gap-8 my-auto justify-around">

<Qrcode class="max-w-xl" url="https://www.youtube.com/@TheAlexLichter/" note="My YouTube Channel @TheAlexLichter" />
<Qrcode class="max-w-xl" url="https://dejavue.fm/?ref=enterjs" note="The DejaVue Podcast &nbsp; dejavue.fm" />

</div>
