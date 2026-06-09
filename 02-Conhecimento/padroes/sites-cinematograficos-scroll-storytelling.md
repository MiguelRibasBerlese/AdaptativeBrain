---
type: conhecimento
status: ativo
tags: [webdev, animação, scroll, gsap, threejs, webgl, performance, ux]
created: 2026-06-09
updated: 2026-06-09
---

# Sites Cinematográficos e Scroll Storytelling — Guia Técnico 2026

Pesquisa completa baseada em fontes técnicas de 2024-2026, documentação oficial, repositórios GitHub e cases reais de produção. Originalmente pesquisado no Perplexity.

---

## 1. TÉCNICA E TECNOLOGIA

### Como funciona Scroll-driven Animation

Scroll-driven animation transforma o scroll em uma timeline controlável — o usuário "dirige" a animação pela posição de scroll.

```js
// GSAP ScrollTrigger - exemplo prático
const tl = gsap.timeline({
  scrollTrigger: {
    trigger: ".section",
    start: "top 90%",
    end: "bottom 30%",
    scrub: 1,     // suavização de 1 segundo
    pin: true,    // fixa a seção durante animação
  }
});
tl.to(".orb", { x: 500, scale: 2, rotation: 360 });
```

Diferença crucial:
- **Scroll tradicional**: Scroll → trigger → animação roda sozinha
- **Scroll-driven**: Scroll → controla timeline → usuário controla velocidade

---

### Comparativo: CSS Nativo vs GSAP ScrollTrigger vs Three.js

| Critério | CSS Nativo | GSAP ScrollTrigger | Three.js |
|---|---|---|---|
| Bundle size | 0 KB | ~48 KB gzipped | ~64 KB + deps |
| Main thread | Zero (compositor) | Moderado | Alto (WebGL) |
| INP impact | Zero | Alto se mal usado | Alto |
| Pinning | Básico | Avançado | Manual |
| Browser support | ~90% (Chrome 115+, Safari 18+) | Universal | Universal |
| Curva aprendizado | Baixa | Média | Alta |

**Regra prática:**
1. Animação simples (hover, fade)? → CSS puro
2. Sequência complexa multi-elementos? → GSAP
3. Scroll controla progresso? → GSAP + ScrollTrigger
4. 3D/WebGL? → Three.js (geralmente com GSAP para scroll)

---

### Stack do Lando Norris (landonorris.com)

| Componente | Tecnologia |
|---|---|
| Plataforma | Webflow + Motion.page |
| Animações scroll | GSAP + ScrollTrigger |
| Animação 3D/Helmet | Rive.app |
| WebGL | Custom WebGL + Three.js |
| Otimização | Lazy-loading + asset optimization |

**Stack típica "cinematográfica" production-ready:**

```js
import Lenis from 'lenis'
import gsap from 'gsap'
import ScrollTrigger from 'gsap/ScrollTrigger'

const lenis = new Lenis({
  lerp: 0.08,         // suavização (menor = mais fluido)
  smoothTouch: false, // nativo no mobile
  duration: 1.2
})

// Critical: conectar Lenis ao ScrollTrigger
lenis.on('scroll', ScrollTrigger.update)
gsap.ticker.add((time) => {
  lenis.raf(time * 1000)
})
gsap.ticker.lagSmoothing(0)
```

---

### WebGL vs CSS Puro vs Canvas

| Tecnologia | Quando usar | Performance |
|---|---|---|
| CSS puro | Hover effects, fade-ins simples | Excelente (GPU) |
| Canvas 2D | Gráficos 2D, até ~500 objetos | Boa |
| WebGL | 3D, >500 objetos, shaders, texturas pesadas | Excelente (GPU paralelo) |

Dados reais:
- Canvas 2D inicial: ~15ms vs WebGL: ~40ms (WebGL mais lento para carregar)
- Canvas 2D por frame: até 1.2ms vs WebGL: ~0.01ms (WebGL 120x mais rápido por render)

Regra: `< 500 elementos → HTML+CSS → Canvas 2D → WebGL/Three.js`

---

### Bibliotecas 2025/2026

**Lenis vs Locomotive Scroll:**

| Característica | Lenis | Locomotive Scroll |
|---|---|---|
| Peso | Mais leve | Mais pesado |
| Manutenção | Ativo (Studio Freight) | v2 menos ativo |
| Mobile | smoothTouch: false (nativo) | Pode desabilitar |
| Recomendação 2025 | ✅ Preferido | Usar se precisar parallax pronto |

**GSAP vs Framer Motion vs Motion One:**

| Biblioteca | Caso ideal |
|---|---|
| GSAP | Complexo, multi-step, timeline control, scroll-dependent |
| Framer Motion | React apps, prototipagem rápida, UI animations |
| Motion One | Animations simples, bundle mínimo (7KB) |
| CSS puro | Simple state transitions (hover, basic fade) |

---

### CSS Scroll-driven Animations sem JavaScript (Chrome 115+)

```css
/* Progress bar — ZERO JS */
.progress-bar {
  position: fixed;
  top: 0; left: 0;
  height: 4px;
  background: #2563eb;
  transform-origin: left;
  animation: grow-progress linear both;
  animation-timeline: scroll();
}

@keyframes grow-progress {
  from { transform: scaleX(0); }
  to { transform: scaleX(1); }
}

/* Fade-in on scroll */
.reveal {
  animation: fade-in-up linear both;
  animation-timeline: view();
  animation-range: entry 0% entry 100%;
}

@keyframes fade-in-up {
  from { opacity: 0; transform: translateY(40px); }
  to { opacity: 1; transform: translateY(0); }
}
```

**Propriedades compositor-friendly (off-main-thread):**
- ✅ `transform`, `opacity`, `filter`, `backdrop-filter`, `clip-path`
- ❌ `width`, `height`, `top`, `left`, `margin`, `background-color`

> Aviso crítico: Se qualquer keyframe tocar propriedade main-thread, TODA a animação vai para main thread.

---

## 2. THREE.JS ESPECIFICAMENTE

### Câmera seguindo Path (CatmullRomCurve3) com Scroll

```js
import { CatmullRomCurve3 } from 'three'

const curve = new CatmullRomCurve3([
  new THREE.Vector3(-10, 0, 0),
  new THREE.Vector3(0, 10, 0),
  new THREE.Vector3(10, 0, 0),
  new THREE.Vector3(0, -10, 0)
])

function updateCamera(scrollProgress) {
  const point = curve.getPoint(scrollProgress)
  camera.position.copy(point)
  camera.lookAt(curve.getPoint(scrollProgress + 0.01))
}

window.addEventListener('scroll', () => {
  const progress = window.scrollY / (document.scrollHeight - window.innerHeight)
  updateCamera(progress)
})
```

### R3F vs Three.js Vanilla

| Critério | Three.js Vanilla | React Three Fiber (R3F) |
|---|---|---|
| Controle | Full granular, melhor fine-tuning | Overhead do React, mas eficiente |
| Quando usar | Non-React, aprendizado WebGL, games, shaders avançados | React apps existentes, rapid prototyping |
| UI + 3D | Manual (CSS3DRenderer) | Suave (React components) |
| Ecosystem | Tradicional | Rich (Drei, Tubular, etc.) |

**Regra:** Vanilla se precisar controle preciso OU não é React. R3F se já é dev React.

### Misturar HTML/CSS com Three.js (projeção manual)

```js
// Converter 3D world position para 2D screen position
const vector = new THREE.Vector3()
vector.setFromMatrixPosition(mesh.matrixWorld)
vector.project(camera)

const x = (vector.x * .5 + .5) * window.innerWidth
const y = (-(vector.y * .5) + .5) * window.innerHeight

htmlElement.style.transform = `translate(${x}px, ${y}px)`
```

---

## 3. PERFORMANCE

### Como manter 60fps

```js
// 1. Apenas propriedades compositor-friendly
.animated {
  will-change: transform, opacity;  /* ✅ */
  /* will-change: width, top — ❌ */
}

// 2. Lenis + GSAP integração correta
lenis.on('scroll', ScrollTrigger.update)
gsap.ticker.add((time) => { lenis.raf(time * 1000) })
gsap.ticker.lagSmoothing(0)
```

### Core Web Vitals 2026

| Métrica | Good | Needs Improvement | Poor |
|---|---|---|---|
| LCP | ≤ 2.5s | 2.5-4s | > 4s |
| INP | ≤ 200ms | 200-500ms | > 500ms |
| CLS | ≤ 0.1 | 0.1-0.25 | > 0.25 |

**Impacto real:**
- CSS scroll-driven (nativo): Zero impacto no INP
- GSAP ScrollTrigger: Pode degradar INP em páginas complexas
- JavaScript scroll listeners: 50-100ms de delay → INP piorado

**Chrome case study:** CSS manteve 60fps com main thread artificialmente bloqueado. JavaScript equivalent caiu para single-digit fps.

### Scroll-jacking e Acessibilidade

John Mueller (Google): não é intrinsicamente abusivo para SEO, mas é sinal UX on-page.

**Quando prejudica:**
- User perde controle intuitivo de navegação
- Mobile (touch scrolling)
- Usuários com vestibular disorders

```css
@media (prefers-reduced-motion: reduce) {
  .animated {
    animation: none;
    opacity: 1;
    transform: none;
  }
}
```

---

## 4. RIVE vs LOTTIE

| Métrica | Rive | Lottie |
|---|---|---|
| Formato | Binary (.riv) | JSON |
| File Size | 16KB exemplo | 240KB equivalente |
| Decodificação | Binary (rápido) | JSON parse (CPU-heavy) |
| GPU-accelerated | Sim (Metal, Vulkan) | Core Animation |
| State Machines | Nativo | Não (precisa JS manual) |
| RAM iOS | 25 MB | 49 MB |

**Duolingo:** 15x redução file size adotando Rive.

### Integrar Rive com Three.js

```js
// CanvasTexture (Rive dentro de cena 3D)
const canvasEl = document.getElementById("rive-canvas")
const texture = new THREE.CanvasTexture(canvasEl)

useFrame(() => {
  if (textureRef.current) {
    textureRef.current.needsUpdate = true  // CRÍTICO
  }
})
```

---

## 5. WEBGL — INSTANCED RENDERING

```js
// ✅ CORRETO: 1 draw call para 300 objetos
const instancedMesh = new THREE.InstancedMesh(geometry, material, 300)
const dummy = new THREE.Object3D()

for (let i = 0; i < 300; i++) {
  dummy.position.set(x[i], y[i], z[i])
  dummy.updateMatrix()
  instancedMesh.setMatrixAt(i, dummy.matrix)
}
scene.add(instancedMesh)
```

Real estate demo: 9.000 → 300 draw calls com instanced rendering.

---

## 6. GSAP — GERENCIAMENTO DE MEMÓRIA

```js
// Cleanup para evitar memory leak
let currentTween = null

function startAnimation() {
  if (currentTween) {
    currentTween.kill()
    currentTween = null
  }
  currentTween = gsap.to(element, { x: 100, duration: 2, repeat: -1 })
}

useEffect(() => {
  return () => { if (currentTween) currentTween.kill() }
}, [])
```

---

## 7. MERCADO E APLICAÇÃO COMERCIAL

### Custos estimados

| Nível | Brasil | Internacional | Stack típica |
|---|---|---|---|
| Básico (GSAP + CSS) | R$ 15k–40k | $5k–$15k | GSAP + Lenis |
| Intermediário (3D leve) | R$ 40k–80k | $15k–$40k | Three.js + GSAP |
| Premium (Full cinematic) | R$ 80k–250k+ | $40k–$150k+ | Three.js + WebGL + Custom shaders |

### Quando faz sentido

✅ Brand websites, product launches, portfolio de agência, campaign sites temporários, experiências imersivas.

❌ E-commerce funcional, blogs, SaaS dashboards, startups early-stage.

### Tendências 2025-2026

1. CSS scroll-driven nativo substituindo JS para efeitos simples
2. Lenis + GSAP como padrão para smooth scroll
3. 3D leve (WebGL otimizado, não heavy scenes)
4. Performance-first (Core Web Vitals obrigatórios)
5. Accessibility-by-default (prefers-reduced-motion)

---

## 8. CHECKLIST PRODUCTION-READY

```bash
npm install gsap@latest lenis@latest
# Se precisar 3D/interativo:
npm install @rive-app/webgl
```

### Performance checklist

- [ ] Usar apenas `transform`, `opacity`, `filter` nas animações
- [ ] `will-change` apenas em elementos animados, remover depois
- [ ] `prefers-reduced-motion` implementado
- [ ] Testar em mobile real
- [ ] WebGL/Rive lazy load com IntersectionObserver
- [ ] Web Workers ou `requestIdleCallback` para init pesado
- [ ] Máximo 3-5 animações simultâneas no mobile
- [ ] `{ passive: true }` em scroll listeners
- [ ] `content-visibility: auto` para off-screen sections
- [ ] Preload LCP element no `<head>`
- [ ] Reserve space para imagens (width/height ou aspect-ratio)

---

## 9. RECURSOS E REFERÊNCIAS

| Recurso | Link |
|---|---|
| GSAP Scroll Documentation | https://gsap.com/scroll/ |
| CSS Scroll-driven Guide | https://webperfclinic.com/article/css-scroll-driven-animations-performance-guide |
| Cinematic Tutorial (DEV.to) | https://dev.to/jkimdd/how-i-built-a-cinematic-scroll-experience-with-gsap-and-scrolltrigger-11hj |
| Awwwards Storytelling | https://www.awwwards.com/websites/storytelling/ |
| Three.js + GSAP GitHub | https://github.com/AkbarBakhshi/threejs-gsap-scroll-animation |
| R3F Tutorial GitHub | https://github.com/lilsugsy/R3F-ScrollControls-Tutorial-With-Gsap |
| Câmera path GitHub | https://github.com/MauryaAnurag/camera-move-path-threejs |
| scrollytelling lib | https://github.com/basementstudio/scrollytelling |
| Codrops Cinematic 3D | https://tympanus.net/codrops/2025/11/19/how-to-build-cinematic-3d-scroll-experiences-with-gsap/ |
| Rive + Three.js tutorial | https://whoisryosuke.com/blog/2025/mixing-rive-and-threejs |
| Lando Norris case study | https://www.itsoffbrand.com/our-work/lando-norris |
| GSAP vs Motion | https://motion.dev/docs/gsap-vs-motion |

### Canais YouTube

- GSAP Official — tutorials de ScrollTrigger
- Three.js Journey — Three.js do básico ao avançado
- Bruno Simon — 3D web development (Three.js + R3F)

### Comunidades

| Comunidade | Foco |
|---|---|
| r/webgl | WebGL performance, comparações |
| r/threejs | Three.js, R3F vs vanilla |
| Codrops | Artigos técnicos avançados |
| Awwwards | Inspiração + técnicos de sites premiados |

### Newsletters e Blogs

- Codrops (tympanus.net)
- Web Perf Clinic — performance-focused
- GreenSock Blog — GSAP updates + cases
- Studio Freight — creators do Lenis
