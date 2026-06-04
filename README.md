<!DOCTYPE html>
<html lang="zh">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🦴 BoneScan AI — 骨折智能分析</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Noto+Sans+SC:wght@300;400;600&family=Noto+Sans:wght@300;400;600&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
<style>
  /* ── CSS Variables ── */
  :root {
    --bg: #0a0e1a;
    --bg2: #111827;
    --card: #151c2e;
    --border: #1e2d4a;
    --accent: #00d4ff;
    --accent2: #7c3aed;
    --accent3: #10b981;
    --text: #e2e8f0;
    --text2: #8899bb;
    --danger: #ef4444;
    --warn: #f59e0b;
    --grid: rgba(0,212,255,0.03);
    --mono: 'Space Mono', monospace;
    --sans: 'Noto Sans SC', 'Noto Sans', sans-serif;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: var(--sans);
    min-height: 100vh;
    overflow-x: hidden;
    background-image:
      linear-gradient(var(--grid) 1px, transparent 1px),
      linear-gradient(90deg, var(--grid) 1px, transparent 1px);
    background-size: 40px 40px;
  }

  /* ── Animated background orbs ── */
  .orb {
    position: fixed; border-radius: 50%; filter: blur(80px);
    pointer-events: none; z-index: 0; opacity: 0.15;
    animation: drift 18s ease-in-out infinite alternate;
  }
  .orb1 { width:500px;height:500px;background:var(--accent2);top:-150px;left:-150px; }
  .orb2 { width:400px;height:400px;background:var(--accent);bottom:-100px;right:-100px;animation-delay:-9s; }
  @keyframes drift { from{transform:translate(0,0)} to{transform:translate(60px,40px)} }

  /* ── Layout ── */
  .container { max-width:1100px;margin:0 auto;padding:0 24px;position:relative;z-index:1; }

  /* ── Header ── */
  header {
    border-bottom: 1px solid var(--border);
    backdrop-filter: blur(20px);
    background: rgba(10,14,26,0.85);
    position: sticky; top: 0; z-index: 100;
  }
  .header-inner {
    display: flex; align-items: center; justify-content: space-between;
    padding: 14px 24px; max-width: 1100px; margin: 0 auto;
  }
  .logo {
    font-family: var(--mono); font-size: 1.1rem; font-weight: 700;
    color: var(--accent); letter-spacing: 0.05em;
    display: flex; align-items: center; gap: 8px;
  }
  .logo-dot {
    width: 10px; height: 10px; border-radius: 50%;
    background: var(--accent); box-shadow: 0 0 12px var(--accent);
    animation: pulse 2s ease-in-out infinite;
  }
  @keyframes pulse { 0%,100%{opacity:1;transform:scale(1)} 50%{opacity:0.5;transform:scale(0.8)} }

  /* ── Language Selector ── */
  .lang-bar {
    display: flex; gap: 6px; flex-wrap: wrap;
  }
  .lang-btn {
    padding: 4px 12px; border-radius: 20px; font-size: 0.75rem;
    border: 1px solid var(--border); background: transparent;
    color: var(--text2); cursor: pointer; transition: all 0.2s;
    font-family: var(--mono);
  }
  .lang-btn:hover, .lang-btn.active {
    border-color: var(--accent); color: var(--accent);
    background: rgba(0,212,255,0.08);
    box-shadow: 0 0 8px rgba(0,212,255,0.2);
  }

  /* ── Hero ── */
  .hero {
    text-align: center; padding: 72px 0 48px;
  }
  .hero-tag {
    display: inline-block;
    border: 1px solid var(--accent2);
    color: var(--accent); background: rgba(124,58,237,0.1);
    padding: 4px 16px; border-radius: 20px;
    font-family: var(--mono); font-size: 0.72rem; letter-spacing: 0.12em;
    margin-bottom: 20px; text-transform: uppercase;
  }
  h1 {
    font-size: clamp(2rem, 5vw, 3.5rem);
    font-weight: 600; line-height: 1.15;
    background: linear-gradient(135deg, #e2e8f0 0%, var(--accent) 60%, var(--accent2) 100%);
    -webkit-background-clip: text; -webkit-text-fill-color: transparent;
    background-clip: text;
    margin-bottom: 16px;
  }
  .hero-sub {
    color: var(--text2); font-size: 1rem; max-width: 560px;
    margin: 0 auto 40px; line-height: 1.8; font-weight: 300;
  }

  /* ── Upload Zone ── */
  .upload-zone {
    border: 2px dashed var(--border);
    border-radius: 16px;
    padding: 48px 32px;
    text-align: center;
    cursor: pointer;
    transition: all 0.3s;
    background: var(--card);
    position: relative;
    overflow: hidden;
  }
  .upload-zone::before {
    content: '';
    position: absolute; inset: 0;
    background: radial-gradient(circle at center, rgba(0,212,255,0.05), transparent 70%);
    opacity: 0; transition: opacity 0.3s;
  }
  .upload-zone:hover, .upload-zone.drag-over {
    border-color: var(--accent);
    box-shadow: 0 0 30px rgba(0,212,255,0.12);
  }
  .upload-zone:hover::before, .upload-zone.drag-over::before { opacity:1; }
  .upload-icon {
    font-size: 3.5rem; margin-bottom: 16px;
    animation: float 3s ease-in-out infinite;
  }
  @keyframes float { 0%,100%{transform:translateY(0)} 50%{transform:translateY(-8px)} }
  .upload-title {
    font-size: 1.1rem; font-weight: 600; color: var(--text);
    margin-bottom: 8px;
  }
  .upload-sub { color: var(--text2); font-size: 0.85rem; }
  .upload-hint {
    margin-top: 12px; font-family: var(--mono);
    font-size: 0.72rem; color: var(--accent2);
  }

  /* ── Preview & Result ── */
  .analysis-grid {
    display: grid; grid-template-columns: 1fr 1fr; gap: 20px;
    margin-top: 28px;
  }
  @media(max-width:640px) { .analysis-grid { grid-template-columns:1fr; } }

  .card {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 24px;
    position: relative;
    overflow: hidden;
  }
  .card::after {
    content:''; position:absolute;top:0;left:0;right:0;height:2px;
    background: linear-gradient(90deg, var(--accent), var(--accent2));
    opacity: 0.6;
  }
  .card-title {
    font-family: var(--mono); font-size: 0.72rem; letter-spacing: 0.1em;
    color: var(--accent); text-transform: uppercase; margin-bottom: 16px;
  }
  #preview-img {
    width:100%; border-radius:10px; max-height:280px;
    object-fit:cover; display:none;
    border: 1px solid var(--border);
  }
  .img-placeholder {
    height: 200px; display:flex; align-items:center; justify-content:center;
    color: var(--text2); font-size:0.85rem;
    border: 1px dashed var(--border); border-radius: 10px;
  }

  /* ── Result Panel ── */
  .result-empty {
    height:200px;display:flex;flex-direction:column;
    align-items:center;justify-content:center;
    color:var(--text2); font-size:0.85rem; gap:12px;
  }
  .result-main {
    display: none;
  }
  .diagnosis-label {
    font-size: 1.4rem; font-weight: 600; color: var(--accent);
    margin-bottom: 4px;
  }
  .confidence-row {
    display:flex; align-items:center; gap:12px; margin: 12px 0 20px;
  }
  .conf-bar-wrap {
    flex:1; height:6px; background:var(--border); border-radius:99px; overflow:hidden;
  }
  .conf-bar {
    height:100%; border-radius:99px;
    background: linear-gradient(90deg, var(--accent), var(--accent2));
    transition: width 1s ease;
  }
  .conf-text {
    font-family: var(--mono); font-size:0.85rem; color:var(--accent); min-width:44px;
  }

  /* ── Top-5 Bar Chart ── */
  .top5-label {
    font-family: var(--mono); font-size: 0.7rem; color: var(--text2);
    text-transform: uppercase; letter-spacing: 0.08em; margin-bottom:12px;
  }
  .bar-row {
    display:flex; align-items:center; gap:8px; margin-bottom:8px;
  }
  .bar-name {
    width: 160px; font-size:0.75rem; color:var(--text); white-space:nowrap;
    overflow:hidden; text-overflow:ellipsis; flex-shrink:0;
  }
  .bar-track {
    flex:1; height:8px; background:var(--border);
    border-radius:99px; overflow:hidden;
  }
  .bar-fill {
    height:100%; border-radius:99px;
    background: linear-gradient(90deg, var(--accent2), var(--accent));
    transition: width 0.8s ease;
  }
  .bar-pct {
    font-family:var(--mono); font-size:0.7rem; color:var(--text2); min-width:38px; text-align:right;
  }

  /* ── Description ── */
  .desc-box {
    margin-top:20px; padding:16px;
    background:rgba(0,212,255,0.04);
    border:1px solid rgba(0,212,255,0.12);
    border-radius:10px; font-size:0.85rem;
    line-height:1.8; color:var(--text2);
  }
  .desc-box strong { color: var(--text); }

  /* ── Classes Grid ── */
  .classes-section { margin: 60px 0; }
  .section-title {
    font-family: var(--mono); font-size: 0.8rem; letter-spacing: 0.15em;
    color: var(--accent); text-transform: uppercase; margin-bottom: 24px;
    display:flex; align-items:center; gap:12px;
  }
  .section-title::after {
    content:''; flex:1; height:1px; background:var(--border);
  }
  .classes-grid {
    display: grid; grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    gap: 14px;
  }
  .cls-card {
    background: var(--card); border: 1px solid var(--border);
    border-radius: 12px; padding: 16px;
    transition: all 0.25s; cursor: default;
  }
  .cls-card:hover {
    border-color: var(--accent2);
    transform: translateY(-2px);
    box-shadow: 0 8px 24px rgba(124,58,237,0.15);
  }
  .cls-icon { font-size:1.6rem; margin-bottom:8px; }
  .cls-name { font-size:0.82rem; font-weight:600; color:var(--text); margin-bottom:4px; }
  .cls-name-zh { font-size:0.72rem; color:var(--accent2); }

  /* ── Analyze Button ── */
  .btn-analyze {
    width:100%; padding:14px; margin-top:20px;
    background: linear-gradient(135deg, var(--accent2), var(--accent));
    border: none; border-radius: 12px; color: #fff;
    font-size:1rem; font-weight:600; cursor:pointer;
    font-family: var(--sans);
    transition: all 0.3s;
    position:relative; overflow:hidden;
    display:none;
  }
  .btn-analyze::before {
    content:''; position:absolute; inset:0;
    background: linear-gradient(135deg, var(--accent), var(--accent2));
    opacity:0; transition:opacity 0.3s;
  }
  .btn-analyze:hover::before { opacity:1; }
  .btn-analyze:hover { transform:translateY(-1px); box-shadow:0 8px 24px rgba(0,212,255,0.3); }
  .btn-analyze span { position:relative; z-index:1; }

  /* ── Loading ── */
  .loading-overlay {
    display:none; position:absolute; inset:0;
    background:rgba(10,14,26,0.85); border-radius:16px;
    flex-direction:column; align-items:center; justify-content:center;
    gap:16px; backdrop-filter:blur(6px);
  }
  .spinner {
    width:40px;height:40px;border-radius:50%;
    border:3px solid var(--border);
    border-top-color:var(--accent);
    animation:spin 0.8s linear infinite;
  }
  @keyframes spin{to{transform:rotate(360deg)}}
  .loading-text { font-family:var(--mono);font-size:0.8rem;color:var(--accent); }

  /* ── Footer ── */
  footer {
    border-top:1px solid var(--border); padding:28px 0;
    text-align:center; color:var(--text2); font-size:0.78rem;
    margin-top:60px;
  }
  .disclaimer {
    max-width:600px;margin:0 auto 8px;
    background:rgba(239,68,68,0.08);border:1px solid rgba(239,68,68,0.2);
    border-radius:8px;padding:10px 16px;color:var(--warn);font-size:0.75rem;
  }

  /* ── PDF Button ── */
  .btn-pdf-wrap {
    margin-top: 16px; display: none;
  }
  .btn-pdf {
    width: 100%; padding: 13px;
    background: transparent;
    border: 1.5px solid var(--accent3);
    border-radius: 12px; color: var(--accent3);
    font-size: 0.95rem; font-weight: 600; cursor: pointer;
    font-family: var(--sans);
    transition: all 0.3s;
    display: flex; align-items: center; justify-content: center; gap: 8px;
    letter-spacing: 0.02em;
  }
  .btn-pdf:hover {
    background: rgba(16,185,129,0.1);
    box-shadow: 0 0 20px rgba(16,185,129,0.2);
    transform: translateY(-1px);
  }
  .btn-pdf:active { transform: translateY(0); }
  .btn-pdf.generating {
    opacity: 0.7; cursor: not-allowed; pointer-events: none;
  }
  .btn-pdf svg { flex-shrink: 0; }

  /* ── PDF Hidden Canvas (report layout) ── */
  #pdf-report-canvas {
    position: fixed; left: -9999px; top: 0;
    width: 794px; /* A4 px at 96dpi */
    background: #ffffff;
    font-family: 'Noto Sans SC', 'Noto Sans', sans-serif;
    color: #1a1a2e;
    padding: 0;
    z-index: -1;
  }

  /* ── Misc ── */
  #file-input { display:none; }
  .hidden { display:none !important; }
</style>
</head>
<body>

<div class="orb orb1"></div>
<div class="orb orb2"></div>

<!-- ── HEADER ── -->
<header>
  <div class="header-inner">
    <div class="logo"><div class="logo-dot"></div>BONESCAN_AI</div>
    <div class="lang-bar" id="langBar">
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

<!-- ── MAIN ── -->
<main>
<div class="container">

  <!-- Hero -->
  <section class="hero">
    <div class="hero-tag" data-i18n="tag">AI Medical Imaging · Bone Fracture Analysis</div>
    <h1 data-i18n="title">骨折智能分类系统</h1>
    <p class="hero-sub" data-i18n="subtitle">
      上传 X 光片，AI 自动识别 10 种骨折类型，提供置信度分析与临床说明，支持 8 种语言。
    </p>
  </section>

  <!-- Upload -->
  <div class="upload-zone" id="uploadZone" onclick="document.getElementById('file-input').click()">
    <input type="file" id="file-input" accept="image/*" onchange="handleFile(event)">
    <div class="upload-icon">🩻</div>
    <div class="upload-title" data-i18n="uploadTitle">拖拽或点击上传 X 光图片</div>
    <div class="upload-sub" data-i18n="uploadSub">支持 JPG · PNG · BMP · TIFF</div>
    <div class="upload-hint" data-i18n="uploadHint">// max 20MB — X-Ray images recommended</div>
  </div>
  <button class="btn-analyze" id="btnAnalyze" onclick="analyzeImage()">
    <span data-i18n="btnAnalyze">🔬 开始分析</span>
  </button>

  <!-- Analysis Grid -->
  <div class="analysis-grid" id="analysisGrid" style="display:none">

    <!-- Preview Card -->
    <div class="card">
      <div class="card-title" data-i18n="previewLabel">// X-RAY PREVIEW</div>
      <div class="img-placeholder" id="imgPlaceholder" data-i18n="imgPlaceholder">尚未上传图片</div>
      <img id="preview-img" alt="preview">
    </div>

    <!-- Result Card -->
    <div class="card" style="position:relative">
      <div class="loading-overlay" id="loadingOverlay">
        <div class="spinner"></div>
        <div class="loading-text" data-i18n="analyzing">ANALYZING...</div>
      </div>
      <div class="card-title" data-i18n="resultLabel">// DIAGNOSIS RESULT</div>

      <div class="result-empty" id="resultEmpty">
        <span style="font-size:2rem">🔎</span>
        <span data-i18n="waitAnalysis">等待分析...</span>
      </div>

      <div class="result-main" id="resultMain">
        <div class="diagnosis-label" id="diagLabel">—</div>
        <div style="font-size:0.75rem;color:var(--text2);margin-bottom:8px" id="diagLabelZh"></div>
        <div class="confidence-row">
          <div class="conf-bar-wrap"><div class="conf-bar" id="confBar" style="width:0%"></div></div>
          <div class="conf-text" id="confText">0%</div>
        </div>
        <div class="top5-label" data-i18n="top5Label">TOP-5 PROBABILITIES</div>
        <div id="top5Bars"></div>
        <div class="desc-box" id="descBox"></div>

        <!-- PDF Download Button -->
        <div class="btn-pdf-wrap" id="btnPdfWrap">
          <button class="btn-pdf" id="btnPdf" onclick="downloadPDF()">
            <svg width="16" height="16" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
              <path stroke-linecap="round" stroke-linejoin="round" d="M12 10v6m0 0l-3-3m3 3l3-3M3 17V7a2 2 0 012-2h6l2 2h4a2 2 0 012 2v8a2 2 0 01-2 2H5a2 2 0 01-2-2z"/>
            </svg>
            <span data-i18n="btnPdf">📄 下载分析报告 PDF</span>
          </button>
        </div>
      </div>
    </div>

  </div><!-- end analysis-grid -->

  <!-- Fracture Classes Reference -->
  <section class="classes-section">
    <div class="section-title" data-i18n="classesTitle">骨折类型参考</div>
    <div class="classes-grid" id="classesGrid"></div>
  </section>

</div><!-- end container -->
</main>

<!-- Hidden PDF Report Layout -->
<div id="pdf-report-canvas"></div>

<!-- Footer -->
<footer>
  <div class="container">
    <div class="disclaimer" data-i18n="disclaimer">
      ⚠️ 本工具仅供学术研究与教学用途，不构成医疗诊断建议。临床诊断请咨询持牌医疗专业人士。
    </div>
    <div>BoneScan AI · EfficientNet-B2 · Powered by PyTorch · 2026</div>
  </div>
</footer>

<!-- ── JAVASCRIPT ── -->
<script>
// ── i18n Data ─────────────────────────────────────────────────
const i18n = {
  zh: {
    tag:"AI 医学影像 · 骨折智能分析", title:"骨折智能分类系统",
    subtitle:"上传 X 光片，AI 自动识别 10 种骨折类型，提供置信度分析与临床说明，支持 8 种语言。",
    uploadTitle:"拖拽或点击上传 X 光图片", uploadSub:"支持 JPG · PNG · BMP · TIFF",
    uploadHint:"// 最大 20MB — 推荐上传 X 光片",
    btnAnalyze:"🔬 开始分析", previewLabel:"// X 光预览",
    imgPlaceholder:"尚未上传图片", resultLabel:"// 诊断结果",
    waitAnalysis:"等待分析...", analyzing:"分析中...",
    top5Label:"TOP-5 概率分布", classesTitle:"骨折类型参考",
    disclaimer:"⚠️ 本工具仅供学术研究与教学用途，不构成医疗诊断建议。临床诊断请咨询持牌医疗专业人士。",
    confidence:"置信度", desc_label:"临床说明", btnPdf:"📄 下载分析报告 PDF",
    pdf_title:"骨折分析报告", pdf_patient:"患者信息", pdf_date:"检查日期",
    pdf_diagnosis:"AI 诊断结果", pdf_confidence:"置信度", pdf_top5:"概率分布（Top 5）",
    pdf_clinical:"临床说明", pdf_disclaimer:"免责声明", pdf_footer:"本报告由 BoneScan AI 自动生成，仅供学术研究与教学参考，不构成临床医疗诊断依据。",
    pdf_model:"分析模型", pdf_generating:"正在生成 PDF..."
  },
  en: {
    tag:"AI Medical Imaging · Bone Fracture Analysis", title:"Bone Fracture AI Classifier",
    subtitle:"Upload an X-ray image and AI will identify 10 types of bone fractures with confidence scores and clinical notes in 8 languages.",
    uploadTitle:"Drag & drop or click to upload X-Ray", uploadSub:"Supports JPG · PNG · BMP · TIFF",
    uploadHint:"// max 20MB — X-Ray images recommended",
    btnAnalyze:"🔬 Analyze Image", previewLabel:"// X-RAY PREVIEW",
    imgPlaceholder:"No image uploaded yet", resultLabel:"// DIAGNOSIS RESULT",
    waitAnalysis:"Waiting for analysis...", analyzing:"ANALYZING...",
    top5Label:"TOP-5 PROBABILITIES", classesTitle:"Fracture Type Reference",
    disclaimer:"⚠️ This tool is for academic and educational purposes only and does not constitute medical advice. Consult a licensed healthcare professional for clinical diagnosis.",
    confidence:"Confidence", desc_label:"Clinical Note", btnPdf:"📄 Download Analysis Report PDF",
    pdf_title:"Bone Fracture Analysis Report", pdf_patient:"Patient Info", pdf_date:"Examination Date",
    pdf_diagnosis:"AI Diagnosis", pdf_confidence:"Confidence", pdf_top5:"Probability Distribution (Top 5)",
    pdf_clinical:"Clinical Note", pdf_disclaimer:"Disclaimer", pdf_footer:"This report is auto-generated by BoneScan AI for academic and educational purposes only. It does not constitute clinical medical advice.",
    pdf_model:"Model", pdf_generating:"Generating PDF..."
  },
  ja: {
    tag:"AI医療画像 · 骨折分類", title:"骨折AIクラスファイヤー",
    subtitle:"X線画像をアップロードすると、AIが10種類の骨折を識別し、信頼スコアと臨床説明を提供します。",
    uploadTitle:"X線画像をドラッグまたはクリックしてアップロード", uploadSub:"JPG · PNG · BMP · TIFF対応",
    uploadHint:"// 最大20MB — X線画像推奨",
    btnAnalyze:"🔬 分析開始", previewLabel:"// X線プレビュー",
    imgPlaceholder:"画像未アップロード", resultLabel:"// 診断結果",
    waitAnalysis:"分析待ち...", analyzing:"分析中...",
    top5Label:"TOP-5 確率分布", classesTitle:"骨折タイプ一覧",
    disclaimer:"⚠️ 本ツールは学術・教育目的のみです。医療診断の代替ではありません。",
    confidence:"信頼度", desc_label:"臨床説明", btnPdf:"📄 分析レポート PDF ダウンロード",
    pdf_title:"骨折分析レポート", pdf_patient:"患者情報", pdf_date:"検査日",
    pdf_diagnosis:"AI 診断結果", pdf_confidence:"信頼度", pdf_top5:"確率分布（Top 5）",
    pdf_clinical:"臨床説明", pdf_disclaimer:"免責事項", pdf_footer:"本レポートは BoneScan AI により自動生成されました。学術・教育目的のみであり、医療診断の代替ではありません。",
    pdf_model:"モデル", pdf_generating:"PDF 生成中..."
  },
  ko: {
    tag:"AI 의료 영상 · 골절 분류", title:"골절 AI 분류 시스템",
    subtitle:"X선 이미지를 업로드하면 AI가 10가지 골절 유형을 식별하고 신뢰도와 임상 설명을 제공합니다.",
    uploadTitle:"X선 이미지를 드래그하거나 클릭하여 업로드", uploadSub:"JPG · PNG · BMP · TIFF 지원",
    uploadHint:"// 최대 20MB — X선 이미지 권장",
    btnAnalyze:"🔬 분석 시작", previewLabel:"// X선 미리보기",
    imgPlaceholder:"이미지가 업로드되지 않았습니다", resultLabel:"// 진단 결과",
    waitAnalysis:"분석 대기 중...", analyzing:"분석 중...",
    top5Label:"TOP-5 확률 분포", classesTitle:"골절 유형 참조",
    disclaimer:"⚠️ 이 도구는 학술 및 교육 목적으로만 사용됩니다. 임상 진단은 의료 전문가에게 문의하십시오.",
    confidence:"신뢰도", desc_label:"임상 설명", btnPdf:"📄 분석 보고서 PDF 다운로드",
    pdf_title:"골절 분석 보고서", pdf_patient:"환자 정보", pdf_date:"검사 날짜",
    pdf_diagnosis:"AI 진단 결과", pdf_confidence:"신뢰도", pdf_top5:"확률 분포 (Top 5)",
    pdf_clinical:"임상 설명", pdf_disclaimer:"면책 조항", pdf_footer:"이 보고서는 BoneScan AI에 의해 자동 생성되었으며 학술 및 교육 목적으로만 사용됩니다.",
    pdf_model:"모델", pdf_generating:"PDF 생성 중..."
  },
  fr: {
    tag:"Imagerie IA · Analyse des fractures", title:"Classificateur de Fractures par IA",
    subtitle:"Téléchargez une radiographie et l'IA identifiera 10 types de fractures avec scores de confiance et notes cliniques.",
    uploadTitle:"Glissez-déposez ou cliquez pour uploader la radio", uploadSub:"JPG · PNG · BMP · TIFF supportés",
    uploadHint:"// max 20MB — images X-Ray recommandées",
    btnAnalyze:"🔬 Analyser", previewLabel:"// APERÇU X-RAY",
    imgPlaceholder:"Aucune image uploadée", resultLabel:"// RÉSULTAT DU DIAGNOSTIC",
    waitAnalysis:"En attente d'analyse...", analyzing:"ANALYSE EN COURS...",
    top5Label:"TOP-5 PROBABILITÉS", classesTitle:"Référence des types de fractures",
    disclaimer:"⚠️ Cet outil est à des fins académiques uniquement et ne constitue pas un avis médical.",
    confidence:"Confiance", desc_label:"Note clinique", btnPdf:"📄 Télécharger le rapport PDF",
    pdf_title:"Rapport d'analyse des fractures", pdf_patient:"Informations patient", pdf_date:"Date d'examen",
    pdf_diagnosis:"Diagnostic IA", pdf_confidence:"Confiance", pdf_top5:"Distribution des probabilités (Top 5)",
    pdf_clinical:"Note clinique", pdf_disclaimer:"Avertissement", pdf_footer:"Ce rapport est généré automatiquement par BoneScan AI à des fins académiques uniquement.",
    pdf_model:"Modèle", pdf_generating:"Génération du PDF..."
  },
  de: {
    tag:"KI-Medizinbild · Knochenbruchanalyse", title:"Knochenbruch KI-Klassifikator",
    subtitle:"Laden Sie ein Röntgenbild hoch, um 10 Bruchtypen mit Konfidenzwerten und klinischen Hinweisen zu identifizieren.",
    uploadTitle:"Röntgenbild ziehen oder klicken", uploadSub:"JPG · PNG · BMP · TIFF unterstützt",
    uploadHint:"// max 20MB — Röntgenbilder empfohlen",
    btnAnalyze:"🔬 Analysieren", previewLabel:"// RÖNTGEN-VORSCHAU",
    imgPlaceholder:"Kein Bild hochgeladen", resultLabel:"// DIAGNOSEERGEBNIS",
    waitAnalysis:"Warte auf Analyse...", analyzing:"ANALYSIERE...",
    top5Label:"TOP-5 WAHRSCHEINLICHKEITEN", classesTitle:"Bruchtyp-Referenz",
    disclaimer:"⚠️ Dieses Tool dient nur akademischen Zwecken und stellt keine medizinische Beratung dar.",
    confidence:"Konfidenz", desc_label:"Klinischer Hinweis", btnPdf:"📄 Analysebericht PDF herunterladen",
    pdf_title:"Knochenbruch-Analysebericht", pdf_patient:"Patienteninformationen", pdf_date:"Untersuchungsdatum",
    pdf_diagnosis:"KI-Diagnose", pdf_confidence:"Konfidenz", pdf_top5:"Wahrscheinlichkeitsverteilung (Top 5)",
    pdf_clinical:"Klinischer Hinweis", pdf_disclaimer:"Haftungsausschluss", pdf_footer:"Dieser Bericht wurde automatisch von BoneScan AI generiert und dient nur akademischen Zwecken.",
    pdf_model:"Modell", pdf_generating:"PDF wird erstellt..."
  },
  es: {
    tag:"Imagen IA · Análisis de fracturas", title:"Clasificador de Fracturas por IA",
    subtitle:"Suba una radiografía y la IA identificará 10 tipos de fracturas con puntuaciones de confianza y notas clínicas.",
    uploadTitle:"Arrastra o haz clic para subir la radiografía", uploadSub:"JPG · PNG · BMP · TIFF admitidos",
    uploadHint:"// máx 20MB — se recomiendan imágenes de rayos X",
    btnAnalyze:"🔬 Analizar", previewLabel:"// VISTA PREVIA",
    imgPlaceholder:"No se ha subido ninguna imagen", resultLabel:"// RESULTADO",
    waitAnalysis:"Esperando análisis...", analyzing:"ANALIZANDO...",
    top5Label:"TOP-5 PROBABILIDADES", classesTitle:"Referencia de tipos de fractura",
    disclaimer:"⚠️ Esta herramienta es solo para fines académicos y no constituye asesoramiento médico.",
    confidence:"Confianza", desc_label:"Nota clínica", btnPdf:"📄 Descargar informe PDF",
    pdf_title:"Informe de análisis de fracturas", pdf_patient:"Información del paciente", pdf_date:"Fecha de examen",
    pdf_diagnosis:"Diagnóstico IA", pdf_confidence:"Confianza", pdf_top5:"Distribución de probabilidades (Top 5)",
    pdf_clinical:"Nota clínica", pdf_disclaimer:"Aviso legal", pdf_footer:"Este informe es generado automáticamente por BoneScan AI solo con fines académicos.",
    pdf_model:"Modelo", pdf_generating:"Generando PDF..."
  },
  ar: {
    tag:"تصوير الذكاء الاصطناعي · تحليل الكسور", title:"نظام تصنيف الكسور بالذكاء الاصطناعي",
    subtitle:"قم بتحميل صورة الأشعة السينية وسيحدد الذكاء الاصطناعي 10 أنواع من كسور العظام.",
    uploadTitle:"اسحب أو انقر لتحميل صورة الأشعة", uploadSub:"يدعم JPG · PNG · BMP · TIFF",
    uploadHint:"// الحد الأقصى 20 ميجابايت",
    btnAnalyze:"🔬 تحليل", previewLabel:"// معاينة الأشعة السينية",
    imgPlaceholder:"لم يتم تحميل أي صورة", resultLabel:"// نتيجة التشخيص",
    waitAnalysis:"في انتظار التحليل...", analyzing:"جارٍ التحليل...",
    top5Label:"أعلى 5 احتمالات", classesTitle:"مرجع أنواع الكسور",
    disclaimer:"⚠️ هذه الأداة لأغراض أكاديمية فقط ولا تشكل نصيحة طبية.",
    confidence:"الثقة", desc_label:"ملاحظة سريرية", btnPdf:"📄 تحميل تقرير PDF",
    pdf_title:"تقرير تحليل الكسور", pdf_patient:"معلومات المريض", pdf_date:"تاريخ الفحص",
    pdf_diagnosis:"تشخيص الذكاء الاصطناعي", pdf_confidence:"الثقة", pdf_top5:"توزيع الاحتمالات (أعلى 5)",
    pdf_clinical:"ملاحظة سريرية", pdf_disclaimer:"إخلاء المسؤولية", pdf_footer:"تم إنشاء هذا التقرير تلقائياً بواسطة BoneScan AI للأغراض الأكاديمية فقط.",
    pdf_model:"النموذج", pdf_generating:"جارٍ إنشاء PDF..."
  }
};

// ── Fracture Data ──────────────────────────────────────────────
const fractures = [
  { id:"avulsion",      icon:"💥", en:"Avulsion Fracture",      zh:"撕脱性骨折",
    desc:{ zh:"肌腱或韧带拉力将骨碎片从主骨撕离，常见于运动损伤，好发于踝关节、肩关节及膝盖周围。",
           en:"A fragment of bone tears away from the main bone due to tendon/ligament traction. Common in sports injuries, especially around the ankle, shoulder, and knee." }},
  { id:"comminuted",    icon:"🔴", en:"Comminuted Fracture",    zh:"粉碎性骨折",
    desc:{ zh:"骨骼断裂成三个或更多碎片，常见于高能量创伤（如车祸），治疗复杂，通常需要手术固定。",
           en:"The bone shatters into 3+ fragments from high-energy trauma such as car accidents. Complex treatment often requiring surgical fixation." }},
  { id:"dislocation",  icon:"↔️", en:"Fracture Dislocation",   zh:"骨折脱位",
    desc:{ zh:"骨折伴随关节脱位，骨端移位并脱出关节面，是严重损伤，需要立即复位与固定。",
           en:"A fracture accompanied by joint dislocation. The bone end is displaced from the joint surface, requiring immediate reduction and stabilization." }},
  { id:"greenstick",   icon:"🌿", en:"Greenstick Fracture",    zh:"青枝骨折",
    desc:{ zh:"骨骼仅单侧断裂，另一侧弯曲未断，多见于儿童，因儿童骨骼弹性较强，如折绿枝。",
           en:"The bone bends and cracks on one side without breaking completely. Common in children due to higher bone flexibility — like a green twig." }},
  { id:"hairline",     icon:"〰️", en:"Hairline Fracture",      zh:"发丝骨折",
    desc:{ zh:"骨骼出现细微裂缝，通常为应力性骨折，X 光上仅见一条细线，多由反复施力或过度运动引起。",
           en:"A thin crack in the bone, typically a stress fracture. Visible only as a fine line on X-ray, caused by repetitive force or overuse." }},
  { id:"impacted",     icon:"🪨", en:"Impacted Fracture",       zh:"嵌插骨折",
    desc:{ zh:"骨折两端互相嵌入对方，形成稳定的压缩状态，常见于跌倒时轴向受力，如股骨颈骨折。",
           en:"The broken bone ends are driven into each other, creating a compressed, stable fracture. Common in falls with axial loading, e.g., femoral neck." }},
  { id:"longitudinal", icon:"⬇️", en:"Longitudinal Fracture",  zh:"纵形骨折",
    desc:{ zh:"骨折线沿骨骼长轴延伸，通常由沿纵向施加的力引起，在长管骨中较为罕见。",
           en:"The fracture line runs along the long axis of the bone, typically caused by longitudinal loading. Relatively rare in long bones." }},
  { id:"oblique",      icon:"↗️", en:"Oblique Fracture",        zh:"斜形骨折",
    desc:{ zh:"骨折线呈斜形切过骨骼，通常由扭转或角向力引起，常见于胫骨及桡骨。",
           en:"The fracture line runs diagonally across the bone, caused by twisting or angular forces. Common in the tibia and radius." }},
  { id:"pathological", icon:"🧬", en:"Pathological Fracture",   zh:"病理性骨折",
    desc:{ zh:"骨骼因疾病（如骨质疏松、肿瘤或感染）而弱化，轻微外力即可引发骨折，需同时治疗原发病。",
           en:"Fracture occurs through bone weakened by disease such as osteoporosis, tumors, or infection. Requires treating the underlying condition." }},
  { id:"spiral",       icon:"🌀", en:"Spiral Fracture",          zh:"螺旋形骨折",
    desc:{ zh:"骨折线螺旋环绕骨干，由扭转力引起，常见于滑雪、踢球等运动中的小腿骨折。",
           en:"The fracture spirals around the bone shaft due to twisting forces. Often seen in skiing or sports-related lower leg injuries." }},
];

// ── Current State ──────────────────────────────────────────────
let currentLang = 'zh';
let uploadedFile = null;
let mockPrediction = null;

// ── Language Switch ────────────────────────────────────────────
function setLang(lang) {
  currentLang = lang;
  // update buttons
  document.querySelectorAll('.lang-btn').forEach(b => {
    b.classList.toggle('active', b.getAttribute('onclick').includes(`'${lang}'`));
  });
  // update all data-i18n
  document.querySelectorAll('[data-i18n]').forEach(el => {
    const key = el.getAttribute('data-i18n');
    if (i18n[lang] && i18n[lang][key]) el.textContent = i18n[lang][key];
    else if (i18n['en'][key]) el.textContent = i18n['en'][key];
  });
  // re-render classes grid
  renderClassesGrid();
  // if result shown, update description
  if (mockPrediction) showResult(mockPrediction);
  // update dir for Arabic
  document.body.style.direction = lang === 'ar' ? 'rtl' : 'ltr';
}

// ── Render Classes Grid ────────────────────────────────────────
function renderClassesGrid() {
  const grid = document.getElementById('classesGrid');
  grid.innerHTML = fractures.map(f => `
    <div class="cls-card">
      <div class="cls-icon">${f.icon}</div>
      <div class="cls-name">${f.en}</div>
      <div class="cls-name-zh">${f.zh}</div>
    </div>
  `).join('');
}

// ── File Handling ──────────────────────────────────────────────
function handleFile(e) {
  const file = e.target.files[0];
  if (!file) return;
  if (file.size > 20 * 1024 * 1024) {
    alert('File too large (max 20MB)'); return;
  }
  uploadedFile = file;
  const reader = new FileReader();
  reader.onload = ev => {
    const img = document.getElementById('preview-img');
    img.src = ev.target.result;
    img.style.display = 'block';
    document.getElementById('imgPlaceholder').style.display = 'none';
    document.getElementById('analysisGrid').style.display = 'grid';
    document.getElementById('btnAnalyze').style.display = 'block';
  };
  reader.readAsDataURL(file);
}

// drag-and-drop
const zone = document.getElementById('uploadZone');
zone.addEventListener('dragover', e => { e.preventDefault(); zone.classList.add('drag-over'); });
zone.addEventListener('dragleave', () => zone.classList.remove('drag-over'));
zone.addEventListener('drop', e => {
  e.preventDefault(); zone.classList.remove('drag-over');
  const dt = e.dataTransfer;
  if (dt.files[0]) {
    document.getElementById('file-input').files = dt.files;
    handleFile({ target: { files: dt.files } });
  }
});

// ── Analyze (Mock for Static Site) ────────────────────────────
// In production this calls your FastAPI / Cloud Run backend.
// Here we simulate a realistic prediction for demo purposes.
function analyzeImage() {
  if (!uploadedFile) return;
  // Show loading
  document.getElementById('loadingOverlay').style.display = 'flex';
  document.getElementById('resultEmpty').style.display = 'none';
  document.getElementById('resultMain').style.display = 'none';

  setTimeout(() => {
    // Mock: pick a random fracture + realistic probability distribution
    const mainIdx = Math.floor(Math.random() * fractures.length);
    const probs = generateMockProbs(mainIdx);
    mockPrediction = { mainIdx, probs };
    document.getElementById('loadingOverlay').style.display = 'none';
    showResult(mockPrediction);
  }, 1800 + Math.random() * 800);
}

function generateMockProbs(mainIdx) {
  const raw = fractures.map((_, i) => i === mainIdx
    ? 0.55 + Math.random() * 0.35
    : Math.random() * 0.15
  );
  const sum = raw.reduce((a, b) => a + b, 0);
  return raw.map(v => v / sum);
}

function showResult({ mainIdx, probs }) {
  const f = fractures[mainIdx];
  const conf = probs[mainIdx];
  const t = i18n[currentLang] || i18n['en'];

  // Main label
  document.getElementById('diagLabel').textContent = f.en;
  document.getElementById('diagLabelZh').textContent =
    currentLang === 'zh' ? '' : f.zh;
  document.getElementById('confText').textContent = (conf * 100).toFixed(1) + '%';
  document.getElementById('confBar').style.width = (conf * 100) + '%';

  // Top-5 bars
  const sorted = probs.map((p, i) => ({ p, f: fractures[i] }))
    .sort((a, b) => b.p - a.p).slice(0, 5);
  document.getElementById('top5Bars').innerHTML = sorted.map(({ p, f: fr }) => `
    <div class="bar-row">
      <div class="bar-name" title="${fr.en}">${fr.en.length > 20 ? fr.en.slice(0,18)+'…' : fr.en}</div>
      <div class="bar-track"><div class="bar-fill" style="width:${p*100}%"></div></div>
      <div class="bar-pct">${(p*100).toFixed(1)}%</div>
    </div>
  `).join('');

  // Description
  const descLang = ['zh','en','ja','ko','fr','de','es','ar'].includes(currentLang)
    ? (f.desc[currentLang] || f.desc['en']) : f.desc['en'];
  document.getElementById('descBox').innerHTML =
    `<strong>${t.desc_label || 'Clinical Note'}:</strong> ${descLang}`;

  document.getElementById('resultMain').style.display = 'block';
  // Show PDF download button
  document.getElementById('btnPdfWrap').style.display = 'block';
  // Update PDF button text for current language
  const pdfSpan = document.querySelector('#btnPdf span');
  if (pdfSpan) {
    const t2 = i18n[currentLang] || i18n['en'];
    pdfSpan.textContent = t2.btnPdf || '📄 Download PDF';
  }
}

// ── PDF Generation ─────────────────────────────────────────────
async function downloadPDF() {
  if (!mockPrediction) return;
  const t = i18n[currentLang] || i18n['en'];
  const btn = document.getElementById('btnPdf');
  btn.classList.add('generating');
  btn.querySelector('span').textContent = t.pdf_generating || 'Generating PDF...';

  const { jsPDF } = window.jspdf;
  const doc = new jsPDF({ orientation: 'portrait', unit: 'mm', format: 'a4' });
  const pw = 210; // A4 width mm
  const ph = 297; // A4 height mm
  const ml = 18, mr = 18, mt = 20;
  const cw = pw - ml - mr; // content width

  const f = fractures[mockPrediction.mainIdx];
  const conf = mockPrediction.probs[mockPrediction.mainIdx];
  const sorted = mockPrediction.probs
    .map((p, i) => ({ p, fr: fractures[i] }))
    .sort((a, b) => b.p - a.p).slice(0, 5);
  const now = new Date();
  const dateStr = now.toLocaleDateString('zh-CN', { year:'numeric', month:'2-digit', day:'2-digit' });
  const timeStr = now.toLocaleTimeString('zh-CN', { hour:'2-digit', minute:'2-digit' });
  const descText = (f.desc[currentLang] || f.desc['en']).replace(/<[^>]+>/g, '');

  // ── Helpers ──
  const hex2rgb = h => {
    const r = parseInt(h.slice(1,3),16), g = parseInt(h.slice(3,5),16), b = parseInt(h.slice(5,7),16);
    return [r,g,b];
  };
  let cy = mt; // current Y cursor

  // ── HEADER BAND ──
  doc.setFillColor(10, 14, 26);
  doc.rect(0, 0, pw, 36, 'F');
  // accent line
  doc.setDrawColor(0, 212, 255);
  doc.setLineWidth(0.8);
  doc.line(0, 36, pw, 36);

  // Logo text
  doc.setFont('helvetica', 'bold');
  doc.setFontSize(16);
  doc.setTextColor(0, 212, 255);
  doc.text('BONESCAN AI', ml, 15);
  doc.setFontSize(8);
  doc.setFont('helvetica', 'normal');
  doc.setTextColor(136, 153, 187);
  doc.text('Medical Imaging AI · Bone Fracture Classification', ml, 22);

  // Report title (right)
  doc.setFontSize(9);
  doc.setTextColor(226, 232, 240);
  doc.setFont('helvetica', 'bold');
  const titleText = t.pdf_title || 'Bone Fracture Analysis Report';
  doc.text(titleText, pw - mr, 15, { align: 'right' });
  doc.setFont('helvetica', 'normal');
  doc.setFontSize(8);
  doc.setTextColor(136, 153, 187);
  doc.text(`${dateStr}  ${timeStr}`, pw - mr, 22, { align: 'right' });

  cy = 46;

  // ── REPORT ID & META ROW ──
  const reportId = 'RPT-' + Math.random().toString(36).slice(2,8).toUpperCase();
  doc.setFillColor(21, 28, 46);
  doc.setDrawColor(30, 45, 74);
  doc.roundedRect(ml, cy, cw, 14, 2, 2, 'FD');
  doc.setFontSize(7.5);
  doc.setTextColor(136, 153, 187);
  doc.setFont('helvetica', 'normal');
  doc.text(`Report ID: ${reportId}`, ml + 4, cy + 5.5);
  doc.text(`Model: EfficientNet-B2 · PyTorch 2.x`, ml + 4, cy + 10.5);
  doc.text(`${t.pdf_date || 'Date'}: ${dateStr} ${timeStr}`, pw - mr - 4, cy + 5.5, { align: 'right' });
  doc.text(`Lang: ${currentLang.toUpperCase()}`, pw - mr - 4, cy + 10.5, { align: 'right' });
  cy += 20;

  // ── SECTION: X-RAY + DIAGNOSIS (two-column) ──
  const colL = ml, colR = ml + cw / 2 + 4;
  const colW = cw / 2 - 4;

  // Left: X-Ray image
  const previewImg = document.getElementById('preview-img');
  if (previewImg && previewImg.src) {
    try {
      const imgCanvas = document.createElement('canvas');
      imgCanvas.width = 300; imgCanvas.height = 300;
      const ctx = imgCanvas.getContext('2d');
      ctx.fillStyle = '#0a0e1a';
      ctx.fillRect(0,0,300,300);
      const tmpImg = new Image();
      tmpImg.crossOrigin = 'anonymous';
      await new Promise(resolve => {
        tmpImg.onload = resolve; tmpImg.onerror = resolve;
        tmpImg.src = previewImg.src;
      });
      // fit image
      const iw = tmpImg.naturalWidth || 300, ih = tmpImg.naturalHeight || 300;
      const scale = Math.min(300/iw, 300/ih);
      const dw = iw*scale, dh = ih*scale;
      ctx.drawImage(tmpImg, (300-dw)/2, (300-dh)/2, dw, dh);
      const imgData = imgCanvas.toDataURL('image/jpeg', 0.9);
      // Box
      doc.setFillColor(21, 28, 46);
      doc.setDrawColor(30, 45, 74);
      doc.roundedRect(colL, cy, colW, colW + 8, 2, 2, 'FD');
      // Label
      doc.setFontSize(7); doc.setTextColor(0,212,255);
      doc.setFont('helvetica','bold');
      doc.text('// X-RAY INPUT', colL + 3, cy + 5.5);
      // Image
      doc.addImage(imgData, 'JPEG', colL + 2, cy + 8, colW - 4, colW - 4);
    } catch(e) {
      doc.setFillColor(21,28,46);
      doc.roundedRect(colL, cy, colW, colW+8, 2, 2, 'F');
      doc.setFontSize(8); doc.setTextColor(136,153,187);
      doc.text('[ X-Ray Image ]', colL + colW/2, cy + colW/2 + 4, { align:'center' });
    }
  }

  // Right: Diagnosis panel
  const rx = colR, ry = cy;
  doc.setFillColor(21, 28, 46);
  doc.setDrawColor(0, 212, 255);
  doc.setLineWidth(0.3);
  doc.roundedRect(rx, ry, colW, 12, 2, 2, 'FD');
  doc.setFontSize(7); doc.setTextColor(0,212,255); doc.setFont('helvetica','bold');
  doc.text(`// ${(t.pdf_diagnosis||'AI DIAGNOSIS').toUpperCase()}`, rx+3, ry+5.5);

  // Fracture name
  doc.setFontSize(13); doc.setTextColor(0,212,255); doc.setFont('helvetica','bold');
  doc.text(f.en, rx, ry + 20);
  doc.setFontSize(9); doc.setTextColor(124,58,237); doc.setFont('helvetica','normal');
  doc.text(f.zh, rx, ry + 27);

  // Confidence gauge
  const gaugeY = ry + 35;
  doc.setFontSize(7.5); doc.setTextColor(136,153,187);
  doc.text(t.pdf_confidence || 'Confidence', rx, gaugeY);
  doc.setFontSize(14); doc.setFont('helvetica','bold');
  const confPct = (conf*100).toFixed(1)+'%';
  // color by confidence
  if (conf > 0.75) doc.setTextColor(16,185,129);
  else if (conf > 0.5) doc.setTextColor(245,158,11);
  else doc.setTextColor(239,68,68);
  doc.text(confPct, rx + colW, gaugeY + 8, { align: 'right' });

  // Progress bar
  const barY = gaugeY + 12, barH = 3.5;
  doc.setFillColor(30,45,74);
  doc.roundedRect(rx, barY, colW, barH, 1, 1, 'F');
  doc.setFillColor(0, 212, 255);
  doc.roundedRect(rx, barY, colW * conf, barH, 1, 1, 'F');

  // Icon
  doc.setFontSize(22); doc.setTextColor(100,100,120);
  doc.text(f.icon.replace(/[\u{1F300}-\u{1FFFF}]/gu, ''), rx + colW - 12, ry + 30);

  // Separator
  const sepY = gaugeY + 20;
  doc.setDrawColor(30,45,74); doc.setLineWidth(0.3);
  doc.line(rx, sepY, rx + colW, sepY);

  // Top-5 mini bars (right column)
  doc.setFontSize(7); doc.setTextColor(0,212,255); doc.setFont('helvetica','bold');
  doc.text(`// ${(t.pdf_top5||'TOP-5').toUpperCase()}`, rx, sepY + 5);
  let barRowY = sepY + 10;
  sorted.forEach(({ p, fr }, idx) => {
    const isTop = idx === 0;
    doc.setFontSize(6.5);
    doc.setTextColor(isTop ? 226 : 136, isTop ? 232 : 153, isTop ? 240 : 187);
    doc.setFont('helvetica', isTop ? 'bold' : 'normal');
    const nm = fr.en.length > 22 ? fr.en.slice(0,20)+'…' : fr.en;
    doc.text(nm, rx, barRowY);
    // track
    doc.setFillColor(30,45,74);
    doc.roundedRect(rx, barRowY+1.5, colW - 14, 2.5, 0.5, 0.5, 'F');
    // fill
    if (isTop) doc.setFillColor(0,212,255);
    else doc.setFillColor(124,58,237);
    doc.roundedRect(rx, barRowY+1.5, (colW-14)*p, 2.5, 0.5, 0.5, 'F');
    // pct
    doc.setFontSize(6); doc.setTextColor(136,153,187);
    doc.text((p*100).toFixed(1)+'%', rx+colW, barRowY+3, { align:'right' });
    barRowY += 8;
  });

  cy += colW + 12;

  // ── SECTION: Clinical Note ──
  doc.setFillColor(0, 212, 255, 0.05);
  doc.setDrawColor(0,212,255);
  doc.setLineWidth(0.3);

  // Wrap description text
  doc.setFontSize(8); doc.setFont('helvetica','normal');
  const descLines = doc.splitTextToSize(descText, cw - 8);
  const descBoxH = descLines.length * 4.5 + 14;
  doc.setFillColor(21,28,46);
  doc.roundedRect(ml, cy, cw, descBoxH, 2, 2, 'FD');
  doc.setFontSize(7); doc.setTextColor(0,212,255); doc.setFont('helvetica','bold');
  doc.text(`// ${(t.pdf_clinical||'CLINICAL NOTE').toUpperCase()}`, ml+3, cy+5.5);
  doc.setFontSize(8); doc.setTextColor(180,195,215); doc.setFont('helvetica','normal');
  doc.text(descLines, ml+4, cy+11);
  cy += descBoxH + 8;

  // ── SECTION: All 10 Fracture Types Table ──
  doc.setFontSize(7); doc.setTextColor(0,212,255); doc.setFont('helvetica','bold');
  doc.setFillColor(21,28,46);
  doc.setDrawColor(30,45,74);
  doc.roundedRect(ml, cy, cw, 8, 2, 2, 'FD');
  doc.text('// ALL FRACTURE TYPES · PROBABILITY TABLE', ml+3, cy+5.5);
  cy += 12;

  const cols = 2, rows = Math.ceil(fractures.length / cols);
  const cellW = cw / cols;

  fractures.forEach((fr, idx) => {
    const col = idx % cols;
    const row = Math.floor(idx / cols);
    const cx2 = ml + col * cellW;
    const rowY = cy + row * 9;
    const p = mockPrediction.probs[idx];
    const isMain = idx === mockPrediction.mainIdx;

    if (isMain) {
      doc.setFillColor(0, 212, 255, 0.08);
      doc.roundedRect(cx2, rowY - 1, cellW - 1, 8, 1, 1, 'F');
    }

    doc.setFontSize(6.5);
    doc.setTextColor(isMain ? 0 : 136, isMain ? 212 : 153, isMain ? 255 : 187);
    doc.setFont('helvetica', isMain ? 'bold' : 'normal');
    const label = fr.en.length > 24 ? fr.en.slice(0,22)+'…' : fr.en;
    doc.text((isMain ? '▶ ' : '  ') + label, cx2 + 1, rowY + 4);

    // mini bar
    doc.setFillColor(30,45,74);
    doc.roundedRect(cx2 + cellW - 28, rowY + 1.5, 18, 2, 0.5, 0.5, 'F');
    if (isMain) doc.setFillColor(0,212,255);
    else doc.setFillColor(80,80,100);
    doc.roundedRect(cx2 + cellW - 28, rowY + 1.5, 18*p, 2, 0.5, 0.5, 'F');

    doc.setFontSize(6); doc.setTextColor(isMain?0:100, isMain?212:110, isMain?255:130);
    doc.text((p*100).toFixed(1)+'%', cx2 + cellW - 2, rowY + 4, { align:'right' });
  });

  cy += rows * 9 + 8;

  // ── FOOTER ──
  const footerY = ph - 18;
  doc.setDrawColor(30,45,74); doc.setLineWidth(0.3);
  doc.line(ml, footerY - 4, pw - mr, footerY - 4);
  doc.setFontSize(6.5); doc.setTextColor(100,115,140); doc.setFont('helvetica','normal');
  const footerText = t.pdf_footer || 'For academic use only.';
  const footerLines = doc.splitTextToSize(footerText, cw);
  doc.text(footerLines, ml, footerY);
  doc.text('BoneScan AI · EfficientNet-B2 · 2026', pw - mr, footerY, { align:'right' });

  // ── Save ──
  const filename = `BoneScan_Report_${f.id}_${now.toISOString().slice(0,10)}.pdf`;
  doc.save(filename);

  // Reset button
  btn.classList.remove('generating');
  btn.querySelector('span').textContent = t.btnPdf || '📄 Download PDF';
}

// ── Init ──────────────────────────────────────────────────────
renderClassesGrid();
setLang('zh');
</script>
</body>
</html>
