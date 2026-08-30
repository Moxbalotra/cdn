<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mox Rathore — Edge Node</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500;600;700&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#060911;
    --bg2:#0a101c;
    --panel:#0d1526;
    --panel-line:#1b2740;
    --cyan:#00f0ff;
    --green:#39ff99;
    --amber:#ffb454;
    --text:#e8edf5;
    --muted:#7c8aa0;
    --muted2:#4c5a75;
  }
  *{margin:0;padding:0;box-sizing:border-box;}
  html{scroll-behavior:smooth;}
  body{
    background:var(--bg);
    color:var(--text);
    font-family:'Inter',sans-serif;
    overflow-x:hidden;
    min-height:100vh;
    position:relative;
  }
  ::selection{background:var(--cyan);color:#000;}

  /* ---------- background network canvas ---------- */
  #net{
    position:fixed;
    inset:0;
    width:100%;
    height:100%;
    z-index:0;
    background:
      radial-gradient(ellipse 80% 60% at 50% 0%, #0d1830 0%, var(--bg) 60%);
  }
  .grid-overlay{
    position:fixed;
    inset:0;
    z-index:1;
    pointer-events:none;
    background-image:
      linear-gradient(rgba(0,240,255,0.035) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0,240,255,0.035) 1px, transparent 1px);
    background-size:44px 44px;
    mask-image: radial-gradient(ellipse 70% 70% at 50% 20%, black 20%, transparent 75%);
  }
  .scanline{
    position:fixed;
    inset:0;
    z-index:2;
    pointer-events:none;
    background:repeating-linear-gradient(
      to bottom,
      rgba(255,255,255,0.012) 0px,
      rgba(255,255,255,0.012) 1px,
      transparent 1px,
      transparent 3px
    );
    opacity:0.5;
  }

  main{position:relative;z-index:3;max-width:900px;margin:0 auto;padding:64px 24px 100px;}

  /* ---------- topbar ---------- */
  .topbar{
    display:flex;
    align-items:center;
    justify-content:space-between;
    padding-bottom:36px;
    border-bottom:1px solid var(--panel-line);
    margin-bottom:48px;
  }
  .logo{
    font-family:'JetBrains Mono',monospace;
    font-weight:700;
    font-size:20px;
    letter-spacing:0.5px;
    color:var(--cyan);
    display:flex;
    align-items:center;
    gap:8px;
  }
  .logo::before{
    content:'';
    width:9px;height:9px;border-radius:50%;
    background:var(--green);
    box-shadow:0 0 10px var(--green), 0 0 20px var(--green);
    animation:blink 1.8s ease-in-out infinite;
  }
  .logo{font-weight:500;color:var(--text);font-size:16px;}
  .logo-cdn{
    color:var(--cyan);
    font-weight:700;
    margin-left:6px;
    padding:2px 8px;
    border:1px solid rgba(0,240,255,0.35);
    border-radius:6px;
    font-size:13px;
    letter-spacing:1px;
  }
  @keyframes blink{0%,100%{opacity:1;}50%{opacity:0.3;}}
  .status-pill{
    font-family:'JetBrains Mono',monospace;
    font-size:11.5px;
    color:var(--muted);
    border:1px solid var(--panel-line);
    padding:6px 12px;
    border-radius:20px;
    letter-spacing:0.3px;
    display:flex;
    align-items:center;
    gap:7px;
  }
  .status-pill span.dot{width:6px;height:6px;border-radius:50%;background:var(--green);}

  /* ---------- hero / terminal ---------- */
  .hero{margin-bottom:64px;}
  .eyebrow{
    font-family:'JetBrains Mono',monospace;
    color:var(--green);
    font-size:13px;
    letter-spacing:2px;
    text-transform:uppercase;
    margin-bottom:18px;
    opacity:0;
    animation:fadeUp 0.6s ease forwards 0.1s;
  }
  .hero h1{
    font-family:'Space Grotesk',sans-serif;
    font-size:clamp(38px,7vw,64px);
    font-weight:700;
    line-height:1.05;
    letter-spacing:-1px;
    margin-bottom:20px;
    opacity:0;
    animation:fadeUp 0.7s ease forwards 0.25s;
  }
  .hero h1 .wave{display:inline-block;animation:wave 2.4s ease-in-out infinite;transform-origin:70% 70%;}
  @keyframes wave{
    0%,100%{transform:rotate(0deg);}
    10%{transform:rotate(16deg);}
    20%{transform:rotate(-8deg);}
    30%{transform:rotate(16deg);}
    40%{transform:rotate(-4deg);}
    50%{transform:rotate(10deg);}
    60%{transform:rotate(0deg);}
  }
  .hero .accent{
    background:linear-gradient(90deg,var(--cyan),var(--green));
    -webkit-background-clip:text;
    background-clip:text;
    -webkit-text-fill-color:transparent;
  }
  .terminal{
    font-family:'JetBrains Mono',monospace;
    font-size:15px;
    color:var(--muted);
    background:rgba(13,21,38,0.55);
    border:1px solid var(--panel-line);
    border-radius:10px;
    padding:16px 18px;
    max-width:560px;
    opacity:0;
    animation:fadeUp 0.7s ease forwards 0.4s;
  }
  .terminal .line1::before{content:'guest@cdn:~$ ';color:var(--muted2);}
  #typed{color:var(--text);}
  .cursor{
    display:inline-block;
    width:8px;height:16px;
    background:var(--cyan);
    margin-left:2px;
    vertical-align:-2px;
    animation:caret 0.9s steps(1) infinite;
  }
  @keyframes caret{0%,49%{opacity:1;}50%,100%{opacity:0;}}

  @keyframes fadeUp{
    from{opacity:0;transform:translateY(14px);}
    to{opacity:1;transform:translateY(0);}
  }

  .terminal{max-width:620px;}
  #termHistory{margin-top:2px;}
  #termHistory .out{padding:3px 0;color:var(--muted);}
  #termHistory .out b{color:var(--text);}
  #termHistory .cmd{padding:6px 0 3px;color:var(--text);}
  #termHistory .cmd::before{content:'guest@cdn:~$ ';color:var(--muted2);}
  #termHistory a{color:var(--cyan);}
  #termInputRow{outline:none;}
  #termLine{outline:none;color:var(--text);}
  .term-hint{
    margin-top:8px;font-size:11.5px;color:var(--muted2);
    opacity:0;transition:opacity 0.4s ease;
  }
  .term-hint.show{opacity:1;}
  .term-hint b{color:var(--green);}

  /* ---------- metrics panel ---------- */
  .metrics{
    display:grid;
    grid-template-columns:repeat(4,1fr);
    gap:1px;
    background:var(--panel-line);
    border:1px solid var(--panel-line);
    border-radius:14px;
    overflow:hidden;
    margin-bottom:56px;
    opacity:0;
    animation:fadeUp 0.7s ease forwards 0.7s;
  }
  .metric{
    background:var(--panel);
    padding:18px 16px;
    text-align:left;
  }
  .metric .m-lbl{
    font-family:'JetBrains Mono',monospace;
    font-size:10.5px;color:var(--muted2);
    letter-spacing:1px;text-transform:uppercase;
    display:flex;align-items:center;gap:6px;
    margin-bottom:8px;
  }
  .metric .m-lbl .lv{width:5px;height:5px;border-radius:50%;background:var(--green);box-shadow:0 0 6px var(--green);animation:blink 2s ease-in-out infinite;}
  .metric .m-val{
    font-family:'JetBrains Mono',monospace;
    font-size:22px;font-weight:700;color:var(--text);
    font-variant-numeric:tabular-nums;
  }
  .metric .m-val span{font-size:13px;color:var(--muted);font-weight:500;}
  .metric .m-bar{
    margin-top:10px;height:3px;border-radius:2px;background:rgba(255,255,255,0.06);overflow:hidden;
  }
  .metric .m-bar i{
    display:block;height:100%;background:linear-gradient(90deg,var(--cyan),var(--green));
    width:0%;transition:width 1s ease;
  }
  @media (max-width:640px){
    .metrics{grid-template-columns:repeat(2,1fr);}
  }

  /* copy-email button */
  .copy-btn{
    background:none;border:1px solid var(--panel-line);
    color:var(--muted);font-family:'JetBrains Mono',monospace;
    font-size:11px;border-radius:6px;padding:2px 8px;margin-left:8px;
    cursor:pointer;transition:border-color 0.2s ease,color 0.2s ease;
    vertical-align:1px;
  }
  .copy-btn:hover{border-color:var(--cyan);color:var(--cyan);}
  .copy-btn.copied{border-color:var(--green);color:var(--green);}

  /* ---------- profile node card ---------- */
  .node-card{
    position:relative;
    display:flex;
    align-items:center;
    gap:24px;
    background:linear-gradient(180deg, rgba(13,21,38,0.75), rgba(13,21,38,0.4));
    border:1px solid var(--panel-line);
    border-radius:16px;
    padding:26px 28px;
    margin-bottom:56px;
    opacity:0;
    animation:fadeUp 0.7s ease forwards 0.55s;
    overflow:hidden;
  }
  .node-card::before{
    content:'';
    position:absolute;
    top:-2px;left:-2px;right:-2px;bottom:-2px;
    background:conic-gradient(from 0deg, transparent 0%, var(--cyan) 8%, transparent 16%);
    animation:rotate-border 5s linear infinite;
    opacity:0.35;
    z-index:0;
  }
  .node-card-inner{
    position:relative;
    z-index:1;
    display:flex;
    align-items:center;
    gap:24px;
    width:100%;
    background:var(--panel);
    border-radius:14px;
    padding:20px 22px;
  }
  @keyframes rotate-border{to{transform:rotate(360deg);}}
  .avatar-wrap{position:relative;width:82px;height:82px;flex:none;}
  .avatar-ring{
    position:absolute;inset:-8px;
    border-radius:50%;
    border:1.5px dashed rgba(0,240,255,0.35);
    animation:spin 14s linear infinite;
  }
  @keyframes spin{to{transform:rotate(360deg);}}
  .avatar{
    width:82px;height:82px;border-radius:50%;
    background:radial-gradient(circle at 30% 30%, #16233b, #060911);
    border:1px solid var(--panel-line);
    display:flex;align-items:center;justify-content:center;
    font-family:'Space Grotesk',sans-serif;
    font-weight:700;font-size:28px;color:var(--cyan);
    box-shadow:0 0 24px rgba(0,240,255,0.15);
  }
  .node-meta h2{
    font-family:'Space Grotesk',sans-serif;
    font-size:21px;font-weight:600;letter-spacing:0.2px;
    margin-bottom:4px;
  }
  .node-meta .role{
    font-family:'JetBrains Mono',monospace;
    font-size:12.5px;color:var(--green);letter-spacing:0.3px;
  }
  .node-stats{
    margin-left:auto;
    display:flex;gap:26px;
  }
  .stat{text-align:right;}
  .stat .num{
    font-family:'JetBrains Mono',monospace;
    font-size:20px;font-weight:700;color:var(--text);
    font-variant-numeric:tabular-nums;
  }
  .stat .lbl{font-size:11px;color:var(--muted);letter-spacing:0.5px;text-transform:uppercase;margin-top:2px;display:flex;align-items:center;justify-content:flex-end;gap:5px;}
  .live-dot{width:5px;height:5px;border-radius:50%;background:var(--green);box-shadow:0 0 6px var(--green);animation:blink 2s ease-in-out infinite;}

  /* ---------- log / bullet section ---------- */
  .section-label{
    font-family:'JetBrains Mono',monospace;
    font-size:12px;color:var(--muted2);
    letter-spacing:2px;text-transform:uppercase;
    margin-bottom:16px;
    display:flex;align-items:center;gap:10px;
  }
  .section-label::after{content:'';flex:1;height:1px;background:var(--panel-line);}

  .log{margin-bottom:56px;}
  .log-line{
    display:flex;
    gap:14px;
    padding:14px 0;
    border-bottom:1px solid var(--panel-line);
    opacity:0;
    transform:translateX(-10px);
    transition:opacity 0.6s ease, transform 0.6s ease;
  }
  .log-line.in-view{opacity:1;transform:translateX(0);}
  .log-line:last-child{border-bottom:none;}
  .log-icon{
    flex:none;width:30px;height:30px;border-radius:8px;
    background:rgba(0,240,255,0.06);
    border:1px solid var(--panel-line);
    display:flex;align-items:center;justify-content:center;
    font-size:15px;
  }
  .log-text{font-size:15px;color:var(--muted);line-height:1.5;}
  .log-text b{color:var(--text);font-weight:600;}
  .log-text a{color:var(--cyan);text-decoration:none;border-bottom:1px dotted rgba(0,240,255,0.4);}
  .log-text a:hover{color:var(--green);border-color:var(--green);}

  /* ---------- connect / social nodes ---------- */
  .connect{margin-bottom:20px;}
  .social-grid{
    display:flex;
    flex-wrap:wrap;
    gap:14px;
  }
  .social-node{
    position:relative;
    display:flex;align-items:center;gap:10px;
    padding:12px 18px;
    border-radius:10px;
    border:1px solid var(--panel-line);
    background:var(--panel);
    color:var(--text);
    text-decoration:none;
    font-family:'JetBrains Mono',monospace;
    font-size:13.5px;
    transition:transform 0.25s ease, border-color 0.25s ease, box-shadow 0.25s ease;
  }
  .social-node:hover{
    transform:translateY(-3px);
    border-color:var(--cyan);
    box-shadow:0 8px 24px rgba(0,240,255,0.12);
  }
  .social-node .ic{width:16px;height:16px;flex:none;}
  .social-node::before{
    content:'';
    position:absolute;left:12px;top:50%;
    width:5px;height:5px;border-radius:50%;
    background:var(--muted2);
    display:none;
  }

  footer{
    margin-top:60px;
    padding-top:26px;
    border-top:1px solid var(--panel-line);
    font-family:'JetBrains Mono',monospace;
    font-size:12.5px;
    color:var(--muted2);
    display:flex;
    justify-content:space-between;
    flex-wrap:wrap;
    gap:10px;
  }
  footer .ping{color:var(--green);}

  @media (max-width:560px){
    .node-card-inner{flex-wrap:wrap;}
    .node-stats{margin-left:0;width:100%;justify-content:space-between;margin-top:14px;padding-top:14px;border-top:1px solid var(--panel-line);}
  }

  @media (prefers-reduced-motion: reduce){
    *{animation-duration:0.01ms !important; animation-iteration-count:1 !important; transition-duration:0.01ms !important;}
  }
</style>
</head>
<body>

<canvas id="net"></canvas>
<div class="grid-overlay"></div>
<div class="scanline"></div>

<main>
  <div class="topbar">
    <div class="logo">Mox Rathore <span class="logo-cdn">CDN</span></div>
    <div class="status-pill"><span class="dot"></span>edge node online</div>
  </div>

  <section class="hero">
    <div class="eyebrow">// personal edge node</div>
    <h1>Hi <span class="wave">👋</span>, I'm <span class="accent">Mox Rathore</span></h1>
    <div class="terminal" id="terminal">
      <div class="line1"><span id="typed"></span><span class="cursor" id="introCursor"></span></div>
      <div id="termHistory"></div>
      <div class="line1" id="termInputRow" style="display:none;">
        <span style="color:var(--muted2);">guest@cdn:~$&nbsp;</span><span id="termLine" contenteditable="true" spellcheck="false"></span><span class="cursor"></span>
      </div>
      <div class="term-hint" id="termHint">type <b>help</b> and press enter</div>
    </div>
  </section>

  <div class="metrics">
    <div class="metric">
      <div class="m-lbl"><span class="lv"></span>latency</div>
      <div class="m-val"><span id="mLatency">--</span><span> ms</span></div>
      <div class="m-bar"><i id="mLatencyBar"></i></div>
    </div>
    <div class="metric">
      <div class="m-lbl"><span class="lv"></span>requests</div>
      <div class="m-val"><span id="mReq">--</span><span>/s</span></div>
      <div class="m-bar"><i id="mReqBar"></i></div>
    </div>
    <div class="metric">
      <div class="m-lbl"><span class="lv"></span>cache hit</div>
      <div class="m-val"><span id="mCache">--</span><span>%</span></div>
      <div class="m-bar"><i id="mCacheBar"></i></div>
    </div>
    <div class="metric">
      <div class="m-lbl"><span class="lv"></span>regions</div>
      <div class="m-val">04<span> pop</span></div>
      <div class="m-bar"><i style="width:100%;"></i></div>
    </div>
  </div>

  <div class="node-card">
    <div class="node-card-inner">
      <div class="avatar-wrap">
        <div class="avatar-ring"></div>
        <div class="avatar">MR</div>
      </div>
      <div class="node-meta">
        <h2>Mox Rathore</h2>
        <div class="role">web developer · from India 🇮🇳</div>
      </div>
      <div class="node-stats">
        <div class="stat"><div class="num" id="views">0</div><div class="lbl"><span class="live-dot"></span>views</div></div>
        <div class="stat"><div class="num">100%</div><div class="lbl">uptime</div></div>
      </div>
    </div>
  </div>

  <div class="log">
    <div class="section-label">status log</div>
    <div class="log-line"><div class="log-icon">🛰️</div><div class="log-text">Currently working on <b>My Personal Project</b></div></div>
    <div class="log-line"><div class="log-icon">🌱</div><div class="log-text">Currently learning <b>Web &amp; Tech</b></div></div>
    <div class="log-line"><div class="log-icon">🤝</div><div class="log-text">Looking to collaborate on <b>Instagram</b></div></div>
    <div class="log-line"><div class="log-icon">💬</div><div class="log-text">Ask me about <b>Web Development</b> and many more</div></div>
    <div class="log-line"><div class="log-icon">📡</div><div class="log-text">Reach me at <a href="mailto:Hello@moxrathore.com">Hello@moxrathore.com</a><button class="copy-btn" id="copyEmailBtn">copy</button></div></div>
  </div>

  <div class="connect">
    <div class="section-label">connect</div>
    <div class="social-grid">
      <a class="social-node" href="https://www.twitter.com/moxrathore/" target="_blank" rel="noopener">
        <svg class="ic" viewBox="0 0 24 24" fill="none" stroke="#1d9bf0" stroke-width="1.8"><path d="M23 4.5c-.8.36-1.66.6-2.56.7a4.5 4.5 0 0 0 1.97-2.48 9 9 0 0 1-2.83 1.08 4.48 4.48 0 0 0-7.63 4.08A12.7 12.7 0 0 1 2.6 3.16a4.48 4.48 0 0 0 1.39 5.98 4.4 4.4 0 0 1-2.03-.56v.06a4.48 4.48 0 0 0 3.6 4.4 4.5 4.5 0 0 1-2.02.08 4.48 4.48 0 0 0 4.19 3.12A9 9 0 0 1 1 18.58 12.68 12.68 0 0 0 7.88 20.6c8.26 0 12.78-6.84 12.78-12.78l-.01-.58A9.14 9.14 0 0 0 23 4.5z"/></svg>
        twitter
      </a>
      <a class="social-node" href="https://instagram.com/moxrathore" target="_blank" rel="noopener">
        <svg class="ic" viewBox="0 0 24 24" fill="none" stroke="#e1306c" stroke-width="1.8"><rect x="3" y="3" width="18" height="18" rx="5"/><circle cx="12" cy="12" r="4"/><circle cx="17.5" cy="6.5" r="1"/></svg>
        instagram
      </a>
      <a class="social-node" href="https://www.youtube.com/moxrathore" target="_blank" rel="noopener">
        <svg class="ic" viewBox="0 0 24 24" fill="none" stroke="#ff0000" stroke-width="1.8"><path d="M22 8.5s-.2-1.6-.86-2.3c-.82-.9-1.74-.9-2.17-.96C15.9 5 12 5 12 5h-.02s-3.9 0-6.95.24c-.43.06-1.35.06-2.17.96C2.2 6.9 2 8.5 2 8.5S1.8 10.4 1.8 12.3v1.5c0 1.9.2 3.8.2 3.8s.2 1.6.86 2.3c.82.9 1.9.87 2.38.97C6.8 21 12 21 12 21s3.9 0 6.97-.24c.43-.06 1.35-.06 2.17-.96.66-.7.86-2.3.86-2.3s.2-1.9.2-3.8v-1.5c0-1.9-.2-3.8-.2-3.8z"/><path d="M10 15l5-3-5-3v6z" fill="#ff0000" stroke="none"/></svg>
        youtube
      </a>
      <a class="social-node" href="https://www.fb.com/moxrathore" target="_blank" rel="noopener">
        <svg class="ic" viewBox="0 0 24 24" fill="none" stroke="#1877f2" stroke-width="1.8"><path d="M14 9h3V6h-3c-1.66 0-3 1.34-3 3v2H8v3h3v7h3v-7h3l1-3h-4V9c0-.55.45-1 1-1z"/></svg>
        facebook
      </a>
      <a class="social-node" href="https://api.whatsapp.com/send/?phone=916375324945" target="_blank" rel="noopener">
        <svg class="ic" viewBox="0 0 24 24" fill="none" stroke="#25d366" stroke-width="1.8"><path d="M20.5 3.5A10 10 0 0 0 3.6 16L2 22l6.2-1.6A10 10 0 1 0 20.5 3.5z"/><path d="M8.5 8.5c0 4 3 7 7 7 .8 0 1.5-.7 1.5-1.5 0-.3-.1-.5-.3-.6l-2-1.4a.7.7 0 0 0-.8 0l-.6.5a5.8 5.8 0 0 1-2.8-2.8l.5-.6a.7.7 0 0 0 0-.8l-1.4-2a.6.6 0 0 0-.6-.3C9.2 7 8.5 7.7 8.5 8.5z" fill="#25d366" stroke="none"/></svg>
        whatsapp
      </a>
    </div>
  </div>

  <footer>
    <div><span class="ping">●</span> ping mox.dev — 12ms — status: reachable</div>
    <div>© 2026 Mox Rathore. served from the nearest edge.</div>
  </footer>
</main>

<script>
// ---------- typing effect, then hand off to a live command line ----------
const phrase = "web developer · building things on the edge_";
const typedEl = document.getElementById('typed');
const introCursor = document.getElementById('introCursor');
const termInputRow = document.getElementById('termInputRow');
const termLine = document.getElementById('termLine');
const termHistory = document.getElementById('termHistory');
const termHint = document.getElementById('termHint');
let i = 0;
function type(){
  if(i <= phrase.length){
    typedEl.textContent = phrase.slice(0, i);
    i++;
    setTimeout(type, 45);
  } else {
    introCursor.style.display = 'none';
    termInputRow.style.display = 'block';
    termHint.classList.add('show');
    termLine.focus();
  }
}
setTimeout(type, 900);

// ---------- real commands the visitor can type ----------
const COMMANDS = {
  help: "available: <b>whoami</b>, <b>social</b>, <b>contact</b>, <b>skills</b>, <b>ping</b>, <b>clear</b>",
  whoami: "Mox Rathore — web developer, currently building <b>My Personal Project</b> and learning web &amp; tech.",
  social: 'twitter <a href="https://www.twitter.com/moxrathore/" target="_blank">↗</a> · instagram <a href="https://instagram.com/moxrathore" target="_blank">↗</a> · youtube <a href="https://www.youtube.com/moxrathore" target="_blank">↗</a> · facebook <a href="https://www.fb.com/moxrathore" target="_blank">↗</a> · whatsapp <a href="https://api.whatsapp.com/send/?phone=916375324945" target="_blank">↗</a>',
  contact: 'reach me at <a href="mailto:Hello@moxrathore.com">Hello@moxrathore.com</a>',
  skills: "web development, frontend design, and generally shipping things fast",
  ping: "pong — 12ms — edge node reachable ✅",
};
function runCommand(raw){
  const cmd = raw.trim().toLowerCase();
  const cmdLine = document.createElement('div');
  cmdLine.className = 'cmd';
  cmdLine.textContent = raw;
  termHistory.appendChild(cmdLine);

  if(cmd === 'clear'){
    termHistory.innerHTML = '';
    return;
  }
  const out = document.createElement('div');
  out.className = 'out';
  out.innerHTML = COMMANDS[cmd] || `command not found: <b>${raw.replace(/</g,'&lt;')}</b> — try <b>help</b>`;
  termHistory.appendChild(out);
  termHistory.scrollTop = termHistory.scrollHeight;
}
termLine.addEventListener('keydown', (e)=>{
  if(e.key === 'Enter'){
    e.preventDefault();
    const val = termLine.textContent;
    if(val.trim().length){ runCommand(val); }
    termLine.textContent = '';
  }
});
document.getElementById('terminal').addEventListener('click', ()=> termLine.focus());

// ---------- copy email button ----------
const copyBtn = document.getElementById('copyEmailBtn');
if(copyBtn){
  copyBtn.addEventListener('click', async ()=>{
    try{
      await navigator.clipboard.writeText('Hello@moxrathore.com');
      copyBtn.textContent = 'copied ✓';
      copyBtn.classList.add('copied');
      setTimeout(()=>{ copyBtn.textContent = 'copy'; copyBtn.classList.remove('copied'); }, 1800);
    }catch(err){ copyBtn.textContent = 'error'; }
  });
}

// ---------- live edge metrics (simulated, animated) ----------
function animateMetric(elId, barId, from, to, unit, decimals){
  const el = document.getElementById(elId);
  const bar = document.getElementById(barId);
  let steps = 0;
  const maxSteps = 40;
  const timer = setInterval(()=>{
    steps++;
    const v = from + (to - from) * (steps / maxSteps);
    el.textContent = decimals ? v.toFixed(decimals) : Math.round(v);
    if(bar) bar.style.width = Math.min(100, (v / (to * 1.15)) * 100) + '%';
    if(steps >= maxSteps){
      clearInterval(timer);
      // gentle live jitter after reaching target
      setInterval(()=>{
        const jitter = to + (Math.random()-0.5) * (to * 0.12);
        el.textContent = decimals ? jitter.toFixed(decimals) : Math.round(jitter);
      }, 2200);
    }
  }, 30);
}
setTimeout(()=>{
  animateMetric('mLatency','mLatencyBar', 0, 12, 'ms', 0);
  animateMetric('mReq','mReqBar', 0, 428, '/s', 0);
  animateMetric('mCache','mCacheBar', 0, 96.4, '%', 1);
}, 1600);

// ---------- real, auto-incrementing view counter ----------
// Uses a free public hit-counter API: every real page load increments the
// count by 1 and returns the true total — no fake/random numbers.
const viewsEl = document.getElementById('views');
const VIEW_NAMESPACE = 'moxrathore.com';
const VIEW_KEY = 'cdn-page-views';

function animateTo(el, target){
  let cur = Number(el.textContent) || 0;
  function step(){
    cur += Math.ceil((target - cur) / 12) || (target > cur ? 1 : -1);
    if((target >= cur && cur >= target) || Math.abs(target - cur) < 1){ el.textContent = target; return; }
    el.textContent = cur;
    requestAnimationFrame(step);
  }
  step();
}

async function loadRealViews(){
  try{
    // "hit" endpoint: increments by 1 on every real page load and
    // returns the up-to-date total count as JSON, e.g. { value: 833 }
    const res = await fetch(`https://abacus.jasoncameron.dev/hit/${VIEW_NAMESPACE}/${VIEW_KEY}`, { cache:'no-store' });
    if(!res.ok) throw new Error('counter unavailable');
    const data = await res.json();
    const total = data.value;
    localStorage.setItem('mr_last_views', String(total));
    animateTo(viewsEl, total);
  }catch(err){
    // offline / API unreachable — fall back to the last known real count
    // so we never show a made-up number
    const cached = Number(localStorage.getItem('mr_last_views'));
    if(cached) animateTo(viewsEl, cached);
    else viewsEl.textContent = '—';
  }
}
setTimeout(loadRealViews, 1200);

// ---------- scroll reveal for log lines ----------
const lines = document.querySelectorAll('.log-line');
const io = new IntersectionObserver((entries)=>{
  entries.forEach((e, idx)=>{
    if(e.isIntersecting){
      setTimeout(()=>e.target.classList.add('in-view'), idx * 90);
      io.unobserve(e.target);
    }
  });
}, {threshold:0.2});
lines.forEach(l=>io.observe(l));

// ---------- network / data-packet canvas background ----------
const canvas = document.getElementById('net');
const ctx = canvas.getContext('2d');
let W, H, nodes = [], packets = [];
const reduceMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;

function resize(){
  W = canvas.width = window.innerWidth;
  H = canvas.height = window.innerHeight;
}
resize();
window.addEventListener('resize', resize);

const NODE_COUNT = Math.min(46, Math.floor((window.innerWidth * window.innerHeight) / 26000));
for(let n=0; n<NODE_COUNT; n++){
  nodes.push({
    x: Math.random()*W,
    y: Math.random()*H,
    vx: (Math.random()-0.5)*0.18,
    vy: (Math.random()-0.5)*0.18,
    r: Math.random()*1.6 + 0.8
  });
}

function spawnPacket(){
  if(nodes.length < 2) return;
  const a = nodes[Math.floor(Math.random()*nodes.length)];
  const b = nodes[Math.floor(Math.random()*nodes.length)];
  packets.push({a, b, t:0, speed: 0.006 + Math.random()*0.008});
}
setInterval(spawnPacket, 550);

function dist(a,b){ return Math.hypot(a.x-b.x, a.y-b.y); }

// mouse becomes a live, glowing node in the network
const mouse = { x: -9999, y: -9999, active:false };
window.addEventListener('mousemove', (e)=>{
  mouse.x = e.clientX; mouse.y = e.clientY; mouse.active = true;
});
window.addEventListener('mouseleave', ()=>{ mouse.active = false; });

function draw(){
  ctx.clearRect(0,0,W,H);

  // connections
  for(let i=0;i<nodes.length;i++){
    for(let j=i+1;j<nodes.length;j++){
      const d = dist(nodes[i], nodes[j]);
      if(d < 150){
        ctx.strokeStyle = `rgba(0,240,255,${0.14 * (1 - d/150)})`;
        ctx.lineWidth = 1;
        ctx.beginPath();
        ctx.moveTo(nodes[i].x, nodes[i].y);
        ctx.lineTo(nodes[j].x, nodes[j].y);
        ctx.stroke();
      }
    }
  }

  // cursor as a live node — pulls in connections from nearby nodes
  if(mouse.active){
    nodes.forEach(n=>{
      const d = dist(n, mouse);
      if(d < 190){
        ctx.strokeStyle = `rgba(57,255,153,${0.28 * (1 - d/190)})`;
        ctx.lineWidth = 1.1;
        ctx.beginPath();
        ctx.moveTo(n.x, n.y);
        ctx.lineTo(mouse.x, mouse.y);
        ctx.stroke();
      }
    });
    const g = ctx.createRadialGradient(mouse.x, mouse.y, 0, mouse.x, mouse.y, 16);
    g.addColorStop(0, 'rgba(57,255,153,0.55)');
    g.addColorStop(1, 'rgba(57,255,153,0)');
    ctx.fillStyle = g;
    ctx.beginPath();
    ctx.arc(mouse.x, mouse.y, 16, 0, Math.PI*2);
    ctx.fill();
    ctx.beginPath();
    ctx.fillStyle = '#39ff99';
    ctx.arc(mouse.x, mouse.y, 3, 0, Math.PI*2);
    ctx.fill();
  }

  // nodes
  nodes.forEach(n=>{
    n.x += n.vx; n.y += n.vy;
    if(n.x < 0 || n.x > W) n.vx *= -1;
    if(n.y < 0 || n.y > H) n.vy *= -1;
    ctx.beginPath();
    ctx.fillStyle = 'rgba(0,240,255,0.55)';
    ctx.arc(n.x, n.y, n.r, 0, Math.PI*2);
    ctx.fill();
  });

  // packets travelling along a straight line between two random nodes
  packets = packets.filter(p => p.t < 1);
  packets.forEach(p=>{
    p.t += p.speed;
    const x = p.a.x + (p.b.x - p.a.x) * p.t;
    const y = p.a.y + (p.b.y - p.a.y) * p.t;
    const grad = ctx.createRadialGradient(x,y,0,x,y,6);
    grad.addColorStop(0, 'rgba(57,255,153,0.9)');
    grad.addColorStop(1, 'rgba(57,255,153,0)');
    ctx.fillStyle = grad;
    ctx.beginPath();
    ctx.arc(x,y,6,0,Math.PI*2);
    ctx.fill();
    ctx.beginPath();
    ctx.fillStyle = '#39ff99';
    ctx.arc(x,y,1.6,0,Math.PI*2);
    ctx.fill();
  });

  if(!reduceMotion) requestAnimationFrame(draw);
}
if(!reduceMotion){
  draw();
} else {
  // static single frame for reduced motion users
  ctx.clearRect(0,0,W,H);
  nodes.forEach(n=>{
    ctx.beginPath();
    ctx.fillStyle = 'rgba(0,240,255,0.5)';
    ctx.arc(n.x, n.y, n.r, 0, Math.PI*2);
    ctx.fill();
  });
}
</script>

</body>
</html>
