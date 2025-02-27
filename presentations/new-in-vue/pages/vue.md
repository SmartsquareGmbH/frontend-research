---
transition: fade-out
---

# `<script setup>`

````md magic-move {lines: true}
```vue
<script>
export default {
  data() {
    return {
      example: "Hello World"
    }
  }
}
</script>

<template>
  <span>{{ example }}</span>
</template>
```

```vue
<script>
export default {
  setup() {
    const example = "Hello World"
  
    return { example }
  }
}
</script>

<template>
  <span>{{ example }}</span>
</template>
```

```vue
<script setup lang="ts">
  const example = "Hello World"
</script>

<template>
  <span>{{ example }}</span>
</template>
```
````

---
transition: fade-out
---

# Composition API basics 1

```vue twoslash
<script setup lang="ts">
  import { ref } from "vue"

  const count = ref(0)

  function increment() {
    count.value++
  }
</script>

<template>
  <button @click="increment">
    {{ count }}
  </button>
</template>
```

---
transition: fade-out
---

# Composition API basics 1 - warum Refs?

- Explizite Reaktivität mit der `Ref<T>` Struktur.
- Zugriff mit `.value` (aber nicht immer).

<br>

```ts
// pseudo code, not actual implementation
const myRef = {
  _value: 0,
  get value() {
    track()
    return this._value
  },
  set value(newValue) {
    this._value = newValue
    trigger()
  }
}
```

---
transition: fade-out
---

# Composition API basics 2

```vue {all|9|14} twoslash
<script setup lang="ts">
  import { computed, ref } from "vue"

  const users = ref([
    { "name": "Paul", "age": 25 },
    { "name": "Anna", "age": 30 },
  ])
  
  const userNames = computed(() => users.value.map(user => user.name))
</script>

<template>
  <ul>
    <li v-for="name in userNames" :key="name">
      {{ name }}
    </li>
  </ul>
</template>
```

<arrow v-click="[1, 2]" x1="450" y1="350" x2="410" y2="300" color="red" width="2" arrowSize="1" />

---
transition: fade-out
---

# Composition API basics 3

```vue twoslash
<script setup lang="ts">
  import { ref, watch } from "vue"

  const prices = ref([100, 2, 30])
  const total = ref(0)
  
  watch(prices, (newPrices) => {
    total.value = newPrices.reduce((acc, price) => acc + price, 0)
  })
</script>

<template>
  <span>{{ total }}</span>
</template>
```

---
transition: fade-out
---

# Composition API basics 4

```vue twoslash
<script setup lang="ts">
  type Form = {
    name: string
    email: string
  }

  const emit = defineEmits<{
    submit: [form: Form]
  }>()

  function submitForm(form: Form) {
    emit("submit", form)
  }
</script>
```

---
transition: fade-out
---

# Composition API Ref vs Reactive

```vue twoslash
<script setup lang="ts">
  import { ref, reactive } from "vue"

  const paul = { name: "Paul" }

  const paulRef = ref(paul)
  const paulReactive = reactive(paul)
</script>
```

<br>

- `Reactive` erzeugt einen Proxy um das Objekt.
- Objekt kann nicht ersetzt werden und funktioniert nicht für primitive Typen.
- `Ref` wird empfohlen.

---
transition: fade-out
---

# Composables

```ts twoslash
import { ref, onMounted, onUnmounted } from "vue"

export function useMouse() {
  const x = ref(0)
  const y = ref(0)

  function update(event: MouseEvent) {
    x.value = event.pageX
    y.value = event.pageY
  }

  onMounted(() => window.addEventListener("mousemove", update))
  onUnmounted(() => window.removeEventListener("mousemove", update))

  return { x, y }
}
```

---
transition: fade-out
---

# Composables

```vue
<script setup lang="ts">
  const { x, y } = useMouse()
</script>

<template>
  <div>
    <span>X: {{ x }}</span>
    <span>Y: {{ y }}</span>
  </div>
</template>
```

---
transition: fade-out
---

# Composables: Echtes Beispiel

```ts
import { enUS, de } from "date-fns/locale"

export const useDateFnsLocale = () => {
  const { locale } = useI18n()

  if (locale.value === "de") {
    return de
  } else {
    return enUS
  }
}
```

---
transition: fade-out
---

# [VueUse](https://vueuse.org/)

<br>

```ts
import { useMouse } from "@vueuse/core"

const { x, y, sourceType } = useMouse()
```

<br>

- [Riesige Sammlung](https://vueuse.org/functions.html) von hilfreichen Composables.
  - State, Browser, Sensoren, Animationen, etc.
- Kleiner Footprint und keine Abhängigkeiten.

---
transition: fade-out
---

# Composables vs. Mixins

- Haben einen ähnlichen Anwendungszweck.
- Composables werden empfohlen und Mixins sind "deprecated".
  - Composables sind einfacher wiederzuverwenden und sind nicht mit der Komponente verbunden.
  - Mixins sind schwerer zu verfolgen und können zu Konflikten führen.
