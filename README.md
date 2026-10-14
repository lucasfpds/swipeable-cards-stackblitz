# Swipeable Cards estilo Tinder

> Cards empilhados que podem ser arrastados para esquerda/direita com spring physics e feedback tátil.

## Tecnologias

- **Vue 3** com `<script setup>` (JavaScript)
- **Vite** como bundler
- **CSS puro** — sem pré-processador, sem framework
- **Pointer Events** nativos para arrasto (mouse + touch)
- **Spring physics** com `requestAnimationFrame` (motor próprio, sem libs)
- **Web Vibration API** para feedback tátil

## Requisitos

- Node.js 18+

## Como executar

```bash
npm install
npm run dev
```

Abra http://localhost:5173 no navegador.

## Scripts

| Script | Descrição |
| --- | --- |
| `npm run dev` | Servidor de desenvolvimento (Vite) |
| `npm run build` | Build de produção |
| `npm run preview` | Serve o build de produção |

## Estrutura

```
src/
  main.js              entry: cria o app e importa o CSS
  App.vue              pilha de cards, arrasto, spring e descarte
  styles.css           layout, cards, overlays LIKE/NOPE
```

### Funcionalidades

- 8 perfis fictícios com fotos via `picsum.photos` (por seed)
- Renderização dos 3 cards do topo com profundidade visual (scale + translate)
- Arrasto com pointer events (mouse e touch), rotação proporcional ao deslocamento
- Overlays LIKE/NOPE com opacidade proporcional ao deslocamento
- Spring physics no retorno ao centro (k = 0.08, c = 0.12)
- Animação de descarte (translateX ±140% + rotate ±30deg) ao cruzar o threshold
- Feedback tátil via `navigator.vibrate(15)` ao cruzar o limite
- Botão "recomeçar" ao esvaziar a pilha


## Rodando no StackBlitz

Basta importar o repositório e rodar normalmente:

```
https://stackblitz.com/github/lucasfpds/swipeable-cards-stackblitz
```

O projeto não depende de binários nativos — Vite é JavaScript puro, então
sobe sem bindings nativos no WebContainer do StackBlitz.
