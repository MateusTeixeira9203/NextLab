# Nexlab Landing Page — Redesign Completo
**Data:** 2026-06-08  
**Abordagem:** Revolução Visual Completa (Abordagem A)  
**Estética alvo:** Digital Art / Agency Tech + Dark Cinematic  
**Arquivo alvo:** `nexlab.html` (single-file, HTML + CSS + JS)

---

## 1. Sistema Visual Global

### Grain & Textura Cinematográfica
- Camada SVG (`feTurbulence` + `feColorMatrix`) cobre 100% da página
- Opacidade: ~4%, `pointer-events: none`, `position: fixed`, `z-index: 9999`
- Resultado: textura de "filme analógico" que eleva tudo por baixo

### Gradientes Ampliados
- Aurora blobs: 4 blobs (era 3), escala 2-3x maior, movimento mais lento
- 4º blob: tom âmbar/coral (#ff9f5b) cria tensão cromática com o ciano existente
- Animações `float` com durações mais longas (25s–40s) e trajetórias distintas

### Paleta
Mantém a paleta atual com mais riqueza:
- Fundo: `#07070f` (sem alteração)
- Gradiente principal: `#5b8cff → #9a6bff → #ff6bc1` (sem alteração)
- Accent cyan: `#43e7e0` (sem alteração)
- Novo accent âmbar: `#ff9f5b` (apenas nos blobs e detalhes)
- Noise/textura: camada overlay em cima de tudo

### Tipografia Dramática
- H1 hero: `clamp(4rem, 9vw, 8.5rem)`, Syne 800, `letter-spacing: -0.04em`
- Títulos de seção: layout editorial com número decorativo (01–04) em DM Mono `~12rem`, `opacity: 0.05`, posicionado atrás
- Body copy: DM Sans, sem alteração estrutural

### Cursor Magnético Customizado
- Substitui cursor padrão: ponto pequeno (6px) + anel externo (24px)
- Seguimento com `lerp` suave (fator 0.12 por frame)
- Hover em links/botões: anel expande para 48px + mistura com cor do elemento
- Muda de cor sutilmente por seção via `data-cursor-color` nos wrappers
- `@media (pointer: coarse)` desativa em touch devices

### Scroll Cinematográfico
- Hero: parallax leve no canvas (`translateY` proporcional ao scroll)
- Capabilities: cards deslizam da lateral (alternando esquerda/direita)
- Mockups: surgem com `scale(0.95) + opacity 0` → `scale(1) + opacity 1`
- Process: cards revelam com `clipPath` wipe vertical
- Todos via `IntersectionObserver` com `threshold: 0.15`

---

## 2. Navegação (sem alterações estruturais)
- Scroll state: mantém glassmorphism atual
- Apenas refinamento visual: borda mais fina, backdrop-blur levemente maior

---

## 3. Hero Section

### Layout
- Texto alinhado à esquerda, ~55% da largura em desktop
- Canvas de partículas ocupa toda a área de fundo (não apenas metade)

### Tipografia
```
[badge]  Nexlab · Estúdio de Web Design
Transformamos        ← linha 1, clip reveal de baixo para cima
negócios em          ← linha 2, leve deslocamento horizontal +8px
experiências.        ← linha 3, gradient inline azul→magenta
```
- Animação de entrada: `clip-path: inset(100% 0 0 0)` → `inset(0% 0 0 0)` por linha
- Stagger: 0ms, 160ms, 320ms entre linhas

### Subheading
- DM Sans, linha decorativa vertical à esquerda (3px, gradient)
- Copy: *"Sites que não existem pra existir — existem pra vender, impressionar e crescer junto com o seu negócio."*

### Canvas de Partículas (refatorado)
- Partículas maiores: raio 2–5px (era 1–3px)
- Linhas de conexão: `lineWidth` 0.8px com glow `shadowBlur: 8`
- Força magnética do mouse: afasta partículas próximas com decaimento suave
- Glow do cursor: raio 120px (era 80px), mais luminoso

### CTAs
- Primário: `liquid border` — `conic-gradient` girando no contorno em repouso, interior acende no hover
- Secundário: apenas texto + seta animada (`→` desloca +6px no hover)

### Scroll Hint
- Linha vertical que "pinga" com keyframe: cresce de cima para baixo, some, repete
- Substitui o indicador de bolinha atual

---

## 4. Capabilities Section

### Título Editorial
```
O QUE           ← DM Mono, tracking: 0.3em, muted, ~0.85rem
FAZEMOS         ← Syne 800, oversized, clip reveal letra a letra (stagger 30ms)
[número "01" decorativo atrás, ~12rem, opacity 5%]
```

### Grid
- Desktop: 4 colunas com **stagger de altura** — cards 1 e 3 em `margin-top: 0`, cards 2 e 4 em `margin-top: 2rem`
- Tablet: 2x2
- Mobile: 1 coluna

### Cards
- Número decorativo `01–04` no canto superior direito: DM Mono, ~6rem, `opacity: 0.06`
- Fundo com `background-image: url("data:image/svg+xml,...")` noise sutil
- Hover: borda gradiente animada (conic-gradient, 2s) + ícone glow + `translateY(-6px)`
- Entrada: stagger 120ms, `translateY(40px) opacity 0` → posição final

---

## 5. Portfolio Section

### Título Editorial
```
TRABALHOS       ← Syne 800 com número "02" decorativo atrás
por segmento    ← DM Sans italic, muted
```

### Layout Assimétrico
```
[  Card 1 — Clínica — largura total, altura ~520px  ]
[ Card 2 — Barbearia ]  [ Card 3 — Petshop ]
[   Card 4 — Hotel — largura total, altura ~520px   ]
```

### Mockups Elevados
- **Camada de profundidade**: reflexo desfocado abaixo da janela do browser (`::after` com `filter: blur(20px)`, escala e opacidade reduzidas)
- **Hover**:
  - Janela sobe `translateY(-12px)`, sombra expande dramaticamente
  - Etiqueta lateral desliza com segmento + palavras-chave emocionais
  - Fundo do card acende com glow temático (azul para clínica, dourado para barbearia, rosa para petshop, verde para hotel)
- Conteúdo interno dos mockups: tipografia mais expressiva, melhor hierarquia

### Entrada
- Card 1 (largo): entra primeiro, `translateY(60px) → 0`
- Cards 2 e 3: stagger 100ms após card 1
- Card 4 (largo): stagger 200ms após cards 2/3

---

## 6. Process Section

### Título Editorial
```
COMO            ← DM Mono, tracking largo, muted
FUNCIONA        ← Syne 800 com número "03" decorativo atrás
```

### Layout
- Desktop: linha do tempo **horizontal** — 4 cards em sequência
- Tablet/Mobile: coluna vertical (mantém lógica atual)

### Linha Conectora Animada
- Desktop: linha horizontal fina entre os cards
- Preenchimento via `clip-path: inset(0 X% 0 0)` animado com `IntersectionObserver`
- Gradiente azul→magenta, `transition: clip-path 1.2s ease`

### Cards
- Número backdrop: Syne 800, ~10rem, `opacity: 0.08`
- Linha vertical de destaque à esquerda: 3px, acende com gradient ao entrar em view
- Ícone acima do título

### Copy Revisado
```
01 Conversa       → "A gente ouve antes de criar."
02 Design         → "Cada pixel com intenção."
03 No ar          → "Seu site vivo, no Google, no WhatsApp."
04 Suporte        → "A gente não some depois do lançamento."
```

---

## 7. Pricing Section

### Título Editorial
```
PLANOS          ← Syne 800 com número "04" decorativo atrás
e investimento  ← DM Sans italic, muted
```

### Layout
- 3 colunas em desktop, card Profissional ~10% maior física e visualmente
- Mobile: coluna única

### Cards
- **Essencial / Premium**: glass sutil, borda fina `rgba(255,255,255,0.08)`
- **Profissional**:
  - Borda conic-gradient girando (3s, mais espesso: 2px)
  - Fundo com noise texture próprio
  - Glow externo: `box-shadow` com cor do gradiente, 40px spread
  - Preço em Syne 800, ~3.5rem

### Tipografia de Preço
```
R$ 1.800     ← Syne 800, 3.5rem
a 2.200      ← DM Sans, muted, 1.1rem
/projeto     ← DM Mono, tracking largo, 0.8rem
```

### Feature List
- Stagger 60ms por item na entrada
- Ícone check → acende ciano na entrada

### Barra de Recorrência
- Glass próprio, centrada
- Ícone `∞` animado à esquerda
- Copy: *"Hospedagem, manutenção e você pode dormir tranquilo."*

---

## 8. CTA Final

### Background
- Dois orbes de cor (azul `#5b8cff` e magenta `#ff6bc1`) orbitam lentamente em sentidos opostos
- Duração: 20s e 28s, `animation-timing-function: linear`

### Headline
```
Seu negócio merece    ← DM Sans, muted, 1.1rem
um site à altura.     ← Syne 800, oversized, gradient inline
```

### Botão WhatsApp
- Pulse ring duplo que irradia (verde #25d366)
- Copy: *"Vamos conversar →"*
- Seta anima `+4px` no hover

### Micro-copy de confiança
*"Resposta em até 2h · Sem compromisso · 100% personalizado"*  
DM Mono, muted, tracking largo

---

## 9. Footer

- Logo maior, centralizado
- Linha fina gradiente separando do conteúdo
- Três ícones sociais (Instagram, Behance, LinkedIn) com hover colorido
- Copyright: *"Feito com intenção · Nexlab © 2025"* em DM Mono
- Grain levemente mais intenso (~6%)

---

## Ordem de Implementação Recomendada

1. Sistema global (grain, cursor, aurora blobs expandidos, variáveis CSS)
2. Tipografia e títulos editoriais de seção (sistema de números decorativos)
3. Hero (clip reveal, canvas refatorado, CTAs, scroll hint)
4. Capabilities (stagger grid, cards com noise)
5. Portfolio (layout assimétrico, mockups com reflexo)
6. Process (timeline horizontal, linha conectora)
7. Pricing (card featured elevado, feature list stagger)
8. CTA (orbes, pulse ring)
9. Footer (refinamento final)
10. QA: cursor em touch devices, reduced-motion, mobile breakpoints

---

## Constraints Técnicas

- Single file (`nexlab.html`) — HTML + CSS + JS inline
- Sem dependências externas além das já existentes (Google Fonts)
- Performance: grain via SVG filter (não imagem), cursor via rAF, partículas otimizadas com DPR
- Acessibilidade: `prefers-reduced-motion` desativa animações de entrada e cursor
- Mobile: cursor desativado em `pointer: coarse`
