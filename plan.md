# Swipeable Cards estilo Tinder

> Cards empilhados que podem ser arrastados para esquerda/direita com spring physics e feedback tátil.

## Stack

- Vite + Vue 3 (`<script setup>`, JavaScript) — sem TypeScript, sem lint/test, zero libs extras
- CSS puro em `src/styles.css` (importado em `main.js`)
- Arquivos: `index.html`, `package.json` (deps: `vue`), `vite.config.js`, `.stackblitzrc`, `src/main.js`, `src/App.vue`, `src/styles.css`
- `.stackblitzrc`: `{ "startCommand": "npm run dev" }` (mesmo padrão de `animated-graphics-with-d3-stackblitz`)
- App.vue único — cada card é um item do `v-for`, sem componentes extras

## Implementação

### 1. Dados e pilha de cards

- Array de ~8 perfis: `{ nome, idade, foto, bio }` (fotos via `https://picsum.photos/seed/<seed>/400/500`)
- Renderizar apenas os 3 primeiros do array: o do topo interativo; os 2 de trás com `scale(0.95 / 0.9)` + deslocamento vertical (profundidade) e `pointer-events: none`
- Cards em `position: absolute` dentro de um container com proporção fixa; `z-index` decrescente

### 2. Arrasto (pointer events)

- `pointerdown` no card do topo → `setPointerCapture` + `touch-action: none` no card
- `pointermove` atualiza `x`, `y` e rotação `rotate(x * 0.05deg)`, aplicados via `transform` **sem transition** durante o gesto
- Overlays `LIKE` / `NOPE` nos cantos, com opacidade proporcional a `|x| / threshold` (threshold ≈ 100px)
- Feedback tátil: `navigator.vibrate(15)` ao cruzar o threshold pela primeira vez no gesto (no-op silencioso em navegadores sem suporte — iOS)

### 3. Ao soltar: spring ou descarte

- `|x| < threshold`: mola de volta ao centro via `requestAnimationFrame` —
  `v += (-k * x - c * v) * dt; x += v * dt` (k ≈ 0.08, c ≈ 0.12), para quando `|x| < 0.5 && |v| < 0.5`
- `|x| >= threshold`: animação de saída (`translateX(±140%) rotate(±30deg)`, transition 300ms ease-out), remove o card do topo e reseta o estado do próximo
- Pilha vazia: mensagem de fim + botão "recomeçar" (repõe o array)

## Checklist — 100% da descrição

- [ ] Cards empilhados (com profundidade visual)
- [ ] Arrastar para esquerda/direita (mouse e touch)
- [ ] Spring physics no retorno ao centro
- [ ] Feedback tátil ao cruzar o limite
