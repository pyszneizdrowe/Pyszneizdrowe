## Cześć 👋

<!doctype html>
<html lang="ru">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Insta Slides — PyszneIZdrowe</title>

<link href="https://fonts.googleapis.com/css2?family=Lora:wght@400;700&family=Montserrat:wght@400;700&display=swap" rel="stylesheet">

<style>
  :root{
    --bg:#0f1724; --panel:#0b1220; --accent:#f59e0b; --muted:#cbd5e1;
  }
  *{box-sizing:border-box}
  body{
    margin:0;
    font-family:Montserrat,system-ui,Arial;
    background:linear-gradient(180deg,#071024 0%, #07192a 100%);
    color:var(--muted);
    min-height:100vh;
    display:flex;
    gap:18px;
    padding:18px
  }
  .left{
    width:420px;
    max-width:40vw;
    background:var(--panel);
    padding:16px;
    border-radius:10px;
    box-shadow:0 6px 20px rgba(2,6,23,.6)
  }
  h1{margin:0 0 12px 0;font-size:18px;color:#fff}
  label{display:block;margin-top:10px;font-size:13px;color:#cbd5e1}
  textarea{
    width:100%;height:140px;padding:8px;border-radius:6px;
    border:1px solid rgba(255,255,255,.06);
    background:#071426;color:#fff;resize:vertical
  }
  .controls{display:flex;gap:8px;flex-wrap:wrap}
  .controls > *{flex:1 1 48%}
  .small{font-size:13px;padding:6px;border-radius:6px}
  .btn{
    background:var(--accent);border:none;color:#071022;
    padding:8px 10px;border-radius:8px;cursor:pointer;font-weight:700
  }
  .panel-row{display:flex;gap:8px;align-items:center;margin-top:8px}
  .preview{flex:1;display:grid;place-items:center;gap:12px}
  .canvas-wrap{background:#000;padding:10px;border-radius:12px}
  .slides{display:flex;gap:8px;flex-wrap:wrap}
  .slide{
    width:180px;height:225px;border-radius:8px;overflow:hidden;
    border:1px solid rgba(255,255,255,.06);
    position:relative;display:flex;align-items:center;justify-content:center
  }
  .slide img{width:100%;height:100%;object-fit:cover;display:block}
  .slide .text{
    position:absolute;left:12px;right:12px;bottom:12px;color:#fff;
    font-family:Lora,serif;text-shadow:0 6px 20px rgba(0,0,0,.6);font-size:20px
  }
  .input-file{display:none}
  footer{margin-top:12px;font-size:12px;color:#93c5fd}
  .range{width:100%}
  .option{display:flex;gap:8px;align-items:center}
  .note{font-size:12px;color:#94a3b8;margin-top:8px}

  /* === ДОБАВЛЕНО для адаптивности === */
  .canvas-wrap{ width:100%; }
  .canvas-wrap canvas{
    width:100%;
    height:auto;
    display:block;
  }

  @media (max-width: 900px){
    body{
      flex-direction: column;
      gap:12px;
      padding:12px;
      overflow-x:hidden;
    }
    .left{
      width:100%;
      max-width:none;
    }
  }
</style>
</head>
<body>

<div class="left">
  <h1>Insta Slides — PyszneIZdrowe</h1>

  <label>Вставьте текст — один абзац = один слайд</label>
  <textarea id="inputText" placeholder="Вставьте текст здесь..."></textarea>

  <div class="panel-row">
    <div style="flex:1">
      <label>Шрифт</label>
      <select id="font" class="small">
        <option value="Lora">Lora (serif)</option>
        <option value="Montserrat">Montserrat (sans)</option>
        <option value="system-ui">System</option>
      </select>
    </div>
    <div style="width:120px">
      <label>Выравнивание</label>
      <select id="align" class="small">
        <option value="center">Center</option>
        <option value="left">Left</option>
        <option value="right">Right</option>
      </select>
    </div>
  </div>

  <div class="panel-row">
    <div style="flex:1">
      <label>Размер шрифта (px)</label>
      <input id="fontSize" type="number" class="small" value="48" min="14" max="120">
    </div>
    <div style="width:140px">
      <label>Интерлиньяж</label>
      <input id="lineHeight" type="number" class="small" value="1.15" step="0.05" min="1" max="2">
    </div>
  </div>

  <div class="panel-row">
    <div style="flex:1">
      <label>Отступ текста (px)</label>
      <input id="padding" type="number" class="small" value="48" min="4" max="220">
    </div>
    <div style="width:140px">
      <label>Прозрачность задника</label><input id="backdrop" type="number" class="small" value="0" min="0" max="1" step="0.05">
    </div>
  </div>

  <div class="panel-row">
    <div style="flex:1">
      <label>Радиус углов (px)</label>
      <input id="radius" type="number" class="small" value="20" min="0" max="200">
    </div>
    <div style="width:140px">
      <label>Ширина холста</label>
      <select id="size" class="small">
        <option value="1080x1350">1080×1350 (4:5)</option>
        <option value="1080x1080">1080×1080 (1:1)</option>
        <option value="1080x1920">1080×1920 (9:16)</option>
      </select>
    </div>
  </div>

  <div style="margin-top:10px;display:flex;gap:8px">
    <button class="btn" id="generate">Update Slides</button>
    <button id="clear" class="small">Clear</button>
    <label style="display:inline-block" class="small">
      <input id="showArrow" type="checkbox"> Показать стрелку "Swipe"
    </label>
  </div>

  <label style="margin-top:12px">Загрузить изображения (можно менять по слайду)</label>
  <input id="imageLoader" class="input-file" type="file" accept="image/*" multiple>
  <div class="panel-row">
    <button id="addImages" class="small">Загрузить фоны</button>
    <button id="downloadAll" class="small">Скачать все (ZIP)</button>
  </div>

  <div class="note">Нажмите и удерживайте миниатюру слайда, чтобы скачать её как изображение.</div>
  <footer>Сделано для <strong>pyszneizdrowe.io</strong></footer>
</div>

<div class="preview">
  <div class="canvas-wrap" id="canvasContainer">
    <!-- rendered canvas will appear here scaled -->
  </div>

  <div class="slides" id="slidesContainer"></div>
</div>

<script>
/* Simple slide generator: split textarea by blank-line or newline -> create canvases */
const inputText = document.getElementById('inputText');
const generateBtn = document.getElementById('generate');
const clearBtn = document.getElementById('clear');
const slidesContainer = document.getElementById('slidesContainer');
const canvasContainer = document.getElementById('canvasContainer');
const fontEl = document.getElementById('font');
const alignEl = document.getElementById('align');
const fontSizeEl = document.getElementById('fontSize');
const lineHeightEl = document.getElementById('lineHeight');
const paddingEl = document.getElementById('padding');
const radiusEl = document.getElementById('radius');
const sizeEl = document.getElementById('size');
const backdropEl = document.getElementById('backdrop');
const imageLoader = document.getElementById('imageLoader');
const addImages = document.getElementById('addImages');
const showArrow = document.getElementById('showArrow');
const downloadAll = document.getElementById('downloadAll');

function previewScaleFor(w){
  const available = canvasContainer.clientWidth || window.innerWidth;
  return Math.min(available / w, 1); // не увеличиваем >100%
}

let backgrounds = []; // File objects or dataURLs

function parseSlides(text){
  // split by two newlines or each newline if single line paragraphs
  const parts = text.split(/\n{2,}/).map(p=>p.trim()).filter(Boolean);
  if(parts.length===0 && text.trim()!==''){
    // split by single newline fallback
    return text.split(/\n/).map(p=>p.trim()).filter(Boolean);
  }
  return parts;
}

function sizeFromSelect(){
  const v = sizeEl.value.split('x');
  return {w: parseInt(v[0]), h: parseInt(v[1])};
}

function renderSlides(){
  const slides = parseSlides(inputText.value);
  slidesContainer.innerHTML = '';
  canvasContainer.innerHTML = '';
  if (slides.length === 0) {
    canvasContainer.innerHTML = '<div style="color:#9aaed0;padding:18px">Вставьте текст и нажмите Update Slides</div>';
    return;
  }

  const { w, h } = sizeFromSelect();
  const scale = previewScaleFor(w);
  const previewW = Math.round(w * scale);
  const previewH = Math.round(h * scale);

  slides.forEach((txt, idx) => {
    // полный канвас (для экспорта / источника превью)
    const c = document.createElement('canvas');
    c.width = w; c.height = h;
    c.dataset.index = idx;
    drawSlideOnCanvas(c, txt, backgrounds[idx] || null);// миниатюра ~225px по высоте с сохранением пропорций
    const thumb = document.createElement('div');
    thumb.className = 'slide';
    const targetH = 225;
    const targetW = Math.round(targetH * (w / h));
    thumb.style.width = targetW + 'px';
    thumb.style.height = targetH + 'px';

    const img = new Image();
    img.src = c.toDataURL('image/jpeg', 0.92);
    thumb.appendChild(img);

    const textEl = document.createElement('div');
    textEl.className = 'text';
    textEl.innerText = txt.length > 80 ? txt.slice(0, 80) + '…' : txt;
    thumb.appendChild(textEl);

    slidesContainer.appendChild(thumb);

    // долгое нажатие — скачать этот слайд
    let pressTimer = null;
    const downloadCurrent = () => downloadDataUrl(c.toDataURL('image/png'), 'slide-' + (idx + 1) + '.png');
    thumb.addEventListener('mousedown', (e) => { e.preventDefault(); pressTimer = setTimeout(downloadCurrent, 700); });
    ['mouseup','mouseleave'].forEach(ev => thumb.addEventListener(ev, () => clearTimeout(pressTimer)));
    thumb.addEventListener('touchstart', () => { pressTimer = setTimeout(downloadCurrent, 700); });
    thumb.addEventListener('touchend', () => clearTimeout(pressTimer));

    // клик по миниатюре — показать большой превью
    thumb.addEventListener('click', () => {
      canvasContainer.innerHTML = '';
      const vcanvas = document.createElement('canvas');
      vcanvas.width = previewW;
      vcanvas.height = previewH;
      const ctx = vcanvas.getContext('2d');
      const imgFull = new Image();
      imgFull.onload = () => { ctx.drawImage(imgFull, 0, 0, vcanvas.width, vcanvas.height); };
      imgFull.src = c.toDataURL('image/jpeg', 0.95);
      canvasContainer.appendChild(vcanvas);
    });

    // прячем полный канвас (нужен для экспорта всех)
    c.style.display = 'none';
    canvasContainer.appendChild(c);
  });

  // первый слайд — сразу показать в большом превью
  const firstCanvas = canvasContainer.querySelector('canvas[style*="display: none"]');
  if (firstCanvas) {
    const preview = document.createElement('canvas');
    preview.width = previewW;
    preview.height = previewH;
    const ctx = preview.getContext('2d');
    const img = new Image();
    img.onload = () => ctx.drawImage(img, 0, 0, preview.width, preview.height);
    img.src = firstCanvas.toDataURL('image/jpeg', 0.95);
    canvasContainer.innerHTML = '';
    canvasContainer.appendChild(preview);
  }
}

function drawSlideOnCanvas(canvas, text, bgData){
  const ctx = canvas.getContext('2d');
  const w = canvas.width;
  const h = canvas.height;

  // background
  if (bgData) {
    if (typeof bgData === 'string') {
      const img = new Image();
      img.onload = ()=> { ctx.drawImage(img,0,0,w,h); drawTextBlock(); };
      img.src = bgData;
    } else if (bgData instanceof HTMLImageElement) {
      ctx.drawImage(bgData,0,0,w,h);
      drawTextBlock();
    } else {
      // file object - create dataURL
      const reader = new FileReader();
      reader.onload = ()=> {
        const img = new Image();
        img.onload = ()=> { ctx.drawImage(img,0,0,w,h); drawTextBlock(); };
        img.src = reader.result;
      };
      reader.readAsDataURL(bgData);
    }
  } else {
    // default gradient
    const g = ctx.createLinearGradient(0,0,0,h);
    g.addColorStop(0,'#0f1724'); g.addColorStop(1,'#07192a');
    ctx.fillStyle = g;
    ctx.fillRect(0,0,w,h);
    drawTextBlock();
  }

  function drawTextBlock(){
    // backdrop opacity
    const pad = Number(paddingEl.value)||48;
    const radius = Number(radiusEl.value)||20; // (пока не используем, но оставим для будущего)
    const backdrop = Number(backdropEl.value)||0;
    if (backdrop > 0) {
      ctx.fillStyle = rgba(0,0,0,${backdrop});
      ctx.fillRect(0, h - Math.min(h*0.6, pad*8) - pad, w, Math.min(h*0.6, pad*8) + pad);
    }

    // text
    let fontSize = Number(fontSizeEl.value)||48;
    const lineHeight = Number(lineHeightEl.value)||1.15;
    const fontName = fontEl.value || 'Lora';
    ctx.fillStyle = '#fff';
    ctx.textAlign = alignEl.value || 'center';
    ctx.
    textBaseline = 'bottom';
    ctx.font = ${fontSize}px ${fontName};

    // wrap text
    const maxW = w - pad*2;
    const words = text.split(/\s+/);
    let lines = [];
    let cur = '';
    const recomputeLines = () => {
      lines = [];
      cur = '';
      words.forEach(word=>{
        const test = cur ? cur + ' ' + word : word;
        if (ctx.measureText(test).width > maxW) {
          if (cur) lines.push(cur);
          cur = word;
        } else cur = test;
      });
      if (cur) lines.push(cur);
    };
    recomputeLines();

    // adjust font size if lines too many
    const maxLines = Math.floor((h - pad*2) / (fontSize * lineHeight));
    while (lines.length > maxLines && fontSize > 14) {
      fontSize = Math.floor(fontSize * 0.94);
      ctx.font = ${fontSize}px ${fontName};
      recomputeLines();
    }

    // draw lines bottom-up with padding
    let x;
    if (alignEl.value === 'center') x = w/2;
    else if (alignEl.value === 'left') x = pad;
    else x = w - pad;

    const totalH = lines.length * fontSize * lineHeight;
    let startY = h - pad - ((lines.length-1) * fontSize * lineHeight);

    ctx.fillStyle = '#fff';
    for (let i=0;i<lines.length;i++){
      const y = startY + i * fontSize * lineHeight + fontSize;
      ctx.fillText(lines[i], x, y);
    }

    // optional swipe arrow
    if (showArrow.checked) {
      ctx.fillStyle = 'rgba(255,255,255,0.85)';
      ctx.beginPath();
      ctx.moveTo(w-80, h-40);
      ctx.lineTo(w-40, h-40);
      ctx.lineTo(w-60, h-20);
      ctx.closePath();
      ctx.fill();
    }
  }
}

function downloadDataUrl(dataUrl, filename){
  const a = document.createElement('a');
  a.href = dataUrl;
  a.download = filename;
  document.body.appendChild(a);
  a.click();
  a.remove();
}

generateBtn.addEventListener('click', ()=> renderSlides());
clearBtn.addEventListener('click', ()=> { inputText.value=''; slidesContainer.innerHTML=''; canvasContainer.innerHTML=''; });

addImages.addEventListener('click', ()=> imageLoader.click());
imageLoader.addEventListener('change', (e)=>{
  const files = Array.from(e.target.files);
  backgrounds = files; // по индексу к слайдам
  renderSlides();
});

// download all: простой вариант — скачать по одному (кнопка названа ZIP, но без JSZip сохраняем поштучно)
downloadAll.addEventListener('click', ()=>{
  const canvases = Array.from(canvasContainer.querySelectorAll('canvas')).filter(c=>c.width>0);
  canvases.forEach((c,i)=> {
    const url = c.toDataURL('image/png');
    downloadDataUrl(url, 'slide-'+(i+1)+'.png');
  });
});

// initial sample
inputText.value = "Добро пожаловать в Pyszne i Zdrowe!\n\nМы готовим вкусные полуфабрикаты без лишнего сахара.\n\nСоветы по хранению и рецепту — добавьте сюда текст.";
renderSlides();
window.addEventListener('resize', renderSlides);
</script>
</body>
</html>
