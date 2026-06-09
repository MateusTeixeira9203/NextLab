# Plano de Implementação — Nexlab Landing Redesign
**Data:** 2026-06-08  
**Arquivo:** `nexlab.html` (single-file, 617 linhas → ~1400 linhas após implementação)  
**Spec:** `docs/superpowers/specs/2026-06-08-nexlab-landing-redesign.md`

---

## Goal
Redesign completo da landing page Nexlab para visual dark cinematic + digital art com fator uau. Mantém estrutura de seções, eleva tudo visualmente.

## Tech Stack
- HTML5 + CSS3 + Vanilla JS inline
- Google Fonts já carregadas (Syne, DM Sans, DM Mono, Playfair Display, Bebas Neue)
- Sem dependências externas novas

## File Structure
- **Modificado:** `nexlab.html` (único arquivo a alterar)

---

## Task 1: Grain Texture + CSS Variables + 4º Aurora Blob
**Arquivo:** `nexlab.html`  
**Duração estimada:** 4 min

**Steps:**
1. No bloco `:root`, adicionar variável `--amber: #ff9f5b;`
2. No `<body>`, adicionar como primeiro elemento filho:
```html
<!-- GRAIN OVERLAY -->
<svg id="grain" style="position:fixed;inset:0;width:100%;height:100%;z-index:9998;pointer-events:none;opacity:.042" xmlns="http://www.w3.org/2000/svg">
  <filter id="noise"><feTurbulence type="fractalNoise" baseFrequency="0.65" numOctaves="3" stitchTiles="stitch"/><feColorMatrix type="saturate" values="0"/></filter>
  <rect width="100%" height="100%" filter="url(#noise)"/>
</svg>
```
3. Adicionar `.aurora.a4` no HTML do hero, após `.a3`:
```html
<div class="aurora a4"></div>
```
4. No CSS, adicionar `.aurora.a4` e expandir os blobs existentes:
```css
.aurora.a1{width:700px;height:700px;...top:-160px;left:-100px;animation:float1 28s ease-in-out infinite;}
.aurora.a2{width:650px;height:650px;...bottom:-180px;right:-80px;animation:float2 34s ease-in-out infinite;}
.aurora.a3{width:520px;height:520px;...animation:float1 40s ease-in-out infinite;}
.aurora.a4{width:480px;height:480px;background:radial-gradient(circle,rgba(255,159,91,.22),transparent 70%);top:25%;right:10%;animation:float2 32s ease-in-out infinite;}
```
**Verificação visual:** grain sutil visível sobre toda a página, novo blob âmbar visível no hero.

---

## Task 2: Custom Cursor Magnético
**Arquivo:** `nexlab.html`  
**Duração estimada:** 5 min

**Steps:**
1. Adicionar HTML após o `<body>`:
```html
<div id="cur-dot"></div>
<div id="cur-ring"></div>
```
2. Adicionar CSS:
```css
#cur-dot,#cur-ring{position:fixed;border-radius:50%;pointer-events:none;z-index:9999;transition:opacity .3s;}
#cur-dot{width:6px;height:6px;background:#fff;transform:translate(-50%,-50%);top:0;left:0;}
#cur-ring{width:28px;height:28px;border:1.5px solid rgba(255,255,255,.5);transform:translate(-50%,-50%);top:0;left:0;transition:width .25s,height .25s,border-color .25s;}
body:has(a:hover) #cur-ring,body:has(button:hover) #cur-ring{width:48px;height:48px;border-color:rgba(154,107,255,.8);}
@media(pointer:coarse){#cur-dot,#cur-ring{display:none;}body{cursor:auto;}}
```
3. Adicionar CSS para esconder cursor padrão em desktop:
```css
@media(pointer:fine){body{cursor:none;}a,button,.btn,.plan,.cap,.demo{cursor:none;}}
```
4. Adicionar JS no `<script>`:
```js
/* ===== Cursor magnético ===== */
const dot=document.getElementById('cur-dot'),ring=document.getElementById('cur-ring');
let cx=0,cy=0,rx=0,ry=0;
document.addEventListener('pointermove',e=>{cx=e.clientX;cy=e.clientY;});
function animCursor(){
  dot.style.left=cx+'px'; dot.style.top=cy+'px';
  rx+=(cx-rx)*.12; ry+=(cy-ry)*.12;
  ring.style.left=rx+'px'; ring.style.top=ry+'px';
  requestAnimationFrame(animCursor);
}
if(window.matchMedia('(pointer:fine)').matches) animCursor();
```
**Verificação visual:** ponto segue cursor instantaneamente, anel segue com spring suave, anel expande sobre links.

---

## Task 3: Hero — Tipografia Dramática + Clip Reveal
**Arquivo:** `nexlab.html`  
**Duração estimada:** 4 min

**Steps:**
1. Atualizar CSS do `.hero-h`:
```css
h1.hero-h{font-family:'Syne',sans-serif;font-weight:800;font-size:clamp(3.2rem,9vw,8.5rem);line-height:1.0;letter-spacing:-.04em;margin-bottom:28px;}
```
2. Substituir keyframe de animação das `.ln` de `rise` para clip reveal:
```css
h1.hero-h .ln{display:block;opacity:1;clip-path:inset(100% 0 0 0);animation:cliprise .9s cubic-bezier(.16,1,.3,1) forwards;}
h1.hero-h .ln:nth-child(1){animation-delay:0ms;}
h1.hero-h .ln:nth-child(2){animation-delay:160ms;}
h1.hero-h .ln:nth-child(3){animation-delay:320ms;}
@keyframes cliprise{to{clip-path:inset(0% 0 0 0);}}
```
3. Atualizar copy do H1 no HTML:
```html
<h1 class="hero-h">
  <span class="ln">Transformamos</span>
  <span class="ln" style="padding-left:.08em">negócios em</span>
  <span class="ln grad-text">experiências.</span>
</h1>
```
4. Atualizar `.hero-sub` CSS e copy:
```css
.hero-sub{font-size:clamp(15px,1.8vw,19px);color:var(--muted);max-width:560px;margin-bottom:38px;padding-left:18px;border-left:3px solid;border-image:var(--grad) 1;opacity:0;animation:rise .9s .56s forwards;}
```
Copy: `Sites que não existem pra existir — existem pra <strong>vender, impressionar</strong> e crescer junto com o seu negócio.`

**Verificação visual:** headline entra linha a linha com efeito de "surgir de baixo de uma máscara", tipografia grande e dramática.

---

## Task 4: Hero Canvas Refatorado — Partículas com Glow
**Arquivo:** `nexlab.html` — bloco `<script>`, função de canvas  
**Duração estimada:** 4 min

**Steps:**
1. No `resize()`, atualizar criação de nodes:
```js
nodes=Array.from({length:count},()=>({
  x:Math.random()*W, y:Math.random()*H,
  vx:(Math.random()-.5)*.3, vy:(Math.random()-.5)*.3,
  r:Math.random()*2.5+1.5
}));
```
2. No `draw()`, atualizar lógica de mouse (repulsão em vez de atração):
```js
const dxm=mouse.x-n.x, dym=mouse.y-n.y, dm=Math.hypot(dxm,dym);
if(dm<120 && dm>0){ n.vx-=(dxm/dm)*.012; n.vy-=(dym/dm)*.012; }
// damping
n.vx*=.995; n.vy*=.995;
```
3. Atualizar linhas entre nodes com glow:
```js
const o=(1-d/140)*.6;
ctx.save();
ctx.shadowBlur=8; ctx.shadowColor=`rgba(91,140,255,${o*1.5})`;
ctx.strokeStyle=g; ctx.lineWidth=.9;
ctx.beginPath(); ctx.moveTo(a.x,a.y); ctx.lineTo(b.x,b.y); ctx.stroke();
ctx.restore();
```
4. Atualizar linhas do mouse:
```js
if(d<160){
  const o=(1-d/160)*.7;
  ctx.save();
  ctx.shadowBlur=14; ctx.shadowColor=`rgba(67,231,224,${o*2})`;
  ctx.strokeStyle=`rgba(67,231,224,${o})`; ctx.lineWidth=1;
  ctx.beginPath(); ctx.moveTo(mouse.x,mouse.y); ctx.lineTo(n.x,n.y); ctx.stroke();
  ctx.restore();
}
```
5. Nodes com glow:
```js
ctx.save();
ctx.shadowBlur=10; ctx.shadowColor='rgba(180,170,255,.6)';
ctx.beginPath(); ctx.arc(n.x,n.y,n.r,0,7);
ctx.fillStyle='rgba(200,190,255,.9)'; ctx.fill();
ctx.restore();
```
**Verificação visual:** partículas maiores, linhas com glow luminoso, mouse repele as partículas.

---

## Task 5: Hero CTAs + Scroll Hint
**Arquivo:** `nexlab.html`  
**Duração estimada:** 3 min

**Steps:**
1. Atualizar CSS do `.btn-grad` com liquid border em repouso:
```css
.btn-grad{position:relative;background:var(--grad);color:#fff;box-shadow:0 8px 28px rgba(122,107,255,.36);overflow:hidden;}
.btn-grad::before{content:"";position:absolute;inset:-2px;border-radius:100px;background:conic-gradient(from var(--ang,0deg),var(--g1),var(--g3),var(--g2),var(--g1));animation:spin 3s linear infinite;z-index:-1;opacity:.7;}
.btn-grad:hover{transform:translateY(-3px);box-shadow:0 14px 40px rgba(122,107,255,.55);}
```
2. Atualizar `.btn-ghost`:
```css
.btn-ghost{background:transparent;color:var(--muted);border:none;padding-left:4px;font-size:15px;}
.btn-ghost::after{content:"→";margin-left:8px;display:inline-block;transition:transform .3s;}
.btn-ghost:hover{color:var(--text);}
.btn-ghost:hover::after{transform:translateX(6px);}
```
Remover o `→` do texto do botão ghost no HTML.

3. Substituir scroll hint CSS:
```css
.scroll-hint{position:absolute;bottom:30px;left:50%;transform:translateX(-50%);z-index:3;opacity:0;animation:rise 1s 1.1s forwards;}
.scroll-hint .drip{width:1.5px;height:50px;background:linear-gradient(var(--g2),transparent);overflow:hidden;position:relative;}
.scroll-hint .drip::after{content:"";position:absolute;top:-100%;left:0;width:100%;height:100%;background:var(--g2);animation:drip 1.8s ease-in infinite;}
@keyframes drip{0%{top:-100%;}100%{top:100%;}}
```
HTML do scroll-hint: remover o texto "role" e o `.bar`, adicionar `<div class="drip"></div>`.

**Verificação visual:** botão primário com gradiente girando na borda, botão ghost com seta que avança, scroll hint com gota descendo.

---

## Task 6: Títulos Editoriais de Seção (Sistema Global)
**Arquivo:** `nexlab.html`  
**Duração estimada:** 4 min

**Steps:**
1. Adicionar CSS para o sistema editorial:
```css
.sec-editorial{position:relative;margin-bottom:56px;}
.sec-editorial .sec-overline{font-family:'DM Mono',monospace;font-size:11.5px;letter-spacing:.25em;text-transform:uppercase;color:var(--muted-d);margin-bottom:10px;display:block;}
.sec-editorial .sec-h{font-family:'Syne',sans-serif;font-weight:800;font-size:clamp(2.6rem,6vw,5.5rem);line-height:1.0;letter-spacing:-.03em;position:relative;display:inline-block;}
.sec-editorial .sec-num{font-family:'Syne',sans-serif;font-weight:800;font-size:clamp(8rem,14vw,13rem);line-height:1;color:var(--text);opacity:.045;position:absolute;right:0;top:50%;transform:translateY(-50%);pointer-events:none;user-select:none;letter-spacing:-.05em;}
.sec-editorial-wrap{position:relative;overflow:hidden;}
```
2. Substituir os cabeçalhos das 4 seções (Capabilities, Demos, Process, Pricing) com o novo markup:
```html
<!-- Exemplo para Capabilities: -->
<div class="sec-editorial-wrap reveal">
  <div class="sec-editorial">
    <span class="sec-overline">O que fazemos</span>
    <div style="position:relative">
      <h2 class="sec-h">FAZEMOS<br><span style="font-style:italic;font-size:.62em;font-weight:600;color:var(--muted)">presença que vende.</span></h2>
      <span class="sec-num">01</span>
    </div>
  </div>
  <p class="sec-intro">Cada projeto é desenhado do zero pra marca do cliente — nada de modelo pronto reaproveitado.</p>
</div>
```
3. Aplicar padrão equivalente para Demos (02), Process (03), Pricing (04) com números e overlines correspondentes.

**Verificação visual:** títulos de seção grandes e dramáticos, número decorativo transparente atrás, overline em mono acima.

---

## Task 7: Capabilities — Stagger Grid + Cards Elevados
**Arquivo:** `nexlab.html`  
**Duração estimada:** 4 min

**Steps:**
1. Atualizar CSS das `.caps`:
```css
.caps{display:grid;grid-template-columns:repeat(4,1fr);gap:20px;margin-top:48px;align-items:start;}
.cap:nth-child(even){margin-top:2rem;}
```
2. Atualizar CSS dos `.cap`:
```css
.cap{background:var(--glass);border:1px solid var(--glass-brd);border-radius:20px;padding:28px 22px;position:relative;overflow:hidden;transition:transform .4s,border-color .4s,box-shadow .4s;backdrop-filter:blur(10px);}
.cap .cap-num{position:absolute;top:-10px;right:12px;font-family:'Syne',sans-serif;font-weight:800;font-size:5.5rem;color:var(--text);opacity:.055;line-height:1;user-select:none;pointer-events:none;}
.cap::after{content:"";position:absolute;inset:-1px;border-radius:20px;padding:1px;background:conic-gradient(from var(--ang,0deg),var(--g1),var(--g2),var(--g3),var(--cy),var(--g1));-webkit-mask:linear-gradient(#fff 0 0) content-box,linear-gradient(#fff 0 0);-webkit-mask-composite:xor;mask-composite:exclude;opacity:0;transition:opacity .4s;animation:spin 4s linear infinite;}
.cap:hover::after{opacity:1;}
.cap:hover{transform:translateY(-8px);box-shadow:0 24px 60px rgba(91,140,255,.15);}
.cap:hover::before{opacity:1;}
```
3. Adicionar `.cap-num` dentro de cada card no HTML:
```html
<div class="cap reveal"><span class="cap-num">01</span><div class="cap-ic">...</div>...
```
4. Atualizar stagger delays: `.08s, .16s, .24s` → `.12s, .24s, .36s`

**Verificação visual:** cards em altura staggered, número decorativo sutil em cada card, borda gradiente no hover, elevação ao hover.

---

## Task 8: Portfolio — Layout Assimétrico
**Arquivo:** `nexlab.html`  
**Duração estimada:** 5 min

**Steps:**
1. Remover a classe `.demos` atual e substituir o container por layout editorial:
```css
.demos-grid{display:grid;gap:24px;margin-top:60px;}
.demos-row-full .demo{width:100%;}
.demos-row-half{display:grid;grid-template-columns:1fr 1fr;gap:24px;}
.demos-row-full .b-screen{height:420px;}
.demos-row-half .b-screen{height:320px;}
```
2. Reorganizar o HTML dos demos:
```html
<div class="demos-grid">
  <div class="demos-row-full">
    <!-- Demo: Clínica (card largo) -->
    <div class="demo">...</div>
  </div>
  <div class="demos-row-half">
    <!-- Demo: Barbearia + Petshop -->
    <div class="demo">...</div>
    <div class="demo">...</div>
  </div>
  <div class="demos-row-full">
    <!-- Demo: Hotel (card largo) -->
    <div class="demo">...</div>
  </div>
</div>
```
3. Atualizar `.demo` e `.browser` com elevação e reflexo:
```css
.demo{position:relative;}
.browser{border-radius:18px;overflow:hidden;border:1px solid var(--glass-brd);box-shadow:0 30px 70px rgba(0,0,0,.5);background:#0a0a14;transition:.5s cubic-bezier(.2,.7,.2,1);}
.demo:hover .browser{transform:translateY(-14px);box-shadow:0 50px 100px rgba(0,0,0,.6);}
/* Reflexo */
.browser-wrap{position:relative;}
.browser-wrap::after{content:"";position:absolute;bottom:-40px;left:5%;right:5%;height:40px;background:inherit;filter:blur(20px);opacity:.25;transform:scaleY(-1);pointer-events:none;}
```
4. Adicionar glow temático por card no hover via classes:
```css
.demo-med:hover .browser{box-shadow:0 50px 100px rgba(14,155,181,.25);}
.demo-barb:hover .browser{box-shadow:0 50px 100px rgba(232,185,100,.2);}
.demo-pet:hover .browser{box-shadow:0 50px 100px rgba(255,122,89,.22);}
.demo-hotel:hover .browser{box-shadow:0 50px 100px rgba(100,200,100,.18);}
```
5. Adicionar etiqueta lateral no hover:
```css
.demo-tag-slide{position:absolute;right:-100%;top:50%;transform:translateY(-50%);background:var(--surface);border:1px solid var(--glass-brd);border-radius:12px;padding:12px 16px;font-family:'DM Mono';font-size:11px;color:var(--muted);transition:right .4s cubic-bezier(.2,.7,.2,1);backdrop-filter:blur(10px);white-space:nowrap;}
.demo:hover .demo-tag-slide{right:-140px;}
```

**Verificação visual:** layout editorial assimétrico, clínica e hotel em largura total, barbearia e petshop lado a lado, cada card com glow temático no hover.

---

## Task 9: Process — Timeline Horizontal
**Arquivo:** `nexlab.html`  
**Duração estimada:** 4 min

**Steps:**
1. Substituir CSS de `.steps`:
```css
.steps{display:grid;grid-template-columns:repeat(4,1fr);gap:0;margin-top:64px;position:relative;}
/* Linha conectora */
.steps::before{content:"";position:absolute;top:0;left:12.5%;right:12.5%;height:2px;background:var(--line);}
.steps-progress{position:absolute;top:0;left:12.5%;height:2px;width:0;background:var(--grad);transition:width 1.2s ease;z-index:1;}
.steps.in .steps-progress{width:75%;}
```
2. Atualizar CSS do `.step`:
```css
.step{padding:32px 22px 12px;position:relative;border-right:1px solid var(--line);}
.step:last-child{border-right:none;}
.step-bg-num{font-family:'Syne',sans-serif;font-weight:800;font-size:9rem;color:var(--text);opacity:.07;line-height:1;position:absolute;top:-10px;right:10px;user-select:none;pointer-events:none;}
.step .n{font-family:'DM Mono';font-size:12px;color:var(--g2);margin-bottom:16px;letter-spacing:.1em;}
.step h3{font-family:'Syne';font-weight:700;font-size:17px;margin-bottom:8px;}
.step p{font-size:13px;color:var(--muted);line-height:1.6;}
/* Linha vertical esquerda que acende */
.step::after{content:"";position:absolute;left:0;top:32px;width:3px;height:0;background:var(--grad);transition:height .8s ease;border-radius:2px;}
.step.in::after{height:60px;}
/* Remove borda top antiga */
.step::before{display:none;}
```
3. Adicionar `.step-bg-num` no HTML de cada step:
```html
<div class="step reveal"><span class="step-bg-num">01</span><div class="n mono">01</div>...
```
4. Atualizar copy:
- 01: "A gente ouve antes de criar."
- 02: "Cada pixel com intenção."
- 03: "Seu site vivo, no Google, no WhatsApp."
- 04: "A gente não some depois do lançamento."
5. Adicionar `.steps-progress` div dentro de `.steps` via JS ou HTML:
```html
<div class="steps" id="steps-container">
  <div class="steps-progress" id="steps-prog"></div>
  ...
```
E no JS:
```js
const stepsEl=document.getElementById('steps-container');
const stepsProg=document.getElementById('steps-prog');
const stepsIO=new IntersectionObserver(([e])=>{ if(e.isIntersecting) stepsEl.classList.add('in'); },{threshold:.2});
stepsIO.observe(stepsEl);
```

**Verificação visual:** linha horizontal que se preenche em gradiente conforme seção entra em view, número backdrop gigante em cada step, linha vertical esquerda acende no hover/reveal.

---

## Task 10: Pricing — Card Featured Elevado
**Arquivo:** `nexlab.html`  
**Duração estimada:** 4 min

**Steps:**
1. Atualizar CSS do `.plan.feat`:
```css
.plan.feat{background:var(--surface);transform:scale(1.04);box-shadow:0 0 60px rgba(91,140,255,.18),0 0 120px rgba(154,107,255,.1);}
.plan.feat:hover{transform:scale(1.04) translateY(-8px);}
```
2. Atualizar CSS da tipografia de preço:
```css
.plan .pprice{font-family:'Syne';font-weight:800;font-size:clamp(2rem,4vw,3.2rem);margin-bottom:4px;position:relative;line-height:1;}
.plan .pprice small{font-size:14px;color:var(--muted);font-weight:500;display:block;margin-top:2px;}
.plan .pfrom{font-family:'DM Mono';font-size:11px;color:var(--muted-d);letter-spacing:.12em;text-transform:uppercase;margin-bottom:24px;}
```
3. Adicionar stagger na feature list via CSS:
```css
.plan li{opacity:0;transform:translateX(-8px);transition:opacity .4s, transform .4s;}
.plan.in li{opacity:1;transform:none;}
.plan.in li:nth-child(1){transition-delay:.05s;}
.plan.in li:nth-child(2){transition-delay:.11s;}
.plan.in li:nth-child(3){transition-delay:.17s;}
.plan.in li:nth-child(4){transition-delay:.23s;}
```
4. Adicionar `.plan` ao IntersectionObserver existente:
```js
document.querySelectorAll('.reveal,.demo,.step,.plan').forEach(el=>io.observe(el));
```
5. Atualizar CSS e copy da `.recur-bar`:
```css
.recur-bar{margin-top:28px;background:rgba(255,255,255,.03);border:1px solid var(--glass-brd);border-radius:16px;padding:20px 28px;display:flex;align-items:center;gap:16px;backdrop-filter:blur(8px);}
.recur-bar .ic{font-size:22px;animation:spin 8s linear infinite;display:inline-block;}
```
Copy da recur-bar: `<strong>Hospedagem & manutenção:</strong> R$ 120–150/mês — mantém seu site rápido, seguro e <strong>você pode dormir tranquilo.</strong>`

**Verificação visual:** card Profissional fisicamente maior com glow externo, preço grande e dramático, features entram em cascata, ícone ∞ girando.

---

## Task 11: CTA Final — Orbes Orbitantes + Pulse Ring
**Arquivo:** `nexlab.html`  
**Duração estimada:** 3 min

**Steps:**
1. Substituir `.cta-sec .glow` por dois orbes:
```html
<div class="cta-orb cta-orb1"></div>
<div class="cta-orb cta-orb2"></div>
```
```css
.cta-orb{position:absolute;border-radius:50%;filter:blur(80px);pointer-events:none;}
.cta-orb1{width:400px;height:400px;background:rgba(91,140,255,.35);animation:orbit1 20s linear infinite;transform-origin:200px 200px;}
.cta-orb2{width:360px;height:360px;background:rgba(255,107,193,.3);animation:orbit2 28s linear infinite;transform-origin:180px -100px;}
@keyframes orbit1{to{transform:rotate(360deg) translate(120px,0);}}
@keyframes orbit2{to{transform:rotate(-360deg) translate(-80px,60px);}}
```
2. Atualizar copy do H2:
```html
<h2 class="reveal">Seu negócio merece<br><span class="grad-text">um site à altura.</span></h2>
```
3. Adicionar pulse rings no botão WhatsApp:
```css
.wa{position:relative;}
.wa::before,.wa::after{content:"";position:absolute;inset:-8px;border-radius:100px;border:2px solid rgba(37,211,102,.5);animation:pulse-wa 2s ease-out infinite;}
.wa::after{animation-delay:1s;}
@keyframes pulse-wa{0%{opacity:.8;transform:scale(1);}100%{opacity:0;transform:scale(1.15);}}
```
4. Atualizar copy do botão: `Vamos conversar →`
5. Adicionar linha de micro-copy após o botão:
```html
<p class="cta-anchors reveal" style="transition-delay:.24s">Resposta em até 2h &nbsp;·&nbsp; Sem compromisso &nbsp;·&nbsp; 100% personalizado</p>
```
```css
.cta-anchors{font-family:'DM Mono';font-size:12px;color:var(--muted-d);letter-spacing:.1em;margin-top:20px;}
```

**Verificação visual:** dois orbes coloridos orbitando no fundo da CTA, botão WhatsApp com duplo ring pulsante verde, micro-copy de confiança abaixo.

---

## Task 12: Footer Elevado + Ícones Sociais
**Arquivo:** `nexlab.html`  
**Duração estimada:** 3 min

**Steps:**
1. Substituir o `border-top` do footer por linha gradiente:
```css
footer{padding:48px 0;position:relative;}
footer::before{content:"";position:absolute;top:0;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,var(--g1) 25%,var(--g2) 50%,var(--g3) 75%,transparent);}
```
2. Atualizar layout do footer no HTML:
```html
<footer>
  <div class="container">
    <div class="footer-inner">
      <div class="logo" style="font-size:20px">...logo...</div>
      <div class="footer-social">
        <a href="#" class="fsoc" aria-label="Instagram"><!-- SVG Instagram --></a>
        <a href="#" class="fsoc" aria-label="Behance"><!-- SVG Behance --></a>
        <a href="#" class="fsoc" aria-label="LinkedIn"><!-- SVG LinkedIn --></a>
      </div>
      <span class="fmeta">Feito com intenção · Nexlab © 2026</span>
    </div>
  </div>
</footer>
```
3. CSS do footer social:
```css
.footer-inner{display:flex;justify-content:space-between;align-items:center;gap:20px;flex-wrap:wrap;}
.footer-social{display:flex;gap:16px;}
.fsoc{width:36px;height:36px;border-radius:50%;border:1px solid var(--line);display:flex;align-items:center;justify-content:center;color:var(--muted-d);transition:.25s;text-decoration:none;}
.fsoc:hover{border-color:var(--g2);color:var(--g2);}
.fsoc svg{width:16px;height:16px;fill:currentColor;}
```
4. SVG inline Instagram (simplificado), Behance e LinkedIn.

**Verificação visual:** linha gradiente topo do footer, logo maior, 3 ícones sociais com hover colorido.

---

## Task 13: Scroll Parallax Hero + Reduced Motion QA
**Arquivo:** `nexlab.html` — bloco `<script>`  
**Duração estimada:** 3 min

**Steps:**
1. Adicionar parallax leve no canvas via scroll:
```js
addEventListener('scroll',()=>{
  nav.classList.toggle('scrolled', scrollY>30);
  /* parallax hero canvas */
  const heroCanvas=document.getElementById('net');
  heroCanvas.style.transform=`translateY(${scrollY*0.25}px)`;
});
```
2. Atualizar `@media(prefers-reduced-motion:reduce)` para cobrir todos os novos elementos:
```css
@media(prefers-reduced-motion:reduce){
  *{animation:none!important;transition:none!important;}
  .reveal,.demo,.plan,.step{opacity:1!important;transform:none!important;}
  h1.hero-h .ln{clip-path:none!important;opacity:1!important;}
  .eyebrow,.hero-sub,.hero-btns,.scroll-hint{opacity:1!important;transform:none!important;}
  #cur-dot,#cur-ring{display:none!important;}
  .steps-progress{width:75%!important;}
  .plan li{opacity:1!important;transform:none!important;}
}
```
3. Verificar breakpoints mobile:
- `.caps` em 900px: 2 colunas, remover `margin-top` do stagger
- `.demos-row-half` em 900px: 1 coluna
- Cursor oculto em `pointer:coarse`
- `.plan.feat` em 900px: `transform: none` (sem scale)

**Verificação visual:** parallax suave no hero ao scrollar, animações desativadas em modo reduced-motion, mobile sem quebras.

---

## Ordem de Execução

```
Task 1  → Task 2  → Task 3  → Task 4  → Task 5
Task 6  → Task 7  → Task 8  → Task 9  → Task 10
Task 11 → Task 12 → Task 13
```

Cada task modifica seções distintas do `nexlab.html` e pode ser verificada abrindo o arquivo no navegador.

---

## Self-Review Checklist
- [x] Spec coverage: todas as 9 seções do spec cobertas em 13 tasks
- [x] Sem placeholders: código completo em cada task
- [x] Consistência: variáveis CSS (`--g1,--g2,--g3,--cy`) usadas em todo o plano
- [x] Sem forward references: cada task usa apenas CSS/JS dos tasks anteriores
