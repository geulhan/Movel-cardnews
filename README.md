<!DOCTYPE html>

<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>모벨 카드뉴스 생성기</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;700;900&family=Noto+Serif+KR:wght@400;700&display=swap');

:root {
–dark: #1c1c1c;
–cream: #f5f0e8;
–gold: #c8b882;
–gold-light: #ddd0a0;
–panel: #181818;
–border: #2a2a2a;
–input-bg: #222;
}

- { margin: 0; padding: 0; box-sizing: border-box; }
  body { background: var(–dark); color: var(–cream); font-family: ‘Noto Sans KR’, sans-serif; min-height: 100vh; }

.header {
border-bottom: 1px solid var(–border);
padding: 14px 24px;
display: flex; align-items: center;
background: var(–dark); position: sticky; top: 0; z-index: 50;
}
.logo-mark {
width: 32px; height: 32px; border: 1.5px solid var(–gold);
display: flex; align-items: center; justify-content: center;
font-family: ‘Noto Serif KR’, serif; font-size: 13px; color: var(–gold); font-weight: 700;
margin-right: 10px;
}
.header-title { font-size: 14px; font-weight: 700; }
.header-sub { font-size: 10px; color: #555; letter-spacing: 2px; text-transform: uppercase; }

.app { display: grid; grid-template-columns: 310px 1fr; height: calc(100vh - 57px); }

/* SIDEBAR */
.sidebar {
background: var(–panel); border-right: 1px solid var(–border);
overflow-y: auto; padding: 20px 16px; display: flex; flex-direction: column; gap: 16px;
}
.s-block { background: #1e1e1e; border: 1px solid var(–border); border-radius: 6px; padding: 14px; }
.s-label { font-size: 9px; letter-spacing: 3px; color: var(–gold); text-transform: uppercase; margin-bottom: 12px; display: block; }
.field { margin-bottom: 10px; }
.field:last-child { margin-bottom: 0; }
.field label { font-size: 11px; color: #666; display: block; margin-bottom: 5px; }
.field input, .field textarea, .field select {
width: 100%; background: var(–input-bg); border: 1px solid #333;
border-radius: 4px; color: var(–cream); font-family: ‘Noto Sans KR’, sans-serif;
font-size: 12px; padding: 8px 10px; transition: border-color 0.2s;
}
.field input:focus, .field textarea:focus, .field select:focus { outline: none; border-color: var(–gold); }
.field textarea { resize: vertical; min-height: 64px; line-height: 1.6; }

.logo-upload-area {
border: 1.5px dashed #333; border-radius: 4px;
padding: 16px; text-align: center; cursor: pointer; transition: border-color 0.2s;
}
.logo-upload-area:hover { border-color: var(–gold); }
.logo-preview { max-height: 48px; max-width: 100%; object-fit: contain; display: none; }
.logo-placeholder { font-size: 11px; color: #555; }

.btn-generate {
width: 100%; background: var(–gold); color: var(–dark);
border: none; border-radius: 4px; padding: 13px;
font-family: ‘Noto Sans KR’, sans-serif; font-size: 13px; font-weight: 700;
cursor: pointer; letter-spacing: 1px; transition: background 0.2s, transform 0.1s;
}
.btn-generate:hover { background: var(–gold-light); }
.btn-generate:active { transform: scale(0.98); }
.btn-generate:disabled { background: #333; color: #666; cursor: not-allowed; }

.status { font-size: 11px; text-align: center; min-height: 16px; transition: color 0.3s; color: #555; }
.status.loading { color: var(–gold); }
.status.error { color: #e07070; }
.status.ok { color: #7ec87e; }

/* CANVAS */
.canvas-area { overflow-y: auto; padding: 28px; }
.canvas-top { display: flex; align-items: center; justify-content: space-between; margin-bottom: 20px; }
.canvas-label { font-size: 10px; letter-spacing: 3px; color: #444; text-transform: uppercase; }
.btn-save-hint {
font-size: 11px; color: var(–gold); background: transparent;
border: 1px solid #333; border-radius: 3px; padding: 6px 12px; cursor: pointer;
display: none; font-family: ‘Noto Sans KR’, sans-serif; transition: border-color 0.2s;
}
.btn-save-hint:hover { border-color: var(–gold); }
.btn-save-hint.show { display: block; }

.slides-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(260px, 1fr)); gap: 20px; }
.slide-wrap { display: flex; flex-direction: column; gap: 8px; }
.slide-label { font-size: 10px; color: #444; letter-spacing: 1px; }

/* CARD */
.card {
width: 100%; aspect-ratio: 1/1; position: relative;
overflow: hidden; border-radius: 4px; background: #111;
cursor: pointer; transition: box-shadow 0.3s;
}
.card:hover { box-shadow: 0 0 0 1px var(–gold); }
.card-bg {
position: absolute; inset: 0; background-size: cover; background-position: center;
transition: transform 0.4s ease;
}
.card:hover .card-bg { transform: scale(1.04); }
.card-overlay { position: absolute; inset: 0; }
.card-content { position: absolute; inset: 0; padding: 24px; display: flex; flex-direction: column; }
.gold-bar { position: absolute; bottom: 0; left: 0; right: 0; height: 2px; background: linear-gradient(90deg, var(–gold), transparent); }
.card-num { position: absolute; top: 18px; right: 20px; font-size: 10px; color: rgba(200,184,130,0.4); font-weight: 700; letter-spacing: 1px; }

/* COVER */
.type-cover .card-overlay { background: linear-gradient(145deg, rgba(28,28,28,0.5) 0%, rgba(28,28,28,0.88) 100%); }
.type-cover .card-content { justify-content: flex-end; }
.cover-tag { font-size: 9px; letter-spacing: 3px; color: var(–gold); text-transform: uppercase; margin-bottom: 10px; display: flex; align-items: center; gap: 7px; }
.cover-tag::before { content:’’; display:block; width:18px; height:1px; background:var(–gold); }
.cover-title { font-family: ‘Noto Serif KR’, serif; font-weight: 700; color: var(–cream); line-height: 1.4; word-break: keep-all; }
.cover-sub { color: rgba(245,240,232,0.6); line-height: 1.7; word-break: keep-all; margin-top: 8px; }

/* CONTENT */
.type-content .card-overlay { background: linear-gradient(170deg, rgba(28,28,28,0.4) 0%, rgba(28,28,28,0.85) 60%); }
.type-content .card-content { justify-content: flex-end; }
.content-title { font-family: ‘Noto Serif KR’, serif; font-weight: 700; color: var(–cream); line-height: 1.4; word-break: keep-all; margin-bottom: 8px; }
.content-body { color: rgba(245,240,232,0.72); line-height: 1.75; word-break: keep-all; }
.content-point { position: absolute; top: 18px; left: 22px; background: var(–gold); color: var(–dark); font-size: 10px; font-weight: 700; padding: 3px 8px; border-radius: 2px; letter-spacing: 1px; }

/* CTA */
.type-cta .card-overlay { background: rgba(28,28,28,0.85); }
.type-cta .card-content { justify-content: center; align-items: center; text-align: center; }
.cta-logo-img { max-width: 55%; max-height: 28%; object-fit: contain; margin-bottom: 14px; }
.cta-logo-text { font-family: ‘Noto Serif KR’, serif; font-size: 16px; font-weight: 700; color: var(–cream); letter-spacing: 1px; margin-bottom: 6px; }
.cta-divider { width: 32px; height: 1px; background: var(–gold); margin: 0 auto 10px; }
.cta-msg { color: rgba(245,240,232,0.65); line-height: 1.7; word-break: keep-all; }
.cta-address { font-size: 10px; color: #666; margin-top: 10px; line-height: 1.6; }
.gold-frame { position: absolute; inset: 16px; border: 1px solid rgba(200,184,130,0.2); border-radius: 2px; pointer-events: none; }

/* EDIT PANEL */
.edit-panel { background: #1a1a1a; border: 1px solid var(–border); border-radius: 5px; padding: 12px; display: none; }
.edit-panel.open { display: block; }
.ep-label { font-size: 9px; letter-spacing: 2px; color: var(–gold); text-transform: uppercase; margin-bottom: 10px; display: block; }
.ep-field { margin-bottom: 10px; }
.ep-field label { font-size: 10px; color: #666; display: block; margin-bottom: 4px; }
.ep-field textarea {
width: 100%; background: #222; border: 1px solid #333;
border-radius: 3px; color: var(–cream); font-family: ‘Noto Sans KR’, sans-serif;
font-size: 11px; padding: 7px 9px; resize: vertical; min-height: 52px; line-height: 1.6;
}
.ep-field textarea:focus { outline: none; border-color: var(–gold); }
.range-row { display: flex; align-items: center; gap: 8px; margin-bottom: 8px; }
.range-row label { font-size: 10px; color: #666; min-width: 36px; }
.range-row input[type=“range”] { flex: 1; accent-color: var(–gold); }
.range-val { font-size: 10px; color: var(–gold); min-width: 30px; text-align: right; }
.color-row { display: flex; gap: 6px; flex-wrap: wrap; align-items: center; }
.color-btn { width: 26px; height: 26px; border-radius: 3px; border: 2px solid transparent; cursor: pointer; transition: border-color 0.2s; }
.color-btn:hover, .color-btn.active { border-color: var(–cream); }
.ep-img-btn {
width: 100%; background: #222; border: 1px dashed #444;
color: #888; border-radius: 3px; padding: 8px;
font-size: 11px; cursor: pointer; position: relative;
font-family: ‘Noto Sans KR’, sans-serif; transition: border-color 0.2s; text-align: center;
}
.ep-img-btn:hover { border-color: var(–gold); color: var(–gold); }
.ep-img-btn input { position: absolute; inset: 0; opacity: 0; cursor: pointer; }
.ep-row { display: flex; gap: 6px; margin-top: 8px; }
.ep-row button {
flex: 1; background: #222; border: 1px solid #333; color: #888;
border-radius: 3px; padding: 7px; font-size: 10px; cursor: pointer;
font-family: ‘Noto Sans KR’, sans-serif; transition: all 0.2s;
}
.ep-row button:hover { border-color: var(–gold); color: var(–gold); }

.slide-btns { display: flex; gap: 6px; }
.slide-btn {
flex: 1; background: #1e1e1e; border: 1px solid var(–border);
color: #666; border-radius: 3px; padding: 6px;
font-size: 10px; cursor: pointer; font-family: ‘Noto Sans KR’, sans-serif; transition: all 0.2s;
}
.slide-btn:hover { border-color: var(–gold); color: var(–gold); }

.empty { grid-column: 1/-1; text-align: center; padding: 80px 20px; }
.empty-icon { font-size: 36px; margin-bottom: 14px; color: #2a2a2a; }
.empty p { font-size: 12px; line-height: 2; color: #2e2e2e; }

.loading-overlay {
position: fixed; inset: 0; background: rgba(0,0,0,0.9);
display: none; align-items: center; justify-content: center;
flex-direction: column; gap: 18px; z-index: 200;
}
.loading-overlay.show { display: flex; }
.spinner { width: 44px; height: 44px; border: 2px solid #2a2a2a; border-top-color: var(–gold); border-radius: 50%; animation: spin 0.8s linear infinite; }
@keyframes spin { to { transform: rotate(360deg); } }
.loading-step { font-size: 12px; color: #666; letter-spacing: 2px; }

.toast {
position: fixed; bottom: 28px; left: 50%; transform: translateX(-50%);
background: #2a2a2a; border: 1px solid var(–border);
color: var(–cream); font-size: 12px; padding: 10px 20px;
border-radius: 4px; z-index: 300; opacity: 0; transition: opacity 0.3s; pointer-events: none;
white-space: nowrap;
}
.toast.show { opacity: 1; }

::-webkit-scrollbar { width: 3px; }
::-webkit-scrollbar-track { background: #111; }
::-webkit-scrollbar-thumb { background: #2a2a2a; border-radius: 2px; }

@media (max-width: 760px) {
.app { grid-template-columns: 1fr; height: auto; }
.canvas-area { padding: 16px; }
.slides-grid { grid-template-columns: 1fr 1fr; gap: 12px; }
}
</style>

</head>
<body>

<div class="header">
  <div class="logo-mark">M</div>
  <div>
    <div class="header-title">모벨 카드뉴스 생성기</div>
    <div class="header-sub">Mobel Performance Training</div>
  </div>
</div>

<div class="app">
  <aside class="sidebar">

```
<div class="s-block">
  <span class="s-label">⚙ API 설정</span>
  <div class="field">
    <label>Anthropic API Key</label>
    <input type="password" id="anthropicKey" placeholder="sk-ant-..." />
  </div>
  <div class="field">
    <label>Google API Key</label>
    <input type="password" id="googleKey" placeholder="AIza..." />
  </div>
  <div class="field">
    <label>Google 검색엔진 ID (CX)</label>
    <input type="text" id="googleCx" placeholder="cx=..." />
  </div>
</div>

<div class="s-block">
  <span class="s-label">◈ 로고 등록 (마지막 슬라이드 자동 삽입)</span>
  <div class="logo-upload-area" onclick="document.getElementById('logoInput').click()">
    <input type="file" id="logoInput" accept="image/*" style="display:none" onchange="handleLogo(event)" />
    <img class="logo-preview" id="logoPreview" />
    <div class="logo-placeholder" id="logoPlaceholder">
      <div style="font-size:22px;margin-bottom:6px;color:#444;">＋</div>
      <div>로고 이미지 업로드</div>
      <div style="margin-top:4px;color:#444;font-size:10px;">PNG / JPG / SVG 권장</div>
    </div>
  </div>
</div>

<div class="s-block">
  <span class="s-label">✦ 콘텐츠 설정</span>
  <div class="field">
    <label>카드뉴스 주제</label>
    <input type="text" id="topic" placeholder="예: 고관절 가동성 운동 3가지" />
  </div>
  <div class="field">
    <label>카테고리</label>
    <select id="category">
      <option value="운동 정보">운동 정보</option>
      <option value="모벨 철학">모벨 철학</option>
      <option value="건강 정보">건강 정보</option>
      <option value="통증 & 재활">통증 & 재활</option>
      <option value="퍼포먼스">퍼포먼스</option>
    </select>
  </div>
  <div class="field">
    <label>추가 요청사항 (선택)</label>
    <textarea id="extra" placeholder="예: 초보자 대상, 친근한 말투로..."></textarea>
  </div>
</div>

<button class="btn-generate" id="genBtn" onclick="generate()">카드뉴스 생성</button>
<div class="status" id="statusMsg"></div>
```

  </aside>

  <main class="canvas-area">
    <div class="canvas-top">
      <div class="canvas-label">카드뉴스 미리보기</div>
      <button class="btn-save-hint" id="saveHint" onclick="showToast('각 슬라이드 우클릭 → 이미지로 저장 또는 스크린샷 후 1:1 크롭하세요')">💾 저장 방법</button>
    </div>
    <div class="slides-grid" id="slidesGrid">
      <div class="empty">
        <div class="empty-icon">✦</div>
        <p>API 키 입력 → 주제 입력 → 생성 버튼<br><br>
        <span style="color:#2a2a2a;">로고는 한 번만 등록하면 매번 자동 삽입됩니다</span></p>
      </div>
    </div>
  </main>
</div>

<div class="loading-overlay" id="loadingOverlay">
  <div class="spinner"></div>
  <div class="loading-step" id="loadingStep">준비 중...</div>
</div>

<div class="toast" id="toast"></div>

<script>
const BG_COLORS = ['#1c1c1c','#141414','#0f1a14','#12181f','#1a1510','#2a2520','#f5f0e8','#c8b882','#4a3f2f','#2c1810'];
let logoDataUrl = null;
let slides = [];

function handleLogo(e) {
  const file = e.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = ev => {
    logoDataUrl = ev.target.result;
    document.getElementById('logoPreview').src = logoDataUrl;
    document.getElementById('logoPreview').style.display = 'block';
    document.getElementById('logoPlaceholder').style.display = 'none';
    if (slides.length > 0) renderSlides();
    showToast('로고 등록 완료! 이후 모든 카드뉴스 마지막 슬라이드에 자동 삽입됩니다');
  };
  reader.readAsDataURL(file);
}

async function generate() {
  const aKey = document.getElementById('anthropicKey').value.trim();
  const gKey = document.getElementById('googleKey').value.trim();
  const gCx = document.getElementById('googleCx').value.trim();
  const topic = document.getElementById('topic').value.trim();
  const category = document.getElementById('category').value;
  const extra = document.getElementById('extra').value.trim();

  if (!aKey) return setStatus('Anthropic API 키를 입력해주세요', 'error');
  if (!gKey || !gCx) return setStatus('Google API 키와 CX를 입력해주세요', 'error');
  if (!topic) return setStatus('주제를 입력해주세요', 'error');

  document.getElementById('genBtn').disabled = true;
  showLoading('Claude로 카피 생성 중...');

  try {
    const copy = await generateCopy(aKey, topic, category, extra);
    setLoadingStep('Google 이미지 검색 중...');
    const images = await searchImages(gKey, gCx, topic);
    setLoadingStep('카드뉴스 완성 중...');
    slides = buildSlides(copy, images, category);
    renderSlides();
    hideLoading();
    setStatus('생성 완료! ✓', 'ok');
    document.getElementById('saveHint').classList.add('show');
  } catch (err) {
    hideLoading();
    setStatus('오류: ' + err.message, 'error');
    console.error(err);
  }
  document.getElementById('genBtn').disabled = false;
}

async function generateCopy(key, topic, category, extra) {
  const prompt = `당신은 퍼스널트레이닝 스튜디오 '모벨 퍼포먼스 트레이닝'의 인스타그램 카드뉴스 카피라이터입니다.
브랜드 철학: "통증에서 퍼포먼스로" — 움직임의 질을 높이는 것에 집중.
말투: 전문적이지만 친근하고 직접적. 과장 없이 핵심만.

주제: ${topic}
카테고리: ${category}
${extra ? '추가 요청: ' + extra : ''}

6장 카드뉴스 카피를 아래 JSON으로만 반환하세요. 다른 텍스트 없이:
{
  "slides": [
    { "type": "cover", "title": "강렬한 제목", "subtitle": "한 줄 부제목", "imageQuery": "영어 이미지 검색어" },
    { "type": "content", "point": "01", "title": "소제목", "body": "2~3줄 본문 내용", "imageQuery": "영어 이미지 검색어" },
    { "type": "content", "point": "02", "title": "소제목", "body": "2~3줄 본문 내용", "imageQuery": "영어 이미지 검색어" },
    { "type": "content", "point": "03", "title": "소제목", "body": "2~3줄 본문 내용", "imageQuery": "영어 이미지 검색어" },
    { "type": "content", "point": "04", "title": "소제목", "body": "2~3줄 본문 내용", "imageQuery": "영어 이미지 검색어" },
    { "type": "cta", "cta": "짧은 CTA 문구 한 줄", "address": "광주 남구 봉선중앙로 5, 1층" }
  ]
}`;

  const res = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'x-api-key': key,
      'anthropic-version': '2023-06-01',
      'anthropic-dangerous-direct-browser-access': 'true'
    },
    body: JSON.stringify({
      model: 'claude-sonnet-4-20250514',
      max_tokens: 1500,
      messages: [{ role: 'user', content: prompt }]
    })
  });
  if (!res.ok) { const e = await res.json(); throw new Error(e.error?.message || 'Claude API 오류'); }
  const data = await res.json();
  const text = data.content[0].text.trim().replace(/```json|```/g, '').trim();
  return JSON.parse(text);
}

async function searchImages(key, cx, topic) {
  const queries = [topic + ' fitness', topic + ' exercise', topic + ' workout', topic + ' health'];
  const images = [];
  for (const q of queries) {
    try {
      const url = `https://www.googleapis.com/customsearch/v1?key=${key}&cx=${cx}&q=${encodeURIComponent(q)}&searchType=image&num=2&imgSize=large&rights=cc_publicdomain|cc_attribute`;
      const res = await fetch(url);
      const data = await res.json();
      if (data.items) data.items.forEach(item => images.push(item.link));
    } catch(e) {}
    if (images.length >= 8) break;
  }
  return images;
}

function buildSlides(copy, images, category) {
  return copy.slides.map((s, i) => ({
    ...s,
    bgColor: '#1c1c1c',
    imageUrl: images[i] || null,
    titleSize: s.type === 'cover' ? 22 : s.type === 'cta' ? 14 : 18,
    bodySize: 13,
    category
  }));
}

function renderSlides() {
  const grid = document.getElementById('slidesGrid');
  grid.innerHTML = '';
  slides.forEach((slide, idx) => {
    const wrap = document.createElement('div');
    wrap.className = 'slide-wrap';
    wrap.innerHTML = `
      <div class="slide-label">SLIDE ${String(idx+1).padStart(2,'0')} / 06<\/div>
      <div class="card ${getClass(slide.type)}" id="card-${idx}" style="background:${slide.bgColor}">
        <div class="card-bg" id="bg-${idx}" style="${slide.imageUrl ? `background-image:url('${slide.imageUrl}')` : ''}"><\/div>
        <div class="card-overlay"><\/div>
        ${cardHTML(slide, idx)}
        <div class="gold-bar"><\/div>
      <\/div>
      <div class="slide-btns">
        <button class="slide-btn" onclick="toggleEdit(${idx})">✎ 편집<\/button>
        <button class="slide-btn" onclick="showToast('우클릭 → 이미지로 저장, 또는 스크린샷 후 크롭하세요')">↓ 저장<\/button>
      <\/div>
      <div class="edit-panel" id="ep-${idx}">${editHTML(slide, idx)}<\/div>
    `;
    grid.appendChild(wrap);
  });
}

function getClass(t) { return t==='cover'?'type-cover':t==='cta'?'type-cta':'type-content'; }

function cardHTML(s, i) {
  if (s.type === 'cover') return `
    <div class="card-content">
      <div class="cover-tag">${s.category}<\/div>
      <div class="cover-title" id="t-${i}" style="font-size:${s.titleSize}px">${s.title}<\/div>
      <div class="cover-sub" id="b-${i}" style="font-size:${s.bodySize}px">${s.subtitle||''}<\/div>
    <\/div>
    <div class="card-num">01<\/div>`;
  if (s.type === 'cta') return `
    <div class="card-content">
      ${logoDataUrl ? `<img class="cta-logo-img" src="${logoDataUrl}" />` : `<div class="cta-logo-text">모벨 퍼포먼스 트레이닝</div>`}
      <div class="cta-divider"><\/div>
      <div class="cta-msg" id="t-${i}" style="font-size:${s.titleSize}px">${s.cta||''}<\/div>
      <div class="cta-address" id="b-${i}">${s.address||'광주 남구 봉선중앙로 5, 1층'}<\/div>
    <\/div>
    <div class="gold-frame"><\/div>`;
  return `
    <div class="card-content">
      <div class="content-title" id="t-${i}" style="font-size:${s.titleSize}px">${s.title}<\/div>
      <div class="content-body" id="b-${i}" style="font-size:${s.bodySize}px">${s.body||''}<\/div>
    <\/div>
    <div class="content-point">${s.point||'0'+i}<\/div>
    <div class="card-num">0${i+1}<\/div>`;
}

function editHTML(s, i) {
  const tLabel = s.type==='cover'?'제목':s.type==='cta'?'CTA 문구':'소제목';
  const bLabel = s.type==='cover'?'부제목':s.type==='cta'?'주소/정보':'본문';
  const tVal = s.type==='cta' ? (s.cta||'') : (s.title||'');
  const bVal = s.type==='cover' ? (s.subtitle||'') : s.type==='cta' ? (s.address||'') : (s.body||'');
  return `
    <span class="ep-label">✦ 슬라이드 ${i+1} 편집<\/span>
    <div class="ep-field">
      <label>${tLabel}<\/label>
      <textarea oninput="upText(${i},'t',this.value)">${tVal}<\/textarea>
    <\/div>
    <div class="ep-field">
      <label>${bLabel}<\/label>
      <textarea oninput="upText(${i},'b',this.value)">${bVal}<\/textarea>
    <\/div>
    <div class="ep-field">
      <label>글씨 크기<\/label>
      <div class="range-row">
        <label>제목<\/label>
        <input type="range" min="11" max="36" value="${s.titleSize}" oninput="upSize(${i},'t',this.value);this.nextElementSibling.textContent=this.value+'px'">
        <span class="range-val">${s.titleSize}px<\/span>
      <\/div>
      <div class="range-row">
        <label>본문<\/label>
        <input type="range" min="9" max="20" value="${s.bodySize}" oninput="upSize(${i},'b',this.value);this.nextElementSibling.textContent=this.value+'px'">
        <span class="range-val">${s.bodySize}px<\/span>
      <\/div>
    <\/div>
    <div class="ep-field">
      <label>배경색<\/label>
      <div class="color-row">
        ${BG_COLORS.map(c=>`<div class="color-btn ${s.bgColor===c?'active':''}" style="background:${c}" title="${c}" onclick="upBg(${i},'${c}',this)"></div>`).join('')}
        <input type="color" value="${s.bgColor}" title="직접 선택" oninput="upBg(${i},this.value,null)" style="width:26px;height:26px;border:none;background:none;cursor:pointer;padding:0;border-radius:3px;">
      <\/div>
    <\/div>
    ${s.type!=='cta'?`
    <div class="ep-field">
      <label>배경 이미지 교체</label>
      <div class="ep-img-btn">📁 이미지 파일 업로드<input type="file" accept="image/*" onchange="upImg(${i},event)"></div>
    </div>`:''}
    <div class="ep-row">
      <button onclick="clearImg(${i})">이미지 제거<\/button>
      <button onclick="toggleEdit(${i})">닫기<\/button>
    <\/div>`;
}

function toggleEdit(i) {
  const ep = document.getElementById(`ep-${i}`);
  const open = ep.classList.contains('open');
  document.querySelectorAll('.edit-panel').forEach(p => p.classList.remove('open'));
  if (!open) ep.classList.add('open');
}

function upText(i, part, val) {
  const el = document.getElementById(`${part}-${i}`);
  if (el) el.textContent = val;
  if (part==='t') { slides[i].title = val; slides[i].cta = val; }
  else { slides[i].subtitle = val; slides[i].body = val; slides[i].address = val; }
}
function upSize(i, part, val) {
  const el = document.getElementById(`${part}-${i}`);
  if (el) el.style.fontSize = val + 'px';
  if (part==='t') slides[i].titleSize = +val; else slides[i].bodySize = +val;
}
function upBg(i, color, btn) {
  slides[i].bgColor = color;
  const card = document.getElementById(`card-${i}`);
  if (card) card.style.background = color;
  if (btn) { document.querySelectorAll(`#ep-${i} .color-btn`).forEach(b=>b.classList.remove('active')); btn.classList.add('active'); }
}
function upImg(i, e) {
  const f = e.target.files[0]; if (!f) return;
  const r = new FileReader();
  r.onload = ev => {
    slides[i].imageUrl = ev.target.result;
    const bg = document.getElementById(`bg-${i}`);
    if (bg) bg.style.backgroundImage = `url('${ev.target.result}')`;
    showToast('이미지 교체 완료');
  };
  r.readAsDataURL(f);
}
function clearImg(i) {
  slides[i].imageUrl = null;
  const bg = document.getElementById(`bg-${i}`);
  if (bg) bg.style.backgroundImage = '';
}

function setStatus(msg, type) {
  const el = document.getElementById('statusMsg');
  el.textContent = msg; el.className = 'status ' + (type||'');
}
function showLoading(step) { document.getElementById('loadingOverlay').classList.add('show'); document.getElementById('loadingStep').textContent = step; }
function setLoadingStep(step) { document.getElementById('loadingStep').textContent = step; }
function hideLoading() { document.getElementById('loadingOverlay').classList.remove('show'); }
function showToast(msg) {
  const t = document.getElementById('toast');
  t.textContent = msg; t.classList.add('show');
  setTimeout(() => t.classList.remove('show'), 3500);
}

document.getElementById('topic').addEventListener('keydown', e => { if (e.key === 'Enter') generate(); });
</script>

</body>
</html>
