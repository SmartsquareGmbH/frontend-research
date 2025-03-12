---
transition: fade-out
layout: 
---

# Nuxt 3

- kompletter Rewrite
- basiert auf vielen eigenen Implementierungen
    - `nitro`: Server engine, überall deploybar, rendert Seiten, cached usw.
    - `h3`: HTTP-Framework, kompatibel mit jeder Runtime
    - `ofetch`: API-Wrapper, isometrisch fetchen
- Nuxi (neue CLI) und Nuxt Dev Tools
- überall einsetzbar, Rendering dynamisch (Client-side, Server-side, Hydration)

---
transition: fade-out
---

# Nuxi CLI

- viele komfortable Befehle wie das Generieren von Strukturen

<img src="../assets/nuxi1.png" width="70%" alt="Nuxi CLI" class="px-6" />
<img v-click src="../assets/nuxi2.png" width="70%" alt="Nuxi CLI" class="px-6" />

---
transition: fade-out
---

# Nuxt Dev Tools

- ersetzt Vue Dev Tools
- eigenes Package statt Browser-Extension
- Nuxt Modules und co. direkt in Dev Tools integriert

<img src="../assets/nuxtDevTools1.png" width="70%" alt="Nuxt Dev Tools" class="px-6" />
<img v-click src="../assets/nuxtDevTools2.png" width="60%" alt="Nuxt Dev Tools" class="absolute bottom-0 right-40 px-6" />

---
transition: fade-out
---

# Routing

- optimiertes Routing mit `<NuxtLink>` (statt `<a>`) und `navigateTo()` (in JS)
- Rendering, Caching und Co. konfigurierbar

```ts
export default defineNuxtConfig({
  routeRules: {
    // Homepage pre-rendered at build time
    '/': { prerender: true },
    // Products page generated on demand, revalidates in background, cached until API response changes
    '/products': { swr: true },
    // Product pages generated on demand, revalidates in background, cached for 1 hour (3600 seconds)
    '/products/**': { swr: 3600 },
    // Blog posts page generated on demand, revalidates in background, cached on CDN for 1 hour (3600 seconds)
    '/blog': { isr: 3600 },
    // Blog post page generated on demand once until next deployment, cached on CDN
    '/blog/**': { isr: true },
    // Admin dashboard renders only on client-side
    '/admin/**': { ssr: false },
    // Add cors headers on API routes
    '/api/**': { cors: true },
    // Redirects legacy urls
    '/old-page': { redirect: '/new-page' }
  }
})
```

<arrow v-click x1="450" y1="450" x2="330" y2="445" color="red" width="2" arrowSize="1" />

---
transition: fade-out
---

# Exception Handling

- `error.vue`-Page bei fatalem Error

```vue
<script setup lang="ts">
import type { NuxtError } from '#app'

const props = defineProps({
  error: Object as () => NuxtError
})

const handleError = () => clearError({ redirect: '/' })
</script>

<template>
  <div>
    <h2>{{ error.statusCode }}</h2>
    <button @click="handleError">Clear errors</button>
  </div>
</template>
```

---
transition: fade-out
---

# Exception Handling

- Error selbst werfen mit `createError()`

```vue
<script setup lang="ts">
const route = useRoute()
const { data } = await useFetch(`...`)

if (!data.value) {
  throw createError({
    statusCode: 404,
    statusMessage: 'Page Not Found'
  })
}
</script>
```

---
transition: fade-out
---

# Exception Handling

- `<NuxtErrorBoundary>` zum Catchen von Errors im UI

```vue
<template>
  <NuxtErrorBoundary @error="someErrorLogger">
    <!-- Put normal content here for no error case -->
    <template #error="{ error, clearError }">
      Oh no, an error occurred: {{ error }}
      <button @click="clearError">
        Ignore and continue
      </button>
    </template>
  </NuxtErrorBoundary>
</template>
```

---
transition: fade-out
---

# Exception Handling

- Vues `onErrorCaptured()`-Composable zum programmatischen Catchen von Errors

```vue
<script setup>
onErrorCaptured((error, instance, info) => {
  // Global error logging that catches everything
  console.error(`Global error: ${error.message}`)
  console.log(`Error occurred in component:`, instance)
  console.log(`Error info:`, info)

  // Let the error continue to propagate to UI boundaries
  return true
})
</script>
```
---
transition: fade-out
---

# Exception Handling

Wann was verwenden?
- `<NuxtErrorBoundary>`: UI-spezifisches Error-Handling
- `onErrorCaptured()`: globales oder komplexes Error-Handling, UI-unabhängig, kann Propagation stoppen
- `error.vue`-Page, wenn fataler Error auftritt
  - Server-Error oder `createError("...", { fatal: true })`
