<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Parchment — AI Document Intelligence</title>
<link rel="preconnect" href="https://fonts.googleapis.com"/>
<link href="https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,300;0,14..32,400;0,14..32,500;0,14..32,600;1,14..32,400&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet"/>
<script src="https://cdnjs.cloudflare.com/ajax/libs/marked/9.1.6/marked.min.js"></script>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/styles/tokyo-night-dark.min.css"/>
<script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/highlight.min.js"></script>

<style>
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

:root {
  /* Palette — deep space navy with warm indigo accent */
  --void:       #080b12;
  --base:       #0c1018;
  --surface:    #111520;
  --raised:     #161c2a;
  --overlay:    #1c2336;
  --line:       #1f2740;
  --line-soft:  #192030;

  --accent:     #5b8dee;
  --accent-hi:  #7aa3f5;
  --accent-lo:  rgba(91,141,238,0.12);
  --accent-md:  rgba(91,141,238,0.22);
  --purple:     #9b7ff4;
  --teal:       #3dd6c8;
  --amber:      #f5a623;
  --rose:       #f06080;
  --green:      #3ecf8e;

  --t1: #dce3f0;
  --t2: #7e8fad;
  --t3: #3d4d6a;
  --t4: #252e42;

  --font: 'Inter', -apple-system, sans-serif;
  --mono: 'JetBrains Mono', monospace;
  --r:  8px;
  --rl: 14px;
  --rx: 20px;
  --sw: 270px;
  --shadow: 0 4px 24px rgba(0,0,0,.5);
}

html, body { height: 100%; overflow: hidden; background: var(--void); color: var(--t1); font-family: var(--font); font-size: 14px; line-height: 1.5; -webkit-font-smoothing: antialiased; }

/* ════════ LAYOUT ════════ */
.shell { display: flex; height: 100vh; }

/* ════════ SIDEBAR ════════ */
.sidebar {
  width: var(--sw);
  flex-shrink: 0;
  background: var(--base);
  border-right: 1px solid var(--line);
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.brand {
  padding: 22px 20px 18px;
  border-bottom: 1px solid var(--line-soft);
  display: flex;
  align-items: center;
  gap: 11px;
}
.brand-mark {
  width: 36px; height: 36px;
  background: linear-gradient(140deg, var(--accent) 0%, var(--purple) 100%);
  border-radius: 10px;
  display: flex; align-items: center; justify-content: center;
  flex-shrink: 0;
  box-shadow: 0 0 20px rgba(91,141,238,0.3);
}
.brand-mark svg { width: 18px; height: 18px; fill: white; }
.brand-name { font-size: 15px; font-weight: 600; letter-spacing: -0.4px; color: var(--t1); }
.brand-tagline { font-size: 10.5px; color: var(--t3); letter-spacing: 0.3px; margin-top: 1px; }

/* New chat btn */
.new-chat-btn {
  margin: 14px 14px 6px;
  display: flex; align-items: center; gap: 8px;
  padding: 9px 14px;
  background: var(--accent-lo);
  border: 1px solid rgba(91,141,238,0.25);
  border-radius: var(--r);
  cursor: pointer;
  color: var(--accent-hi);
  font-size: 12.5px; font-weight: 500;
  font-family: var(--font);
  transition: background .15s, border-color .15s;
}
.new-chat-btn:hover { background: var(--accent-md); border-color: rgba(91,141,238,0.4); }
.new-chat-btn svg { width: 14px; height: 14px; stroke: currentColor; flex-shrink: 0; }

/* Section label */
.sec-label {
  padding: 14px 18px 6px;
  font-size: 10px;
  text-transform: uppercase;
  letter-spacing: 1.2px;
  color: var(--t4);
  font-weight: 600;
}

/* Document list */
.doc-list { flex: 1; overflow-y: auto; padding: 0 10px 10px; }
.doc-list::-webkit-scrollbar { width: 3px; }
.doc-list::-webkit-scrollbar-thumb { background: var(--line); border-radius: 2px; }

.doc-item {
  display: flex; align-items: center; gap: 9px;
  padding: 9px 10px;
  border-radius: var(--r);
  cursor: pointer;
  transition: background .12s;
  border: 1px solid transparent;
  margin-bottom: 2px;
}
.doc-item:hover { background: var(--raised); }
.doc-item.active { background: var(--accent-lo); border-color: rgba(91,141,238,0.2); }
.doc-icon {
  width: 28px; height: 32px;
  background: linear-gradient(160deg, #e8f0ff 0%, #c4d5ff 100%);
  border-radius: 4px;
  display: flex; align-items: center; justify-content: center;
  flex-shrink: 0;
  position: relative;
}
.doc-icon::after {
  content: '';
  position: absolute; top: 2px; right: -1px;
  width: 0; height: 0;
  border-left: 5px solid #8aa8f5;
  border-top: 5px solid transparent;
}
.doc-icon svg { width: 13px; height: 13px; fill: #4a6fd4; }
.doc-meta { flex: 1; min-width: 0; }
.doc-name { font-size: 12.5px; color: var(--t1); overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
.doc-size { font-size: 10.5px; color: var(--t3); margin-top: 1px; }
.doc-del { opacity: 0; padding: 4px; border-radius: 4px; cursor: pointer; transition: opacity .12s, background .12s; }
.doc-item:hover .doc-del { opacity: 1; }
.doc-del:hover { background: rgba(240,96,128,0.15); }
.doc-del svg { width: 12px; height: 12px; stroke: var(--rose); display: block; }

.empty-docs { padding: 20px 14px; text-align: center; }
.empty-docs svg { width: 32px; height: 32px; stroke: var(--t4); margin-bottom: 10px; }
.empty-docs p { font-size: 12px; color: var(--t3); line-height: 1.6; }

/* Sidebar footer */
.sidebar-foot {
  border-top: 1px solid var(--line-soft);
  padding: 14px 16px;
}
.url-row { display: flex; flex-direction: column; gap: 5px; }
.url-label { font-size: 10px; text-transform: uppercase; letter-spacing: 0.9px; color: var(--t3); font-weight: 600; }
.url-field {
  display: flex; align-items: center; gap: 0;
  background: var(--surface); border: 1px solid var(--line);
  border-radius: var(--r); overflow: hidden; transition: border-color .15s;
}
.url-field:focus-within { border-color: var(--accent); }
.url-field input {
  flex: 1; background: transparent; border: none; outline: none;
  padding: 7px 10px; font-size: 11.5px; color: var(--t2);
  font-family: var(--mono);
}
.url-field input::placeholder { color: var(--t4); }
.ping-btn {
  width: 32px; height: 32px; border: none; background: transparent;
  cursor: pointer; display: flex; align-items: center; justify-content: center;
  color: var(--t3); transition: color .15s;
}
.ping-btn:hover { color: var(--accent); }
.ping-btn svg { width: 13px; height: 13px; stroke: currentColor; }

.conn-status { display: flex; align-items: center; gap: 6px; margin-top: 8px; }
.conn-dot { width: 6px; height: 6px; border-radius: 50%; background: var(--t4); flex-shrink: 0; }
.conn-dot.ok { background: var(--green); box-shadow: 0 0 8px rgba(62,207,142,.5); }
.conn-dot.err { background: var(--rose); }
.conn-dot.checking { background: var(--amber); animation: pulse 1s infinite; }
@keyframes pulse { 0%,100%{opacity:.4} 50%{opacity:1} }
.conn-text { font-size: 11px; color: var(--t3); }

/* ════════ MAIN ════════ */
.main { flex: 1; display: flex; flex-direction: column; overflow: hidden; background: var(--void); }

/* Top bar */
.topbar {
  display: flex; align-items: center; justify-content: space-between;
  padding: 0 24px;
  height: 56px;
  border-bottom: 1px solid var(--line-soft);
  background: var(--base);
  flex-shrink: 0;
}
.topbar-left { display: flex; align-items: center; gap: 10px; min-width: 0; }
.context-pill {
  display: flex; align-items: center; gap: 7px;
  background: var(--accent-lo);
  border: 1px solid rgba(91,141,238,0.2);
  border-radius: 20px;
  padding: 5px 12px 5px 8px;
  max-width: 320px;
}
.context-pill .pill-icon { width: 18px; height: 20px; flex-shrink: 0; }
.context-pill .pill-name { font-size: 12.5px; color: var(--accent-hi); font-weight: 500; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
.context-pill .pill-pages { font-size: 11px; color: var(--t3); flex-shrink: 0; }
.no-context { font-size: 12.5px; color: var(--t3); font-style: italic; }

.topbar-right { display: flex; gap: 6px; }
.icon-btn {
  width: 34px; height: 34px;
  background: transparent; border: 1px solid var(--line);
  border-radius: var(--r); cursor: pointer;
  display: flex; align-items: center; justify-content: center;
  color: var(--t3); transition: all .12s;
}
.icon-btn:hover { background: var(--raised); color: var(--t1); border-color: var(--line); }
.icon-btn svg { width: 15px; height: 15px; stroke: currentColor; }

/* ── Messages ── */
.feed {
  flex: 1; overflow-y: auto;
  padding: 32px 0 16px;
  display: flex; flex-direction: column;
  scroll-behavior: smooth;
}
.feed::-webkit-scrollbar { width: 4px; }
.feed::-webkit-scrollbar-thumb { background: var(--line); border-radius: 3px; }

/* Welcome screen */
.welcome {
  flex: 1; display: flex; flex-direction: column;
  align-items: center; justify-content: center;
  padding: 40px 24px; gap: 0;
  animation: fadeIn .4s ease;
}
@keyframes fadeIn { from{opacity:0;transform:translateY(10px)} to{opacity:1;transform:translateY(0)} }
.welcome-glyph {
  width: 64px; height: 64px;
  background: linear-gradient(140deg, var(--accent), var(--purple));
  border-radius: 18px;
  display: flex; align-items: center; justify-content: center;
  margin-bottom: 20px;
  box-shadow: 0 8px 32px rgba(91,141,238,0.3);
}
.welcome-glyph svg { width: 28px; height: 28px; fill: white; }
.welcome h2 { font-size: 22px; font-weight: 600; letter-spacing: -0.5px; color: var(--t1); margin-bottom: 8px; }
.welcome p { font-size: 14px; color: var(--t2); text-align: center; max-width: 380px; line-height: 1.7; margin-bottom: 28px; }
.chips { display: flex; flex-wrap: wrap; gap: 8px; justify-content: center; max-width: 500px; }
.chip {
  padding: 8px 16px;
  background: var(--raised);
  border: 1px solid var(--line);
  border-radius: 20px;
  font-size: 12.5px; color: var(--t2);
  cursor: pointer; transition: all .12s;
}
.chip:hover { background: var(--overlay); color: var(--t1); border-color: rgba(91,141,238,0.3); }

/* Message rows */
.msg-wrap {
  padding: 4px 24px;
  display: flex;
  gap: 14px;
  animation: msgIn .2s ease;
  max-width: 900px;
  width: 100%;
  margin: 0 auto;
}
@keyframes msgIn { from{opacity:0;transform:translateY(6px)} to{opacity:1;transform:translateY(0)} }
.msg-wrap.user { flex-direction: row-reverse; }
.msg-wrap.system-row { justify-content: center; padding: 6px 24px; }

.av {
  width: 32px; height: 32px;
  border-radius: 9px;
  flex-shrink: 0;
  display: flex; align-items: center; justify-content: center;
  margin-top: 2px;
}
.av.ai { background: linear-gradient(140deg, var(--accent), var(--purple)); }
.av.user { background: linear-gradient(140deg, var(--teal), #3a8ef5); }
.av svg { width: 15px; height: 15px; fill: white; }

.bubble {
  background: var(--surface);
  border: 1px solid var(--line);
  border-radius: var(--rl);
  padding: 14px 18px;
  max-width: 78%;
  font-size: 14px;
  line-height: 1.7;
  color: var(--t1);
}
.msg-wrap.user .bubble {
  background: var(--raised);
  border-color: var(--line);
  border-top-right-radius: 4px;
  color: var(--t1);
}
.msg-wrap.ai .bubble { border-top-left-radius: 4px; }

.sys-pill {
  font-size: 11.5px; color: var(--t3);
  background: var(--raised);
  border: 1px solid var(--line-soft);
  border-radius: 20px; padding: 4px 12px;
}

/* Typing */
.typing { display: flex; gap: 5px; align-items: center; padding: 2px 0; }
.typing b {
  width: 6px; height: 6px; border-radius: 50%;
  background: var(--accent); display: block;
  animation: tpulse 1.1s infinite ease-in-out;
}
.typing b:nth-child(2) { animation-delay: .22s; }
.typing b:nth-child(3) { animation-delay: .44s; }
@keyframes tpulse { 0%,80%,100%{transform:scale(.6);opacity:.4} 40%{transform:scale(1);opacity:1} }

/* ── Markdown inside bubble ── */
.bubble h1 { font-size: 1.2em; font-weight: 600; color: var(--t1); margin: 18px 0 8px; padding-bottom: 6px; border-bottom: 1px solid var(--line); }
.bubble h2 { font-size: 1.08em; font-weight: 600; color: var(--t1); margin: 16px 0 7px; }
.bubble h3 { font-size: 1em; font-weight: 600; color: var(--accent-hi); margin: 14px 0 6px; }
.bubble h4 { font-size: .95em; font-weight: 600; color: var(--t2); margin: 12px 0 5px; }
.bubble h1:first-child,.bubble h2:first-child,.bubble h3:first-child { margin-top: 0; }
.bubble p { margin: 8px 0; color: var(--t1); }
.bubble p:first-child { margin-top: 0; }
.bubble p:last-child { margin-bottom: 0; }
.bubble ul { padding-left: 18px; margin: 8px 0; list-style: none; }
.bubble ul li::before { content: '–'; color: var(--accent); margin-right: 8px; margin-left: -18px; }
.bubble ol { padding-left: 18px; margin: 8px 0; }
.bubble li { margin: 5px 0; color: var(--t1); }
.bubble strong { color: var(--t1); font-weight: 600; }
.bubble em { color: var(--t2); }
.bubble code:not(pre code) {
  background: rgba(91,141,238,0.1);
  border: 1px solid rgba(91,141,238,0.2);
  border-radius: 5px;
  padding: 1px 6px;
  font-family: var(--mono); font-size: 12px;
  color: var(--accent-hi);
}
.bubble pre {
  background: #0d0e14 !important;
  border: 1px solid var(--line);
  border-radius: var(--r);
  padding: 14px 16px;
  overflow-x: auto;
  margin: 12px 0;
  position: relative;
}
.bubble pre code {
  font-family: var(--mono); font-size: 12px;
  background: transparent !important; border: none !important;
  padding: 0 !important; color: inherit !important;
}
.code-head {
  display: flex; align-items: center; justify-content: space-between;
  background: var(--raised);
  border: 1px solid var(--line);
  border-bottom: none;
  border-radius: var(--r) var(--r) 0 0;
  padding: 6px 12px;
  margin: 12px 0 -1px;
}
.code-lang { font-family: var(--mono); font-size: 10.5px; color: var(--t3); }
.copy-code-btn {
  font-size: 10.5px; color: var(--t3); cursor: pointer;
  background: transparent; border: none; font-family: var(--font);
  padding: 2px 6px; border-radius: 4px; transition: color .12s, background .12s;
}
.copy-code-btn:hover { color: var(--t1); background: var(--overlay); }
.bubble blockquote {
  border-left: 3px solid var(--accent);
  padding: 8px 14px;
  margin: 10px 0;
  background: var(--accent-lo);
  border-radius: 0 var(--r) var(--r) 0;
}
.bubble blockquote p { color: var(--t2); margin: 0; }
.bubble table { border-collapse: collapse; width: 100%; margin: 12px 0; font-size: 13px; }
.bubble th { background: var(--overlay); padding: 8px 12px; border: 1px solid var(--line); font-weight: 600; text-align: left; color: var(--t1); font-size: 12px; text-transform: uppercase; letter-spacing: 0.4px; }
.bubble td { padding: 8px 12px; border: 1px solid var(--line-soft); color: var(--t2); }
.bubble tr:hover td { background: var(--raised); color: var(--t1); }
.bubble a { color: var(--accent-hi); text-decoration: none; border-bottom: 1px solid rgba(91,141,238,0.3); transition: border-color .12s; }
.bubble a:hover { border-color: var(--accent-hi); }
.bubble hr { border: none; border-top: 1px solid var(--line); margin: 16px 0; }

/* ════════ INPUT AREA ════════ */
.input-zone {
  padding: 12px 20px 18px;
  background: var(--base);
  border-top: 1px solid var(--line-soft);
  flex-shrink: 0;
}

/* PDF preview strip — shown when file is attached */
.pdf-strip {
  display: none;
  align-items: center; gap: 8px;
  margin-bottom: 8px;
  padding: 7px 12px;
  background: var(--surface);
  border: 1px solid rgba(91,141,238,0.25);
  border-radius: var(--r);
  animation: fadeIn .15s ease;
}
.pdf-strip.visible { display: flex; }
.pdf-strip-icon {
  width: 26px; height: 30px;
  background: linear-gradient(160deg, #e6edff 0%, #b8ccff 100%);
  border-radius: 4px;
  display: flex; align-items: center; justify-content: center;
  flex-shrink: 0;
}
.pdf-strip-icon svg { width: 12px; height: 12px; fill: #4a6fd4; }
.pdf-strip-name { font-size: 12.5px; color: var(--t1); font-weight: 500; flex: 1; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
.pdf-strip-size { font-size: 11px; color: var(--t3); flex-shrink: 0; }
.pdf-strip-rm { width: 22px; height: 22px; border-radius: 5px; border: none; background: transparent; cursor: pointer; display: flex; align-items: center; justify-content: center; flex-shrink: 0; transition: background .12s; }
.pdf-strip-rm:hover { background: rgba(240,96,128,0.15); }
.pdf-strip-rm svg { width: 11px; height: 11px; stroke: var(--t3); }
.pdf-strip-rm:hover svg { stroke: var(--rose); }

/* Composer box */
.composer {
  display: flex;
  background: var(--surface);
  border: 1.5px solid var(--line);
  border-radius: var(--rl);
  transition: border-color .15s, box-shadow .15s;
  overflow: hidden;
}
.composer:focus-within {
  border-color: var(--accent);
  box-shadow: 0 0 0 3px var(--accent-lo);
}

.composer-actions {
  display: flex; flex-direction: column; justify-content: flex-end;
  padding: 8px 4px 8px 10px; gap: 4px;
}

/* PDF upload button */
.upload-pdf-btn {
  width: 32px; height: 32px;
  border: 1px solid var(--line);
  border-radius: var(--r);
  background: var(--raised);
  cursor: pointer;
  display: flex; align-items: center; justify-content: center;
  position: relative;
  transition: all .12s;
  flex-shrink: 0;
}
.upload-pdf-btn:hover { background: var(--overlay); border-color: var(--accent); }
.upload-pdf-btn svg { width: 14px; height: 14px; stroke: var(--t2); transition: stroke .12s; }
.upload-pdf-btn:hover svg { stroke: var(--accent-hi); }
.upload-pdf-btn .badge {
  position: absolute; top: -3px; right: -3px;
  width: 8px; height: 8px; border-radius: 50%;
  background: var(--accent); display: none;
  box-shadow: 0 0 0 2px var(--surface);
}
.upload-pdf-btn.has-file .badge { display: block; }
#pdfFileInput { display: none; }

/* Tooltip */
.upload-pdf-btn::after {
  content: 'Attach PDF';
  position: absolute; bottom: calc(100% + 8px); left: 50%;
  transform: translateX(-50%);
  background: var(--overlay); border: 1px solid var(--line);
  color: var(--t2); font-size: 11px; font-family: var(--font);
  padding: 4px 8px; border-radius: 5px; white-space: nowrap;
  opacity: 0; pointer-events: none; transition: opacity .12s;
}
.upload-pdf-btn:hover::after { opacity: 1; }

textarea {
  flex: 1;
  background: transparent; border: none; outline: none;
  padding: 13px 14px;
  color: var(--t1); font-family: var(--font); font-size: 14px;
  resize: none; line-height: 1.65;
  min-height: 50px; max-height: 200px;
}
textarea::placeholder { color: var(--t4); }
textarea::-webkit-scrollbar { width: 3px; }
textarea::-webkit-scrollbar-thumb { background: var(--line); border-radius: 2px; }

.send-wrap { display: flex; flex-direction: column; justify-content: flex-end; padding: 8px 10px 8px 4px; }
.send-btn {
  width: 36px; height: 36px;
  background: var(--accent); border: none; border-radius: var(--r);
  cursor: pointer; display: flex; align-items: center; justify-content: center;
  transition: all .12s; flex-shrink: 0;
}
.send-btn:hover:not(:disabled) { background: var(--accent-hi); transform: scale(1.06); }
.send-btn:disabled { opacity: 0.35; cursor: not-allowed; transform: none !important; }
.send-btn svg { width: 16px; height: 16px; fill: white; }

.composer-foot {
  display: flex; align-items: center; justify-content: flex-end;
  padding: 4px 0 0;
}
.hint-text { font-size: 11px; color: var(--t4); }

/* Progress */
.prog-bar { height: 2px; background: var(--line-soft); border-radius: 1px; overflow: hidden; margin-bottom: 8px; display: none; }
.prog-fill { height: 100%; width: 0; background: linear-gradient(90deg, var(--accent), var(--purple)); border-radius: 1px; transition: width .25s ease; }

/* ════════ TOAST ════════ */
.toast-shelf {
  position: fixed; top: 16px; right: 16px;
  display: flex; flex-direction: column; gap: 6px; z-index: 999;
}
.toast {
  background: var(--raised); border: 1px solid var(--line);
  border-radius: var(--r); padding: 10px 14px;
  font-size: 12.5px; color: var(--t1);
  box-shadow: var(--shadow);
  animation: slideIn .2s ease;
  max-width: 300px;
  display: flex; align-items: center; gap: 8px;
}
.toast.success { border-color: rgba(62,207,142,.3); }
.toast.error   { border-color: rgba(240,96,128,.3); }
.toast-dot { width: 6px; height: 6px; border-radius: 50%; flex-shrink: 0; }
.toast.success .toast-dot { background: var(--green); }
.toast.error   .toast-dot { background: var(--rose); }
@keyframes slideIn { from{opacity:0;transform:translateX(14px)} to{opacity:1;transform:translateX(0)} }

/* Responsive */
@media(max-width:700px){
  .sidebar { display: none; }
  .msg-wrap { padding: 4px 14px; }
  .input-zone { padding: 10px 12px 14px; }
  .topbar { padding: 0 14px; }
}
</style>
</head>
<body>

<div class="shell">

  <!-- ══ SIDEBAR ══ -->
  <aside class="sidebar">

    <div class="brand">
      <div class="brand-mark">
        <svg viewBox="0 0 20 20"><path d="M4 3h8l4 4v10a1 1 0 01-1 1H4a1 1 0 01-1-1V4a1 1 0 011-1z"/><path d="M12 3v4h4" fill="rgba(0,0,0,0.3)"/></svg>
      </div>
      <div>
        <div class="brand-name">Parchment</div>
        <div class="brand-tagline">Document Intelligence</div>
      </div>
    </div>

    <button class="new-chat-btn" onclick="clearChat()">
      <svg viewBox="0 0 24 24" fill="none" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
        <path d="M12 5v14M5 12h14"/>
      </svg>
      New conversation
    </button>

    <div class="sec-label">Documents</div>
    <div class="doc-list" id="docList">
      <div class="empty-docs">
        <svg viewBox="0 0 24 24" fill="none" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round">
          <path d="M14 2H6a2 2 0 00-2 2v16a2 2 0 002 2h12a2 2 0 002-2V8z"/><polyline points="14,2 14,8 20,8"/>
        </svg>
        <p>Attach a PDF in the chat below to start analysing</p>
      </div>
    </div>

    <div class="sidebar-foot">
      <div class="url-row">
        <span class="url-label">Backend URL</span>
        <div class="url-field">
          <input type="text" id="backendUrl" placeholder="http://localhost:8000" value="http://localhost:8000" oninput="scheduleTest()"/>
          <button class="ping-btn" onclick="testConn()" title="Test connection">
            <svg viewBox="0 0 24 24" fill="none" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M5 12.55a11 11 0 0114.08 0"/><path d="M1.42 9a16 16 0 0121.16 0"/><path d="M8.53 16.11a6 6 0 016.95 0"/><circle cx="12" cy="20" r="1" fill="currentColor"/>
            </svg>
          </button>
        </div>
      </div>
      <div class="conn-status">
        <div class="conn-dot" id="connDot"></div>
        <span class="conn-text" id="connText">Not tested</span>
      </div>
    </div>
  </aside>

  <!-- ══ MAIN ══ -->
  <main class="main">

    <header class="topbar">
      <div class="topbar-left">
        <div id="ctxPill" class="context-pill" style="display:none;">
          <svg class="pill-icon" viewBox="0 0 14 18" fill="none">
            <rect x="1" y="1" width="10" height="16" rx="1.5" fill="#e6edff" stroke="#8aa8f5" stroke-width="1"/>
            <path d="M10 1l3 3" stroke="#8aa8f5" stroke-width="1" stroke-linecap="round"/>
            <path d="M3 7h8M3 10h6M3 13h4" stroke="#8aa8f5" stroke-width="1" stroke-linecap="round"/>
          </svg>
          <span class="pill-name" id="ctxName"></span>
          <span class="pill-pages" id="ctxSize"></span>
        </div>
        <span class="no-context" id="noCtx">No document attached</span>
      </div>
      <div class="topbar-right">
        <button class="icon-btn" onclick="clearChat()" title="Clear conversation">
          <svg viewBox="0 0 24 24" fill="none" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <polyline points="3,6 5,6 21,6"/><path d="M19 6l-1 14a2 2 0 01-2 2H8a2 2 0 01-2-2L5 6"/><path d="M10 11v6M14 11v6"/><path d="M9 6V4a1 1 0 011-1h4a1 1 0 011 1v2"/>
          </svg>
        </button>
      </div>
    </header>

    <div class="feed" id="feed">
      <div class="welcome" id="welcomeScreen">
        <div class="welcome-glyph">
          <svg viewBox="0 0 20 20"><path d="M4 3h8l4 4v10a1 1 0 01-1 1H4a1 1 0 01-1-1V4a1 1 0 011-1z"/><path d="M12 3v4h4" fill="rgba(0,0,0,0.3)"/></svg>
        </div>
        <h2>Ask anything about your PDF</h2>
        <p>Attach a document using the paperclip icon, then ask questions, get summaries, or extract structured data.</p>
        <div class="chips">
          <div class="chip" onclick="chip(this)">Summarize the document</div>
          <div class="chip" onclick="chip(this)">List the key findings</div>
          <div class="chip" onclick="chip(this)">Extract all dates & figures</div>
          <div class="chip" onclick="chip(this)">What is the main argument?</div>
          <div class="chip" onclick="chip(this)">Create a table of contents</div>
        </div>
      </div>
    </div>

    <div class="input-zone">
      <div class="prog-bar" id="progBar"><div class="prog-fill" id="progFill"></div></div>

      <!-- PDF preview strip -->
      <div class="pdf-strip" id="pdfStrip">
        <div class="pdf-strip-icon">
          <svg viewBox="0 0 20 20"><path d="M4 2h8l4 4v12a1 1 0 01-1 1H4a1 1 0 01-1-1V3a1 1 0 011-1z"/><path d="M12 2v4h4" fill="rgba(0,0,0,0.25)"/></svg>
        </div>
        <span class="pdf-strip-name" id="stripName"></span>
        <span class="pdf-strip-size" id="stripSize"></span>
        <button class="pdf-strip-rm" onclick="removeAttached()" title="Remove">
          <svg viewBox="0 0 24 24" fill="none" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/>
          </svg>
        </button>
      </div>

      <div class="composer" id="composer">
        <!-- Left: PDF upload button -->
        <div class="composer-actions">
          <button class="upload-pdf-btn" id="uploadBtn" onclick="document.getElementById('pdfFileInput').click()" title="Attach PDF">
            <svg viewBox="0 0 24 24" fill="none" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M21.44 11.05l-9.19 9.19a6 6 0 01-8.49-8.49l9.19-9.19a4 4 0 015.66 5.66l-9.2 9.19a2 2 0 01-2.83-2.83l8.49-8.48"/>
            </svg>
            <div class="badge"></div>
          </button>
        </div>
        <input type="file" id="pdfFileInput" accept=".pdf"/>

        <!-- Textarea -->
        <textarea
          id="queryBox"
          placeholder="Ask a question about your document…"
          rows="1"
          oninput="autoGrow(this)"
          onkeydown="handleKey(event)"
        ></textarea>

        <!-- Right: Send button -->
        <div class="send-wrap">
          <button class="send-btn" id="sendBtn" onclick="send()" title="Send">
            <svg viewBox="0 0 24 24"><path d="M22 2L11 13M22 2L15 22l-4-9-9-4 20-7z"/></svg>
          </button>
        </div>
      </div>

      <div class="composer-foot">
        <span class="hint-text">Enter to send · Shift+Enter for new line</span>
      </div>
    </div>
  </main>
</div>

<div class="toast-shelf" id="toasts"></div>

<script>
// ── Config & State ──
let attachedFile  = null;   // File object
let docHistory    = [];     // [{id, name, size}]
let isLoading     = false;
let testTimer     = null;

// ── Marked ──
marked.setOptions({ breaks: true, gfm: true });

// ── File attach ──
const pdfInput = document.getElementById('pdfFileInput');
pdfInput.addEventListener('change', () => {
  const f = pdfInput.files[0];
  if (f) attachPDF(f);
  pdfInput.value = '';
});

function attachPDF(file) {
  attachedFile = file;
  // Strip
  document.getElementById('pdfStrip').classList.add('visible');
  document.getElementById('stripName').textContent = file.name;
  document.getElementById('stripSize').textContent = fmtSize(file.size);
  // Upload btn badge
  document.getElementById('uploadBtn').classList.add('has-file');
  // Topbar
  document.getElementById('ctxPill').style.display = 'flex';
  document.getElementById('ctxName').textContent = file.name;
  document.getElementById('ctxSize').textContent = fmtSize(file.size);
  document.getElementById('noCtx').style.display = 'none';
  // Sidebar doc list
  addToDocList(file);
  // Focus textarea
  document.getElementById('queryBox').focus();
  toast(`📄 ${file.name} attached`, 'success');
}

function removeAttached() {
  attachedFile = null;
  document.getElementById('pdfStrip').classList.remove('visible');
  document.getElementById('uploadBtn').classList.remove('has-file');
  document.getElementById('ctxPill').style.display = 'none';
  document.getElementById('noCtx').style.display = '';
}

function addToDocList(file) {
  const id = Date.now();
  docHistory.unshift({ id, name: file.name, size: file.size });
  renderDocList();
}

function renderDocList() {
  const el = document.getElementById('docList');
  if (!docHistory.length) {
    el.innerHTML = `<div class="empty-docs"><svg viewBox="0 0 24 24" fill="none" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><path d="M14 2H6a2 2 0 00-2 2v16a2 2 0 002 2h12a2 2 0 002-2V8z"/><polyline points="14,2 14,8 20,8"/></svg><p>Attach a PDF in the chat below to start analysing</p></div>`;
    return;
  }
  el.innerHTML = docHistory.map(d => `
    <div class="doc-item active">
      <div class="doc-icon">
        <svg viewBox="0 0 20 20"><path d="M4 2h8l4 4v12a1 1 0 01-1 1H4a1 1 0 01-1-1V3a1 1 0 011-1z"/></svg>
      </div>
      <div class="doc-meta">
        <div class="doc-name" title="${d.name}">${d.name}</div>
        <div class="doc-size">${fmtSize(d.size)}</div>
      </div>
      <button class="doc-del" onclick="removeDoc(${d.id})" title="Remove">
        <svg viewBox="0 0 24 24" fill="none" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
      </button>
    </div>
  `).join('');
}

function removeDoc(id) {
  docHistory = docHistory.filter(d => d.id !== id);
  renderDocList();
  if (attachedFile && docHistory.length === 0) removeAttached();
}

// ── Send ──
async function send() {
  const ta    = document.getElementById('queryBox');
  const query = ta.value.trim();
  if (!query || isLoading) return;

  hideWelcome();
  appendMsg('user', query, false);
  ta.value = ''; ta.style.height = '';

  const typingId = showTyping();
  setLoading(true);

  try {
    const base = document.getElementById('backendUrl').value.trim().replace(/\/$/, '');
    const fd = new FormData();
    fd.append('query', query);
    if (attachedFile) fd.append('file', attachedFile, attachedFile.name);

    const res = await fetch(base + '/query', { method: 'POST', body: fd });
    if (!res.ok) {
      const err = await res.text().catch(() => res.statusText);
      throw new Error(`${res.status} — ${err.slice(0,120)}`);
    }
    const data = await res.json();
    const answer = data.answer ?? data.response ?? data.result ?? JSON.stringify(data, null, 2);
    removeEl(typingId);
    appendMsg('ai', answer, true);
  } catch (err) {
    removeEl(typingId);
    appendMsg('ai', `**Could not reach the backend.**\n\n> ${err.message}\n\nMake sure your server is running and the URL in the sidebar is correct.`, true);
  }

  setLoading(false);
}

// ── Message rendering ──
function appendMsg(role, text, md) {
  const feed = document.getElementById('feed');
  const id = 'msg-' + Date.now();
  const isAI = role === 'ai';

  const avSVG = isAI
    ? `<svg viewBox="0 0 20 20"><path d="M4 3h8l4 4v10a1 1 0 01-1 1H4a1 1 0 01-1-1V4a1 1 0 011-1z"/></svg>`
    : `<svg viewBox="0 0 24 24"><circle cx="12" cy="8" r="4"/><path d="M4 20c0-4 3.6-7 8-7s8 3 8 7"/></svg>`;

  const content = md ? renderMd(text) : `<p>${esc(text)}</p>`;

  const div = document.createElement('div');
  div.id = id;
  div.className = `msg-wrap ${role}`;
  div.innerHTML = `
    <div class="av ${role}">${avSVG}</div>
    <div class="bubble">${content}</div>
  `;
  feed.appendChild(div);
  feed.scrollTop = feed.scrollHeight;
  if (md) div.querySelectorAll('pre code').forEach(b => hljs.highlightElement(b));
  return id;
}

function showTyping() {
  const feed = document.getElementById('feed');
  const id = 'typing-' + Date.now();
  const div = document.createElement('div');
  div.id = id; div.className = 'msg-wrap ai';
  div.innerHTML = `
    <div class="av ai"><svg viewBox="0 0 20 20"><path d="M4 3h8l4 4v10a1 1 0 01-1 1H4a1 1 0 01-1-1V4a1 1 0 011-1z"/></svg></div>
    <div class="bubble"><div class="typing"><b></b><b></b><b></b></div></div>
  `;
  feed.appendChild(div);
  feed.scrollTop = feed.scrollHeight;
  return id;
}

function sysPill(text) {
  const feed = document.getElementById('feed');
  const div = document.createElement('div');
  div.className = 'msg-wrap system-row';
  div.innerHTML = `<div class="sys-pill">${text}</div>`;
  feed.appendChild(div);
  feed.scrollTop = feed.scrollHeight;
}

function renderMd(text) {
  // Pre-process: add code-head div before each fenced block
  let html = marked.parse(text);
  // Wrap pre for syntax label + copy btn
  html = html.replace(/<pre><code class="language-(\w+)">/g, (_, lang) =>
    `<div class="code-head"><span class="code-lang">${lang}</span><button class="copy-code-btn" onclick="copyBlock(this)">Copy</button></div><pre><code class="language-${lang}">`
  );
  html = html.replace(/<pre><code>/g,
    `<div class="code-head"><span class="code-lang">code</span><button class="copy-code-btn" onclick="copyBlock(this)">Copy</button></div><pre><code>`
  );
  return html;
}

function copyBlock(btn) {
  const code = btn.closest('.code-head').nextElementSibling.querySelector('code').innerText;
  navigator.clipboard.writeText(code).then(() => {
    const orig = btn.textContent;
    btn.textContent = 'Copied!';
    setTimeout(() => btn.textContent = orig, 1600);
  });
}

// ── Helpers ──
function hideWelcome() {
  const w = document.getElementById('welcomeScreen');
  if (w) w.remove();
}

function clearChat() {
  const feed = document.getElementById('feed');
  feed.innerHTML = `
    <div class="welcome" id="welcomeScreen">
      <div class="welcome-glyph"><svg viewBox="0 0 20 20"><path d="M4 3h8l4 4v10a1 1 0 01-1 1H4a1 1 0 01-1-1V4a1 1 0 011-1z"/><path d="M12 3v4h4" fill="rgba(0,0,0,0.3)"/></svg></div>
      <h2>Ask anything about your PDF</h2>
      <p>Attach a document using the paperclip icon, then ask questions, get summaries, or extract structured data.</p>
      <div class="chips">
        <div class="chip" onclick="chip(this)">Summarize the document</div>
        <div class="chip" onclick="chip(this)">List the key findings</div>
        <div class="chip" onclick="chip(this)">Extract all dates &amp; figures</div>
        <div class="chip" onclick="chip(this)">What is the main argument?</div>
        <div class="chip" onclick="chip(this)">Create a table of contents</div>
      </div>
    </div>`;
}

function chip(el) {
  document.getElementById('queryBox').value = el.textContent;
  document.getElementById('queryBox').focus();
}

function autoGrow(el) {
  el.style.height = 'auto';
  el.style.height = Math.min(el.scrollHeight, 200) + 'px';
}

function handleKey(e) {
  if (e.key === 'Enter' && !e.shiftKey) { e.preventDefault(); send(); }
}

function setLoading(v) {
  isLoading = v;
  document.getElementById('sendBtn').disabled = v;
  const pb = document.getElementById('progBar');
  const pf = document.getElementById('progFill');
  if (v) {
    pb.style.display = 'block';
    let w = 0;
    const t = setInterval(() => {
      w = Math.min(w + Math.random() * 10, 88);
      pf.style.width = w + '%';
      if (!isLoading) clearInterval(t);
    }, 180);
    pb._t = t;
  } else {
    clearInterval(pb._t);
    pf.style.width = '100%';
    setTimeout(() => { pb.style.display = 'none'; pf.style.width = '0'; }, 350);
  }
}

function removeEl(id) { document.getElementById(id)?.remove(); }

function esc(s) {
  return s.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;');
}

function fmtSize(b) {
  if (b < 1024) return b + ' B';
  if (b < 1048576) return (b/1024).toFixed(1) + ' KB';
  return (b/1048576).toFixed(1) + ' MB';
}

// ── Toast ──
function toast(msg, type='success') {
  const shelf = document.getElementById('toasts');
  const el = document.createElement('div');
  el.className = `toast ${type}`;
  el.innerHTML = `<div class="toast-dot"></div>${msg}`;
  shelf.appendChild(el);
  setTimeout(() => el.remove(), 3200);
}

// ── Connection test ──
function scheduleTest() {
  clearTimeout(testTimer);
  testTimer = setTimeout(testConn, 900);
}

async function testConn() {
  const dot  = document.getElementById('connDot');
  const text = document.getElementById('connText');
  const base = document.getElementById('backendUrl').value.trim().replace(/\/$/, '');
  if (!base) return;

  dot.className = 'conn-dot checking'; text.textContent = 'Checking…';
  try {
    const r = await fetch(base + '/health', { signal: AbortSignal.timeout(4000) });
    if (r.ok) { dot.className='conn-dot ok'; text.textContent='Connected'; return; }
    throw new Error();
  } catch {
    try {
      const r2 = await fetch(base + '/', { signal: AbortSignal.timeout(3000) });
      if (r2.ok) { dot.className='conn-dot ok'; text.textContent='Server reachable'; return; }
    } catch {}
    dot.className = 'conn-dot err'; text.textContent = 'Unreachable';
  }
}

// ── Init ──
window.addEventListener('DOMContentLoaded', () => {
  const saved = localStorage.getItem('parchment_url');
  if (saved) document.getElementById('backendUrl').value = saved;
  document.getElementById('backendUrl').addEventListener('change', () => {
    localStorage.setItem('parchment_url', document.getElementById('backendUrl').value);
  });
});
</script>
</body>
</html>
