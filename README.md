## Cześć 👋

Pyszne i Zdrowe 💚
<html lang="ru">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Insta Slides — PyszneIZdrowe</title>
<link href="https://fonts.googleapis.com/css2?family=Lora:wght@400;700&family=Montserrat:wght@400;700&display=swap" rel="stylesheet">
<style>
  :root{ --bg:#0f1724; --panel:#0b1220; --accent:#f59e0b; --muted:#cbd5e1; }
  *{ box-sizing:border-box }
  body{ margin:0; font-family:Montserrat,system-ui,Arial;
    background:linear-gradient(180deg,#071024 0%, #07192a 100%);
    color:var(--muted); min-height:100vh; display:flex; gap:18px; padding:18px; }
  .left{ width:420px; max-width:40vw; background:var(--panel); padding:16px;
    border-radius:10px; box-shadow:0 6px 20px rgba(2,6,23,.6); }
  h1{ margin:0 0 12px 0; font-size:18px; color:#fff }
  label{ display:block; margin-top:10px; font-size:13px; color:#cbd5e1 }
  textarea{ width:100%; height:140px; padding:8px; border-radius:6px;
    border:1px solid rgba(255,255,255,.06); background:#071426; color:#fff; resize:vertical; }
  .small{ font-size:13px; padding:6px; border-radius:6px }
  .btn{ background:var(--accent); border:none; color:#071022; padding:8px 10px;
    border-radius:8px; cursor:pointer; font-weight:700 }
  .panel-row{ display:flex; gap:8px; align-items:center; margin-top:8px }
  .preview{ flex:1; display:grid; place-items:center; gap:12px }
  .canvas-wrap{ background:#000; padding:10px; border-radius:12px }
  .slides{ display:flex; gap:8px; flex-wrap:wrap }
  .slide{ width:180px; height:225px; border-radius:8px; overflow:hidden;
    border:1px solid rgba(255,255,255,.06); position:relative;
    display:flex; align-items:center; justify-content:center; }
  .slide img{ width:100%; height:100%; object-fit:cover; display:block }
  .slide .text{ position:absolute; left:12px; right:12px; bottom:12px; color:#fff;
    font-family:Lora,serif; text-shadow:0 6px 20px rgba(0,0,0,.6); font-size:20px; }
  footer{ margin-top:12px; font-size:12px; color:#93c5fd }
  .note{ font-size:12px; color:#94a3b8; margin-top:8px }
  .input-file{ position:fixed; left:-9999px; top:-9999px; width:1px; height:1px;
    opacity:0; pointer-events:none; }
  .canvas-wrap{ width:100%; }
  .canvas-wrap canvas{ width:100%; height:auto; display:block; }
  @media (max-width: 900px){
    body{ flex-direction: column; gap:12px; padding:12px; overflow-x:hidden; }
    .left{ width:100%; max-width:none; }
  }
</style>
</head>
<body>

<div class="left">
  <h1>Insta Slides — PyszneIZdrowe</h1>

  <label>Вставьте текст</label>
  <textarea id="inputText" placeholder="Абзац = один слайд"></textarea>

  <div class="panel-row">
    <div style="flex:1">
      <label>Шрифт</label>
      <select id="font" class="small">
        <option value="Lora">Lora (serif)</option>
        <option value="Montserrat">Montserrat (sans)</option>
      </select>
    </div>
    <div style="width:120px">
      <label>Выравнивание</label>
      <select id="align" class="small">
        <option value="center">Центр</option>
        <option value="left">Слева</option>
        <option value="right">Справа</option>
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
      <input id="lineHeight" type="number" class="small" value="1.2" step="0.05" min="1" max="2">
    </div>
  </div>

  <div class="panel-row">
    <div style="flex:1">
      <label>Позиция текста</label>
      <select id="vpos" class="small">
        <option value="top">Сверху</option>
        <option value="upper">Выше центра</option>
        <option value="center" selected>По центру</option>
        <option value="lower">Ниже центра</option>
        <option value="bottom">Внизу</option>
      </select>
    </div>
  </div>

  <div style="margin-top:10px;display:flex;gap:8px;align-items:center;flex-wrap:wrap">
    <button class="btn" id="generate">Update</button>
<button id="clear" class="small">Clear</button>
    <label class="small" style="display:inline-flex;align-items:center;gap:6px;margin:0">
      <input id="showArrow" type="checkbox"> Стрелка Swipe
    </label>
  </div>

  <label style="margin-top:12px">Фоны</label>
  <label for="imageLoader" class="small btn" style="cursor:pointer;">Загрузить</label>
  <input id="imageLoader" class="input-file" type="file" accept="image/*" multiple>

  <div class="panel-row" style="margin-top:10px">
    <button id="downloadAll" class="small">Скачать все (PNG)</button>
  </div>

  <div class="note">Удерживайте миниатюру, чтобы скачать её.</div>
</div>

<div class="preview">
  <div class="canvas-wrap" id="canvasContainer"></div>
  <div class="slides" id="slidesContainer"></div>
</div>

<script>
const inputText=document.getElementById('inputText');
const fontEl=document.getElementById('font');
const alignEl=document.getElementById('align');
const fontSizeEl=document.getElementById('fontSize');
const lineHeightEl=document.getElementById('lineHeight');
const vposEl=document.getElementById('vpos');
const paddingEl=document.getElementById('padding');
const backdropEl=document.getElementById('backdrop');
const showArrow=document.getElementById('showArrow');
const generateBtn=document.getElementById('generate');
const clearBtn=document.getElementById('clear');
const downloadAll=document.getElementById('downloadAll');
const slidesContainer=document.getElementById('slidesContainer');
const canvasContainer=document.getElementById('canvasContainer');
const imageLoader=document.getElementById('imageLoader');

let backgrounds=[];

function parseSlides(text){
  return text.split(/\n{2,}/).map(p=>p.trim()).filter(Boolean);
}
function sizeFromSelect(){ return {w:1080,h:1920}; } // фиксируем insta-story

function drawCoverImage(ctx,img,w,h){
  const iw=img.width,ih=img.height;
  const scale=Math.max(w/iw,h/ih);
  const dw=iw*scale,dh=ih*scale;
  const dx=(w-dw)/2,dy=(h-dh)/2;
  ctx.drawImage(img,dx,dy,dw,dh);
}

function renderSlides(){
  const slides=parseSlides(inputText.value);
  slidesContainer.innerHTML=''; canvasContainer.innerHTML='';
  if(!slides.length){ return; }
  const {w,h}=sizeFromSelect();
  slides.forEach((txt,idx)=>{
    const c=document.createElement('canvas');
    c.width=w; c.height=h;
    const bg=backgrounds.length?backgrounds[idx%backgrounds.length]:null;
    drawSlideOnCanvas(c,txt,bg);
    const thumb=document.createElement('div'); thumb.className='slide';
    const img=new Image(); img.src=c.toDataURL('image/png'); thumb.appendChild(img);
    slidesContainer.appendChild(thumb);
    c.style.display='none'; canvasContainer.appendChild(c);
  });
}

function drawSlideOnCanvas(canvas,text,bgData){
  const ctx=canvas.getContext('2d'); const {w,h}=canvas;
  if(bgData){ const img=new Image(); img.onload=()=>{ drawCoverImage(ctx,img,w,h); drawText(); }; img.src=bgData; }
  else { ctx.fillStyle='#07192a'; ctx.fillRect(0,0,w,h); drawText(); }
  function drawText(){
    let fontSize=Number(fontSizeEl.value)||48;
    const lineHeight=Number(lineHeightEl.value)||1.2;
    ctx.font=`${fontSize}px ${fontEl.value}`;
    ctx.fillStyle='#fff'; ctx.textAlign=alignEl.value; ctx.textBaseline='alphabetic';
    const words=text.split(/\s+/); let lines=['']; words.forEach(word=>{
      const test=lines[lines.length-1]+' '+word;
      if(ctx.measureText(test).width>w-100){ lines.push(word); }
      else { lines[lines.length-1]=test.trim(); }
    });
    const totalH=lines.length*fontSize*lineHeight;
    const safeTop=200, safeBottom=200;
    let baseY;
    switch(vposEl.value){
      case 'top': baseY=safeTop+fontSize; break;
      case 'upper': baseY=h/2-totalH/2 - h*0.15; break;
      case 'center': baseY=h/2-totalH/2; break;
      case 'lower': baseY=h/2-totalH/2 + h*0.15; break;
      case 'bottom': baseY=h-safeBottom-totalH; break;
    }
    let x=(alignEl.value==='center')?w/2:(alignEl.value==='left'?50:w-50);
    lines.forEach((line,i)=> ctx.fillText(line,x,baseY+i*fontSize*lineHeight));
    if(showArrow.checked){ ctx.
fillRect(w/2-40,h-60,80,5); }
  }
}

function downloadDataUrl(url,fn){
  const a=document.createElement('a'); a.href=url; a.download=fn;
  document.body.appendChild(a); a.click(); a.remove();
}

/* events */
generateBtn.addEventListener('click',renderSlides);
clearBtn.addEventListener('click',()=>{ inputText.value=''; renderSlides(); });
imageLoader.addEventListener('change',e=>{
  backgrounds=[]; Array.from(e.target.files).forEach(file=>{
    const reader=new FileReader(); reader.onload=()=>{ backgrounds.push(reader.result); renderSlides(); };
    reader.readAsDataURL(file);
  });
});
downloadAll.addEventListener('click',()=>{
  const canvases=Array.from(canvasContainer.querySelectorAll('canvas')).filter(c=>c.width>0);
  canvases.forEach((c,i)=> downloadDataUrl(c.toDataURL('image/png'),'slide-'+(i+1)+'.png'));
});

/* init */
inputText.value="Пример для Instagram сторис.\n\nТеперь текст можно двигать по высоте!";
renderSlides();
</script>
</body>
</html>

