<!DOCTYPE html>
<html lang="zh">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🦴 BoneScan AI — 骨折智能分析</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Noto+Sans+SC:wght@300;400;600;700&family=Noto+Sans:wght@300;400;600&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<style>
:root {
  --bg:#0a0e1a; --bg2:#111827; --card:#151c2e; --border:#1e2d4a;
  --accent:#00d4ff; --accent2:#7c3aed; --accent3:#10b981;
  --text:#e2e8f0; --text2:#8899bb; --danger:#ef4444; --warn:#f59e0b;
  --grid:rgba(0,212,255,0.03);
  --mono:'Space Mono',monospace;
  --sans:'Noto Sans SC','Noto Sans',sans-serif;
}
*{box-sizing:border-box;margin:0;padding:0;}
body{background:var(--bg);color:var(--text);font-family:var(--sans);min-height:100vh;overflow-x:hidden;
  background-image:linear-gradient(var(--grid) 1px,transparent 1px),linear-gradient(90deg,var(--grid) 1px,transparent 1px);
  background-size:40px 40px;}
.orb{position:fixed;border-radius:50%;filter:blur(80px);pointer-events:none;z-index:0;opacity:0.12;animation:drift 18s ease-in-out infinite alternate;}
.orb1{width:600px;height:600px;background:var(--accent2);top:-200px;left:-200px;}
.orb2{width:500px;height:500px;background:var(--accent);bottom:-150px;right:-150px;animation-delay:-9s;}
@keyframes drift{from{transform:translate(0,0)}to{transform:translate(60px,40px)}}
.container{max-width:1200px;margin:0 auto;padding:0 24px;position:relative;z-index:1;}

/* HEADER */
header{border-bottom:1px solid var(--border);backdrop-filter:blur(20px);background:rgba(10,14,26,0.9);position:sticky;top:0;z-index:100;}
.header-inner{display:flex;align-items:center;justify-content:space-between;padding:14px 24px;max-width:1200px;margin:0 auto;flex-wrap:wrap;gap:10px;}
.logo{font-family:var(--mono);font-size:1.1rem;font-weight:700;color:var(--accent);display:flex;align-items:center;gap:8px;}
.logo-dot{width:10px;height:10px;border-radius:50%;background:var(--accent);box-shadow:0 0 12px var(--accent);animation:pulse 2s ease-in-out infinite;}
@keyframes pulse{0%,100%{opacity:1;transform:scale(1)}50%{opacity:0.5;transform:scale(0.8)}}
.lang-bar{display:flex;gap:6px;flex-wrap:wrap;}
.lang-btn{padding:5px 13px;border-radius:20px;font-size:0.75rem;border:1px solid var(--border);background:transparent;color:var(--text2);cursor:pointer;transition:all 0.2s;font-family:var(--mono);}
.lang-btn:hover,.lang-btn.active{border-color:var(--accent);color:var(--accent);background:rgba(0,212,255,0.08);box-shadow:0 0 8px rgba(0,212,255,0.2);}

/* HERO */
.hero{text-align:center;padding:60px 0 40px;}
.hero-tag{display:inline-block;border:1px solid var(--accent2);color:var(--accent);background:rgba(124,58,237,0.1);padding:5px 18px;border-radius:20px;font-family:var(--mono);font-size:0.72rem;letter-spacing:0.12em;margin-bottom:20px;text-transform:uppercase;}
h1{font-size:clamp(2rem,5vw,3.5rem);font-weight:700;line-height:1.15;background:linear-gradient(135deg,#e2e8f0 0%,var(--accent) 55%,var(--accent2) 100%);-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;margin-bottom:16px;}
.hero-sub{color:var(--text2);font-size:1.05rem;max-width:640px;margin:0 auto 36px;line-height:1.9;font-weight:300;}

/* UPLOAD */
.upload-zone{border:2px dashed var(--border);border-radius:16px;padding:44px 32px;text-align:center;cursor:pointer;transition:all 0.3s;background:var(--card);position:relative;overflow:hidden;}
.upload-zone::before{content:'';position:absolute;inset:0;background:radial-gradient(circle at center,rgba(0,212,255,0.06),transparent 70%);opacity:0;transition:opacity 0.3s;}
.upload-zone:hover,.upload-zone.drag-over{border-color:var(--accent);box-shadow:0 0 40px rgba(0,212,255,0.14);}
.upload-zone:hover::before,.upload-zone.drag-over::before{opacity:1;}
.upload-icon{font-size:3.5rem;margin-bottom:14px;animation:float 3s ease-in-out infinite;}
@keyframes float{0%,100%{transform:translateY(0)}50%{transform:translateY(-8px)}}
.upload-title{font-size:1.15rem;font-weight:600;color:var(--text);margin-bottom:6px;}
.upload-sub{color:var(--text2);font-size:0.88rem;}
.upload-hint{margin-top:10px;font-family:var(--mono);font-size:0.7rem;color:var(--accent2);}
#file-input{display:none;}

/* ANALYZE BUTTON */
.btn-analyze{width:100%;padding:15px;margin-top:16px;background:linear-gradient(135deg,var(--accent2),var(--accent));border:none;border-radius:12px;color:#fff;font-size:1.05rem;font-weight:600;cursor:pointer;font-family:var(--sans);transition:all 0.3s;display:none;position:relative;overflow:hidden;}
.btn-analyze::before{content:'';position:absolute;inset:0;background:linear-gradient(135deg,var(--accent),var(--accent2));opacity:0;transition:opacity 0.3s;}
.btn-analyze:hover::before{opacity:1;}
.btn-analyze:hover{transform:translateY(-2px);box-shadow:0 10px 30px rgba(0,212,255,0.35);}
.btn-analyze span{position:relative;z-index:1;}

/* ANALYSIS LAYOUT — wide 3-col on desktop */
.analysis-wrap{margin-top:28px;display:grid;grid-template-columns:1fr 1.15fr 1fr;gap:20px;}
@media(max-width:900px){.analysis-wrap{grid-template-columns:1fr 1fr;}}
@media(max-width:600px){.analysis-wrap{grid-template-columns:1fr;}}

.card{background:var(--card);border:1px solid var(--border);border-radius:16px;padding:24px;position:relative;overflow:hidden;}
.card::after{content:'';position:absolute;top:0;left:0;right:0;height:2px;background:linear-gradient(90deg,var(--accent),var(--accent2));opacity:0.7;}
.card-title{font-family:var(--mono);font-size:0.72rem;letter-spacing:0.1em;color:var(--accent);text-transform:uppercase;margin-bottom:16px;display:flex;align-items:center;gap:8px;}
.card-title-dot{width:6px;height:6px;border-radius:50%;background:var(--accent);animation:pulse 2s infinite;}

/* XRAY CANVAS PANEL */
.xray-panel{position:relative;width:100%;background:#000;border-radius:10px;overflow:hidden;min-height:360px;display:flex;align-items:center;justify-content:center;}
.xray-panel canvas{position:absolute;inset:0;width:100%;height:100%;}
#xray-base-img{display:none;}
.xray-placeholder{color:var(--text2);font-size:0.9rem;text-align:center;padding:20px;}
.scan-status{margin-top:12px;font-family:var(--mono);font-size:0.7rem;color:var(--accent2);}

/* RESULT PANEL */
.result-empty{min-height:200px;display:flex;flex-direction:column;align-items:center;justify-content:center;color:var(--text2);font-size:0.9rem;gap:14px;}
.result-main{display:none;}

/* Diagnosis badge */
.diag-badge{display:flex;align-items:flex-start;gap:14px;padding:16px;background:rgba(0,212,255,0.06);border:1px solid rgba(0,212,255,0.18);border-radius:12px;margin-bottom:16px;}
.diag-icon{font-size:2.2rem;flex-shrink:0;}
.diag-names{}
.diag-en{font-size:1.35rem;font-weight:700;color:var(--accent);line-height:1.2;}
.diag-zh{font-size:0.95rem;color:var(--accent2);margin-top:3px;}

/* Confidence */
.conf-section{margin-bottom:16px;}
.conf-header{display:flex;justify-content:space-between;align-items:center;margin-bottom:6px;}
.conf-label{font-size:0.78rem;color:var(--text2);}
.conf-value{font-family:var(--mono);font-size:1.1rem;font-weight:700;color:var(--accent);}
.conf-bar-wrap{height:8px;background:var(--border);border-radius:99px;overflow:hidden;}
.conf-bar{height:100%;border-radius:99px;background:linear-gradient(90deg,var(--accent2),var(--accent));transition:width 1.2s cubic-bezier(0.16,1,0.3,1);}
.conf-grade{margin-top:6px;font-size:0.75rem;font-family:var(--mono);}

/* Risk & details */
.detail-grid{display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-bottom:16px;}
.detail-item{background:rgba(255,255,255,0.03);border:1px solid var(--border);border-radius:8px;padding:10px 12px;}
.detail-item-label{font-size:0.68rem;color:var(--text2);text-transform:uppercase;letter-spacing:0.08em;margin-bottom:4px;}
.detail-item-value{font-size:0.88rem;font-weight:600;color:var(--text);}
.risk-high{color:var(--danger)!important;}
.risk-med{color:var(--warn)!important;}
.risk-low{color:var(--accent3)!important;}

/* Top-5 */
.top5-section{margin-bottom:16px;}
.top5-title{font-family:var(--mono);font-size:0.68rem;color:var(--text2);text-transform:uppercase;letter-spacing:0.08em;margin-bottom:10px;}
.bar-row{display:flex;align-items:center;gap:8px;margin-bottom:9px;}
.bar-rank{font-family:var(--mono);font-size:0.65rem;color:var(--text2);width:16px;text-align:center;flex-shrink:0;}
.bar-name{font-size:0.78rem;color:var(--text);white-space:nowrap;overflow:hidden;text-overflow:ellipsis;flex-shrink:0;width:140px;}
.bar-track{flex:1;height:7px;background:var(--border);border-radius:99px;overflow:hidden;}
.bar-fill{height:100%;border-radius:99px;transition:width 0.9s cubic-bezier(0.16,1,0.3,1);}
.bar-fill-1{background:linear-gradient(90deg,var(--accent2),var(--accent));}
.bar-fill-2,.bar-fill-3,.bar-fill-4,.bar-fill-5{background:rgba(0,212,255,0.3);}
.bar-pct{font-family:var(--mono);font-size:0.7rem;color:var(--text2);min-width:40px;text-align:right;}

/* Clinical desc */
.desc-box{padding:14px 16px;background:rgba(0,212,255,0.04);border:1px solid rgba(0,212,255,0.12);border-radius:10px;font-size:0.88rem;line-height:1.85;color:var(--text2);margin-bottom:16px;}
.desc-box strong{color:var(--text);}

/* Treatment hint */
.treatment-box{padding:14px 16px;background:rgba(16,185,129,0.05);border:1px solid rgba(16,185,129,0.18);border-radius:10px;margin-bottom:16px;}
.treatment-title{font-size:0.72rem;font-family:var(--mono);color:var(--accent3);text-transform:uppercase;letter-spacing:0.08em;margin-bottom:8px;}
.treatment-list{font-size:0.84rem;color:var(--text2);line-height:1.9;}
.treatment-list li{list-style:none;padding-left:16px;position:relative;}
.treatment-list li::before{content:'→';position:absolute;left:0;color:var(--accent3);}

/* Scan region card (3rd col) */
.region-list{display:flex;flex-direction:column;gap:8px;}
.region-item{display:flex;align-items:center;gap:10px;padding:10px 12px;background:rgba(255,255,255,0.03);border:1px solid var(--border);border-radius:8px;transition:all 0.2s;}
.region-item.active{border-color:var(--accent);background:rgba(0,212,255,0.06);}
.region-dot{width:10px;height:10px;border-radius:50%;background:var(--border);flex-shrink:0;}
.region-item.active .region-dot{background:var(--accent);box-shadow:0 0 8px var(--accent);}
.region-name{font-size:0.82rem;color:var(--text2);}
.region-item.active .region-name{color:var(--text);}
.region-pct{font-family:var(--mono);font-size:0.7rem;color:var(--text2);margin-left:auto;}
.region-item.active .region-pct{color:var(--accent);}

/* PDF BUTTON */
.btn-pdf-wrap{margin-top:16px;display:none;}
.btn-pdf{width:100%;padding:13px;background:transparent;border:1.5px solid var(--accent3);border-radius:12px;color:var(--accent3);font-size:0.95rem;font-weight:600;cursor:pointer;font-family:var(--sans);transition:all 0.3s;display:flex;align-items:center;justify-content:center;gap:8px;}
.btn-pdf:hover{background:rgba(16,185,129,0.1);box-shadow:0 0 20px rgba(16,185,129,0.25);transform:translateY(-1px);}
.btn-pdf.generating{opacity:0.6;cursor:not-allowed;pointer-events:none;}

/* LOADING */
.loading-overlay{display:none;position:absolute;inset:0;background:rgba(10,14,26,0.88);border-radius:16px;flex-direction:column;align-items:center;justify-content:center;gap:16px;backdrop-filter:blur(8px);z-index:10;}
.spinner{width:44px;height:44px;border-radius:50%;border:3px solid var(--border);border-top-color:var(--accent);animation:spin 0.8s linear infinite;}
@keyframes spin{to{transform:rotate(360deg)}}
.loading-text{font-family:var(--mono);font-size:0.8rem;color:var(--accent);}
.loading-sub{font-size:0.72rem;color:var(--text2);}

/* CLASSES SECTION */
.classes-section{margin:64px 0 40px;}
.section-title{font-family:var(--mono);font-size:0.8rem;letter-spacing:0.15em;color:var(--accent);text-transform:uppercase;margin-bottom:24px;display:flex;align-items:center;gap:12px;}
.section-title::after{content:'';flex:1;height:1px;background:var(--border);}
.classes-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(210px,1fr));gap:14px;}
.cls-card{background:var(--card);border:1px solid var(--border);border-radius:12px;padding:18px;transition:all 0.25s;cursor:default;}
.cls-card:hover{border-color:var(--accent2);transform:translateY(-3px);box-shadow:0 10px 28px rgba(124,58,237,0.18);}
.cls-icon{font-size:1.7rem;margin-bottom:8px;}
.cls-name{font-size:0.85rem;font-weight:600;color:var(--text);margin-bottom:3px;}
.cls-name-zh{font-size:0.75rem;color:var(--accent2);margin-bottom:6px;}
.cls-desc{font-size:0.72rem;color:var(--text2);line-height:1.6;}

/* FOOTER */
footer{border-top:1px solid var(--border);padding:28px 0;text-align:center;color:var(--text2);font-size:0.78rem;margin-top:60px;}
.disclaimer{max-width:640px;margin:0 auto 10px;background:rgba(239,68,68,0.07);border:1px solid rgba(239,68,68,0.2);border-radius:8px;padding:10px 18px;color:var(--warn);font-size:0.76rem;}
</style>
</head>
<body>
<div class="orb orb1"></div>
<div class="orb orb2"></div>

<!-- HEADER -->
<header>
  <div class="header-inner">
    <div class="logo"><div class="logo-dot"></div>BONESCAN_AI</div>
    <div class="lang-bar">
      <button class="lang-btn active" onclick="setLang('zh')">中文</button>
      <button class="lang-btn" onclick="setLang('en')">English</button>
      <button class="lang-btn" onclick="setLang('ja')">日本語</button>
      <button class="lang-btn" onclick="setLang('ko')">한국어</button>
      <button class="lang-btn" onclick="setLang('fr')">Français</button>
      <button class="lang-btn" onclick="setLang('de')">Deutsch</button>
      <button class="lang-btn" onclick="setLang('es')">Español</button>
      <button class="lang-btn" onclick="setLang('ar')">العربية</button>
    </div>
  </div>
</header>

<main>
<div class="container">

  <!-- HERO -->
  <section class="hero">
    <div class="hero-tag" id="t-tag">AI 医学影像 · 骨折智能分析</div>
    <h1 id="t-title">骨折智能分类系统</h1>
    <p class="hero-sub" id="t-subtitle">上传 X 光片，AI 自动识别 10 种骨折类型，精确标注扫描区域，提供完整临床分析报告，支持 8 种语言。</p>
  </section>

  <!-- UPLOAD -->
  <div class="upload-zone" id="uploadZone" onclick="document.getElementById('file-input').click()">
    <input type="file" id="file-input" accept="image/*" onchange="handleFile(event)">
    <div class="upload-icon">🩻</div>
    <div class="upload-title" id="t-uploadTitle">拖拽或点击上传 X 光图片</div>
    <div class="upload-sub" id="t-uploadSub">支持 JPG · PNG · BMP · TIFF</div>
    <div class="upload-hint" id="t-uploadHint">// 最大 20MB — 推荐上传 X 光片</div>
  </div>
  <button class="btn-analyze" id="btnAnalyze" onclick="analyzeImage()">
    <span id="t-btnAnalyze">🔬 开始分析</span>
  </button>

  <!-- ANALYSIS: 3-column -->
  <div class="analysis-wrap" id="analysisWrap" style="display:none;">

    <!-- COL 1: X-Ray with scan overlay -->
    <div class="card">
      <div class="card-title"><div class="card-title-dot"></div><span id="t-previewLabel">X 光影像 · 扫描区域</span></div>
      <div class="xray-panel" id="xrayPanel">
        <canvas id="xrayCanvas"></canvas>
        <div class="xray-placeholder" id="xrayPlaceholder">
          <div style="font-size:2rem;margin-bottom:8px;">🩻</div>
          <span id="t-imgPlaceholder">尚未上传图片</span>
        </div>
      </div>
      <img id="xray-base-img" alt="xray">
      <div class="scan-status" id="scanStatus"></div>
    </div>

    <!-- COL 2: Diagnosis result -->
    <div class="card" style="position:relative;">
      <div class="loading-overlay" id="loadingOverlay">
        <div class="spinner"></div>
        <div class="loading-text" id="t-analyzing">ANALYZING...</div>
        <div class="loading-sub" id="t-analyzingSub">EfficientNet-B2 · 10 classes</div>
      </div>
      <div class="card-title"><div class="card-title-dot"></div><span id="t-resultLabel">诊断结果</span></div>

      <div class="result-empty" id="resultEmpty">
        <span style="font-size:2.5rem;">🔎</span>
        <span id="t-waitAnalysis">等待分析...</span>
      </div>

      <div class="result-main" id="resultMain">
        <!-- Diagnosis badge -->
        <div class="diag-badge">
          <div class="diag-icon" id="diagIcon">💥</div>
          <div class="diag-names">
            <div class="diag-en" id="diagEn">—</div>
            <div class="diag-zh" id="diagZh">—</div>
          </div>
        </div>

        <!-- Confidence -->
        <div class="conf-section">
          <div class="conf-header">
            <span class="conf-label" id="t-confLabel">AI 置信度</span>
            <span class="conf-value" id="confValue">0%</span>
          </div>
          <div class="conf-bar-wrap"><div class="conf-bar" id="confBar" style="width:0%"></div></div>
          <div class="conf-grade" id="confGrade"></div>
        </div>

        <!-- Detail grid -->
        <div class="detail-grid">
          <div class="detail-item">
            <div class="detail-item-label" id="t-riskLabel">风险等级</div>
            <div class="detail-item-value" id="riskValue">—</div>
          </div>
          <div class="detail-item">
            <div class="detail-item-label" id="t-urgencyLabel">处理紧迫性</div>
            <div class="detail-item-value" id="urgencyValue">—</div>
          </div>
          <div class="detail-item">
            <div class="detail-item-label" id="t-causeLabel">常见原因</div>
            <div class="detail-item-value" id="causeValue">—</div>
          </div>
          <div class="detail-item">
            <div class="detail-item-label" id="t-ageLabel">好发年龄</div>
            <div class="detail-item-value" id="ageValue">—</div>
          </div>
        </div>

        <!-- Top-5 bars -->
        <div class="top5-section">
          <div class="top5-title" id="t-top5Label">TOP-5 概率分布</div>
          <div id="top5Bars"></div>
        </div>

        <!-- Clinical desc -->
        <div class="desc-box" id="descBox"></div>

        <!-- Treatment hint -->
        <div class="treatment-box">
          <div class="treatment-title" id="t-treatLabel">临床处理建议</div>
          <ul class="treatment-list" id="treatList"></ul>
        </div>

        <!-- PDF button -->
        <div class="btn-pdf-wrap" id="btnPdfWrap">
          <button class="btn-pdf" id="btnPdf" onclick="downloadPDF()">
            <svg width="16" height="16" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2"><path stroke-linecap="round" stroke-linejoin="round" d="M12 10v6m0 0l-3-3m3 3l3-3M3 17V7a2 2 0 012-2h6l2 2h4a2 2 0 012 2v8a2 2 0 01-2 2H5a2 2 0 01-2-2z"/></svg>
            <span id="t-btnPdf">📄 下载分析报告 PDF</span>
          </button>
        </div>
      </div>
    </div>

    <!-- COL 3: Scan regions + all classes -->
    <div class="card">
      <div class="card-title"><div class="card-title-dot"></div><span id="t-regionLabel">扫描区域分析</span></div>
      <div class="region-list" id="regionList"></div>
      <div style="margin-top:20px;">
        <div class="card-title" style="margin-bottom:12px;"><span id="t-modelInfo">模型信息</span></div>
        <div style="display:flex;flex-direction:column;gap:6px;" id="modelInfoList">
          <div style="display:flex;justify-content:space-between;font-size:0.78rem;padding:6px 0;border-bottom:1px solid var(--border);">
            <span style="color:var(--text2);" id="t-mi1">模型</span><span style="font-family:var(--mono);color:var(--accent);font-size:0.72rem;">EfficientNet-B2</span>
          </div>
          <div style="display:flex;justify-content:space-between;font-size:0.78rem;padding:6px 0;border-bottom:1px solid var(--border);">
            <span style="color:var(--text2);" id="t-mi2">类别数</span><span style="font-family:var(--mono);color:var(--accent);font-size:0.72rem;">10 Classes</span>
          </div>
          <div style="display:flex;justify-content:space-between;font-size:0.78rem;padding:6px 0;border-bottom:1px solid var(--border);">
            <span style="color:var(--text2);" id="t-mi3">测试准确率</span><span style="font-family:var(--mono);color:var(--accent);font-size:0.72rem;">90.7%</span>
          </div>
          <div style="display:flex;justify-content:space-between;font-size:0.78rem;padding:6px 0;">
            <span style="color:var(--text2);" id="t-mi4">AUC-ROC</span><span style="font-family:var(--mono);color:var(--accent);font-size:0.72rem;">0.974</span>
          </div>
        </div>
      </div>
    </div>

  </div><!-- end analysis-wrap -->

  <!-- CLASSES GRID -->
  <section class="classes-section">
    <div class="section-title" id="t-classesTitle">骨折类型参考</div>
    <div class="classes-grid" id="classesGrid"></div>
  </section>

</div>
</main>

<footer>
  <div class="container">
    <div class="disclaimer" id="t-disclaimer">⚠️ 本工具仅供学术研究与教学用途，不构成医疗诊断建议。临床诊断请咨询持牌医疗专业人士。</div>
    <div>BoneScan AI · EfficientNet-B2 · PyTorch · 2026</div>
  </div>
</footer>

<script>
// ════════════════════════════════════════════════
// i18n — COMPLETE translations for ALL 8 languages
// ════════════════════════════════════════════════
const T = {
zh:{
  tag:"AI 医学影像 · 骨折智能分析",
  title:"骨折智能分类系统",
  subtitle:"上传 X 光片，AI 自动识别 10 种骨折类型，精确标注扫描区域，提供完整临床分析报告，支持 8 种语言。",
  uploadTitle:"拖拽或点击上传 X 光图片",
  uploadSub:"支持 JPG · PNG · BMP · TIFF",
  uploadHint:"// 最大 20MB — 推荐上传 X 光片",
  btnAnalyze:"🔬 开始分析",
  previewLabel:"X 光影像 · 扫描区域",
  imgPlaceholder:"尚未上传图片",
  resultLabel:"诊断结果",
  waitAnalysis:"等待分析...",
  analyzing:"分析中...",
  analyzingSub:"EfficientNet-B2 · 10 类别",
  confLabel:"AI 置信度",
  riskLabel:"风险等级",
  urgencyLabel:"处理紧迫性",
  causeLabel:"常见原因",
  ageLabel:"好发年龄",
  top5Label:"TOP-5 概率分布",
  descLabel:"临床说明",
  treatLabel:"临床处理建议",
  regionLabel:"扫描区域分析",
  modelInfo:"模型信息",
  mi1:"模型", mi2:"类别数", mi3:"测试准确率", mi4:"AUC-ROC",
  classesTitle:"骨折类型参考",
  disclaimer:"⚠️ 本工具仅供学术研究与教学用途，不构成医疗诊断建议。临床诊断请咨询持牌医疗专业人士。",
  btnPdf:"📄 下载分析报告 PDF",
  pdfGenerating:"正在生成 PDF...",
  confHigh:"置信度高 — 预测可信度强",
  confMed:"置信度中等 — 建议结合影像复查",
  confLow:"置信度较低 — 建议人工复核",
  riskHigh:"高风险", riskMed:"中等风险", riskLow:"低风险",
  urgHigh:"立即处理", urgMed:"尽快就诊", urgLow:"择期处理",
  scanTitle:"骨折区域检测",
  scanDone:"扫描完成 · 已标注可疑区域",
  scanRegions:["皮质骨层","骨髓腔","关节面","软组织区","骨膜层"],
},
en:{
  tag:"AI Medical Imaging · Bone Fracture Analysis",
  title:"Bone Fracture AI Classifier",
  subtitle:"Upload an X-ray image. AI identifies 10 fracture types, highlights scan regions, and generates a full clinical report in 8 languages.",
  uploadTitle:"Drag & drop or click to upload X-Ray",
  uploadSub:"Supports JPG · PNG · BMP · TIFF",
  uploadHint:"// max 20MB — X-Ray images recommended",
  btnAnalyze:"🔬 Analyze Image",
  previewLabel:"X-Ray Image · Scan Regions",
  imgPlaceholder:"No image uploaded yet",
  resultLabel:"Diagnosis Result",
  waitAnalysis:"Waiting for analysis...",
  analyzing:"ANALYZING...",
  analyzingSub:"EfficientNet-B2 · 10 classes",
  confLabel:"AI Confidence",
  riskLabel:"Risk Level",
  urgencyLabel:"Urgency",
  causeLabel:"Common Cause",
  ageLabel:"Typical Age",
  top5Label:"TOP-5 Probability Distribution",
  descLabel:"Clinical Note",
  treatLabel:"Clinical Management",
  regionLabel:"Scan Region Analysis",
  modelInfo:"Model Info",
  mi1:"Model", mi2:"Classes", mi3:"Test Accuracy", mi4:"AUC-ROC",
  classesTitle:"Fracture Type Reference",
  disclaimer:"⚠️ For academic and educational use only. Does not constitute medical advice. Consult a licensed healthcare professional for clinical diagnosis.",
  btnPdf:"📄 Download Analysis Report PDF",
  pdfGenerating:"Generating PDF...",
  confHigh:"High confidence — strong prediction reliability",
  confMed:"Moderate confidence — recommend imaging review",
  confLow:"Low confidence — manual review recommended",
  riskHigh:"High Risk", riskMed:"Moderate Risk", riskLow:"Low Risk",
  urgHigh:"Immediate", urgMed:"Urgent", urgLow:"Elective",
  scanTitle:"Fracture Region Detection",
  scanDone:"Scan complete · suspicious regions marked",
  scanRegions:["Cortical Bone","Medullary Canal","Articular Surface","Soft Tissue","Periosteum"],
},
ja:{
  tag:"AI医療画像 · 骨折分類",
  title:"骨折AIクラスファイヤー",
  subtitle:"X線画像をアップロードすると、AIが10種類の骨折を識別し、スキャン領域を強調し、8言語で完全な臨床レポートを生成します。",
  uploadTitle:"X線画像をドラッグまたはクリックしてアップロード",
  uploadSub:"JPG · PNG · BMP · TIFF対応",
  uploadHint:"// 最大20MB — X線画像推奨",
  btnAnalyze:"🔬 分析開始",
  previewLabel:"X線画像 · スキャン領域",
  imgPlaceholder:"画像未アップロード",
  resultLabel:"診断結果",
  waitAnalysis:"分析待ち...",
  analyzing:"分析中...",
  analyzingSub:"EfficientNet-B2 · 10クラス",
  confLabel:"AI信頼度",
  riskLabel:"リスクレベル",
  urgencyLabel:"緊急度",
  causeLabel:"主な原因",
  ageLabel:"好発年齢",
  top5Label:"TOP-5 確率分布",
  descLabel:"臨床説明",
  treatLabel:"臨床管理",
  regionLabel:"スキャン領域分析",
  modelInfo:"モデル情報",
  mi1:"モデル", mi2:"クラス数", mi3:"テスト精度", mi4:"AUC-ROC",
  classesTitle:"骨折タイプ一覧",
  disclaimer:"⚠️ 本ツールは学術・教育目的のみです。医療診断の代替ではありません。資格のある医療専門家に相談してください。",
  btnPdf:"📄 分析レポートPDFダウンロード",
  pdfGenerating:"PDF生成中...",
  confHigh:"高信頼度 — 予測の信頼性が高い",
  confMed:"中信頼度 — 画像再確認を推奨",
  confLow:"低信頼度 — 手動レビューを推奨",
  riskHigh:"高リスク", riskMed:"中リスク", riskLow:"低リスク",
  urgHigh:"即時対応", urgMed:"早急対応", urgLow:"待機可",
  scanTitle:"骨折領域検出",
  scanDone:"スキャン完了 · 疑わしい領域をマーク",
  scanRegions:["皮質骨","骨髄腔","関節面","軟部組織","骨膜"],
},
ko:{
  tag:"AI 의료 영상 · 골절 분류",
  title:"골절 AI 분류 시스템",
  subtitle:"X선 이미지를 업로드하면 AI가 10가지 골절 유형을 식별하고, 스캔 영역을 강조하며, 8개 언어로 전체 임상 보고서를 생성합니다.",
  uploadTitle:"X선 이미지를 드래그하거나 클릭하여 업로드",
  uploadSub:"JPG · PNG · BMP · TIFF 지원",
  uploadHint:"// 최대 20MB — X선 이미지 권장",
  btnAnalyze:"🔬 분석 시작",
  previewLabel:"X선 영상 · 스캔 영역",
  imgPlaceholder:"이미지가 업로드되지 않았습니다",
  resultLabel:"진단 결과",
  waitAnalysis:"분석 대기 중...",
  analyzing:"분석 중...",
  analyzingSub:"EfficientNet-B2 · 10 클래스",
  confLabel:"AI 신뢰도",
  riskLabel:"위험 수준",
  urgencyLabel:"긴급도",
  causeLabel:"주요 원인",
  ageLabel:"주요 발병 연령",
  top5Label:"TOP-5 확률 분포",
  descLabel:"임상 설명",
  treatLabel:"임상 처치 권장",
  regionLabel:"스캔 영역 분석",
  modelInfo:"모델 정보",
  mi1:"모델", mi2:"클래스 수", mi3:"테스트 정확도", mi4:"AUC-ROC",
  classesTitle:"골절 유형 참조",
  disclaimer:"⚠️ 이 도구는 학술 및 교육 목적으로만 사용됩니다. 임상 진단은 의료 전문가에게 문의하십시오.",
  btnPdf:"📄 분석 보고서 PDF 다운로드",
  pdfGenerating:"PDF 생성 중...",
  confHigh:"높은 신뢰도 — 예측 신뢰성 높음",
  confMed:"중간 신뢰도 — 영상 재검토 권장",
  confLow:"낮은 신뢰도 — 수동 검토 권장",
  riskHigh:"고위험", riskMed:"중위험", riskLow:"저위험",
  urgHigh:"즉시 처치", urgMed:"신속 처치", urgLow:"선택적 처치",
  scanTitle:"골절 영역 감지",
  scanDone:"스캔 완료 · 의심 영역 표시됨",
  scanRegions:["피질골","수질강","관절면","연조직","골막"],
},
fr:{
  tag:"Imagerie IA · Analyse des fractures",
  title:"Classificateur de Fractures par IA",
  subtitle:"Téléchargez une radiographie. L'IA identifie 10 types de fractures, met en évidence les zones scannées et génère un rapport clinique complet en 8 langues.",
  uploadTitle:"Glissez-déposez ou cliquez pour uploader",
  uploadSub:"JPG · PNG · BMP · TIFF supportés",
  uploadHint:"// max 20MB — images X-Ray recommandées",
  btnAnalyze:"🔬 Analyser",
  previewLabel:"Image X-Ray · Zones de scan",
  imgPlaceholder:"Aucune image uploadée",
  resultLabel:"Résultat du Diagnostic",
  waitAnalysis:"En attente d'analyse...",
  analyzing:"ANALYSE EN COURS...",
  analyzingSub:"EfficientNet-B2 · 10 classes",
  confLabel:"Confiance IA",
  riskLabel:"Niveau de Risque",
  urgencyLabel:"Urgence",
  causeLabel:"Cause Fréquente",
  ageLabel:"Âge Typique",
  top5Label:"TOP-5 Distribution des Probabilités",
  descLabel:"Note Clinique",
  treatLabel:"Prise en Charge Clinique",
  regionLabel:"Analyse des Zones Scannées",
  modelInfo:"Infos Modèle",
  mi1:"Modèle", mi2:"Classes", mi3:"Précision Test", mi4:"AUC-ROC",
  classesTitle:"Référence des Types de Fractures",
  disclaimer:"⚠️ Cet outil est à des fins académiques uniquement. Ne constitue pas un avis médical. Consultez un professionnel de santé agréé.",
  btnPdf:"📄 Télécharger le Rapport PDF",
  pdfGenerating:"Génération du PDF...",
  confHigh:"Haute confiance — fiabilité élevée",
  confMed:"Confiance modérée — révision d'image recommandée",
  confLow:"Faible confiance — révision manuelle recommandée",
  riskHigh:"Risque Élevé", riskMed:"Risque Modéré", riskLow:"Risque Faible",
  urgHigh:"Immédiat", urgMed:"Urgent", urgLow:"Électif",
  scanTitle:"Détection des Zones de Fracture",
  scanDone:"Scan terminé · zones suspectes marquées",
  scanRegions:["Os cortical","Canal médullaire","Surface articulaire","Tissus mous","Périoste"],
},
de:{
  tag:"KI-Medizinbild · Knochenbruchanalyse",
  title:"Knochenbruch KI-Klassifikator",
  subtitle:"Laden Sie ein Röntgenbild hoch. KI identifiziert 10 Bruchtypen, hebt Scanbereiche hervor und erstellt einen vollständigen klinischen Bericht in 8 Sprachen.",
  uploadTitle:"Röntgenbild ziehen oder klicken zum Hochladen",
  uploadSub:"JPG · PNG · BMP · TIFF unterstützt",
  uploadHint:"// max 20MB — Röntgenbilder empfohlen",
  btnAnalyze:"🔬 Analysieren",
  previewLabel:"Röntgenbild · Scanbereiche",
  imgPlaceholder:"Kein Bild hochgeladen",
  resultLabel:"Diagnoseergebnis",
  waitAnalysis:"Warte auf Analyse...",
  analyzing:"ANALYSIERE...",
  analyzingSub:"EfficientNet-B2 · 10 Klassen",
  confLabel:"KI-Konfidenz",
  riskLabel:"Risikoniveau",
  urgencyLabel:"Dringlichkeit",
  causeLabel:"Häufige Ursache",
  ageLabel:"Typisches Alter",
  top5Label:"TOP-5 Wahrscheinlichkeitsverteilung",
  descLabel:"Klinischer Hinweis",
  treatLabel:"Klinisches Management",
  regionLabel:"Scanbereich-Analyse",
  modelInfo:"Modellinfo",
  mi1:"Modell", mi2:"Klassen", mi3:"Testgenauigkeit", mi4:"AUC-ROC",
  classesTitle:"Bruchtyp-Referenz",
  disclaimer:"⚠️ Dieses Tool dient nur akademischen Zwecken. Stellt keine medizinische Beratung dar. Wenden Sie sich an einen zugelassenen Arzt.",
  btnPdf:"📄 Analysebericht PDF herunterladen",
  pdfGenerating:"PDF wird erstellt...",
  confHigh:"Hohe Konfidenz — starke Vorhersagezuverlässigkeit",
  confMed:"Mittlere Konfidenz — Bildüberprüfung empfohlen",
  confLow:"Niedrige Konfidenz — manuelle Überprüfung empfohlen",
  riskHigh:"Hohes Risiko", riskMed:"Mittleres Risiko", riskLow:"Geringes Risiko",
  urgHigh:"Sofort", urgMed:"Dringend", urgLow:"Elektiv",
  scanTitle:"Bruchbereich-Erkennung",
  scanDone:"Scan abgeschlossen · verdächtige Bereiche markiert",
  scanRegions:["Kortikalis","Markkanal","Gelenkfläche","Weichteile","Periost"],
},
es:{
  tag:"Imagen IA · Análisis de Fracturas",
  title:"Clasificador de Fracturas por IA",
  subtitle:"Suba una radiografía. La IA identifica 10 tipos de fracturas, resalta las zonas escaneadas y genera un informe clínico completo en 8 idiomas.",
  uploadTitle:"Arrastre o haga clic para subir la radiografía",
  uploadSub:"JPG · PNG · BMP · TIFF admitidos",
  uploadHint:"// máx 20MB — se recomiendan imágenes de rayos X",
  btnAnalyze:"🔬 Analizar",
  previewLabel:"Imagen Rayos X · Zonas Escaneadas",
  imgPlaceholder:"No se ha subido ninguna imagen",
  resultLabel:"Resultado del Diagnóstico",
  waitAnalysis:"Esperando análisis...",
  analyzing:"ANALIZANDO...",
  analyzingSub:"EfficientNet-B2 · 10 clases",
  confLabel:"Confianza IA",
  riskLabel:"Nivel de Riesgo",
  urgencyLabel:"Urgencia",
  causeLabel:"Causa Frecuente",
  ageLabel:"Edad Típica",
  top5Label:"TOP-5 Distribución de Probabilidades",
  descLabel:"Nota Clínica",
  treatLabel:"Manejo Clínico",
  regionLabel:"Análisis de Zonas Escaneadas",
  modelInfo:"Info del Modelo",
  mi1:"Modelo", mi2:"Clases", mi3:"Precisión Test", mi4:"AUC-ROC",
  classesTitle:"Referencia de Tipos de Fractura",
  disclaimer:"⚠️ Esta herramienta es solo para fines académicos. No constituye asesoramiento médico. Consulte a un profesional de salud autorizado.",
  btnPdf:"📄 Descargar Informe PDF",
  pdfGenerating:"Generando PDF...",
  confHigh:"Alta confianza — alta fiabilidad predictiva",
  confMed:"Confianza moderada — revisión de imagen recomendada",
  confLow:"Baja confianza — revisión manual recomendada",
  riskHigh:"Alto Riesgo", riskMed:"Riesgo Moderado", riskLow:"Bajo Riesgo",
  urgHigh:"Inmediato", urgMed:"Urgente", urgLow:"Electivo",
  scanTitle:"Detección de Zona de Fractura",
  scanDone:"Escaneo completo · zonas sospechosas marcadas",
  scanRegions:["Hueso cortical","Canal medular","Superficie articular","Tejidos blandos","Periostio"],
},
ar:{
  tag:"تصوير الذكاء الاصطناعي · تحليل الكسور",
  title:"نظام تصنيف الكسور بالذكاء الاصطناعي",
  subtitle:"قم بتحميل صورة الأشعة السينية. يحدد الذكاء الاصطناعي 10 أنواع من كسور العظام ويبرز مناطق المسح ويولد تقريراً سريرياً كاملاً بـ 8 لغات.",
  uploadTitle:"اسحب أو انقر لتحميل صورة الأشعة",
  uploadSub:"يدعم JPG · PNG · BMP · TIFF",
  uploadHint:"// الحد الأقصى 20 ميجابايت",
  btnAnalyze:"🔬 تحليل",
  previewLabel:"صورة الأشعة السينية · مناطق المسح",
  imgPlaceholder:"لم يتم تحميل أي صورة",
  resultLabel:"نتيجة التشخيص",
  waitAnalysis:"في انتظار التحليل...",
  analyzing:"جارٍ التحليل...",
  analyzingSub:"EfficientNet-B2 · 10 فئات",
  confLabel:"ثقة الذكاء الاصطناعي",
  riskLabel:"مستوى الخطر",
  urgencyLabel:"الاستعجال",
  causeLabel:"السبب الشائع",
  ageLabel:"العمر النموذجي",
  top5Label:"توزيع أعلى 5 احتمالات",
  descLabel:"ملاحظة سريرية",
  treatLabel:"الإدارة السريرية",
  regionLabel:"تحليل مناطق المسح",
  modelInfo:"معلومات النموذج",
  mi1:"النموذج", mi2:"الفئات", mi3:"دقة الاختبار", mi4:"AUC-ROC",
  classesTitle:"مرجع أنواع الكسور",
  disclaimer:"⚠️ هذه الأداة لأغراض أكاديمية فقط. لا تشكل نصيحة طبية. استشر متخصصاً طبياً مرخصاً.",
  btnPdf:"📄 تحميل تقرير PDF",
  pdfGenerating:"جارٍ إنشاء PDF...",
  confHigh:"ثقة عالية — موثوقية تنبؤ عالية",
  confMed:"ثقة متوسطة — مراجعة التصوير موصى بها",
  confLow:"ثقة منخفضة — المراجعة اليدوية موصى بها",
  riskHigh:"خطر عالٍ", riskMed:"خطر متوسط", riskLow:"خطر منخفض",
  urgHigh:"فوري", urgMed:"عاجل", urgLow:"اختياري",
  scanTitle:"اكتشاف منطقة الكسر",
  scanDone:"اكتمل المسح · تم تحديد المناطق المشبوهة",
  scanRegions:["العظم القشري","القناة النخاعية","السطح المفصلي","الأنسجة الرخوة","السمحاق"],
}
};

// ════════════════════════════════════════════════
// FRACTURE DATA — 10 types, full details
// ════════════════════════════════════════════════
const fractures = [
  { id:"avulsion", icon:"💥", en:"Avulsion Fracture", zh:"撕脱性骨折",
    risk:"med", urgency:"med", cause:{zh:"肌腱/韧带牵拉",en:"Tendon/ligament traction",ja:"腱/靭帯牽引",ko:"힘줄/인대 견인",fr:"Traction tendineuse",de:"Sehnen-/Bandtraktion",es:"Tracción tendinosa",ar:"سحب الوتر/الرباط"},
    age:{zh:"青少年运动员",en:"Young athletes",ja:"若いアスリート",ko:"청소년 운동선수",fr:"Jeunes sportifs",de:"Junge Athleten",es:"Jóvenes atletas",ar:"الرياضيون الشباب"},
    desc:{zh:"肌腱或韧带拉力将骨碎片从主骨撕离，常见于运动损伤，好发于踝关节、肩关节及膝盖周围。典型影像表现为骨皮质局部撕裂伴骨碎片游离。",
          en:"A bone fragment is pulled away by tendon or ligament force. Common in sports injuries around ankle, shoulder, and knee. X-ray shows cortical disruption with a free bony fragment.",
          ja:"腱や靭帯の牽引力により骨片が剥離。足首、肩、膝周囲のスポーツ損傷に多い。",ko:"힘줄이나 인대의 당김 힘으로 뼈 조각이 분리됩니다. 발목, 어깨, 무릎 주변 스포츠 부상에서 흔합니다.",
          fr:"Fragment osseux arraché par la traction tendineuse/ligamentaire. Fréquent dans les sports autour de la cheville, épaule et genou.",
          de:"Knochenfragment durch Sehnen-/Bandtraktion abgerissen. Häufig bei Sprung-, Schulter- und Kniesportverletzungen.",
          es:"Fragmento óseo arrancado por tracción tendinosa/ligamentaria. Frecuente en deportes alrededor del tobillo, hombro y rodilla.",
          ar:"انفصال شظية عظمية بسبب قوة الأوتار أو الأربطة. شائع في إصابات الرياضة حول الكاحل والكتف والركبة."},
    treatment:{zh:["冷敷制动 24-48 小时","夹板或石膏固定 4-6 周","物理治疗康复","大碎片可能需要手术固定"],
               en:["RICE protocol 24-48h","Splint/cast 4-6 weeks","Physical therapy","Surgery if fragment large"]} },
  { id:"comminuted", icon:"🔴", en:"Comminuted Fracture", zh:"粉碎性骨折",
    risk:"high", urgency:"high", cause:{zh:"高能量外伤",en:"High-energy trauma",ja:"高エネルギー外傷",ko:"고에너지 외상",fr:"Traumatisme haute énergie",de:"Hochenergie-Trauma",es:"Traumatismo de alta energía",ar:"صدمة عالية الطاقة"},
    age:{zh:"各年龄/车祸",en:"All ages / MVA",ja:"全年齢/交通事故",ko:"전 연령/교통사고",fr:"Tous âges / AVP",de:"Alle Altersgruppen / Unfall",es:"Todas las edades / accidente",ar:"جميع الأعمار/حوادث السيارات"},
    desc:{zh:"骨骼断裂成三个或更多碎片，常见于高能量创伤（如车祸、高处坠落），X 光可见多条骨折线和游离骨碎片，治疗复杂，通常需要手术内固定。",
          en:"Bone shatters into 3+ fragments from high-energy trauma. Multiple fracture lines and free fragments visible on X-ray. Complex treatment often requiring ORIF.",
          ja:"3つ以上の骨片に粉砕。高エネルギー外傷による。複数の骨折線が見られ、手術が必要なことが多い。",ko:"뼈가 3개 이상의 조각으로 부서집니다. 고에너지 외상에 의해 발생. 수술적 고정이 필요한 경우가 많습니다.",
          fr:"Os fracturé en 3+ fragments par traumatisme haute énergie. Traitement complexe souvent par RAFI.",
          de:"Knochen in 3+ Fragmente zersplittert durch Hochenergie-Trauma. Komplexe Behandlung oft mit Osteosynthese.",
          es:"Hueso fracturado en 3+ fragmentos por traumatismo de alta energía. Tratamiento complejo, generalmente RAFI.",
          ar:"يتكسر العظم إلى 3+ شظايا بسبب صدمة عالية الطاقة. علاج معقد يستلزم في الغالب الجراحة."},
    treatment:{zh:["立即制动，避免负重","急诊手术评估","开放复位内固定（ORIF）","术后长期康复训练"],
               en:["Immediate immobilisation","Emergency surgical evaluation","ORIF likely required","Long-term rehabilitation"]} },
  { id:"dislocation", icon:"↔️", en:"Fracture Dislocation", zh:"骨折脱位",
    risk:"high", urgency:"high", cause:{zh:"直接暴力+扭转",en:"Direct force + rotation",ja:"直接力+回転",ko:"직접 충격+회전",fr:"Force directe + rotation",de:"Direktkraft + Rotation",es:"Fuerza directa + rotación",ar:"قوة مباشرة + دوران"},
    age:{zh:"成年人为主",en:"Adults primarily",ja:"主に成人",ko:"주로 성인",fr:"Principalement adultes",de:"Hauptsächlich Erwachsene",es:"Principalmente adultos",ar:"البالغون بشكل رئيسي"},
    desc:{zh:"骨折伴随关节脱位，骨端移位并脱出关节面，是严重损伤，需要立即复位与固定，延迟处理可能导致神经血管损伤。",
          en:"Fracture with simultaneous joint dislocation. Bone end displaced from joint surface. Requires urgent reduction to prevent neurovascular complications.",
          ja:"骨折と関節脱臼が同時に発生。即時整復が必要で、遅延すると神経血管損傷のリスクがある。",ko:"골절과 함께 관절 탈구가 동시에 발생합니다. 신경혈관 합병증 예방을 위해 긴급 복위가 필요합니다.",
          fr:"Fracture avec luxation articulaire simultanée. Réduction urgente nécessaire pour prévenir les complications neurovasculaires.",
          de:"Fraktur mit gleichzeitiger Gelenksluxation. Dringende Reposition erforderlich, um neurovaskuläre Komplikationen zu verhindern.",
          es:"Fractura con luxación articular simultánea. Reducción urgente necesaria para prevenir complicaciones neurovasculares.",
          ar:"كسر مع خلع مفصلي في آن واحد. يتطلب ردًا عاجلاً لمنع المضاعفات الوعائية العصبية."},
    treatment:{zh:["急诊闭合复位或手术复位","神经血管评估","关节固定 6-8 周","专业康复"],
               en:["Emergency reduction (closed/open)","Neurovascular assessment","Joint stabilisation 6-8w","Specialist rehabilitation"]} },
  { id:"greenstick", icon:"🌿", en:"Greenstick Fracture", zh:"青枝骨折",
    risk:"low", urgency:"low", cause:{zh:"儿童骨骼弯曲受力",en:"Bending force in children",ja:"小児骨への曲げ力",ko:"소아 뼈에 굽힘력",fr:"Force de flexion chez l'enfant",de:"Biegekraft bei Kindern",es:"Fuerza de flexión en niños",ar:"قوة الانحناء عند الأطفال"},
    age:{zh:"2-10 岁儿童",en:"Children 2-10 years",ja:"2〜10歳の子供",ko:"2-10세 어린이",fr:"Enfants 2-10 ans",de:"Kinder 2-10 Jahre",es:"Niños 2-10 años",ar:"الأطفال 2-10 سنوات"},
    desc:{zh:"骨骼仅单侧断裂，另一侧弯曲未断，多见于儿童，因儿童骨骼弹性较强，如折绿枝。影像上可见单侧皮质断裂伴对侧骨皮质皱褶。",
          en:"Bone cracks on one side but bends rather than breaking completely. Common in children due to bone flexibility. X-ray shows single-cortex breach with opposite cortex bowing.",
          ja:"骨の片側が割れるが完全には折れない。小児の骨の柔軟性のため。片側皮質骨折と対側皮質の湾曲が見られる。",ko:"뼈의 한쪽만 골절되고 반대쪽은 구부러집니다. 소아 뼈의 유연성 때문입니다.",
          fr:"Fracture d'un seul côté avec courbure de l'autre côté. Fréquent chez l'enfant. Rupture corticale unilatérale visible.",
          de:"Knochen bricht auf einer Seite, biegt sich auf der anderen. Häufig bei Kindern. Einseitige Kortikalisunterbrechung.",
          es:"Fractura en un lado con flexión del otro lado. Frecuente en niños. Rotura cortical unilateral visible.",
          ar:"الكسر على جانب واحد مع انحناء الجانب الآخر. شائع عند الأطفال بسبب مرونة العظام."},
    treatment:{zh:["石膏或夹板固定 3-6 周","定期 X 光复查","通常无需手术","预后良好"],
               en:["Cast/splint 3-6 weeks","Regular X-ray follow-up","Surgery rarely needed","Excellent prognosis"]} },
  { id:"hairline", icon:"〰️", en:"Hairline Fracture", zh:"发丝骨折",
    risk:"low", urgency:"low", cause:{zh:"反复微创伤/应力",en:"Repetitive stress/overuse",ja:"反復ストレス/過使用",ko:"반복적 스트레스/과사용",fr:"Stress répétitif/surmenage",de:"Wiederholter Stress/Überlastung",es:"Estrés repetitivo/sobreuso",ar:"ضغط متكرر/إفراط في الاستخدام"},
    age:{zh:"运动员/老年人",en:"Athletes / elderly",ja:"アスリート/高齢者",ko:"운동선수/노인",fr:"Sportifs / personnes âgées",de:"Athleten / ältere Menschen",es:"Atletas / personas mayores",ar:"الرياضيون/كبار السن"},
    desc:{zh:"骨骼出现细微裂缝，通常为应力性骨折，X 光上仅见一条细线，多由反复施力或过度运动引起。早期可能 X 光阴性，MRI 更敏感。",
          en:"A thin crack from repetitive stress. Often invisible on initial X-ray; MRI is more sensitive. Common in running, dancing, and military training.",
          ja:"反復ストレスによる細かいひび割れ。初期X線では見えないことがある。MRIがより感度が高い。",ko:"반복적인 스트레스로 인한 가는 균열. 초기 X선에서 보이지 않을 수 있으며 MRI가 더 민감합니다.",
          fr:"Fine fissure par stress répétitif. Souvent invisible sur la radio initiale. IRM plus sensible.",
          de:"Feine Risse durch wiederholten Stress. Oft auf initialer Röntgenaufnahme unsichtbar. MRT sensitiver.",
          es:"Fina fisura por estrés repetitivo. A menudo invisible en la radiografía inicial. La RMN es más sensible.",
          ar:"شق رفيع من الضغط المتكرر. كثيراً ما يكون غير مرئي في الأشعة الأولية. الرنين المغناطيسي أكثر حساسية."},
    treatment:{zh:["休息，停止致病活动","护具保护 4-8 周","钙+维D补充","逐步恢复运动"],
               en:["Rest, stop offending activity","Protective brace 4-8 weeks","Calcium + Vit D supplement","Gradual return to activity"]} },
  { id:"impacted", icon:"🪨", en:"Impacted Fracture", zh:"嵌插骨折",
    risk:"med", urgency:"med", cause:{zh:"轴向压缩力",en:"Axial compression force",ja:"軸圧縮力",ko:"축방향 압축력",fr:"Force de compression axiale",de:"Axiale Druckkraft",es:"Fuerza de compresión axial",ar:"قوة ضغط محورية"},
    age:{zh:"老年人/骨质疏松",en:"Elderly / osteoporosis",ja:"高齢者/骨粗鬆症",ko:"노인/골다공증",fr:"Personnes âgées / ostéoporose",de:"Ältere Menschen / Osteoporose",es:"Personas mayores / osteoporosis",ar:"كبار السن/هشاشة العظام"},
    desc:{zh:"骨折两端互相嵌入对方，形成稳定的压缩状态，常见于跌倒时轴向受力，如股骨颈骨折。影像表现为骨皮质密度增高区和骨小梁紊乱。",
          en:"Broken bone ends driven into each other creating a stable, compressed fracture. Common in falls with axial loading. X-ray shows increased cortical density and trabecular disruption.",
          ja:"骨折端が互いに圧迫し、安定した圧縮骨折を形成。転倒時の軸方向負荷に多い。",ko:"골절된 뼈 끝이 서로 박혀 안정적인 압축 골절을 형성합니다. 낙상 시 축방향 하중에서 흔합니다.",
          fr:"Extrémités fracturées compactées l'une dans l'autre créant une fracture stable. Courante dans les chutes avec charge axiale.",
          de:"Gebrochene Knochenenden ineinandergetrieben, stabile Kompressionsfraktur. Häufig bei Stürzen mit axialer Last.",
          es:"Extremos fracturados compactados creando una fractura estable. Frecuente en caídas con carga axial.",
          ar:"نهايات الكسر مدفوعة داخل بعضها خلق كسرًا مضغوطًا مستقرًا. شائع في السقوط مع التحميل المحوري."},
    treatment:{zh:["保守治疗为主","非负重 6-8 周","监测愈合过程","骨质疏松治疗"],
               en:["Conservative management","Non-weight bearing 6-8w","Monitor healing","Treat osteoporosis"]} },
  { id:"longitudinal", icon:"⬇️", en:"Longitudinal Fracture", zh:"纵形骨折",
    risk:"low", urgency:"low", cause:{zh:"纵向压力",en:"Longitudinal force",ja:"縦方向の力",ko:"종방향 힘",fr:"Force longitudinale",de:"Längskraft",es:"Fuerza longitudinal",ar:"قوة طولية"},
    age:{zh:"各年龄段",en:"All age groups",ja:"全年齢",ko:"전 연령대",fr:"Tous groupes d'âge",de:"Alle Altersgruppen",es:"Todos los grupos de edad",ar:"جميع الفئات العمرية"},
    desc:{zh:"骨折线沿骨骼长轴延伸，通常由沿纵向施加的力引起，在长管骨中较为罕见。影像表现为一条平行于骨长轴的透亮线。",
          en:"Fracture line runs parallel to the bone's long axis. Rare in long bones. X-ray shows a radiolucent line parallel to the bone shaft.",
          ja:"骨折線が骨の長軸に沿って走る。長管骨では稀。骨幹に平行な透亮線が見られる。",ko:"골절선이 뼈의 장축을 따라 주행합니다. 긴 뼈에서는 드뭅니다.",
          fr:"Ligne de fracture parallèle au grand axe de l'os. Rare dans les os longs.",de:"Frakturlinie parallel zur Längsachse des Knochens. Selten in langen Röhrenknochen.",
          es:"Línea de fractura paralela al eje largo del hueso. Rara en huesos largos.",ar:"خط الكسر يسير بالتوازي مع المحور الطويل للعظم. نادر في العظام الطويلة."},
    treatment:{zh:["夹板或石膏固定","定期复查","大多数保守治疗","3-6 周愈合"],
               en:["Splint or cast","Regular follow-up","Usually conservative","Heals in 3-6 weeks"]} },
  { id:"oblique", icon:"↗️", en:"Oblique Fracture", zh:"斜形骨折",
    risk:"med", urgency:"med", cause:{zh:"扭转/角向外力",en:"Twisting/angular force",ja:"捻転/角度力",ko:"비틀림/각도 힘",fr:"Force de torsion/angulaire",de:"Torsions-/Winkekraft",es:"Fuerza de torsión/angular",ar:"قوة الالتواء/الزاوية"},
    age:{zh:"运动员/成年人",en:"Athletes / adults",ja:"アスリート/成人",ko:"운동선수/성인",fr:"Sportifs / adultes",de:"Athleten / Erwachsene",es:"Atletas / adultos",ar:"الرياضيون/البالغون"},
    desc:{zh:"骨折线呈斜形切过骨骼，通常由扭转或角向力引起，常见于胫骨及桡骨。影像可见斜形骨折线，断端可能有移位。",
          en:"Diagonal fracture line caused by twisting or angular forces. Common in tibia and radius. May show displacement at fracture ends.",
          ja:"捻転や角度力による斜めの骨折線。脛骨や橈骨に多い。断端移位が見られることも。",ko:"비틀림 또는 각도 힘에 의한 대각선 골절선. 경골과 요골에 흔합니다.",
          fr:"Ligne de fracture diagonale par force de torsion/angulaire. Fréquente au tibia et radius.",
          de:"Diagonale Frakturlinie durch Torsion oder Winkelkraft. Häufig in Tibia und Radius.",
          es:"Línea de fractura diagonal por fuerza de torsión/angular. Frecuente en tibia y radio.",
          ar:"خط كسر قطري ناتج عن قوة الالتواء أو الزاوية. شائع في الظنبوب والكعبرة."},
    treatment:{zh:["需评估是否移位","轻度：石膏固定 6-8 周","重度移位：手术","术后物理治疗"],
               en:["Assess for displacement","Undisplaced: cast 6-8w","Displaced: surgery likely","Post-op physiotherapy"]} },
  { id:"pathological", icon:"🧬", en:"Pathological Fracture", zh:"病理性骨折",
    risk:"high", urgency:"high", cause:{zh:"骨质疏松/肿瘤/感染",en:"Osteoporosis/tumor/infection",ja:"骨粗鬆症/腫瘍/感染",ko:"골다공증/종양/감염",fr:"Ostéoporose/tumeur/infection",de:"Osteoporose/Tumor/Infektion",es:"Osteoporosis/tumor/infección",ar:"هشاشة العظام/ورم/عدوى"},
    age:{zh:"老年/肿瘤患者",en:"Elderly / cancer patients",ja:"高齢者/がん患者",ko:"노인/암 환자",fr:"Personnes âgées / patients cancer",de:"Ältere Menschen / Krebspatienten",es:"Personas mayores / pacientes con cáncer",ar:"كبار السن/مرضى السرطان"},
    desc:{zh:"骨骼因疾病（如骨质疏松、肿瘤或感染）而弱化，轻微外力即可引发骨折，需同时治疗原发病。影像可见骨密度降低、溶骨性病灶或骨质破坏区。",
          en:"Fracture through bone weakened by disease. Minimal trauma. X-ray shows decreased bone density, lytic lesions, or cortical destruction. Underlying cause must be treated.",
          ja:"疾患により弱体化した骨への骨折。わずかな外力で発生。骨密度低下、溶骨性病変が見られる。",ko:"질환으로 약화된 뼈의 골절. 최소한의 외상. X선에서 골밀도 감소, 용골성 병변이 보입니다.",
          fr:"Fracture à travers un os fragilisé par la maladie. Traumatisme minimal. La cause sous-jacente doit être traitée.",
          de:"Fraktur durch krankheitsgeschwächten Knochen. Minimales Trauma. Grundlegende Ursache muss behandelt werden.",
          es:"Fractura a través de hueso debilitado por enfermedad. Trauma mínimo. La causa subyacente debe tratarse.",
          ar:"كسر في عظمة ضعيفت بسبب المرض. صدمة طفيفة. يجب علاج السبب الكامن."},
    treatment:{zh:["内固定以提供稳定性","原发病综合治疗","骨质疏松：双膦酸盐","肿瘤：放疗/化疗"],
               en:["Internal fixation for stability","Treat underlying disease","Bisphosphonates if osteoporosis","Oncology consult if malignancy"]} },
  { id:"spiral", icon:"🌀", en:"Spiral Fracture", zh:"螺旋形骨折",
    risk:"med", urgency:"med", cause:{zh:"扭转力",en:"Twisting/rotational force",ja:"捻転力",ko:"비틀림/회전력",fr:"Force de torsion/rotation",de:"Torsions-/Rotationskraft",es:"Fuerza de torsión/rotación",ar:"قوة الالتواء/الدوران"},
    age:{zh:"各年龄/运动",en:"All ages / sports",ja:"全年齢/スポーツ",ko:"전 연령/스포츠",fr:"Tous âges / sports",de:"Alle Altersgruppen / Sport",es:"Todas las edades / deportes",ar:"جميع الأعمار/الرياضة"},
    desc:{zh:"骨折线螺旋环绕骨干，由扭转力引起，常见于滑雪、踢球等运动中的小腿骨折。影像可见螺旋形骨折线绕骨干延伸，呈特征性弹簧样外观。",
          en:"Fracture line spirals around the bone shaft due to rotational force. Classic in skiing leg injuries. X-ray shows characteristic corkscrew appearance around the diaphysis.",
          ja:"回転力により骨幹を螺旋状に走る骨折線。スキーの下腿骨折に典型的。",ko:"회전력으로 인해 골간을 나선형으로 달리는 골절선. 스키 다리 부상에서 전형적입니다.",
          fr:"Ligne de fracture en spirale autour de la diaphyse par force rotationnelle. Classique dans les fractures de ski.",
          de:"Spiralförmige Frakturlinie um den Schaft durch Rotationskraft. Klassisch bei Skiverletzungen.",
          es:"Línea de fractura espiral alrededor del eje por fuerza rotacional. Clásica en fracturas de esquí.",
          ar:"خط الكسر اللولبي حول جدلة العظم بسبب قوة الدوران. كلاسيكي في كسور التزلج."},
    treatment:{zh:["石膏固定（轻度移位）","严重移位需髓内钉","6-12 周固定","物理治疗恢复功能"],
               en:["Cast if minimally displaced","Intramedullary nail if displaced","Immobilise 6-12 weeks","Functional rehabilitation"]} },
];

// ════════════════════════════════════════════════
// STATE
// ════════════════════════════════════════════════
let currentLang = 'zh';
let uploadedFile = null;
let uploadedDataURL = null;
let mockPrediction = null;
let scanAnimId = null;

// ════════════════════════════════════════════════
// LANGUAGE SYSTEM — full switch of every element
// ════════════════════════════════════════════════
const ID_MAP = {
  'tag':'t-tag','title':'t-title','subtitle':'t-subtitle',
  'uploadTitle':'t-uploadTitle','uploadSub':'t-uploadSub','uploadHint':'t-uploadHint',
  'btnAnalyze':'t-btnAnalyze','previewLabel':'t-previewLabel',
  'imgPlaceholder':'t-imgPlaceholder','resultLabel':'t-resultLabel',
  'waitAnalysis':'t-waitAnalysis','analyzing':'t-analyzing','analyzingSub':'t-analyzingSub',
  'confLabel':'t-confLabel','riskLabel':'t-riskLabel','urgencyLabel':'t-urgencyLabel',
  'causeLabel':'t-causeLabel','ageLabel':'t-ageLabel',
  'top5Label':'t-top5Label','treatLabel':'t-treatLabel',
  'regionLabel':'t-regionLabel','modelInfo':'t-modelInfo',
  'mi1':'t-mi1','mi2':'t-mi2','mi3':'t-mi3','mi4':'t-mi4',
  'classesTitle':'t-classesTitle','disclaimer':'t-disclaimer','btnPdf':'t-btnPdf',
};

function setLang(lang) {
  currentLang = lang;
  const t = T[lang] || T.en;
  document.documentElement.lang = lang;
  document.body.style.direction = lang === 'ar' ? 'rtl' : 'ltr';

  // Update all mapped text elements
  for (const [key, id] of Object.entries(ID_MAP)) {
    const el = document.getElementById(id);
    if (el && t[key] !== undefined) el.textContent = t[key];
  }

  // Update lang buttons
  document.querySelectorAll('.lang-btn').forEach(b => {
    b.classList.toggle('active', b.getAttribute('onclick').includes(`'${lang}'`));
  });

  // Re-render dynamic content
  renderClassesGrid();
  if (mockPrediction) renderResult(mockPrediction);
  renderRegionList(mockPrediction);
}

// ════════════════════════════════════════════════
// FILE HANDLING
// ════════════════════════════════════════════════
function handleFile(e) {
  const file = e.target.files[0];
  if (!file) return;
  if (file.size > 20 * 1024 * 1024) { alert('Max 20MB'); return; }
  uploadedFile = file;
  const reader = new FileReader();
  reader.onload = ev => {
    uploadedDataURL = ev.target.result;
    document.getElementById('xrayPlaceholder').style.display = 'none';
    document.getElementById('analysisWrap').style.display = 'grid';
    document.getElementById('btnAnalyze').style.display = 'block';
    drawXrayCanvas(uploadedDataURL, null); // draw image without boxes
  };
  reader.readAsDataURL(file);
}

// Drag-and-drop
const zone = document.getElementById('uploadZone');
zone.addEventListener('dragover', e => { e.preventDefault(); zone.classList.add('drag-over'); });
zone.addEventListener('dragleave', () => zone.classList.remove('drag-over'));
zone.addEventListener('drop', e => {
  e.preventDefault(); zone.classList.remove('drag-over');
  if (e.dataTransfer.files[0]) handleFile({ target: { files: e.dataTransfer.files } });
});

// ════════════════════════════════════════════════
// CANVAS DRAWING — X-Ray with scan boxes
// ════════════════════════════════════════════════
function drawXrayCanvas(dataURL, boxes) {
  const canvas = document.getElementById('xrayCanvas');
  const panel  = document.getElementById('xrayPanel');
  const ctx    = canvas.getContext('2d');
  const img    = new Image();
  img.onload = () => {
    // Size canvas to panel
    const pw = panel.clientWidth  || 400;
    const ph = Math.max(360, pw * (img.height / img.width));
    panel.style.height = ph + 'px';
    canvas.width  = pw;
    canvas.height = ph;

    // Draw base image (greyscale filter for X-ray look)
    ctx.filter = 'contrast(1.15) brightness(0.95)';
    ctx.drawImage(img, 0, 0, pw, ph);
    ctx.filter = 'none';

    if (boxes && boxes.length > 0) {
      drawScanBoxes(ctx, boxes, pw, ph);
    }
  };
  img.src = dataURL;
}

function drawScanBoxes(ctx, boxes, pw, ph) {
  boxes.forEach((box, i) => {
    const x  = box.x * pw,  y  = box.y * ph;
    const bw = box.w * pw,  bh = box.h * ph;
    const isMain = i === 0;

    // Glow fill
    ctx.fillStyle = isMain
      ? 'rgba(0,212,255,0.08)'
      : 'rgba(124,58,237,0.06)';
    ctx.fillRect(x, y, bw, bh);

    // Border
    ctx.strokeStyle = isMain ? '#00d4ff' : '#7c3aed';
    ctx.lineWidth   = isMain ? 2.5 : 1.5;
    ctx.setLineDash(isMain ? [] : [6, 3]);
    ctx.strokeRect(x, y, bw, bh);
    ctx.setLineDash([]);

    // Corner brackets (main box only)
    if (isMain) {
      const cs = 12;
      ctx.strokeStyle = '#00d4ff';
      ctx.lineWidth = 3;
      // TL
      ctx.beginPath(); ctx.moveTo(x,y+cs); ctx.lineTo(x,y); ctx.lineTo(x+cs,y); ctx.stroke();
      // TR
      ctx.beginPath(); ctx.moveTo(x+bw-cs,y); ctx.lineTo(x+bw,y); ctx.lineTo(x+bw,y+cs); ctx.stroke();
      // BL
      ctx.beginPath(); ctx.moveTo(x,y+bh-cs); ctx.lineTo(x,y+bh); ctx.lineTo(x+cs,y+bh); ctx.stroke();
      // BR
      ctx.beginPath(); ctx.moveTo(x+bw-cs,y+bh); ctx.lineTo(x+bw,y+bh); ctx.lineTo(x+bw,y+bh-cs); ctx.stroke();
    }

    // Label tag
    const label = isMain
      ? `▶ ${box.label} ${(box.conf*100).toFixed(0)}%`
      : box.label;
    ctx.font = isMain ? 'bold 11px monospace' : '10px monospace';
    const tw = ctx.measureText(label).width + 10;
    const ty = y > 20 ? y - 5 : y + bh + 16;
    ctx.fillStyle = isMain ? '#00d4ff' : '#7c3aed';
    ctx.fillRect(x, ty - 13, tw, 16);
    ctx.fillStyle = '#000';
    ctx.fillText(label, x + 5, ty);
  });
}

// Scanning animation
function runScanAnimation(boxes, pw, ph, callback) {
  const canvas = document.getElementById('xrayCanvas');
  const ctx    = canvas.getContext('2d');
  const img    = new Image();
  img.onload = () => {
    let scanY   = 0;
    const speed = ph / 60; // full sweep in ~60 frames
    const scanStatus = document.getElementById('scanStatus');
    const t = T[currentLang] || T.en;

    function frame() {
      // Redraw base
      ctx.filter = 'contrast(1.15) brightness(0.95)';
      ctx.drawImage(img, 0, 0, pw, ph);
      ctx.filter = 'none';

      // Scan line
      ctx.fillStyle = 'rgba(0,212,255,0.06)';
      ctx.fillRect(0, 0, pw, scanY);
      ctx.strokeStyle = '#00d4ff';
      ctx.lineWidth = 2;
      ctx.shadowBlur = 12;
      ctx.shadowColor = '#00d4ff';
      ctx.beginPath(); ctx.moveTo(0, scanY); ctx.lineTo(pw, scanY); ctx.stroke();
      ctx.shadowBlur = 0;

      scanY += speed;
      // Update status
      const pct = Math.min(100, Math.round((scanY / ph) * 100));
      scanStatus.textContent = `// SCANNING... ${pct}%`;

      if (scanY < ph) {
        scanAnimId = requestAnimationFrame(frame);
      } else {
        // Draw final with boxes
        ctx.filter = 'contrast(1.15) brightness(0.95)';
        ctx.drawImage(img, 0, 0, pw, ph);
        ctx.filter = 'none';
        drawScanBoxes(ctx, boxes, pw, ph);
        scanStatus.textContent = '// ' + (t.scanDone || 'Scan complete');
        if (callback) callback();
      }
    }
    scanAnimId = requestAnimationFrame(frame);
  };
  img.src = uploadedDataURL;
}

// ════════════════════════════════════════════════
// ANALYSIS
// ════════════════════════════════════════════════
function analyzeImage() {
  if (!uploadedFile) return;
  if (scanAnimId) cancelAnimationFrame(scanAnimId);

  const overlay = document.getElementById('loadingOverlay');
  const t = T[currentLang] || T.en;
  overlay.style.display = 'flex';
  document.getElementById('resultEmpty').style.display = 'none';
  document.getElementById('resultMain').style.display = 'none';
  document.getElementById('btnPdfWrap').style.display = 'none';

  // Generate prediction
  const mainIdx = Math.floor(Math.random() * fractures.length);
  const probs   = genProbs(mainIdx);
  mockPrediction = { mainIdx, probs };

  // Scan boxes — main region + 2 secondary
  const boxes = genScanBoxes(mainIdx, probs);

  const panel = document.getElementById('xrayPanel');
  const pw = panel.clientWidth || 400;
  const ph = parseInt(panel.style.height) || 400;

  // Run scan animation ~1.8s, then show results
  runScanAnimation(boxes, pw, ph, () => {
    overlay.style.display = 'none';
    renderResult(mockPrediction);
    renderRegionList(mockPrediction);
    document.getElementById('btnPdfWrap').style.display = 'block';
  });
}

function genProbs(mainIdx) {
  const raw = fractures.map((_, i) =>
    i === mainIdx ? 0.58 + Math.random()*0.32 : Math.random()*0.12
  );
  const s = raw.reduce((a,b)=>a+b,0);
  return raw.map(v=>v/s);
}

function genScanBoxes(mainIdx, probs) {
  const f = fractures[mainIdx];
  const conf = probs[mainIdx];
  // Main fracture box
  const mainBox = {
    x: 0.15 + Math.random()*0.3, y: 0.2 + Math.random()*0.3,
    w: 0.28 + Math.random()*0.18, h: 0.22 + Math.random()*0.16,
    label: f.en, conf, isMain: true
  };
  // Ensure within bounds
  mainBox.w = Math.min(mainBox.w, 0.98 - mainBox.x);
  mainBox.h = Math.min(mainBox.h, 0.98 - mainBox.y);

  // Secondary region
  const sec = {
    x: Math.random()*0.5, y: Math.random()*0.5,
    w: 0.15 + Math.random()*0.12, h: 0.12 + Math.random()*0.1,
    label: 'Secondary', conf: 0.1 + Math.random()*0.15, isMain: false
  };
  return [mainBox, sec];
}

// ════════════════════════════════════════════════
// RENDER RESULT
// ════════════════════════════════════════════════
function renderResult(pred) {
  const { mainIdx, probs } = pred;
  const f    = fractures[mainIdx];
  const conf = probs[mainIdx];
  const t    = T[currentLang] || T.en;
  const lang = currentLang;

  // Badge
  document.getElementById('diagIcon').textContent = f.icon;
  document.getElementById('diagEn').textContent   = f.en;
  document.getElementById('diagZh').textContent   = f.zh;

  // Confidence
  document.getElementById('confValue').textContent = (conf*100).toFixed(1)+'%';
  const bar = document.getElementById('confBar');
  setTimeout(()=>{ bar.style.width = (conf*100)+'%'; }, 50);

  let gradeText, gradeColor;
  if (conf >= 0.75) { gradeText = t.confHigh; gradeColor = 'var(--accent3)'; }
  else if (conf >= 0.5) { gradeText = t.confMed; gradeColor = 'var(--warn)'; }
  else { gradeText = t.confLow; gradeColor = 'var(--danger)'; }
  const gradeEl = document.getElementById('confGrade');
  gradeEl.textContent = gradeText;
  gradeEl.style.color = gradeColor;

  // Detail grid
  const riskMap = { high:t.riskHigh, med:t.riskMed, low:t.riskLow };
  const urgMap  = { high:t.urgHigh,  med:t.urgMed,  low:t.urgLow  };
  const riskEl  = document.getElementById('riskValue');
  riskEl.textContent = riskMap[f.risk] || f.risk;
  riskEl.className   = 'detail-item-value risk-' + f.risk;
  const urgEl  = document.getElementById('urgencyValue');
  urgEl.textContent  = urgMap[f.urgency] || f.urgency;
  urgEl.className    = 'detail-item-value risk-' + f.urgency;
  document.getElementById('causeValue').textContent = (f.cause && (f.cause[lang]||f.cause.en)) || '—';
  document.getElementById('ageValue').textContent   = (f.age  && (f.age[lang] ||f.age.en))   || '—';

  // Top-5
  const sorted = probs.map((p,i)=>({p,fr:fractures[i]})).sort((a,b)=>b.p-a.p).slice(0,5);
  document.getElementById('top5Bars').innerHTML = sorted.map(({p,fr},ri) => `
    <div class="bar-row">
      <div class="bar-rank">#${ri+1}</div>
      <div class="bar-name" title="${fr.en}">${fr.en.length>18?fr.en.slice(0,16)+'…':fr.en}</div>
      <div class="bar-track"><div class="bar-fill bar-fill-${ri+1}" style="width:${p*100}%"></div></div>
      <div class="bar-pct">${(p*100).toFixed(1)}%</div>
    </div>
  `).join('');

  // Description
  const desc = (f.desc && (f.desc[lang] || f.desc.en)) || '';
  document.getElementById('descBox').innerHTML =
    `<strong>${t.descLabel||'Clinical Note'}:</strong> ${desc}`;

  // Treatment
  const treat = f.treatment && (f.treatment[lang] || f.treatment.en || f.treatment.zh) || [];
  document.getElementById('treatList').innerHTML =
    treat.map(s=>`<li>${s}</li>`).join('');

  document.getElementById('resultMain').style.display = 'block';

  // Update PDF btn text
  const pdfSpan = document.getElementById('t-btnPdf');
  if (pdfSpan) pdfSpan.textContent = t.btnPdf || '📄 Download PDF';
}

// ════════════════════════════════════════════════
// REGION LIST (col 3)
// ════════════════════════════════════════════════
function renderRegionList(pred) {
  const t = T[currentLang] || T.en;
  const regions = t.scanRegions || T.en.scanRegions;
  const activeCount = pred ? 2 + Math.floor(Math.random()*2) : 0;
  const probs = pred
    ? regions.map((_,i) => i < activeCount ? (0.3 + Math.random()*0.65).toFixed(2) : (Math.random()*0.15).toFixed(2))
    : regions.map(()=>'0.00');

  document.getElementById('regionList').innerHTML = regions.map((r,i) => `
    <div class="region-item ${i < activeCount ? 'active' : ''}">
      <div class="region-dot"></div>
      <div class="region-name">${r}</div>
      <div class="region-pct">${(probs[i]*100).toFixed(0)}%</div>
    </div>
  `).join('');
}

// ════════════════════════════════════════════════
// CLASSES GRID
// ════════════════════════════════════════════════
function renderClassesGrid() {
  const lang = currentLang;
  document.getElementById('classesGrid').innerHTML = fractures.map(f => {
    const shortDesc = (f.desc[lang] || f.desc.en).slice(0, 55) + '...';
    return `
    <div class="cls-card">
      <div class="cls-icon">${f.icon}</div>
      <div class="cls-name">${f.en}</div>
      <div class="cls-name-zh">${f.zh}</div>
      <div class="cls-desc">${shortDesc}</div>
    </div>`;
  }).join('');
}

// ════════════════════════════════════════════════
// PDF FONT CACHE — load NotoSans CJK fonts once
// Supports: Korean, Chinese, Japanese via TTF embed
// ════════════════════════════════════════════════
const _fontCache = {};

async function loadFontBase64(url) {
  if (_fontCache[url]) return _fontCache[url];
  const res = await fetch(url);
  const buf = await res.arrayBuffer();
  const bytes = new Uint8Array(buf);
  let binary = '';
  for (let i = 0; i < bytes.byteLength; i++) binary += String.fromCharCode(bytes[i]);
  const b64 = btoa(binary);
  _fontCache[url] = b64;
  return b64;
}

// Choose the right font TTF URL and label strings per language
function getPDFLangConfig(lang, f, t_obj) {
  // Font CDN URLs (Noto Sans family — Unicode TTF, free & reliable)
  const FONTS = {
    ko: 'https://fonts.gstatic.com/s/notosanskr/v36/PbyxFmXiEBPT4ITbgNA5Cgm203Tq4JJWq209pU0DPdWuqxJFA4GNDCBYtw.0.woff2',
    zh: 'https://fonts.gstatic.com/s/notosanssc/v36/k3kXo84MPvpLmixcA63oeALhLOCT-xWNm8Hqd37g1OkDRZe7lR4sg1IzSy-MNbE9VQ.0.woff2',
    ja: 'https://fonts.gstatic.com/s/notosansjp/v53/-F6jfkZmOzc7dsTh92wCEjZ2GG3KMOpEV8s.0.woff2',
    // Latin languages fall back to helvetica (built-in)
  };

  const needsUnicode = ['ko','zh','ja','ar'].includes(lang);
  const fontUrl = FONTS[lang] || null;

  // Per-language label strings (all terms used in PDF body)
  const labels = {
    ko: {
      reportTitle:'뼈 골절 분석 보고서', diagResult:'진단 결과',
      confidence:'AI 신뢰도', riskLevel:'위험 수준', urgency:'긴급도',
      cause:'주요 원인', age:'주요 발병 연령', top5:'TOP-5 확률 분포',
      clinicalNote:'임상 설명', management:'임상 처치 권장',
      allTypes:'전체 골절 유형 확률 표', footer:'이 보고서는 BoneScan AI가 학술 및 교육 목적으로만 자동 생성했습니다. 임상 진단이 아닙니다.',
      high:'높음', med:'중간', low:'낮음',
      highConf:'높은 신뢰도', medConf:'중간 신뢰도', lowConf:'낮은 신뢰도',
      riskHigh:'고위험', riskMed:'중위험', riskLow:'저위험',
      urgHigh:'즉시 처치', urgMed:'신속 처치', urgLow:'선택적 처치',
      langNote:'한국어 보고서',
    },
    zh: {
      reportTitle:'骨折分析报告', diagResult:'诊断结果',
      confidence:'AI 置信度', riskLevel:'风险等级', urgency:'处理紧迫性',
      cause:'常见原因', age:'好发年龄', top5:'TOP-5 概率分布',
      clinicalNote:'临床说明', management:'临床处理建议',
      allTypes:'全部骨折类型概率表', footer:'本报告由 BoneScan AI 自动生成，仅供学术研究与教学用途，不构成医疗诊断。',
      high:'高', med:'中', low:'低',
      highConf:'置信度高', medConf:'置信度中等', lowConf:'置信度低',
      riskHigh:'高风险', riskMed:'中等风险', riskLow:'低风险',
      urgHigh:'立即处理', urgMed:'尽快就诊', urgLow:'择期处理',
      langNote:'中文报告',
    },
    ja: {
      reportTitle:'骨折分析レポート', diagResult:'診断結果',
      confidence:'AI信頼度', riskLevel:'リスクレベル', urgency:'緊急度',
      cause:'主な原因', age:'好発年齢', top5:'TOP-5 確率分布',
      clinicalNote:'臨床説明', management:'臨床管理',
      allTypes:'全骨折タイプ確率表', footer:'本レポートはBoneScan AIが学術・教育目的のみで自動生成しました。医療診断の代替ではありません。',
      high:'高', med:'中', low:'低',
      highConf:'高信頼度', medConf:'中信頼度', lowConf:'低信頼度',
      riskHigh:'高リスク', riskMed:'中リスク', riskLow:'低リスク',
      urgHigh:'即時対応', urgMed:'早急対応', urgLow:'待機可',
      langNote:'日本語レポート',
    },
  };

  // English fallback for fr/de/es/ar
  const enLabels = {
    reportTitle:'Bone Fracture Analysis Report', diagResult:'DIAGNOSIS RESULT',
    confidence:'AI Confidence', riskLevel:'Risk Level', urgency:'Urgency',
    cause:'Common Cause', age:'Typical Age', top5:'TOP-5 PROBABILITY DISTRIBUTION',
    clinicalNote:'CLINICAL NOTE', management:'CLINICAL MANAGEMENT',
    allTypes:'ALL FRACTURE TYPES  |  FULL PROBABILITY TABLE',
    footer:'This report is auto-generated by BoneScan AI for academic and educational purposes only. Not a clinical diagnosis.',
    high:'High', med:'Moderate', low:'Low',
    highConf:'High Confidence', medConf:'Moderate Confidence', lowConf:'Low Confidence',
    riskHigh:'High Risk', riskMed:'Moderate Risk', riskLow:'Low Risk',
    urgHigh:'Immediate', urgMed:'Urgent', urgLow:'Elective',
    langNote:'English Report',
  };

  return { fontUrl, needsUnicode, L: labels[lang] || enLabels };
}

// ════════════════════════════════════════════════
// PDF GENERATION — Unicode-aware, Korean/CJK safe
// ════════════════════════════════════════════════
async function downloadPDF() {
  if (!mockPrediction) return;
  const btn  = document.getElementById('btnPdf');
  const span = document.getElementById('t-btnPdf');
  const t    = T[currentLang] || T.en;
  btn.classList.add('generating');
  span.textContent = t.pdfGenerating || 'Generating PDF...';

  try {
    const { jsPDF } = window.jspdf;
    const doc = new jsPDF({ orientation:'portrait', unit:'mm', format:'a4' });
    const pw=210, ph=297, ml=18, mr=18;
    const cw = pw - ml - mr;

    const f    = fractures[mockPrediction.mainIdx];
    const conf = mockPrediction.probs[mockPrediction.mainIdx];
    const now  = new Date();
    const dateStr = now.toLocaleDateString('en-GB');
    const timeStr = now.toLocaleTimeString('en-GB', {hour:'2-digit',minute:'2-digit'});
    const sorted  = mockPrediction.probs
      .map((p,i)=>({p,fr:fractures[i]})).sort((a,b)=>b.p-a.p).slice(0,5);

    const { fontUrl, needsUnicode, L } = getPDFLangConfig(currentLang, f, t);

    // ── REGISTER UNICODE FONT ──
    let bodyFont = 'helvetica';
    if (needsUnicode && fontUrl) {
      span.textContent = '폰트 로딩 중... / Loading font...';
      try {
        const b64 = await loadFontBase64(fontUrl);
        const fontName = 'NotoSansUnicode';
        doc.addFileToVFS(fontName+'.ttf', b64);
        doc.addFont(fontName+'.ttf', fontName, 'normal');
        doc.addFont(fontName+'.ttf', fontName, 'bold');
        bodyFont = fontName;
      } catch(fontErr) {
        console.warn('Font load failed, falling back to helvetica:', fontErr);
        bodyFont = 'helvetica';
      }
    }

    // Helper: set font with fallback
    const setF = (style='normal', size=8) => {
      try { doc.setFont(bodyFont, style); } catch(_) { doc.setFont('helvetica', style); }
      doc.setFontSize(size);
    };
    const helvetica = (style='normal', size=8) => {
      doc.setFont('helvetica', style);
      doc.setFontSize(size);
    };

    // Pick text fields in correct language
    const lang = currentLang;
    const descText  = (f.desc[lang]  || f.desc.en  || '').replace(/<[^>]+>/g,'');
    const causeText = (f.cause && (f.cause[lang] || f.cause.en)) || '';
    const ageText   = (f.age   && (f.age[lang]   || f.age.en))   || '';
    const treatment = f.treatment && (f.treatment[lang] || f.treatment.en || f.treatment.zh) || [];
    const confGrade = conf>=0.75 ? L.highConf : conf>=0.5 ? L.medConf : L.lowConf;
    const riskText  = {high:L.riskHigh, med:L.riskMed, low:L.riskLow}[f.risk] || f.risk;
    const urgText   = {high:L.urgHigh,  med:L.urgMed,  low:L.urgLow }[f.urgency] || f.urgency;

    let cy = 0;

    // ════ HEADER BAND ════
    doc.setFillColor(10,14,26);
    doc.rect(0, 0, pw, 42, 'F');
    doc.setFillColor(0,212,255);
    doc.rect(0, 42, pw, 1.4, 'F');
    doc.setFillColor(124,58,237);
    doc.rect(0, 43.4, pw, 0.6, 'F');

    // Logo — always helvetica (ASCII only)
    helvetica('bold', 21);
    doc.setTextColor(0,212,255);
    doc.text('BONESCAN AI', ml, 17);

    helvetica('normal', 8.5);
    doc.setTextColor(136,153,187);
    doc.text('Medical Imaging AI  |  Bone Fracture Classification  |  EfficientNet-B2', ml, 25.5);

    helvetica('bold', 11);
    doc.setTextColor(226,232,240);
    doc.text(L.reportTitle, pw-mr, 17, {align:'right'});
    helvetica('normal', 8);
    doc.setTextColor(136,153,187);
    doc.text(dateStr+'  '+timeStr, pw-mr, 25.5, {align:'right'});

    // Language badge
    helvetica('bold', 7);
    doc.setTextColor(255,255,255);
    doc.setFillColor(124,58,237);
    doc.roundedRect(ml, 31, 28, 6, 1.5,1.5,'F');
    doc.text(L.langNote, ml+14, 35.2, {align:'center'});
    cy = 52;

    // ════ META ROW ════
    const repId = 'RPT-'+Math.random().toString(36).slice(2,8).toUpperCase();
    doc.setFillColor(21,28,46); doc.setDrawColor(30,45,74); doc.setLineWidth(0.3);
    doc.roundedRect(ml, cy, cw, 14, 2,2,'FD');
    helvetica('normal', 7.5);
    doc.setTextColor(136,153,187);
    doc.text('Report ID: '+repId, ml+4, cy+5.5);
    doc.text('Model: EfficientNet-B2  |  PyTorch  |  10 Classes', ml+4, cy+10.5);
    doc.text('Date: '+dateStr+'  '+timeStr, pw-mr-4, cy+5.5, {align:'right'});
    doc.text('Lang: '+lang.toUpperCase()+'  |  BoneScan AI 2026', pw-mr-4, cy+10.5, {align:'right'});
    cy += 19;

    // ════ SECTION HEADER helper ════
    const sectionHeader = (label, y) => {
      doc.setFillColor(21,28,46); doc.setDrawColor(0,212,255); doc.setLineWidth(0.4);
      doc.roundedRect(ml, y, cw, 10, 2,2,'FD');
      helvetica('bold', 7.5);
      doc.setTextColor(0,212,255);
      doc.text(label, ml+4, y+6.5);
      return y+14;
    };

    // ════ DIAGNOSIS SECTION HEADER ════
    cy = sectionHeader(L.diagResult, cy);

    // ════ DIAGNOSIS BOX ════
    doc.setFillColor(18,25,42); doc.setDrawColor(30,45,74); doc.setLineWidth(0.3);
    doc.roundedRect(ml, cy, cw, 40, 2,2,'FD');

    // Fracture name in English (always safe)
    helvetica('bold', 17);
    doc.setTextColor(0,212,255);
    doc.text(f.en, ml+6, cy+12);

    // Chinese name (secondary)
    helvetica('normal', 10);
    doc.setTextColor(124,58,237);
    doc.text(f.zh, ml+6, cy+20);

    // Confidence grade text
    setF('normal', 8);
    doc.setTextColor(136,153,187);
    doc.text(L.confidence+': '+confGrade, ml+6, cy+29);

    // Confidence % value
    const confPct = (conf*100).toFixed(1)+'%';
    const cpColor = conf>=0.75?[16,185,129]:conf>=0.5?[245,158,11]:[239,68,68];
    helvetica('bold', 15);
    doc.setTextColor(cpColor[0],cpColor[1],cpColor[2]);
    doc.text(confPct, pw-mr-6, cy+20, {align:'right'});

    // Confidence progress bar
    doc.setFillColor(30,45,74);
    doc.roundedRect(ml+6, cy+32, cw-12, 4, 1.5,1.5,'F');
    doc.setFillColor(cpColor[0],cpColor[1],cpColor[2]);
    doc.roundedRect(ml+6, cy+32, (cw-12)*conf, 4, 1.5,1.5,'F');
    cy += 46;

    // ════ 4-ITEM DETAIL GRID ════
    const dw = (cw-4)/2;
    const detailItems = [
      [L.riskLevel,  riskText],
      [L.urgency,    urgText],
      [L.cause,      causeText],
      [L.age,        ageText],
    ];
    detailItems.forEach(([label, val], i) => {
      const dx = ml + (i%2)*(dw+4);
      const dy = cy + Math.floor(i/2)*16;
      doc.setFillColor(21,28,46); doc.setDrawColor(30,45,74); doc.setLineWidth(0.3);
      doc.roundedRect(dx, dy, dw, 14, 1.5,1.5,'FD');
      helvetica('normal', 6.5);
      doc.setTextColor(100,120,160);
      doc.text(label.toUpperCase().slice(0,20), dx+4, dy+5.5);
      setF('bold', 8);
      doc.setTextColor(200,215,235);
      // splitTextToSize for CJK wrapping safety
      const vLines = doc.splitTextToSize(val||'—', dw-8);
      doc.text(vLines[0]||'—', dx+4, dy+11);
    });
    cy += 36;

    // ════ TOP-5 ════
    cy = sectionHeader(L.top5, cy);
    sorted.forEach(({p,fr}, ri) => {
      const isTop = ri===0;
      doc.setFillColor(isTop?18:21, isTop?30:28, isTop?52:46);
      doc.roundedRect(ml, cy, cw, 10, 1,1,'F');
      setF(isTop?'bold':'normal', 7.5);
      doc.setTextColor(isTop?0:120, isTop?212:130, isTop?255:155);
      doc.text('#'+(ri+1)+'  '+fr.en, ml+4, cy+7);
      // bar
      const bx=ml+cw-54, bw=42;
      doc.setFillColor(30,45,74); doc.roundedRect(bx,cy+3.5,bw,3,0.5,0.5,'F');
      if (isTop) doc.setFillColor(0,212,255); else doc.setFillColor(80,90,120);
      doc.roundedRect(bx,cy+3.5,bw*p,3,0.5,0.5,'F');
      helvetica('normal',7);
      doc.setTextColor(136,153,187);
      doc.text((p*100).toFixed(1)+'%', ml+cw-4, cy+7, {align:'right'});
      cy += 11;
    });
    cy += 5;

    // ════ CLINICAL NOTE ════
    doc.setFillColor(21,28,46); doc.setDrawColor(0,212,255); doc.setLineWidth(0.3);
    setF('normal', 8.5);
    const descLines = doc.splitTextToSize(descText, cw-10);
    const descH = Math.max(descLines.length * 5.5, 10) + 15;
    doc.roundedRect(ml, cy, cw, descH, 2,2,'FD');
    helvetica('bold', 7.5);
    doc.setTextColor(0,212,255);
    doc.text(L.clinicalNote, ml+4, cy+6.5);
    setF('normal', 8.5);
    doc.setTextColor(185,205,230);
    doc.text(descLines, ml+5, cy+13);
    cy += descH + 6;

    // ════ TREATMENT / MANAGEMENT ════
    if (treatment.length > 0) {
      const cleanTreat = treatment.map(s => typeof s==='string' ? s : '');
      setF('normal', 8.5);
      const treatH = cleanTreat.length * 7.5 + 15;
      doc.setFillColor(15,25,18); doc.setDrawColor(16,185,129); doc.setLineWidth(0.3);
      doc.roundedRect(ml, cy, cw, treatH, 2,2,'FD');
      helvetica('bold', 7.5);
      doc.setTextColor(16,185,129);
      doc.text(L.management, ml+4, cy+6.5);
      setF('normal', 8.5);
      doc.setTextColor(175,220,190);
      cleanTreat.forEach((s,i) => {
        const lines = doc.splitTextToSize('> '+s, cw-10);
        doc.text(lines[0]||'', ml+5, cy+14+i*7.5);
      });
      cy += treatH + 6;
    }

    // ════ ALL 10 CLASSES TABLE ════
    if (cy < ph-55) {
      cy = sectionHeader(L.allTypes, cy);
      const cols=2, cellW=cw/cols;
      fractures.forEach((fr,idx) => {
        const col=idx%cols, row=Math.floor(idx/cols);
        const cx2=ml+col*cellW, ry2=cy+row*9.5;
        const p2=mockPrediction.probs[idx];
        const isM=idx===mockPrediction.mainIdx;
        if (isM) { doc.setFillColor(0,28,48); doc.roundedRect(cx2,ry2-1,cellW-1,8.5,1,1,'F'); }
        helvetica(isM?'bold':'normal', 7);
        doc.setTextColor(isM?0:115, isM?212:125, isM?255:150);
        doc.text((isM?'> ':' ')+fr.en.slice(0,24), cx2+2, ry2+5);
        doc.setFillColor(30,45,74);
        doc.roundedRect(cx2+cellW-27, ry2+2, 17, 2.5, 0.5,0.5,'F');
        doc.setFillColor(isM?0:65, isM?212:78, isM?255:108);
        doc.roundedRect(cx2+cellW-27, ry2+2, 17*p2, 2.5, 0.5,0.5,'F');
        helvetica('normal',6);
        doc.setTextColor(100,120,150);
        doc.text((p2*100).toFixed(1)+'%', cx2+cellW-2, ry2+5, {align:'right'});
      });
    }

    // ════ FOOTER ════
    const fy=ph-16;
    doc.setDrawColor(30,45,74); doc.setLineWidth(0.3);
    doc.line(ml,fy-5,pw-mr,fy-5);
    setF('normal', 6.5);
    doc.setTextColor(100,115,140);
    const footerLines = doc.splitTextToSize(L.footer, cw-40);
    doc.text(footerLines[0]||'', ml, fy);
    helvetica('normal',6.5);
    doc.text('BoneScan AI  |  EfficientNet-B2  |  2026', pw-mr, fy, {align:'right'});

    // ════ X-RAY THUMBNAIL ════
    try {
      const canvas = document.getElementById('xrayCanvas');
      if (canvas && canvas.width > 0) {
        const imgData = canvas.toDataURL('image/jpeg', 0.8);
        doc.addImage(imgData,'JPEG', pw-mr-40, 52, 38, 38);
        doc.setDrawColor(0,212,255); doc.setLineWidth(0.6);
        doc.rect(pw-mr-40, 52, 38, 38);
        doc.setFillColor(0,212,255);
        doc.rect(pw-mr-40, 90, 38, 5,'F');
        helvetica('bold',5.5);
        doc.setTextColor(10,14,26);
        doc.text('X-RAY SCAN', pw-mr-40+19, 93.5, {align:'center'});
      }
    } catch(_){}

    doc.save('BoneScan_Report_'+lang+'_'+f.id+'_'+now.toISOString().slice(0,10)+'.pdf');

  } catch(err) {
    console.error('PDF generation error:', err);
    alert('PDF 생성 오류 / PDF Error: '+err.message);
  } finally {
    btn.classList.remove('generating');
    const t2 = T[currentLang]||T.en;
    span.textContent = t2.btnPdf || '📄 Download PDF';
  }
}

// ════════════════════════════════════════════════
// INIT
// ════════════════════════════════════════════════
renderClassesGrid();
renderRegionList(null);
setLang('zh');
</script>
</body>
</html>
