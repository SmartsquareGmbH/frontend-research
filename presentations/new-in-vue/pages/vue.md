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
mdc: true
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

<br />
<Mouse />

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

---
transition: fade-out
---

# APIs im Vergleich

- Options API
  - simpler zum Einstieg

<div v-click="1">

- Composition API
  - besser für größere Projekte
  - bessere Strukturierung (nach Logik, nicht nach Typ)
  - bessere Wiederverwendbarkeit (Composables statt Mixins)
  - bessere Typisierung (Typescript)
  - bessere Performance und Effizienz (Vapor Mode ohne Shadow-DOM, Bundle-Size)
  - neue Features, Libraries und Docs meistens für Composition API angepasst oder exklusiv

</div>

---
transition: fade-out
---

# Strukturierung

<div class="flex justify-center">
  <img src="/assets/compositionApi.png" class="h-100" />
</div>

---
transition: fade-out
---

````md magic-move {lines: true}
```vue
<script> // Options API
export default {
  props: { initialItems: { type: Array, required: true } },
  data() {
    return { items: [], searchTerm: '', lastSearchTime: null }
  },
  computed: {
    filteredItems() { return this.items
      .filter(i => i.name.toLowerCase().includes(this.searchTerm.toLowerCase())) },
    totalItems() { return this.items.length },
    favoriteCount() { return this.items.filter(i => i.favorite).length }
  },
  methods: {
    toggleFavorite(item) {
      item.favorite = !item.favorite
      this.$emit('favorite-changed', item)
    },
    logSearch() {
      this.lastSearchTime = new Date()
      console.log(`Search at ${this.lastSearchTime}`)
    }
  },
  created() {
    this.items = this.initialItems.map(item => ({ ...item, favorite: false }))
  }
}
</script>
```

```vue
<script lang="ts"> // Class Component API
@Component export default class MyComponent extends Vue {
  @Prop({ type: Array, required: true }) readonly initialItems!: Item[]

  items: Item[] = []
  searchTerm = ''
  lastSearchTime: Date | null = null

  get filteredItems(): Item[] {
    const term = this.searchTerm.toLowerCase()
    return this.items.filter(i => i.name.toLowerCase().includes(term))
  }
  // totalItems() ...
  get favoriteCount(): number {
    return this.items.filter(i => i.favorite).length
  }

  toggleFavorite(item: Item): Item {
    item.favorite = !item.favorite
      this.$emit('favorite-changed', item)
  }
  // logSearch() ...
  created(): void {
    this.items = this.initialItems.map(item => ({ ...item, favorite: false }))
  }
}
</script>
```


```vue {*|5-7|9-13|15-25}
<script setup> // Composition API
const props = defineProps({ initialItems: { type: Array, required: true } })
const emit = defineEmits(['favorite-changed'])

const items = ref([])
onMounted(() => { items.value = props.initialItems.map(item => ({...item, favorite: false})) })
const totalItems = computed(() => items.value.length)

const favoriteCount = computed(() => items.value.filter(i => i.favorite).length)
function toggleFavorite(item) {
  item.favorite = !item.favorite
  emit('favorite-changed', item)
}

const searchTerm = ref('')
const lastSearchTime = ref(null)
const filteredItems = computed(() => 
  items.value.filter(item => 
    item.name.toLowerCase().includes(searchTerm.value.toLowerCase())
  )
)
function logSearch() {
  lastSearchTime.value = new Date()
  console.log(`Search at ${lastSearchTime.value}`)
}
</script>
```

```ts
export function useSearch(items) { // Composable
  const searchTerm = ref('')
  const lastSearchTime = ref(null)

  const filteredItems = computed(() => 
    items.value.filter(i => i.name.toLowerCase().includes(searchTerm.value.toLowerCase()))
  )

  function logSearch() {
    lastSearchTime.value = new Date()
    console.log(`Search at ${lastSearchTime.value}`)
  }

  return {
    searchTerm,
    filteredItems,
    lastSearchTime,
    logSearch
  }
}
```

```vue
<script setup> // Composition API
const props = defineProps({ initialItems: { type: Array, required: true } })
const emit = defineEmits(['favorite-changed'])

const items = ref([])
onMounted(() => { items.value = props.initialItems.map(item => ({...item, favorite: false})) })
const totalItems = computed(() => items.value.length)

const favoriteCount = computed(() => items.value.filter(i => i.favorite).length)
function toggleFavorite(item) {
  item.favorite = !item.favorite
  emit('favorite-changed', item)
}

const { searchTerm, filteredItems, logSearch } = useSearch(items)
</script>
```
````

---
transition: fade-out
---

# Pinia

- Empfohlene Alternative zu Vuex
- Moderne Schreibweise auf Basis der Composition API

````md magic-move {lines: true}
```ts
export const useAuthStore = defineStore("auth", {
  state: () => ({ user: { name: "admin", password: "test" } }),
  getters: {
    username: (state) => state.name,
  },
  actions: {
    logout() {
      this.user = null
    },
  },
})
```

```ts
export const useAuthStore = defineStore("auth", () => {
  const user = ref({ name: "admin", password: "test" })
  const username = computed(() => user.name)
  function logout() {
    user.value = null
  }

  return { user, username, logout }
})
```
````

---
transition: fade-out
---

# Pinia Usage

````md magic-move {lines: true}
```vue
<script setup lang="ts">
  const store = useAuthStore()
</script>

<template>
  <span>{{ store.user.name }}</span>
  <btn @click="store.logout()">Logout</btn>
</template>
```

```vue
<script setup lang="ts">
  const store = useAuthStore()
  const { user, logout } = storeToRefs(store)
</script>

<template>
  <span>{{ user.name }}</span>
  <btn @click="logout()">Logout</btn>
</template>
```
````

---
transition: fade-out
---

# EventBus in Vue 3

- In Vue 3 gibt es keinen EventBus mehr, aber folgende Alternativen
  - [`useEventBus` aus VueUse](https://vueuse.org/core/useEventBus/)
  - Provide/Inject
  - useState (Nuxt)
  - Pinia

---
transition: fade-out
---

# Provide/Inject

<img src="../assets/provide-inject1.png" />

---
transition: fade-out
---

# Provide/Inject

<img src="../assets/provide-inject2.png" />

---
transition: fade-out
---

# Provide/Inject

<div class="grid grid-cols-2 gap-sm">

```vue
<script setup>
import { provide, ref } from 'vue'

const location = ref('North Pole')

function updateLocation() {
  location.value = 'South Pole'
}

provide('location', {
  location,
  updateLocation
})
</script>
```

```vue
<script setup>
import { inject } from 'vue'

const { location, updateLocation } = inject('location')
</script>

<template>
  <button @click="updateLocation">{{ location }}</button>
</template>
```

</div>

---
transition: fade-out
---

# Typescript (Props)

````md magic-move {lines: true}
```vue
<script setup lang="ts">
  const props = defineProps<{
    color: string
    width?: number
  }>()
</script>

<template>
  <v-icon :color="color" :width="width">md-vue</v-icon>
</template>
```

```vue
<script setup lang="ts">
  const props = withDefaults(defineProps<{
    color: string
    width?: number
  }>(), { width: 200 })
</script>

<template>
  <v-icon :color="color" :width="width">md-vue</v-icon>
</template>
```

```vue
<script setup lang="ts">
  const { color, width = 200 } = defineProps<{
    color: string
    width?: number
  }>()
</script>

<template>
  <v-icon :color="color" :width="width">md-vue</v-icon>
</template>
```

```vue
<script setup lang="ts">
  interface IconProps {
    color: string
    width?: number
  }

  const { color, width = 200 } = defineProps<IconProps>()
</script>

<template>
  <v-icon :color="color" :width="width">md-vue</v-icon>
</template>
```
````

---
transition: fade-out
---

# Typescript (Emit)

````md magic-move {lines: true}
```vue
<script setup lang="ts">
  import { ref, watch } from "vue"

  const text = ref("")
  const emit = defineEmits(['change'])

  watch(text, (newText) => emit('change', newText))
</script>

<template>
  <text-field v-model="text" />
</template>
```

```vue
<script setup lang="ts">
  import { ref, watch } from "vue"

  const text = ref("")
  const emit = defineEmits<{
    (e: 'change', text: string): void
  }>()

  watch(text, (newText) => emit('change', newText))
</script>

<template>
  <text-field v-model="text" />
</template>
```

```vue
<script setup lang="ts">
  import { ref, watch } from "vue"

  const text = ref("")
  const emit = defineEmits<{
    change: [text: string]
  }>()

  watch(text, (newText) => emit('change', newText))
</script>

<template>
  <text-field v-model="text" />
</template>
```
````

---
transition: fade-out
---

# Typescript (Template Refs )

```vue twoslash
<script setup lang="ts">
  import { useTemplateRef } from "vue"

  const form = useTemplateRef('form')
  
  function resetForm() {
    form.value?.reset()
  }
</script>

<template>
  <form ref="form">
    <text-field />
  </form>
</template>
```
