<script setup>
import { ref, computed, onBeforeUnmount } from "vue";

const PROFILES = [
  { nome: "Ana", idade: 26, foto: "https://picsum.photos/seed/ana/400/500", bio: "Designer, adora trilhas e café especial." },
  { nome: "Bruno", idade: 29, foto: "https://picsum.photos/seed/bruno/400/500", bio: "Fotógrafo e músico nas horas vagas." },
  { nome: "Carla", idade: 24, foto: "https://picsum.photos/seed/carla/400/500", bio: "Veterinária apaixonada por gatos." },
  { nome: "Diego", idade: 31, foto: "https://picsum.photos/seed/diego/400/500", bio: "Chef de cozinha, cozinha para poucos." },
  { nome: "Elisa", idade: 27, foto: "https://picsum.photos/seed/elisa/400/500", bio: "Cientista de dados, fã de sci-fi." },
  { nome: "Fábio", idade: 28, foto: "https://picsum.photos/seed/fabio/400/500", bio: "Personal trainer e ciclista amador." },
  { nome: "Gabi", idade: 25, foto: "https://picsum.photos/seed/gabi/400/500", bio: "Jornalista, viaja sempre que pode." },
  { nome: "Heitor", idade: 30, foto: "https://picsum.photos/seed/heitor/400/500", bio: "Arquiteto, coleciona vinis." },
];

const THRESHOLD = 100;
const deck = ref([...PROFILES]);
const x = ref(0);
const y = ref(0);
const rot = ref(0);
const dragging = ref(false);
const crossedThreshold = ref(false);
let pointerId = null;
let startX = 0;
let startY = 0;
let rafId = null;

const top = computed(() => deck.value[0] || null);
const next1 = computed(() => deck.value[1] || null);
const next2 = computed(() => deck.value[2] || null);

const likeOpacity = computed(() => Math.min(1, Math.max(0, x.value / THRESHOLD)));
const nopeOpacity = computed(() => Math.min(1, Math.max(0, -x.value / THRESHOLD)));

function onPointerDown(e) {
  if (!top.value || dragging.value) return;
  dragging.value = true;
  pointerId = e.pointerId;
  startX = e.clientX;
  startY = e.clientY;
  crossedThreshold.value = false;
  e.target.setPointerCapture(e.pointerId);
}

function onPointerMove(e) {
  if (!dragging.value || e.pointerId !== pointerId) return;
  x.value = e.clientX - startX;
  y.value = (e.clientY - startY) * 0.4;
  rot.value = x.value * 0.05;
  const over = Math.abs(x.value) >= THRESHOLD;
  if (over && !crossedThreshold.value) {
    crossedThreshold.value = true;
    if (navigator.vibrate) navigator.vibrate(15);
  } else if (!over) {
    crossedThreshold.value = false;
  }
}

function onPointerUp(e) {
  if (!dragging.value || e.pointerId !== pointerId) return;
  dragging.value = false;
  if (Math.abs(x.value) >= THRESHOLD) {
    discard(x.value > 0 ? "like" : "nope");
  } else {
    springBack();
  }
}

function discard(dir) {
  const el = document.querySelector(".card--top");
  if (el) {
    el.style.transition = "transform 0.3s ease-out, opacity 0.3s ease-out";
    el.style.transform = `translateX(${dir === "like" ? 140 : -140}%) rotate(${dir === "like" ? 30 : -30}deg)`;
    el.style.opacity = "0";
  }
  setTimeout(() => {
    deck.value.shift();
    x.value = 0;
    y.value = 0;
    rot.value = 0;
  }, 300);
}

function springBack() {
  let vx = 0;
  const k = 0.08;
  const c = 0.12;
  const step = () => {
    vx += (-k * x.value - c * vx) * 1;
    x.value += vx;
    y.value += (-k * y.value - c * vx) * 1;
    rot.value = x.value * 0.05;
    if (Math.abs(x.value) < 0.5 && Math.abs(vx) < 0.5) {
      x.value = 0;
      y.value = 0;
      rot.value = 0;
      return;
    }
    rafId = requestAnimationFrame(step);
  };
  rafId = requestAnimationFrame(step);
}

function restart() {
  deck.value = [...PROFILES];
  x.value = 0;
  y.value = 0;
  rot.value = 0;
}

onBeforeUnmount(() => {
  if (rafId) cancelAnimationFrame(rafId);
});
</script>

<template>
  <main class="app">
    <h1>Swipeable Cards</h1>
    <div class="deck">
      <div
        v-if="next2"
        class="card card--back2"
      >
        <img :src="next2.foto" :alt="next2.nome" />
      </div>
      <div
        v-if="next1"
        class="card card--back1"
      >
        <img :src="next1.foto" :alt="next1.nome" />
      </div>
      <div
        v-if="top"
        class="card card--top"
        :style="dragging ? { transform: `translate(${x}px, ${y}px) rotate(${rot}deg)` } : null"
        @pointerdown="onPointerDown"
        @pointermove="onPointerMove"
        @pointerup="onPointerUp"
        @pointercancel="onPointerUp"
      >
        <img :src="top.foto" :alt="top.nome" draggable="false" />
        <div class="card__overlay like" :style="{ opacity: likeOpacity }">LIKE</div>
        <div class="card__overlay nope" :style="{ opacity: nopeOpacity }">NOPE</div>
        <div class="card__info">
          <h2>{{ top.nome }}, {{ top.idade }}</h2>
          <p>{{ top.bio }}</p>
        </div>
      </div>
      <div v-else class="deck__empty">
        <p>Fim dos cards</p>
        <button @click="restart">Recomeçar</button>
      </div>
    </div>
  </main>
</template>
