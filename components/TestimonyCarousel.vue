<template>
  <div class="flex flex-col items-center justify-center mt-6">
    <Transition name="testimony" mode="out-in">
      <div :key="current" class="border rounded-xl p-6 flex flex-col gap-4 w-full max-w-xl shadow-sm">
        <span class="text-base italic text-gray-700 leading-relaxed">"{{ testimonies[current].quote }}"</span>
        <div class="flex items-center gap-3 mt-2">
          <img :src="testimonies[current].img" class="w-10 h-10 rounded-full object-cover shrink-0" />
          <div>
            <div class="font-semibold text-sm">{{ testimonies[current].name }}</div>
            <div class="text-xs text-gray-500">{{ testimonies[current].role }}</div>
          </div>
        </div>
      </div>
    </Transition>

    <div class="flex gap-2 mt-6">
      <button
        v-for="(_, i) in testimonies"
        :key="i"
        class="w-2 h-2 rounded-full transition-colors duration-300"
        :class="i === current ? 'bg-gray-700' : 'bg-gray-300'"
        @click="goTo(i)"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const testimonies = [
  {
    name: 'Solène Oruezabal',
    role: 'Équipe produit',
    img: '/solene.png',
    quote: "Depuis le changement, c'est clairement la charge mentale qui a changé. Je sais que c'est automatique — plus besoin de relancer 25 fois les équipes dans la journée.",
  },
  {
    name: 'Rémi Poulenard',
    role: 'Developer · Wealthcome',
    img: '/remi.jpeg',
    quote: "Les déploiements sont désormais simplifiés, génèrent moins de conflits et mobilisent moins de temps. Résultat : une équipe plus confiante et une vélocité de livraison nettement améliorée.",
  },
  {
    name: 'Martin Pinaud',
    role: 'Équipe produit',
    img: '/martin.jpeg',
    quote: "La chose qui a changé depuis qu'on a le nouveau process, c'est l'organisation. On teste plus facilement, on a une meilleure visualisation de tout ce qui passe en prod/preprod. Pour faire des retours à la direction sur les nouveautés des mises en prod, c'est beaucoup plus simple.",
  },
  {
    name: 'Jonathan Lacoste',
    role: 'CPO · Wealthcome',
    img: '/jonathan.jpeg',
    quote: "Clairement le changelog clair des MEP et la rapidité d'exécution. La régularité des preprod. Le staging pour environnement de test des nouvelles features. On maîtrise ce qui se passe !",
  },
]

const current = ref(0)
let timer

const intervalMs = 8000

function goTo(i) {
  current.value = i
  clearInterval(timer)
  timer = setInterval(next, intervalMs)
}

function next() {
  current.value = (current.value + 1) % testimonies.length
}

onMounted(() => { timer = setInterval(next, intervalMs) })
onUnmounted(() => clearInterval(timer))
</script>

<style scoped>
.testimony-enter-active,
.testimony-leave-active {
  transition: opacity 0.5s, transform 0.5s;
}
.testimony-enter-from {
  opacity: 0;
  transform: translateY(12px);
}
.testimony-leave-to {
  opacity: 0;
  transform: translateY(-12px);
}
</style>
