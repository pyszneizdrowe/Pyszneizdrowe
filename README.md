## Cześć 👋

Pyszne i Zdrowe💚
<html lang="ru">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Insta Slides — PyszneIZdrowe</title>

<link href="https://fonts.googleapis.com/css2?family=Lora:wght@400;700&family=Montserrat:wght@400;700&display=swap" rel="stylesheet">

<style>
  :root{ --bg:#0f1724; --panel:#0b1220; --accent:#f59e0b; --muted:#cbd5e1; }
  *{ box-sizing:border-box }
  body{
    margin:0; font-family:Montserrat,system-ui,Arial;
    background:linear-gradient(180deg,#071024 0%, #07192a 100%);
    color:var(--muted); min-height:100vh; display:flex; gap:18px; padding:18px;
  }
  .left{
    width:420px; max-width:40vw; background:var(--panel); padding:16px;
    border-radius:10px; box-shadow:0 6px 20px rgba(2,6,23,.6);
  }
  h1{ margin:0 0 12px 0; font-size:18px; color:#fff }
  label{ display:block; margin-top:10px; font-size:13px; color:#cbd5e1 }
  textarea{
    width:100%; height:140px; padding:8px; border-radius:6px;
    border:1px solid rgba(255,255,255,.06); background:#071426; color:#fff; resize:vertical;
  }
  .small{ font-size:13px; padding:6px; border-radius:6px }
  .btn{
    background:var(--accent); border:none; color:#071022;
    padding:8px 10px; border-radius:8px; cursor:pointer; font-weight:700
  }
  .panel-row{ display:flex; gap:8px; align-items:center; margin-top:8px; flex-wrap:wrap }
  .preview{ flex:1; display:grid; place-items:center; gap:12px }
  .canvas-wrap{ background:#000; padding:10px; border-radius:12px; width:100% }
  .slides{ display:flex; gap:8px; flex-wrap:wrap }
  .slide{
    width:180px; height:225px; border-radius:8px; overflow:hidden;
    border:1px solid rgba(255,255,255,.06);
    position:relative; display:flex; align-items:center; justify-content:center
  }
  .slide img{ width:100%; height:100%; object-fit:cover; display:block }
  .slide .text{
    position:absolute; left:12px; right:12px; bottom:12px; color:#fff;
    font-family:Lora,serif; text-shadow:0 6px 20px rgba(0,0,0,.6); font-size:20px
  }
  footer{ margin-top:12px; font-size:12px; color:#93c5fd }
  .note{ font-size:12px; color:#94a3b8; margin-top:8px }

  /* input file скрытый, но не display:none — чтобы на iOS работало */
  .input-file{
    position:fixed; left:-9999px; top:-9999px; width:1px; height:1px;
    opacity:0; pointer-events:none;
  }

  .canvas-wrap canvas{ width:100%; height:auto; display:block }

  /* простая «плашка загрузки» */
  .loader{ color:#9aaed0; font-size:13px; padding:18px }

  @media (max-width: 900px){
    body{ flex-direction: column; gap:12px; padding:12px; overflow-x:hidden }
    .left{ width:100%; max-width:none }
  }
</style>
</head>
<body>

<div class="left">
  <h1>Insta Slides — PyszneIZdrowe</h1>

  <label>Текст (пустая строка = новый слайд)</label>
  <textarea id="inputText" placeholder="Абзац = один слайд"></textarea>

  <div class="panel-row">
    <div style="flex:1 1 160px">
      <label>Размер слайда</label>
      <select id="size" class="small">
        <option value="1080x1920">Stories 1080×1920 (9:16)</option>
        <option value="1080x1350" selected>Post 1080×1350 (4:5)</option>
        <option value="1080x1080">Post 1080×1080 (1:1)</option>
      </select>
    </div>
    <div style="flex:1 1 120px">
      <label>Шрифт</label>
      <select id="font" class="small">
        <option value="Lora">Lora (serif)</option>
        <option value="Montserrat">Montserrat (sans)</option>
        <option value="system-ui">System</option>
      </select>
    </div>
    <div style="flex:1 1 120px">
      <label>Интерлиньяж</label>
      <input id="lineHeight" type="number" class="small" value="1.2" step="0.05" min="1" max="2">
    </div>
    <div style="flex:1 1 120px">
      <label>Размер шрифта (px)</label>
      <input id="fontSize" type="number" class="small" value="64" min="14" max="160">
    </div>
  </div>

  <div class="panel-row">
    <div style="flex:1 1 160px">
      <label>Позиция по высоте</label>
      <select id="vpos" class="small">
        <option value="top">Сверху</option>
<option value="upper">Выше центра</option>
        <option value="center" selected>По центру</option>
        <option value="lower">Ниже центра</option>
        <option value="bottom">Внизу</option>
      </select>
    </div>
    <div style="flex:1 1 160px">
      <label>Позиция по ширине</label>
      <select id="hpos" class="small">
        <option value="left">Слева</option>
        <option value="lcenter">Левее центра</option>
        <option value="center" selected>По центру</option>
        <option value="rcenter">Правее центра</option>
        <option value="right">Справа</option>
      </select>
    </div>
    <div style="flex:1 1 120px">
      <label>Прозрачность подложки</label>
      <input id="backdrop" type="number" class="small" value="0" min="0" max="0.9" step="0.05">
    </div>
    <div style="flex:1 1 160px; display:flex; align-items:center; gap:8px">
      <input id="showArrow" type="checkbox">
      <label for="showArrow" style="margin-top:0">Показать стрелку “Swipe”</label>
    </div>
  </div>

  <div class="panel-row">
    <button class="btn" id="generate">Собрать</button>
    <button id="clear" class="small">Очистить</button>

    <!-- Лейбл как кнопка — открывает диалог выбора файлов -->
    <label for="imageLoader" class="small btn" style="cursor:pointer;">Загрузить фоны</label>
    <input id="imageLoader" class="input-file" type="file" accept="image/*" multiple>

    <button id="downloadAll" class="small">Скачать все (PNG)</button>
  </div>

  <div class="note">Фоны «зациклены»: если слайдов больше, чем фото — они повторяются.</div>
  <footer>Сделано для <strong>pyszneizdrowe.io</strong></footer>
</div>

<div class="preview">
  <div class="canvas-wrap" id="canvasContainer"></div>
  <div class="slides" id="slidesContainer"></div>
</div>

<script>
/* ===== DOM refs ===== */
const inputText = document.getElementById('inputText');
const sizeEl = document.getElementById('size');
const fontEl = document.getElementById('font');
const lineHeightEl = document.getElementById('lineHeight');
const fontSizeEl = document.getElementById('fontSize');
const vposEl = document.getElementById('vpos');
const hposEl = document.getElementById('hpos');
const backdropEl = document.getElementById('backdrop');
const showArrow = document.getElementById('showArrow');

const generateBtn = document.getElementById('generate');
const clearBtn = document.getElementById('clear');
const imageLoader = document.getElementById('imageLoader');
const downloadAll = document.getElementById('downloadAll');

const slidesContainer = document.getElementById('slidesContainer');
const canvasContainer = document.getElementById('canvasContainer');

/* ===== State ===== */
let backgrounds = [];   // [{url: objectURL}]
const MIN_FONT = 14;

/* ===== Helpers ===== */
function parseSlides(text){
  const parts = text.split(/\n{2,}/).map(p=>p.trim()).filter(Boolean);
  if (parts.length===0 && text.trim()!=='') {
    return text.split(/\n/).map(p=>p.trim()).filter(Boolean);
  }
  return parts;
}
function sizeFromSelect(){
  const [w,h] = sizeEl.value.split('x').map(n=>parseInt(n,10));
  return {w,h};
}
function safeAreasFor(w,h){
  if (w===1080 && h===1920) return { top:200, bottom:200, side:60 }; // Stories
  if (w===1080 && h===1350) return { top:120, bottom:120, side:60 }; // 4:5
  return { top:100, bottom:100, side:60 };
}
function previewScaleFor(w){
  const available = canvasContainer.clientWidth || window.innerWidth;
  return Math.min(available / w, 1);
}

/* Надёжная загрузка больших изображений */
async function loadImage(src){
  // создаём <img> и ждём декодирования; для больших файлов это критично
  const img = new Image();
  img.decoding = 'async';
  img.src = src;
  // ждём decode(), но если браузер не поддерживает — fallback на onload
  if (img.decode) {
    try { await img.decode(); return img; }
    catch { await new Promise((res,rej)=>{ img.onload=res; img.onerror=rej; }); return img; }
  } else {
    await new Promise((res,rej)=>{ img.onload=res; img.onerror=rej; });
    return img;
  }
}

function drawCoverImage(ctx,img,w,h){
const iw=img.naturalWidth||img.width;
  const ih=img.naturalHeight||img.height;
  const scale = Math.max(w/iw, h/ih);
  const dw = iw*scale, dh = ih*scale;
  const dx = (w - dw)/2, dy = (h - dh)/2;
  ctx.imageSmoothingEnabled = true;
  ctx.imageSmoothingQuality = 'high';
  ctx.drawImage(img, dx, dy, dw, dh);
}

/* ===== Core render (async) ===== */
async function renderSlides(){
  const slides = parseSlides(inputText.value);
  slidesContainer.innerHTML = '';
  canvasContainer.innerHTML = '';

  if (!slides.length){
    canvasContainer.innerHTML = '<div class="loader">Вставьте текст и нажмите «Собрать»</div>';
    return;
  }

  const {w,h} = sizeFromSelect();
  const scale = previewScaleFor(w);
  const previewW = Math.round(w*scale), previewH = Math.round(h*scale);

  // Лоадер
  canvasContainer.innerHTML = '<div class="loader">Рендерим…</div>';

  const fullCanvases = [];

  for (let idx = 0; idx < slides.length; idx++){
    const txt = slides[idx];
    const c = document.createElement('canvas');
    c.width = w; c.height = h;
    await drawSlideOnCanvas(c, txt, backgrounds.length ? backgrounds[idx % backgrounds.length].url : null);
    fullCanvases.push(c);

    // Миниатюра после готовности
    const thumb = document.createElement('div');
    thumb.className = 'slide';
    const tH = 225, tW = Math.round(tH * (w/h));
    thumb.style.width = tW + 'px'; thumb.style.height = tH + 'px';
    const imgEl = new Image();
    imgEl.src = c.toDataURL('image/png'); // PNG для чёткости
    thumb.appendChild(imgEl);
    slidesContainer.appendChild(thumb);

    // press&hold -> download
    let pressTimer = null;
    const dl = () => downloadDataUrl(c.toDataURL('image/png'), 'slide-'+(idx+1)+'.png');
    thumb.addEventListener('mousedown', e=>{ e.preventDefault(); pressTimer=setTimeout(dl,700); });
    ['mouseup','mouseleave'].forEach(ev=> thumb.addEventListener(ev, ()=> clearTimeout(pressTimer)));
    thumb.addEventListener('touchstart', ()=>{ pressTimer=setTimeout(dl,700); });
    thumb.addEventListener('touchend', ()=> clearTimeout(pressTimer));

    // click -> big preview
    thumb.addEventListener('click', ()=>{
      canvasContainer.innerHTML = '';
      const vcanvas = document.createElement('canvas');
      vcanvas.width = previewW; vcanvas.height = previewH;
      const ctx = vcanvas.getContext('2d');
      const imgFull = new Image();
      imgFull.onload = ()=> ctx.drawImage(imgFull, 0, 0, vcanvas.width, vcanvas.height);
      imgFull.src = c.toDataURL('image/png');
      canvasContainer.appendChild(vcanvas);
    });
  }

  // Показываем первый слайд в превью
  if (fullCanvases[0]){
    const preview = document.createElement('canvas');
    preview.width = previewW; preview.height = previewH;
    const ctx = preview.getContext('2d');
    const img = new Image();
    img.onload = ()=> {
      ctx.drawImage(img, 0, 0, preview.width, preview.height);
      // Спрячем исходники в контейнер (для download all)
      canvasContainer.innerHTML = '';
      fullCanvases.forEach(c => { c.style.display='none'; canvasContainer.appendChild(c); });
      canvasContainer.appendChild(preview);
    };
    img.src = fullCanvases[0].toDataURL('image/png');
  }
}

/* Рендер одного слайда — теперь async */
async function drawSlideOnCanvas(canvas, text, bgSrc){
  const ctx = canvas.getContext('2d');
  const w = canvas.width, h = canvas.height;
  const { top:safeTop, bottom:safeBottom, side:sidePad } = safeAreasFor(w,h);

  // фон
  if (bgSrc){
    const img = await loadImage(bgSrc);
    drawCoverImage(ctx, img, w, h);
  } else {
    const g = ctx.createLinearGradient(0,0,0,h);
    g.addColorStop(0,'#0f1724'); g.addColorStop(1,'#07192a');
    ctx.fillStyle = g; ctx.fillRect(0,0,w,h);
  }

  // текст/подложка
  await drawText(ctx, canvas, text, {safeTop, safeBottom, sidePad});
}

async function drawText(ctx, canvas, text, {safeTop, safeBottom, sidePad}){
  const w = canvas.width, h = canvas.height;
  const backdrop = Number(backdropEl.value)||0;
  const lineHeight = Number(lineHeightEl.value)||1.2;
  let fontSize = Number(fontSizeEl.
value)||64;
  const fontName = fontEl.value || 'Lora';
  const vpos = vposEl.value;
  const hpos = hposEl.value;

  // подложка (в пределах safe)
  if (backdrop > 0){
    const padBox = 24;
    const boxY = safeTop + padBox;
    const boxH = (h - safeTop - safeBottom) - padBox*2;
    ctx.fillStyle = rgba(0,0,0,${backdrop});
    ctx.fillRect(sidePad, boxY, w - sidePad*2, boxH);
  }

  // X-позиция и выравнивание
  let x;
  switch (hpos){
    case 'left':     x = sidePad; break;
    case 'lcenter':  x = w*0.33;  break;
    case 'center':   x = w/2;     break;
    case 'rcenter':  x = w*0.67;  break;
    case 'right':    x = w - sidePad; break;
  }
  let align;
  if (hpos==='center'  hpos==='lcenter'  hpos==='rcenter') align='center';
  else if (hpos==='left') align='left';
  else align='right';

  ctx.fillStyle = '#fff';
  ctx.textAlign = align;
  ctx.textBaseline = 'alphabetic';

  // перенос по ширине
  const maxW = (align==='center') ? (w - sidePad*2) : (w - sidePad*1.5);
  const words = String(text||'').split(/\s+/).filter(Boolean);

  const wrapWithFS = (fs) => {
    ctx.font = ${fs}px ${fontName};
    const lines = [];
    let cur = '';
    for (const word of words){
      const test = cur ? cur + ' ' + word : word;
      if (ctx.measureText(test).width > maxW){
        if (cur) lines.push(cur);
        cur = word;
      } else {
        cur = test;
      }
    }
    if (cur) lines.push(cur);
    return lines;
  };

  // авто-уменьшение под высоту safe-зоны
  const availableH = h - safeTop - safeBottom;
  let lines = wrapWithFS(fontSize);
  let totalH = lines.length * fontSize * lineHeight;

  while (totalH > availableH && fontSize > MIN_FONT){
    fontSize = Math.max(MIN_FONT, Math.floor(fontSize * 0.94));
    lines = wrapWithFS(fontSize);
    totalH = lines.length * fontSize * lineHeight;
  }

  // базовая Y-координата
  const safeCenter = safeTop + availableH/2;
  let baseY;
  switch (vpos){
    case 'top':    baseY = safeTop + fontSize; break;
    case 'upper':  baseY = safeCenter - totalH/2 - availableH*0.15; break;
    case 'center': baseY = safeCenter - totalH/2; break;
    case 'lower':  baseY = safeCenter - totalH/2 + availableH*0.15; break;
    case 'bottom': baseY = safeTop + availableH - totalH; break;
    default:       baseY = safeCenter - totalH/2;
  }
  // страховка в пределах safe
  if (baseY < safeTop + fontSize) baseY = safeTop + fontSize;
  if (baseY + totalH > safeTop + availableH) baseY = safeTop + availableH - totalH;

  // рендер
  ctx.font = ${fontSize}px ${fontName};
  for (let i=0;i<lines.length;i++){
    const y = baseY + i * fontSize * lineHeight + fontSize;
    ctx.fillText(lines[i], x, y);
  }

  if (showArrow.checked){
    ctx.fillStyle = 'rgba(255,255,255,0.85)';
    ctx.beginPath();
    ctx.moveTo(w-80, h-40);
    ctx.lineTo(w-40, h-40);
    ctx.lineTo(w-60, h-20);
    ctx.closePath();
    ctx.fill();
  }
}

/* ===== Utils ===== */
function downloadDataUrl(dataUrl, filename){
  const a = document.createElement('a');
  a.href = dataUrl; a.download = filename;
  document.body.appendChild(a); a.click(); a.remove();
}

/* ===== Events ===== */
generateBtn.addEventListener('click', ()=> { renderSlides().catch(console.error); });
clearBtn.addEventListener('click', ()=>{
  inputText.value=''; slidesContainer.innerHTML=''; canvasContainer.innerHTML='';
});

/* Загружаем тяжёлые изображения эффективно:
   - используем Object URL (экономит память);
   - рендерим после того, как ВСЕ ссылки готовы;
   - освобождаем старые URL при новой загрузке. */
imageLoader.addEventListener('change', async (e)=>{
  // revoke старые
  backgrounds.forEach(b=> { try{ URL.revokeObjectURL(b.url); }catch{} });
  backgrounds = Array.from(e.target.files).map(f=> ({ url: URL.createObjectURL(f) }));
  await renderSlides().catch(console.error);
});

downloadAll.addEventListener('click', ()=>{
  const canvases = Array.from(canvasContainer.querySelectorAll('canvas')).filter(c=>c.width>0);
  if (!canvases.length) return;
  canvases.forEach((c,i)=> downloadDataUrl(c.toDataURL('image/png'), 'slide-'+(i+1)+'.png'));
});
/* ===== Init ===== */
inputText.value = "Пример для Instagram.\n\nБольшие изображения теперь грузятся корректно, рендер ждёт декодирование и даёт чёткое превью.";
renderSlides().catch(console.error);
window.addEventListener('resize', ()=> { renderSlides().catch(console.error); });
</script>
</body>
</html>
