<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SpeakMind — Голос в текст за секунды</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Unbounded:wght@300;400;600;700&family=Onest:wght@300;400;500&display=swap" rel="stylesheet">
<style>
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

:root {
  --teal-1: #0d2b2e;
  --teal-2: #0e3a3f;
  --teal-3: #0f4a52;
  --teal-4: #1a6a73;
  --teal-5: #1e8a96;
  --teal-6: #25b3c2;
  --teal-7: #40d4e4;
  --teal-8: #7feaf4;
  --teal-9: #c2f5fa;
  --accent: #25d4c8;
  --accent-glow: rgba(37,212,200,0.18);
  --text-primary: #e8f8fa;
  --text-secondary: rgba(180,230,235,0.7);
  --text-muted: rgba(120,190,200,0.5);
  --card-bg: rgba(14,58,63,0.55);
  --card-border: rgba(37,212,200,0.18);
  --card-hover-border: rgba(37,212,200,0.45);
  --nav-bg: rgba(10,30,33,0.85);
}

html { scroll-behavior: smooth; }

body {
  font-family: 'Onest', sans-serif;
  background: #081c1f;
  color: var(--text-primary);
  min-height: 100vh;
  overflow-x: hidden;
}

body[data-page="signup"] .main-page,
body[data-page="login"] .main-page,
body[data-page="app"] .main-page { display: none; }

body[data-page="signup"] .signup-page { display: block; }
body:not([data-page="signup"]) .signup-page { display: none; }
body[data-page="login"] .login-page { display: block; }
body:not([data-page="login"]) .login-page { display: none; }
body[data-page="app"] .app-page { display: block; }
body:not([data-page="app"]) .app-page { display: none; }
body[data-page="app"]::before, body[data-page="app"]::after { opacity: 0; }

body::before {
  content: '';
  position: fixed;
  inset: 0;
  background:
    radial-gradient(ellipse 80% 60% at 50% -10%, rgba(37,200,220,0.13) 0%, transparent 70%),
    radial-gradient(ellipse 50% 40% at 80% 80%, rgba(15,74,82,0.4) 0%, transparent 60%),
    linear-gradient(160deg, #081c1f 0%, #0a2428 40%, #081c1f 100%);
  pointer-events: none;
  z-index: 0;
}

body::after {
  content: '';
  position: fixed;
  inset: 0;
  background-image:
    linear-gradient(rgba(37,212,200,0.04) 1px, transparent 1px),
    linear-gradient(90deg, rgba(37,212,200,0.04) 1px, transparent 1px);
  background-size: 60px 60px;
  pointer-events: none;
  z-index: 0;
}

nav {
  position: fixed;
  top: 0; left: 0; right: 0;
  z-index: 100;
  padding: 0 40px;
  height: 64px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: var(--nav-bg);
  backdrop-filter: blur(16px);
  border-bottom: 1px solid rgba(37,212,200,0.1);
}

.logo {
  font-family: 'Unbounded', sans-serif;
  font-size: 18px;
  font-weight: 600;
  color: var(--text-primary);
  letter-spacing: -0.02em;
  display: flex;
  align-items: center;
  gap: 10px;
}

.logo-icon {
  width: 32px; height: 32px;
  background: linear-gradient(135deg, var(--teal-5), var(--accent));
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.logo-icon svg { width: 18px; height: 18px; }

.nav-btn {
  font-family: 'Onest', sans-serif;
  font-size: 13px;
  font-weight: 500;
  padding: 9px 22px;
  border-radius: 8px;
  background: var(--accent);
  color: #081c1f;
  border: none;
  cursor: pointer;
  letter-spacing: 0.01em;
  transition: all 0.2s;
}
.nav-btn:hover { background: #1ffff0; transform: translateY(-1px); }
.nav-btn.secondary { background: transparent; color: var(--text-primary); border: 1px solid rgba(37,212,200,0.25); }
.nav-btn.secondary:hover { border-color: var(--accent); background: rgba(37,212,200,0.06); transform: translateY(-1px); }
.nav-actions { display: flex; gap: 12px; align-items: center; }

.hero {
  position: relative;
  z-index: 1;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  padding: 100px 24px 80px;
}

.hero-badge {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 6px 16px;
  border-radius: 99px;
  background: rgba(37,212,200,0.08);
  border: 1px solid rgba(37,212,200,0.2);
  font-size: 12px;
  color: var(--accent);
  letter-spacing: 0.08em;
  text-transform: uppercase;
  margin-bottom: 36px;
  font-weight: 500;
}

.badge-dot {
  width: 6px; height: 6px;
  border-radius: 50%;
  background: var(--accent);
  animation: pulse 2s infinite;
}

@keyframes pulse { 0%,100%{opacity:1;transform:scale(1)} 50%{opacity:0.5;transform:scale(0.8)} }

.hero h1 {
  font-family: 'Unbounded', sans-serif;
  font-size: clamp(36px, 7vw, 72px);
  font-weight: 700;
  line-height: 1.08;
  letter-spacing: -0.03em;
  margin-bottom: 28px;
  max-width: 840px;
}
.hero h1 .highlight {
  background: linear-gradient(90deg, var(--teal-7), var(--accent));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.hero-desc { font-size: 17px; color: var(--text-secondary); max-width: 480px; line-height: 1.65; margin-bottom: 48px; font-weight: 300; }
.hero-cta { display: flex; gap: 14px; align-items: center; flex-wrap: wrap; justify-content: center; margin-bottom: 80px; }

.btn-primary {
  font-family: 'Onest', sans-serif; font-size: 15px; font-weight: 500;
  padding: 14px 32px; border-radius: 10px;
  background: linear-gradient(135deg, var(--teal-5), var(--accent));
  color: #081c1f; border: none; cursor: pointer; transition: all 0.25s; letter-spacing: 0.01em;
}
.btn-primary:hover { transform: translateY(-2px); box-shadow: 0 8px 32px rgba(37,212,200,0.25); }

.btn-secondary {
  font-family: 'Onest', sans-serif; font-size: 15px; font-weight: 400;
  padding: 14px 32px; border-radius: 10px;
  background: transparent; color: var(--text-primary); border: 1px solid rgba(37,212,200,0.25);
  cursor: pointer; transition: all 0.25s;
}
.btn-secondary:hover { border-color: var(--accent); background: rgba(37,212,200,0.06); }

.waveform { display: flex; align-items: center; gap: 4px; margin-bottom: 80px; }
.waveform-bar {
  width: 3px; border-radius: 2px; background: var(--accent);
  animation: wave 1.2s ease-in-out infinite; opacity: 0.7;
}
@keyframes wave { 0%,100%{transform:scaleY(1)} 50%{transform:scaleY(2.5)} }

section { position: relative; z-index: 1; max-width: 1100px; margin: 0 auto; padding: 100px 24px; }
.section-header { text-align: center; margin-bottom: 64px; }
.section-label { font-size: 11px; font-weight: 500; letter-spacing: 0.14em; text-transform: uppercase; color: var(--accent); margin-bottom: 16px; }
.section-title { font-family: 'Unbounded', sans-serif; font-size: clamp(26px,4vw,42px); font-weight: 600; letter-spacing: -0.025em; line-height: 1.15; margin-bottom: 16px; }
.section-sub { font-size: 16px; color: var(--text-secondary); font-weight: 300; }

.features-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; }
.feature-card {
  background: var(--card-bg); border: 1px solid var(--card-border);
  border-radius: 16px; padding: 28px; backdrop-filter: blur(12px);
  transition: all 0.3s; cursor: default;
}
.feature-card:hover { border-color: var(--card-hover-border); background: rgba(14,58,63,0.75); transform: translateY(-3px); }
.card-icon {
  width: 44px; height: 44px; border-radius: 10px;
  background: rgba(37,212,200,0.1); border: 1px solid rgba(37,212,200,0.2);
  display: flex; align-items: center; justify-content: center; margin-bottom: 20px;
}
.card-icon svg { width: 22px; height: 22px; stroke: var(--accent); fill: none; stroke-width: 1.8; stroke-linecap: round; stroke-linejoin: round; }
.card-title { font-family: 'Unbounded', sans-serif; font-size: 15px; font-weight: 600; margin-bottom: 10px; color: var(--text-primary); letter-spacing: -0.01em; }
.card-desc { font-size: 14px; line-height: 1.65; color: var(--text-secondary); font-weight: 300; }

.steps { display: grid; grid-template-columns: repeat(4, 1fr); gap: 0; position: relative; margin-top: 16px; }
.steps::before { content:''; position:absolute; top:22px; left:calc(12.5% + 22px); right:calc(12.5% + 22px); height:1px; background:linear-gradient(90deg,var(--accent),transparent); opacity:0.3; }
.step { text-align: center; padding: 0 16px; }
.step-num { width:44px;height:44px;border-radius:50%;background:rgba(37,212,200,0.08);border:1px solid rgba(37,212,200,0.25);font-family:'Unbounded',sans-serif;font-size:14px;font-weight:600;color:var(--accent);display:flex;align-items:center;justify-content:center;margin:0 auto 20px; }
.step-title { font-family:'Unbounded',sans-serif;font-size:13px;font-weight:600;margin-bottom:8px;color:var(--text-primary);letter-spacing:-0.01em; }
.step-desc { font-size:13px;color:var(--text-secondary);line-height:1.6;font-weight:300; }

.recorder-demo {
  background: var(--card-bg); border: 1px solid var(--card-border);
  border-radius: 24px; padding: 40px; backdrop-filter: blur(16px);
  max-width: 700px; margin: 0 auto;
}
.recorder-header { display:flex;align-items:center;justify-content:space-between;margin-bottom:32px; }
.rec-title { font-family:'Unbounded',sans-serif;font-size:14px;font-weight:500;color:var(--text-primary); }
.rec-tag { font-size:11px;padding:4px 12px;border-radius:99px;background:rgba(220,80,80,0.15);border:1px solid rgba(220,80,80,0.3);color:#f47b7b;font-weight:500;letter-spacing:0.05em;display:flex;align-items:center;gap:6px; }
.rec-dot { width:6px;height:6px;background:#f47b7b;border-radius:50%;animation:pulse 1s infinite; }
.waveform-display { display:flex;align-items:center;gap:3px;height:60px;margin-bottom:24px;padding:0 8px; }
.wd-bar { flex:1;border-radius:2px;background:rgba(37,212,200,0.25);transition:height 0.1s; }
.time-display { text-align:center;font-family:'Unbounded',sans-serif;font-size:32px;font-weight:300;color:var(--text-primary);letter-spacing:0.05em;margin-bottom:28px; }
.recorder-controls { display:flex;align-items:center;justify-content:center;gap:20px; }
.ctrl-btn { width:44px;height:44px;border-radius:50%;background:rgba(37,212,200,0.08);border:1px solid rgba(37,212,200,0.2);display:flex;align-items:center;justify-content:center;cursor:pointer;transition:all 0.2s; }
.ctrl-btn:hover { background:rgba(37,212,200,0.16);border-color:var(--accent); }
.ctrl-btn svg { width:18px;height:18px;stroke:var(--accent);fill:none;stroke-width:1.8;stroke-linecap:round;stroke-linejoin:round; }
.ctrl-btn-main { width:64px;height:64px;border-radius:50%;background:linear-gradient(135deg,var(--teal-5),var(--accent));border:none;display:flex;align-items:center;justify-content:center;cursor:pointer;transition:all 0.2s;box-shadow:0 0 0 0 rgba(37,212,200,0.3);animation:mic-glow 2s infinite; }
@keyframes mic-glow { 0%,100%{box-shadow:0 0 0 0 rgba(37,212,200,0.3)} 50%{box-shadow:0 0 0 12px rgba(37,212,200,0)} }
.ctrl-btn-main:hover { transform:scale(1.05); }
.ctrl-btn-main svg { width:26px;height:26px;stroke:#081c1f;fill:none;stroke-width:2;stroke-linecap:round;stroke-linejoin:round; }
.transcript-box { margin-top:28px;padding-top:28px;border-top:1px solid rgba(37,212,200,0.1); }
.transcript-label { font-size:11px;text-transform:uppercase;letter-spacing:0.1em;color:var(--text-muted);margin-bottom:12px; }
.transcript-text { font-size:14px;line-height:1.7;color:var(--text-secondary);font-weight:300; }
.cursor { display:inline-block;width:2px;height:14px;background:var(--accent);margin-left:2px;vertical-align:text-bottom;animation:blink 1s infinite; }
@keyframes blink { 0%,100%{opacity:1} 50%{opacity:0} }

.cta-section { position:relative;z-index:1;padding:80px 24px 120px;text-align:center; }
.cta-inner { max-width:700px;margin:0 auto;background:var(--card-bg);border:1px solid var(--card-border);border-radius:28px;padding:64px 48px;backdrop-filter:blur(12px); }
.cta-inner h2 { font-family:'Unbounded',sans-serif;font-size:clamp(24px,4vw,38px);font-weight:700;letter-spacing:-0.025em;margin-bottom:16px; }
.cta-inner p { font-size:16px;color:var(--text-secondary);margin-bottom:36px;font-weight:300;line-height:1.6; }

.signup-form { display:flex;flex-direction:column;gap:18px; }
.form-field label { display:block;font-size:12px;text-transform:uppercase;letter-spacing:0.08em;color:var(--text-muted);margin-bottom:6px; }
.form-field input { width:100%;padding:12px 14px;border-radius:10px;border:1px solid rgba(37,212,200,0.25);background:rgba(3,18,20,0.8);color:var(--text-primary);font-family:'Onest',sans-serif;font-size:14px;outline:none;transition:border-color 0.2s,box-shadow 0.2s,background 0.2s; }
.form-field input::placeholder { color:var(--text-muted); }
.form-field input:focus { border-color:var(--accent);box-shadow:0 0 0 1px rgba(37,212,200,0.4);background:rgba(5,26,29,0.95); }
.form-hint { font-size:11px;color:var(--text-muted);margin-top:8px;line-height:1.5; }

/* ─── APP PAGE ─────────────────────────────────────────── */
.app-page {
  display: none;
  position: relative;
  z-index: 1;
  min-height: 100vh;
  background: linear-gradient(180deg, #055064 0%, #054a55 40%, #043e47 100%);
  padding: 86px 18px 140px;
  font-family: 'Onest', sans-serif;
}
body[data-page="app"] .app-page { display: block; }

.app-topbar {
  position: fixed; top: 0; left: 0; right: 0; height: 64px; z-index: 250;
  padding: 0 18px; display: flex; align-items: center; justify-content: space-between;
  background: rgba(3,18,20,0.75); border-bottom: 1px solid rgba(127,234,244,0.16);
  backdrop-filter: blur(10px);
}
.app-title { font-family:'Unbounded',sans-serif;font-size:18px;font-weight:600;color:#e8f8fa;letter-spacing:-0.02em; }
.app-icon-btn { width:42px;height:42px;display:inline-flex;align-items:center;justify-content:center;border-radius:12px;background:rgba(37,212,200,0.06);border:1px solid rgba(37,212,200,0.14);color:#e8f8fa;cursor:pointer; }
.app-icon-btn svg { width:18px;height:18px;fill:none;stroke:#25d4c8;stroke-width:2;stroke-linecap:round;stroke-linejoin:round; }

.app-tabs { display:flex;align-items:baseline;gap:28px;margin-bottom:14px;padding-left:2px; }
.app-tab { background:transparent;border:none;color:rgba(180,230,235,0.7);font-size:13px;font-weight:600;letter-spacing:0.02em;padding:6px 0;cursor:pointer; }
.app-tab.active { color:#7feaf4;border-bottom:2px solid #7feaf4; }

.records-list { display:flex;flex-direction:column;gap:14px; }

.record-card {
  background: rgba(4,52,61,0.55);
  border: 1px solid rgba(127,234,244,0.14);
  border-radius: 14px;
  padding: 14px 14px;
  display: flex;
  flex-direction: column;
  gap: 10px;
  transition: border-color 0.18s, background 0.18s;
}

.record-top {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
}

.record-left { display:flex;flex-direction:column;gap:6px;min-width:0; }
.record-title-row { display:flex;align-items:center;gap:8px; }
.record-star {
  width:22px;height:22px;display:inline-flex;align-items:center;justify-content:center;
  color:rgba(127,234,244,0.5);font-size:18px;line-height:1;cursor:pointer;
  transition:color 0.2s,transform 0.15s;user-select:none;flex-shrink:0;
  background:none;border:none;padding:0;outline:none;
}
.record-star:hover { color:rgba(255,210,50,0.85);transform:scale(1.2); }
.record-star.starred { color:#ffd232;text-shadow:0 0 8px rgba(255,210,50,0.5); }
.record-card.favorited { border-color:rgba(255,210,50,0.3);background:rgba(4,52,61,0.75); }

.favorites-empty { display:flex;flex-direction:column;align-items:center;justify-content:center;padding:60px 20px;text-align:center;gap:14px;color:rgba(180,230,235,0.45); }
.favorites-empty-icon { font-size:48px;opacity:0.4;line-height:1; }
.favorites-empty p { font-size:14px;line-height:1.6;max-width:220px; }

.record-title { font-family:'Unbounded',sans-serif;font-size:15px;font-weight:600;color:#e8f8fa;white-space:nowrap;overflow:hidden;text-overflow:ellipsis; }
.record-sub { display:flex;align-items:center;gap:12px;color:rgba(180,230,235,0.7);font-size:12px;flex-wrap:wrap; }
.record-cat { display:inline-flex;align-items:center;gap:6px; }
.record-cat svg { width:14px;height:14px;stroke:rgba(127,234,244,0.95);stroke-width:2;fill:none; }
.record-right { display:flex;align-items:center;gap:10px;flex-shrink:0; }
.record-duration { font-family:'Unbounded',sans-serif;font-size:14px;color:rgba(180,230,235,0.85);font-weight:500; }

.record-play {
  width:34px;height:34px;border-radius:50%;
  background:rgba(37,212,200,0.12);border:1px solid rgba(37,212,200,0.24);
  display:inline-flex;align-items:center;justify-content:center;cursor:pointer;
  transition: all 0.2s;
}
.record-play:hover { background:rgba(37,212,200,0.25); }
.record-play svg { width:14px;height:14px;stroke:#25d4c8;fill:#25d4c8; }
.record-play.playing { background:rgba(37,212,200,0.3);border-color:var(--accent); }

/* Mini audio player */
.record-player {
  display: none;
  flex-direction: column;
  gap: 8px;
  padding-top: 8px;
  border-top: 1px solid rgba(127,234,244,0.1);
}
.record-player.visible { display: flex; }
.player-progress {
  -webkit-appearance: none;
  appearance: none;
  width: 100%;
  height: 4px;
  border-radius: 2px;
  background: rgba(37,212,200,0.2);
  outline: none;
  cursor: pointer;
}
.player-progress::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 14px; height: 14px;
  border-radius: 50%;
  background: var(--accent);
  cursor: pointer;
  box-shadow: 0 0 6px rgba(37,212,200,0.5);
}
.player-times { display:flex;justify-content:space-between;font-size:11px;color:var(--text-muted); }

/* Transcript on card */
.record-transcript {
  font-size: 13px;
  color: rgba(180,230,235,0.65);
  line-height: 1.6;
  border-top: 1px solid rgba(127,234,244,0.1);
  padding-top: 10px;
  margin-top: 4px;
  display: block;
  white-space: pre-wrap;
  word-break: break-word;
}
.transcript-engine-badge {
  display: inline-block;
  font-size: 10px;
  padding: 2px 8px;
  border-radius: 99px;
  background: rgba(37,212,200,0.1);
  border: 1px solid rgba(37,212,200,0.2);
  color: var(--accent);
  margin-bottom: 6px;
  letter-spacing: 0.05em;
}

/* FAB mic */
.app-fab-mic {
  position: fixed;
  left: 50%; transform: translateX(-50%);
  bottom: 110px;
  width: 86px; height: 86px;
  border-radius: 50%;
  background: radial-gradient(circle at 30% 20%, #7feaf4 0%, #25d4c8 55%, rgba(37,212,200,0.15) 100%);
  border: 2px solid rgba(127,234,244,0.6);
  box-shadow: 0 18px 60px rgba(37,212,200,0.22);
  z-index: 300;
  display: flex; align-items: center; justify-content: center;
  cursor: pointer; transition: all 0.2s;
}
.app-fab-mic:hover { transform: translateX(-50%) scale(1.05); }
.app-fab-mic.recording {
  background: radial-gradient(circle at 30% 20%, #f47b7b 0%, #e05555 55%, rgba(220,80,80,0.15) 100%);
  border-color: rgba(244,123,123,0.6);
  box-shadow: 0 18px 60px rgba(220,80,80,0.3);
  animation: rec-pulse 1s infinite;
}
@keyframes rec-pulse { 0%,100%{box-shadow:0 0 0 0 rgba(220,80,80,0.4),0 18px 60px rgba(220,80,80,0.3)} 50%{box-shadow:0 0 0 18px rgba(220,80,80,0),0 18px 60px rgba(220,80,80,0.3)} }
.app-fab-mic svg { width:30px;height:30px;stroke:#08343b;stroke-width:2;fill:none; }
.app-fab-mic.hidden { display: none !important; }

/* ─── SELECTION CIRCLE ─────────────────────────────────── */
.record-select-circle {
  width: 22px; height: 22px;
  border-radius: 50%;
  border: 2px solid rgba(127,234,244,0.32);
  background: transparent;
  cursor: pointer;
  flex-shrink: 0;
  transition: background 0.18s, border-color 0.18s, transform 0.15s;
  display: inline-flex; align-items: center; justify-content: center;
  padding: 0; outline: none; appearance: none;
  position: relative;
}
.record-select-circle:hover { border-color: rgba(37,212,200,0.7); transform: scale(1.1); }
.record-select-circle.sel {
  background: var(--accent);
  border-color: var(--accent);
}
.record-select-circle.sel::after {
  content: '';
  display: block;
  width: 5px; height: 9px;
  border: 2px solid #081c1f;
  border-top: none; border-left: none;
  transform: rotate(45deg) translateY(-1px);
}
.record-card.card-sel {
  border-color: rgba(37,212,200,0.5);
  background: rgba(14,58,63,0.8);
}

/* ─── TRASH FAB ─────────────────────────────────────────── */
.app-fab-trash {
  position: fixed;
  left: 50%; transform: translateX(-50%);
  bottom: 110px;
  width: 86px; height: 86px;
  border-radius: 50%;
  background: radial-gradient(circle at 30% 20%, #7feaf4 0%, #25d4c8 55%, rgba(37,212,200,0.15) 100%);
  border: 2px solid rgba(127,234,244,0.6);
  box-shadow: 0 18px 60px rgba(37,212,200,0.28);
  z-index: 301;
  display: none; align-items: center; justify-content: center;
  cursor: pointer; transition: transform 0.2s, box-shadow 0.2s;
}
.app-fab-trash.visible { display: flex; }
.app-fab-trash:hover { transform: translateX(-50%) scale(1.05); }
.app-fab-trash svg { width: 30px; height: 30px; stroke: #08343b; fill: none; stroke-width: 2.2; stroke-linecap: round; stroke-linejoin: round; }
.fab-sel-badge {
  position: absolute; top: -3px; right: -3px;
  min-width: 22px; height: 22px; padding: 0 5px;
  border-radius: 11px;
  background: #f47b7b; color: #fff;
  font-size: 11px; font-weight: 700; font-family: 'Onest', sans-serif;
  display: flex; align-items: center; justify-content: center;
  border: 2px solid #054a55;
}

.app-bottom-actions {
  position: fixed; left: 20px; right: 20px; bottom: 24px; z-index: 260;
  display: flex; align-items: center; justify-content: space-between; gap: 12px;
}
.app-bottom-actions .app-icon-btn { width:46px;height:46px; }

body[data-page="app"] footer { display: none; }

footer {
  position: relative; z-index: 1;
  border-top: 1px solid rgba(37,212,200,0.08);
  padding: 32px 40px;
  display: flex; align-items: center; justify-content: space-between;
  max-width: 1200px; margin: 0 auto; flex-wrap: wrap; gap: 16px;
}
.footer-logo { font-family:'Unbounded',sans-serif;font-size:15px;font-weight:600;color:var(--text-muted); }
.footer-links { display:flex;gap:28px; }
.footer-links a { font-size:13px;color:var(--text-muted);text-decoration:none;transition:color 0.2s; }
.footer-links a:hover { color:var(--accent); }

/* ─── TRASH TAB ─────────────────────────────────────────── */
.trash-actions {
  display: flex; gap: 8px;
  padding-top: 10px; margin-top: 2px;
  border-top: 1px solid rgba(127,234,244,0.08);
}
.trash-btn {
  flex: 1; padding: 8px 10px; border-radius: 8px;
  font-family: 'Onest', sans-serif; font-size: 12px; font-weight: 500;
  cursor: pointer; transition: all 0.18s; border: 1px solid transparent;
}
.trash-btn.restore { background: rgba(37,212,200,0.1); color: var(--accent); border-color: rgba(37,212,200,0.25); }
.trash-btn.restore:hover { background: rgba(37,212,200,0.22); }
.trash-btn.del-perm { background: rgba(220,80,80,0.1); color: #f47b7b; border-color: rgba(220,80,80,0.2); }
.trash-btn.del-perm:hover { background: rgba(220,80,80,0.22); }

.trash-empty { display:flex;flex-direction:column;align-items:center;justify-content:center;padding:60px 20px;text-align:center;gap:14px;color:rgba(180,230,235,0.45); }
.trash-empty-icon { font-size:48px;opacity:0.3; }
.trash-empty p { font-size:14px;line-height:1.6;max-width:220px; }

/* ─── MODAL ─────────────────────────────────────────────── */
.modal-overlay {
  display: none;
  position: fixed;
  inset: 0;
  z-index: 500;
  background: rgba(4,20,23,0.85);
  backdrop-filter: blur(8px);
  align-items: center;
  justify-content: center;
  padding: 20px;
}
.modal-overlay.open { display: flex; }

.modal-box {
  background: linear-gradient(145deg, #0d3a40, #0a2d32);
  border: 1px solid rgba(37,212,200,0.3);
  border-radius: 20px;
  padding: 28px 24px;
  width: 100%;
  max-width: 360px;
  box-shadow: 0 24px 80px rgba(0,0,0,0.5);
}
.modal-title {
  font-family: 'Unbounded', sans-serif;
  font-size: 16px;
  font-weight: 600;
  color: #e8f8fa;
  margin-bottom: 20px;
  display: flex;
  align-items: center;
  gap: 10px;
}
.modal-title svg { width:20px;height:20px;stroke:var(--accent);fill:none;stroke-width:2;stroke-linecap:round;stroke-linejoin:round; }

.modal-field { margin-bottom: 16px; }
.modal-field label { display:block;font-size:11px;text-transform:uppercase;letter-spacing:0.08em;color:var(--text-muted);margin-bottom:6px; }
.modal-field input, .modal-field select {
  width: 100%; padding: 11px 13px; border-radius: 10px;
  border: 1px solid rgba(37,212,200,0.22);
  background: rgba(3,18,20,0.8); color: var(--text-primary);
  font-family: 'Onest', sans-serif; font-size: 14px; outline: none;
  transition: border-color 0.2s, box-shadow 0.2s;
}
.modal-field input:focus, .modal-field select:focus { border-color: var(--accent); box-shadow: 0 0 0 1px rgba(37,212,200,0.3); }
.modal-field select { appearance: none; cursor: pointer; }
.modal-field select option { background: #0d3a40; }

.transcription-methods {
  display: flex;
  flex-direction: column;
  gap: 8px;
  margin-bottom: 16px;
}
.transcription-method {
  display: flex;
  align-items: flex-start;
  gap: 10px;
  padding: 10px 12px;
  border-radius: 10px;
  border: 1px solid rgba(37,212,200,0.15);
  background: rgba(3,18,20,0.5);
  cursor: pointer;
  transition: all 0.2s;
}
.transcription-method:hover { border-color: rgba(37,212,200,0.35); background: rgba(3,18,20,0.7); }
.transcription-method input[type="radio"] { accent-color: var(--accent); margin-top: 2px; flex-shrink: 0; }
.transcription-method-name { font-size: 13px; font-weight: 500; color: var(--text-primary); }
.transcription-method-desc { font-size: 11px; color: var(--text-muted); margin-top: 2px; line-height: 1.4; }

.yandex-key-field { display: none; }
.yandex-key-field.visible { display: block; }

.modal-rec-status {
  text-align: center;
  padding: 16px;
  background: rgba(3,18,20,0.5);
  border-radius: 12px;
  margin-bottom: 16px;
  border: 1px solid rgba(127,234,244,0.1);
}
.modal-rec-timer {
  font-family: 'Unbounded', sans-serif;
  font-size: 28px;
  font-weight: 300;
  color: var(--text-primary);
  letter-spacing: 0.05em;
  margin-bottom: 8px;
}
.modal-waveform {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 3px;
  height: 36px;
}
.modal-wave-bar {
  width: 3px;
  border-radius: 2px;
  background: rgba(37,212,200,0.4);
  height: 8px;
  transition: height 0.1s, opacity 0.1s;
}
.modal-wave-bar.active { background: var(--accent); }
.modal-rec-label { font-size: 11px; color: var(--text-muted); letter-spacing: 0.05em; margin-top: 6px; }
.modal-rec-label.recording { color: #f47b7b; }

.modal-actions { display: flex; gap: 10px; margin-top: 4px; }
.modal-btn {
  flex: 1; padding: 12px; border-radius: 10px;
  font-family: 'Onest', sans-serif; font-size: 14px; font-weight: 500;
  cursor: pointer; transition: all 0.2s; border: none;
}
.modal-btn.primary { background: linear-gradient(135deg, var(--teal-5), var(--accent)); color: #081c1f; }
.modal-btn.primary:hover { transform: translateY(-1px); box-shadow: 0 6px 20px rgba(37,212,200,0.25); }
.modal-btn.secondary { background: transparent; color: var(--text-secondary); border: 1px solid rgba(37,212,200,0.2); }
.modal-btn.secondary:hover { border-color: rgba(37,212,200,0.45); }
.modal-btn.danger { background: rgba(220,80,80,0.15); color: #f47b7b; border: 1px solid rgba(220,80,80,0.3); }
.modal-btn.danger:hover { background: rgba(220,80,80,0.25); }
.modal-btn:disabled { opacity: 0.5; cursor: not-allowed; }

.modal-transcribing {
  display: none;
  align-items: center;
  gap: 10px;
  padding: 10px 14px;
  background: rgba(37,212,200,0.06);
  border-radius: 10px;
  margin-bottom: 12px;
  border: 1px solid rgba(37,212,200,0.15);
  font-size: 12px;
  color: var(--text-secondary);
}
.modal-transcribing.visible { display: flex; }

/* ─── LIVE TRANSCRIPT BOX ───────────────────────────────── */
.live-transcript-box {
  min-height: 90px;
  max-height: 160px;
  overflow-y: auto;
  background: rgba(3,18,20,0.6);
  border: 1px solid rgba(37,212,200,0.15);
  border-radius: 12px;
  padding: 12px 14px;
  margin-bottom: 14px;
  font-size: 14px;
  line-height: 1.65;
  color: var(--text-primary);
  font-family: 'Onest', sans-serif;
  word-break: break-word;
  scroll-behavior: smooth;
  position: relative;
}
.live-transcript-box:empty::before,
.live-transcript-box.empty::before {
  content: 'Говорите — текст появится здесь…';
  color: var(--text-muted);
  font-size: 13px;
}
.live-transcript-final { color: var(--text-primary); }
.live-transcript-interim {
  color: rgba(127,234,244,0.65);
  font-style: italic;
}
.live-transcript-box .live-cursor {
  display: inline-block;
  width: 2px; height: 14px;
  background: var(--accent);
  vertical-align: text-bottom;
  margin-left: 2px;
  animation: blink 1s infinite;
  border-radius: 1px;
}
.transcribing-spinner {
  width: 16px; height: 16px;
  border: 2px solid rgba(37,212,200,0.2);
  border-top-color: var(--accent);
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
  flex-shrink: 0;
}
@keyframes spin { to{transform:rotate(360deg)} }

.empty-state {
  display: flex; flex-direction: column; align-items: center;
  justify-content: center; padding: 60px 20px; text-align: center;
  gap: 14px; color: rgba(180,230,235,0.45);
}
.empty-state-icon { font-size: 48px; opacity: 0.3; }
.empty-state p { font-size: 14px; line-height: 1.6; max-width: 220px; }

/* ─── FOLDERS ───────────────────────────────────────────── */
.folder-card {
  display: flex; align-items: center; gap: 14px;
  background: rgba(4,52,61,0.55);
  border: 1px solid rgba(127,234,244,0.18);
  border-radius: 14px; padding: 14px 16px;
  cursor: pointer; transition: all 0.18s;
}
.folder-card:hover { border-color: rgba(37,212,200,0.42); background: rgba(14,58,63,0.78); transform: translateY(-1px); }
.folder-icon-emoji { font-size: 26px; flex-shrink: 0; }
.folder-info { flex: 1; min-width: 0; }
.folder-name { font-family: 'Unbounded', sans-serif; font-size: 14px; font-weight: 600; color: #e8f8fa; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
.folder-count { font-size: 12px; color: rgba(180,230,235,0.5); margin-top: 3px; }
.folder-delete-btn {
  width: 30px; height: 30px; border-radius: 50%;
  background: rgba(220,80,80,0.08); border: 1px solid rgba(220,80,80,0.18);
  display: inline-flex; align-items: center; justify-content: center;
  cursor: pointer; flex-shrink: 0; transition: all 0.18s;
}
.folder-delete-btn:hover { background: rgba(220,80,80,0.22); }
.folder-delete-btn svg { width: 13px; height: 13px; stroke: #f47b7b; fill: none; stroke-width: 2; stroke-linecap: round; }

/* Breadcrumb (shown when inside a folder) */
.folder-breadcrumb {
  display: none; align-items: center; gap: 10px;
  padding: 6px 0 14px;
}
.folder-breadcrumb.visible { display: flex; }
.folder-back-btn {
  display: flex; align-items: center; gap: 5px;
  background: rgba(37,212,200,0.08); border: 1px solid rgba(37,212,200,0.22);
  border-radius: 8px; padding: 6px 12px; color: var(--accent);
  font-size: 13px; font-weight: 500; cursor: pointer; transition: all 0.18s;
}
.folder-back-btn:hover { background: rgba(37,212,200,0.18); }
.folder-back-btn svg { width: 16px; height: 16px; stroke: currentColor; fill: none; stroke-width: 2; stroke-linecap: round; stroke-linejoin: round; }
.folder-current-name { font-family: 'Unbounded', sans-serif; font-size: 14px; font-weight: 600; color: #e8f8fa; }

/* Folder button on record card */
.record-folder-btn {
  width: 34px; height: 34px; border-radius: 50%;
  background: rgba(37,212,200,0.07); border: 1px solid rgba(37,212,200,0.18);
  display: inline-flex; align-items: center; justify-content: center;
  cursor: pointer; transition: all 0.2s;
}
.record-folder-btn:hover { background: rgba(37,212,200,0.18); }
.record-folder-btn svg { width: 14px; height: 14px; stroke: rgba(127,234,244,0.75); fill: none; stroke-width: 2; stroke-linecap: round; stroke-linejoin: round; }
.record-folder-btn.in-folder svg { stroke: var(--accent); }

/* Folder picker modal */
.folder-picker-list { display: flex; flex-direction: column; gap: 6px; max-height: 240px; overflow-y: auto; margin-bottom: 14px; }
.folder-picker-item {
  display: flex; align-items: center; gap: 10px;
  padding: 10px 12px; border-radius: 10px;
  border: 1px solid rgba(37,212,200,0.12);
  background: rgba(3,18,20,0.5);
  cursor: pointer; transition: all 0.18s;
  font-size: 14px; color: var(--text-primary);
}
.folder-picker-item:hover { border-color: rgba(37,212,200,0.35); background: rgba(3,18,20,0.7); }
.folder-picker-item.current { border-color: rgba(37,212,200,0.4); background: rgba(37,212,200,0.08); cursor: default; }
.pi-icon { font-size: 18px; }
.pi-name { flex: 1; }
.pi-check { color: var(--accent); }
.folder-picker-new {
  display: flex; align-items: center; gap: 10px;
  padding: 10px 12px; border-radius: 10px;
  border: 1px dashed rgba(37,212,200,0.3);
  background: transparent; cursor: pointer; transition: all 0.18s;
  font-size: 14px; color: var(--accent);
}
.folder-picker-new:hover { background: rgba(37,212,200,0.07); }

@media (max-width: 900px) {
  .steps { grid-template-columns: repeat(2, 1fr); gap: 32px; }
  .steps::before { display: none; }
  .features-grid { grid-template-columns: 1fr 1fr; }
}
@media (max-width: 600px) {
  nav { padding: 0 20px; }
  .features-grid { grid-template-columns: 1fr; }
  .steps { grid-template-columns: 1fr; }
  .recorder-demo { padding: 24px; }
  footer { flex-direction: column; align-items: center; text-align: center; }
}
</style>
</head>
<body data-page="home">

<!-- ══════════ MAIN PAGE ══════════ -->
<div class="main-page">
<nav>
  <div class="logo">
    <div class="logo-icon">
      <svg viewBox="0 0 18 18" fill="none" stroke="white" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
        <rect x="5" y="2" width="8" height="10" rx="4"/>
        <path d="M3 9c0 3.3 2.7 6 6 6s6-2.7 6-6"/>
        <line x1="9" y1="15" x2="9" y2="18"/><line x1="6" y1="18" x2="12" y2="18"/>
      </svg>
    </div>
    SpeakMind
  </div>
  <div class="nav-actions">
    <button class="nav-btn js-open-signup">Зарегистрироваться →</button>
    <button class="nav-btn secondary js-open-login">Вход →</button>
  </div>
</nav>

<div class="hero">
  <div class="hero-badge"><div class="badge-dot"></div>ИИ-транскрибация нового поколения</div>
  <h1>Превратите <span class="highlight">голос в текст</span><br>за секунды</h1>
  <p class="hero-desc">Записывайте интервью, совещания и встречи с автоматической транскрибацией, анализом и умным поиском по всем записям.</p>
  <div class="hero-cta"><button class="btn-primary js-open-signup">Зарегистрироваться</button></div>
  <div class="waveform">
    <div class="waveform-bar" style="height:18px;animation-delay:0s"></div>
    <div class="waveform-bar" style="height:28px;animation-delay:0.1s"></div>
    <div class="waveform-bar" style="height:36px;animation-delay:0.2s"></div>
    <div class="waveform-bar" style="height:44px;animation-delay:0.15s"></div>
    <div class="waveform-bar" style="height:52px;animation-delay:0.25s"></div>
    <div class="waveform-bar" style="height:60px;animation-delay:0.05s"></div>
    <div class="waveform-bar" style="height:48px;animation-delay:0.3s"></div>
    <div class="waveform-bar" style="height:56px;animation-delay:0.2s"></div>
    <div class="waveform-bar" style="height:40px;animation-delay:0.1s"></div>
    <div class="waveform-bar" style="height:50px;animation-delay:0.15s"></div>
    <div class="waveform-bar" style="height:44px;animation-delay:0.05s"></div>
    <div class="waveform-bar" style="height:36px;animation-delay:0.25s"></div>
    <div class="waveform-bar" style="height:28px;animation-delay:0.2s"></div>
    <div class="waveform-bar" style="height:22px;animation-delay:0.1s"></div>
    <div class="waveform-bar" style="height:16px;animation-delay:0s"></div>
  </div>
</div>

<section>
  <div class="section-header">
    <div class="section-label">Возможности</div>
    <h2 class="section-title">Всё, что нужно для<br>эффективной работы</h2>
    <p class="section-sub">Мощные инструменты для записи, транскрибации и анализа аудио</p>
  </div>
  <div class="features-grid">
    <div class="feature-card"><div class="card-icon"><svg viewBox="0 0 24 24"><path d="m13 2-2 2.5h-3a2 2 0 0 0-2 2v12a2 2 0 0 0 2 2h10a2 2 0 0 0 2-2V6.5a2 2 0 0 0-2-2h-3L13 2z"/><path d="M9 12h6M9 16h6"/></svg></div><div class="card-title">Мгновенная транскрибация</div><p class="card-desc">Превращайте речь в точный текст — с браузерным API или Yandex SpeechKit.</p></div>
    <div class="feature-card"><div class="card-icon"><svg viewBox="0 0 24 24"><circle cx="12" cy="12" r="10"/><path d="M12 16v-4M12 8h.01"/></svg></div><div class="card-title">ИИ-анализ содержания</div><p class="card-desc">Автоматически выделяйте ключевые темы, решения и задачи из любой записи.</p></div>
    <div class="feature-card"><div class="card-icon"><svg viewBox="0 0 24 24"><path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"/><path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"/></svg></div><div class="card-title">Именные записи</div><p class="card-desc">Называйте каждую запись при создании — находите нужное мгновенно.</p></div>
    <div class="feature-card"><div class="card-icon"><svg viewBox="0 0 24 24"><circle cx="11" cy="11" r="8"/><path d="m21 21-4.35-4.35"/></svg></div><div class="card-title">Умный поиск</div><p class="card-desc">Ищите по всем записям сразу. Найдите нужный момент за секунды.</p></div>
    <div class="feature-card"><div class="card-icon"><svg viewBox="0 0 24 24"><path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"/></svg></div><div class="card-title">Прослушивание записей</div><p class="card-desc">Воспроизводите сохранённые записи прямо в приложении с удобным плеером.</p></div>
    <div class="feature-card"><div class="card-icon"><svg viewBox="0 0 24 24"><rect x="3" y="11" width="18" height="11" rx="2" ry="2"/><path d="M7 11V7a5 5 0 0 1 10 0v4"/></svg></div><div class="card-title">Полная конфиденциальность</div><p class="card-desc">Записи хранятся только на вашем устройстве — ничего не уходит на сервер.</p></div>
  </div>
</section>

<section>
  <div class="section-header">
    <div class="section-label">Интерфейс</div>
    <h2 class="section-title">Простой в использовании</h2>
    <p class="section-sub">Нажмите одну кнопку — и начните запись</p>
  </div>
  <div class="recorder-demo">
    <div class="recorder-header">
      <span class="rec-title">Новая запись</span>
      <span class="rec-tag"><span class="rec-dot"></span>REC</span>
    </div>
    <div class="waveform-display" id="waveDisplay"></div>
    <div class="time-display">02:34</div>
    <div class="recorder-controls">
      <button class="ctrl-btn"><svg viewBox="0 0 24 24"><rect x="6" y="4" width="4" height="16" fill="currentColor" stroke="none"/><rect x="14" y="4" width="4" height="16" fill="currentColor" stroke="none"/></svg></button>
      <button class="ctrl-btn-main"><svg viewBox="0 0 24 24"><path d="M12 2a3 3 0 0 0-3 3v7a3 3 0 0 0 6 0V5a3 3 0 0 0-3-3z"/><path d="M19 10v2a7 7 0 0 1-14 0v-2M12 19v3M8 22h8"/></svg></button>
      <button class="ctrl-btn"><svg viewBox="0 0 24 24"><polygon points="5 3 19 12 5 21 5 3" fill="currentColor" stroke="none"/></svg></button>
    </div>
    <div class="transcript-box">
      <div class="transcript-label">Транскрипция</div>
      <div class="transcript-text" id="transcript">Сегодня на встрече обсуждали стратегию на третий квартал...<span class="cursor"></span></div>
    </div>
  </div>
</section>

<section>
  <div class="section-header">
    <div class="section-label">Процесс</div>
    <h2 class="section-title">Как это работает</h2>
  </div>
  <div class="steps">
    <div class="step"><div class="step-num">01</div><div class="step-title">Назови запись</div><p class="step-desc">Введите название — так легко найти нужную запись потом.</p></div>
    <div class="step"><div class="step-num">02</div><div class="step-title">Запись</div><p class="step-desc">Нажмите кнопку и начните говорить. Таймер отсчитывает время.</p></div>
    <div class="step"><div class="step-num">03</div><div class="step-title">Транскрибация</div><p class="step-desc">Выберите браузерный API или Yandex SpeechKit для перевода речи в текст.</p></div>
    <div class="step"><div class="step-num">04</div><div class="step-title">Прослушивание</div><p class="step-desc">Записи сохраняются. Воспроизводите в любое время с транскриптом.</p></div>
  </div>
</section>
</div>

<!-- ══════════ SIGNUP PAGE ══════════ -->
<div class="signup-page">
  <section>
    <div class="section-header">
      <div class="section-label">Регистрация</div>
      <h2 class="section-title">Создайте аккаунт SpeakMind</h2>
      <p class="section-sub">Всего пара полей — и вы готовы транскрибировать встречи и интервью.</p>
    </div>
    <div class="recorder-demo" style="max-width:520px;">
      <div class="recorder-header"><span class="rec-title">Новый аккаунт</span></div>
      <form class="signup-form">
        <div class="form-field"><label for="name">Логин</label><input id="name" type="text" placeholder="Введите логин" required></div>
        <div class="form-field"><label for="password">Пароль</label><input id="password" type="password" placeholder="Придумайте надёжный пароль" required></div>
        <div class="form-field"><label for="passwordRepeat">Повторите пароль</label><input id="passwordRepeat" type="password" placeholder="Повторите пароль" required></div>
        <button type="submit" class="btn-primary" style="width:100%;justify-content:center;display:inline-flex;align-items:center;gap:6px;">Зарегистрироваться</button>
        <p class="form-hint">Нажимая кнопку, вы соглашаетесь с условиями сервиса.</p>
        <button type="button" class="btn-secondary" id="backToHome" style="width:100%;margin-top:10px;">Вернуться на главную</button>
        <button type="button" class="btn-secondary js-open-login" style="width:100%;margin-top:10px;">Уже есть аккаунт? Войти</button>
      </form>
    </div>
  </section>
</div>

<!-- ══════════ LOGIN PAGE ══════════ -->
<div class="login-page">
  <section>
    <div class="section-header">
      <div class="section-label">Вход</div>
      <h2 class="section-title">Войдите в SpeakMind</h2>
      <p class="section-sub">Получайте доступ к вашим записям и транскриптам.</p>
    </div>
    <div class="recorder-demo" style="max-width:520px;">
      <div class="recorder-header"><span class="rec-title">Вход в аккаунт</span></div>
      <form class="signup-form login-form">
        <div class="form-field"><label for="loginEmail">Логин</label><input id="loginEmail" type="text" placeholder="Введите логин" required></div>
        <div class="form-field"><label for="loginPassword">Пароль</label><input id="loginPassword" type="password" placeholder="Введите пароль" required></div>
        <button type="submit" class="btn-primary" style="width:100%;justify-content:center;display:inline-flex;align-items:center;gap:6px;">Войти</button>
        <button type="button" class="btn-secondary" id="backToSignup" style="width:100%;margin-top:10px;">Назад к регистрации</button>
      </form>
    </div>
  </section>
</div>

<!-- ══════════ APP PAGE ══════════ -->
<div class="app-page">
  <div class="app-topbar">
    <button class="app-icon-btn" type="button" aria-label="Поиск" id="searchBtn">
      <svg viewBox="0 0 24 24"><circle cx="11" cy="11" r="7"></circle><line x1="21" y1="21" x2="16.5" y2="16.5"></line></svg>
    </button>
    <div class="app-title">SpeakMind</div>
    <button class="app-icon-btn" id="appLogoutBtn" type="button" aria-label="Выйти">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
        <path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"></path>
        <polyline points="16 17 21 12 16 7"></polyline>
        <line x1="21" y1="12" x2="9" y2="12"></line>
      </svg>
    </button>
  </div>

  <div class="app-tabs">
    <button type="button" class="app-tab active" data-tab="all">ВСЕ ЗАПИСИ</button>
    <button type="button" class="app-tab" data-tab="favorites">ИЗБРАННЫЕ</button>
    <button type="button" class="app-tab" data-tab="trash">КОРЗИНА</button>
  </div>

  <div class="folder-breadcrumb" id="folderBreadcrumb">
    <button class="folder-back-btn" id="folderBackBtn">
      <svg viewBox="0 0 24 24"><polyline points="15 18 9 12 15 6"/></svg>
      Назад
    </button>
    <span class="folder-current-name" id="folderCurrentName"></span>
  </div>

  <div class="records-list" id="allRecordsList"></div>
  <div class="records-list" id="trashRecordsList" style="display:none;"></div>

  <!-- Mic FAB -->
  <button class="app-fab-mic" type="button" id="fabMic" aria-label="Новая запись">
    <svg viewBox="0 0 24 24">
      <path d="M12 14c2 0 3-1.3 3-3V5c0-1.7-1.3-3-3-3S9 3.3 9 5v6c0 1.7 1 3 3 3z"></path>
      <path d="M19 11c0 3.9-3.1 7-7 7s-7-3.1-7-7"></path>
      <path d="M12 18v3"></path>
    </svg>
  </button>

  <!-- Trash FAB (appears when records are selected) -->
  <button class="app-fab-trash" type="button" id="fabTrash" aria-label="В корзину">
    <svg viewBox="0 0 24 24">
      <polyline points="3 6 5 6 21 6"/>
      <path d="M19 6l-1 14H6L5 6"/>
      <path d="M10 11v6M14 11v6"/>
      <path d="M9 6V4h6v2"/>
    </svg>
    <span class="fab-sel-badge" id="fabTrashBadge">0</span>
  </button>

  <div class="app-bottom-actions" aria-hidden="true">
    <button class="app-icon-btn" type="button" id="clearSelectionBtn" aria-label="Снять выделение" title="Снять выделение">
      <svg viewBox="0 0 24 24"><path d="M18 6L6 18M6 6l12 12"/></svg>
    </button>
    <button class="app-icon-btn" type="button" id="newFolderBtn" aria-label="Создать папку" title="Создать папку">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"/><line x1="12" y1="11" x2="12" y2="17"/><line x1="9" y1="14" x2="15" y2="14"/></svg>
    </button>
    <button class="app-icon-btn" type="button" id="settingsBtn" aria-label="Настройки" title="Настройки Yandex API">
      <svg viewBox="0 0 24 24"><circle cx="12" cy="12" r="3"/><path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1-2.83 2.83l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-4 0v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83-2.83l.06-.06A1.65 1.65 0 0 0 4.68 15a1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1 0-4h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 2.83-2.83l.06.06A1.65 1.65 0 0 0 9 4.68a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 4 0v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 2.83l-.06.06A1.65 1.65 0 0 0 19.4 9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 0 4h-.09a1.65 1.65 0 0 0-1.51 1z"/></svg>
    </button>
  </div>
</div>

<!-- ══════════ FOLDER PICKER MODAL ══════════ -->
<div class="modal-overlay" id="folderPickerModal">
  <div class="modal-box">
    <div class="modal-title">
      <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"/></svg>
      Переместить в папку
    </div>
    <div class="folder-picker-list" id="folderPickerList"></div>
    <div class="modal-actions">
      <button class="modal-btn secondary" id="folderPickerCancel">Отмена</button>
    </div>
  </div>
</div>

<!-- ══════════ RECORDING MODAL ══════════ -->
<div class="modal-overlay" id="recordModal">
  <div class="modal-box">
    <div class="modal-title">
      <svg viewBox="0 0 24 24"><path d="M12 2a3 3 0 0 0-3 3v7a3 3 0 0 0 6 0V5a3 3 0 0 0-3-3z"/><path d="M19 10v2a7 7 0 0 1-14 0v-2"/><line x1="12" y1="19" x2="12" y2="22"/></svg>
      Новая запись
    </div>

    <div id="modalStep1">
      <div class="modal-field">
        <label>Название записи</label>
        <input type="text" id="recNameInput" placeholder="Введите название" maxlength="60">
      </div>

      <div class="modal-field">
        <label>Метод транскрибации</label>
        <div class="transcription-methods">
          <label class="transcription-method">
            <input type="radio" name="transcMethod" value="browser" checked>
            <div class="transcription-method-info">
              <div class="transcription-method-name">Браузерный API (бесплатно)</div>
              <div class="transcription-method-desc">Web Speech API — работает офлайн в Chrome/Edge, без ключей</div>
            </div>
          </label>
          <label class="transcription-method">
            <input type="radio" name="transcMethod" value="yandex">
            <div class="transcription-method-info">
              <div class="transcription-method-name">Yandex SpeechKit</div>
              <div class="transcription-method-desc">Высокоточная распознавание через API Яндекса (требует ключ)</div>
            </div>
          </label>
          <label class="transcription-method">
            <input type="radio" name="transcMethod" value="none">
            <div class="transcription-method-info">
              <div class="transcription-method-name">Без транскрибации</div>
              <div class="transcription-method-desc">Только сохранить аудио</div>
            </div>
          </label>
        </div>
      </div>

      <div class="modal-field yandex-key-field" id="yandexKeyField">
        <label>Yandex API ключ (IAM или API-key)</label>
        <input type="password" id="yandexKeyInput" placeholder="t1.9euelZ...">
        <p class="form-hint" style="margin-top:6px;">Ключ используется только локально и не покидает браузер.</p>
      </div>

      <div class="modal-actions">
        <button class="modal-btn secondary" id="modalCancelBtn">Отмена</button>
        <button class="modal-btn primary" id="modalStartBtn">Начать запись</button>
      </div>
    </div>

    <div id="modalStep2" style="display:none;">
      <div class="modal-rec-status">
        <div class="modal-rec-timer" id="modalTimer">00:00</div>
        <div class="modal-waveform" id="modalWaveform"></div>
        <div class="modal-rec-label" id="modalRecLabel">Готов к записи</div>
      </div>

      <div class="live-transcript-box empty" id="liveTranscriptBox"></div>

      <div class="modal-transcribing" id="modalTranscribing">
        <div class="transcribing-spinner"></div>
        <span>Транскрибирую запись…</span>
      </div>

      <div class="modal-actions">
        <button class="modal-btn danger" id="modalDiscardBtn">Удалить</button>
        <button class="modal-btn primary" id="modalStopBtn">Остановить</button>
        <button class="modal-btn primary" id="modalSaveBtn" style="display:none;">Сохранить</button>
      </div>
    </div>
  </div>
</div>

<!-- ══════════ SETTINGS MODAL ══════════ -->
<div class="modal-overlay" id="settingsModal">
  <div class="modal-box">
    <div class="modal-title">
      <svg viewBox="0 0 24 24"><circle cx="12" cy="12" r="3"/><path d="M12 1v2M12 21v2M4.22 4.22l1.42 1.42M18.36 18.36l1.42 1.42M1 12h2M21 12h2M4.22 19.78l1.42-1.42M18.36 5.64l1.42-1.42"/></svg>
      Настройки
    </div>
    <div class="modal-field">
      <label>Yandex API ключ (по умолчанию)</label>
      <input type="password" id="settingsYandexKey" placeholder="Сохранить ключ для всех записей">
    </div>
    <p class="form-hint" style="margin-bottom:16px;">Ключ хранится только в localStorage вашего браузера.</p>
    <div class="modal-actions">
      <button class="modal-btn secondary" id="settingsCancelBtn">Отмена</button>
      <button class="modal-btn primary" id="settingsSaveBtn">Сохранить</button>
    </div>
  </div>
</div>

<footer>
  <div class="footer-logo">SpeakMind</div>
  <div class="footer-links">
    <a href="#">Продукт</a><a href="#">Блог</a><a href="#">Контакты</a><a href="#">Конфиденциальность</a>
  </div>
  <div style="font-size:12px;color:var(--text-muted);">© 2025 SpeakMind</div>
</footer>

<script>
// ── Demo waveform ─────────────────────────────────────────
const display = document.getElementById('waveDisplay');
const bars = [];
for (let i = 0; i < 48; i++) {
  const b = document.createElement('div');
  b.className = 'wd-bar'; b.style.height = '8px';
  display.appendChild(b); bars.push(b);
}
function animateBars() {
  bars.forEach(b => { const h = 8 + Math.random()*52; b.style.height = h+'px'; b.style.opacity = 0.2+(h/60)*0.8; });
  setTimeout(animateBars, 120);
}
animateBars();

// Typewriter
const phrases = [
  'Сегодня на встрече обсуждали стратегию на третий квартал. Команда предложила три новых направления...',
  'Иван: Нам нужно завершить интеграцию до конца месяца. Мария согласилась взять ответственность за API...',
  'Ключевые задачи: обновить документацию, провести ревью кода, назначить встречу с клиентом на пятницу...'
];
let pi = 0, ci = 0;
const transcriptEl = document.querySelector('#transcript');
function typeNext() {
  const phrase = phrases[pi];
  if (ci <= phrase.length) {
    transcriptEl.innerHTML = phrase.slice(0,ci)+'<span class="cursor"></span>';
    ci++; setTimeout(typeNext, 35);
  } else {
    setTimeout(() => { ci = 0; pi = (pi+1)%phrases.length; typeNext(); }, 3000);
  }
}
setTimeout(typeNext, 1000);

// ── Auth ──────────────────────────────────────────────────
const SESSION_KEY = 'speakmind_current_user_v1';
const USERS_KEY = 'speakmind_users_v1';
const YANDEX_KEY_STORE = 'speakmind_yandex_key_v1';


function isLoggedIn() { return !!localStorage.getItem(SESSION_KEY); }
function currentUser() { return localStorage.getItem(SESSION_KEY); }

function navigateTo(page) {
  if (page === 'app' && !isLoggedIn()) { showToast('Войдите в аккаунт.', 'error'); document.body.setAttribute('data-page','login'); window.scrollTo({top:0,behavior:'smooth'}); return; }
  document.body.setAttribute('data-page', page);
  window.scrollTo({top:0,behavior:'smooth'});
  if (page === 'app') {
    selectedIds.clear();
    updateSelectionUI();
    // Reset to "all" tab when entering app
    document.querySelectorAll('.app-tab').forEach(t=>t.classList.remove('active'));
    document.querySelector('.app-tab[data-tab="all"]').classList.add('active');
    const trashList = document.getElementById('trashRecordsList');
    const favList = document.getElementById('favRecordsList');
    allRecordsList.style.display = '';
    if (trashList) trashList.style.display = 'none';
    if (favList) favList.style.display = 'none';
    document.getElementById('fabMic').classList.remove('hidden');
    renderRecordsList();
  }
}

function showToast(msg, type='info') {
  const t = document.createElement('div');
  t.style.cssText = `position:fixed;bottom:28px;left:50%;transform:translateX(-50%);
    background:${type==='error'?'rgba(220,80,80,0.92)':'rgba(37,212,200,0.92)'};
    color:${type==='error'?'#fff':'#081c1f'};padding:12px 24px;border-radius:10px;
    font-size:14px;z-index:9999;backdrop-filter:blur(10px);font-family:'Onest',sans-serif;white-space:nowrap;`;
  t.textContent = msg;
  document.body.appendChild(t);
  setTimeout(() => t.remove(), 3500);
}

(function() { if (document.body.getAttribute('data-page')==='app'&&!isLoggedIn()) document.body.setAttribute('data-page','login'); })();

document.querySelectorAll('.js-open-signup').forEach(b=>b.addEventListener('click',()=>navigateTo('signup')));
document.querySelectorAll('.js-open-login').forEach(b=>b.addEventListener('click',()=>navigateTo('login')));
document.getElementById('backToHome')?.addEventListener('click',()=>navigateTo('home'));
document.getElementById('backToSignup')?.addEventListener('click',()=>navigateTo('signup'));

function loadUsers() { try { return JSON.parse(localStorage.getItem(USERS_KEY)||'{}'); } catch { return {}; } }
function saveUsers(u) { localStorage.setItem(USERS_KEY,JSON.stringify(u)); }
function bytesToHex(b) { return Array.from(b).map(x=>x.toString(16).padStart(2,'0')).join(''); }
async function sha256Hex(s) { const d=new TextEncoder().encode(s); const h=await crypto.subtle.digest('SHA-256',d); return bytesToHex(new Uint8Array(h)); }

document.querySelector('.signup-page .signup-form')?.addEventListener('submit', async e => {
  e.preventDefault();
  const login=(document.getElementById('name')?.value||'').trim();
  const pass1=document.getElementById('password')?.value||'';
  const pass2=document.getElementById('passwordRepeat')?.value||'';
  if (!login) return alert('Введите логин.');
  if (!pass1||pass1.length<6) return alert('Пароль должен быть не короче 6 символов.');
  if (pass1!==pass2) return alert('Пароли не совпадают.');
  const users=loadUsers();
  if (users[login]) return alert('Пользователь с таким логином уже существует.');
  const salt=bytesToHex(crypto.getRandomValues(new Uint8Array(16)));
  const hash=await sha256Hex(`${salt}:${pass1}`);
  users[login]={salt,hash}; saveUsers(users);
  localStorage.setItem(SESSION_KEY,login);
  alert('Регистрация прошла успешно!'); navigateTo('app');
});

document.querySelector('.login-page .login-form')?.addEventListener('submit', async e => {
  e.preventDefault();
  const login=(document.getElementById('loginEmail')?.value||'').trim();
  const pass=document.getElementById('loginPassword')?.value||'';
  if (!login) return alert('Введите логин.'); if (!pass) return alert('Введите пароль.');
  const users=loadUsers(); const user=users[login];
  if (!user) return alert('Неверный логин или пароль.');
  const hash=await sha256Hex(`${user.salt}:${pass}`);
  if (hash!==user.hash) return alert('Неверный логин или пароль.');
  localStorage.setItem(SESSION_KEY,login); navigateTo('app');
});

document.getElementById('appLogoutBtn')?.addEventListener('click', () => {
  if (!confirm('Вы уверены, что хотите выйти из аккаунта?')) return;
  localStorage.removeItem(SESSION_KEY);
  navigateTo('login');
});

// ── Records storage ───────────────────────────────────────
function recordsKey() { return `speakmind_records_${currentUser()}`; }
function trashKey() { return `speakmind_trash_${currentUser()}`; }

function loadRecords() {
  try { return JSON.parse(localStorage.getItem(recordsKey())||'[]'); } catch { return []; }
}
function saveRecords(recs) {
  try { localStorage.setItem(recordsKey(), JSON.stringify(recs)); }
  catch(e) { showToast('Мало памяти — удалите старые записи', 'error'); }
}
function loadTrash() {
  try { return JSON.parse(localStorage.getItem(trashKey())||'[]'); } catch { return []; }
}
function saveTrash(t) {
  try { localStorage.setItem(trashKey(), JSON.stringify(t)); }
  catch(e) { showToast('Мало памяти', 'error'); }
}

// ── Folders storage ───────────────────────────────────────
function foldersKey() { return `speakmind_folders_${currentUser()}`; }
function loadFolders() {
  try { return JSON.parse(localStorage.getItem(foldersKey())||'[]'); } catch { return []; }
}
function saveFolders(f) {
  try { localStorage.setItem(foldersKey(), JSON.stringify(f)); }
  catch(e) { showToast('Мало памяти', 'error'); }
}

let currentFolderId = null;

function updateFolderBreadcrumb() {
  const breadcrumb = document.getElementById('folderBreadcrumb');
  const nameEl = document.getElementById('folderCurrentName');
  if (currentFolderId) {
    const folder = loadFolders().find(f => f.id === currentFolderId);
    breadcrumb.classList.add('visible');
    nameEl.textContent = folder ? folder.name : '';
  } else {
    breadcrumb.classList.remove('visible');
  }
}

function openFolder(folderId) {
  currentFolderId = folderId;
  updateFolderBreadcrumb();
  renderRecordsList();
}

function goToRoot() {
  currentFolderId = null;
  updateFolderBreadcrumb();
  renderRecordsList();
}

function createFolderEntry(name) {
  const folders = loadFolders();
  const folder = { id: 'folder_' + Date.now() + '_' + Math.random().toString(36).slice(2,6), name, createdAt: Date.now() };
  folders.push(folder);
  saveFolders(folders);
  return folder;
}

function moveRecordToFolder(recordId, folderId) {
  const recs = loadRecords();
  const idx = recs.findIndex(r => r.id === recordId);
  if (idx === -1) return;
  recs[idx].folderId = folderId;
  saveRecords(recs);
  renderRecordsList();
  if (folderId) {
    const name = loadFolders().find(f => f.id === folderId)?.name || 'папку';
    showToast(`Перемещено в «${name}»`);
  } else {
    showToast('Убрано из папки');
  }
}

function openFolderPicker(recordId) {
  const folders = loadFolders();
  const recs = loadRecords();
  const rec = recs.find(r => r.id === recordId);
  const list = document.getElementById('folderPickerList');
  list.innerHTML = '';

  // Option to remove from folder
  if (rec && rec.folderId) {
    const rootItem = document.createElement('div');
    rootItem.className = 'folder-picker-item';
    rootItem.innerHTML = `<span class="pi-icon">📋</span><span class="pi-name">Все записи (убрать из папки)</span>`;
    rootItem.addEventListener('click', () => {
      moveRecordToFolder(recordId, null);
      document.getElementById('folderPickerModal').classList.remove('open');
    });
    list.appendChild(rootItem);
  }

  if (folders.length === 0) {
    const empty = document.createElement('div');
    empty.style.cssText = 'text-align:center;padding:16px 0;color:rgba(180,230,235,0.45);font-size:13px;';
    empty.textContent = 'Папок пока нет. Создайте первую!';
    list.appendChild(empty);
  }

  folders.forEach(folder => {
    const isCurrent = rec && rec.folderId === folder.id;
    const item = document.createElement('div');
    item.className = 'folder-picker-item' + (isCurrent ? ' current' : '');
    item.innerHTML = `<span class="pi-icon">📁</span><span class="pi-name">${escHtml(folder.name)}</span>${isCurrent ? '<span class="pi-check">✓</span>' : ''}`;
    if (!isCurrent) {
      item.addEventListener('click', () => {
        moveRecordToFolder(recordId, folder.id);
        document.getElementById('folderPickerModal').classList.remove('open');
      });
    }
    list.appendChild(item);
  });

  // New folder option
  const newItem = document.createElement('div');
  newItem.className = 'folder-picker-new';
  newItem.innerHTML = `<span style="font-size:18px;line-height:1;">+</span><span>Создать новую папку</span>`;
  newItem.addEventListener('click', () => {
    const name = prompt('Название папки:');
    if (!name || !name.trim()) return;
    const folder = createFolderEntry(name.trim());
    moveRecordToFolder(recordId, folder.id);
    document.getElementById('folderPickerModal').classList.remove('open');
  });
  list.appendChild(newItem);

  document.getElementById('folderPickerModal').classList.add('open');
}

function createFolderCard(folder, allRecs) {
  const count = allRecs.filter(r => r.folderId === folder.id).length;
  const card = document.createElement('div');
  card.className = 'folder-card';
  const countLabel = count === 0 ? 'Пусто' : count === 1 ? '1 запись' : count < 5 ? `${count} записи` : `${count} записей`;
  card.innerHTML = `
    <div class="folder-icon-emoji">📁</div>
    <div class="folder-info">
      <div class="folder-name">${escHtml(folder.name)}</div>
      <div class="folder-count">${countLabel}</div>
    </div>
    <button class="folder-delete-btn" title="Удалить папку">
      <svg viewBox="0 0 24 24" stroke-linecap="round"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
    </button>
  `;
  card.addEventListener('click', () => openFolder(folder.id));
  card.querySelector('.folder-delete-btn').addEventListener('click', e => {
    e.stopPropagation();
    if (!confirm(`Удалить папку «${folder.name}»?\nЗаписи останутся, но вернутся в общий список.`)) return;
    const allR = loadRecords();
    allR.forEach(r => { if (r.folderId === folder.id) r.folderId = null; });
    saveRecords(allR);
    saveFolders(loadFolders().filter(f => f.id !== folder.id));
    renderRecordsList();
    showToast(`Папка «${folder.name}» удалена`);
  });
  return card;
}

// ── Selection state ───────────────────────────────────────
let selectedIds = new Set();

function updateSelectionUI() {
  const fabTrash = document.getElementById('fabTrash');
  const fabMic = document.getElementById('fabMic');
  const badge = document.getElementById('fabTrashBadge');
  const count = selectedIds.size;
  // Only show trash FAB on non-trash tab
  const activeTab = document.querySelector('.app-tab.active')?.dataset.tab;
  if (count > 0 && activeTab !== 'trash') {
    fabTrash.classList.add('visible');
    fabMic.classList.add('hidden');
    badge.textContent = count;
  } else {
    fabTrash.classList.remove('visible');
    if (activeTab !== 'trash') fabMic.classList.remove('hidden');
  }
}

function toggleSelection(id, circle, card) {
  if (selectedIds.has(id)) {
    selectedIds.delete(id);
    circle.classList.remove('sel');
    card.classList.remove('card-sel');
  } else {
    selectedIds.add(id);
    circle.classList.add('sel');
    card.classList.add('card-sel');
  }
  updateSelectionUI();
}

// ── Trash management ──────────────────────────────────────
function moveToTrash(id) {
  const recs = loadRecords();
  const rec = recs.find(r => r.id === id);
  if (!rec) return;
  saveRecords(recs.filter(r => r.id !== id));
  const trash = loadTrash();
  trash.unshift({...rec, trashedAt: Date.now()});
  saveTrash(trash);
  selectedIds.delete(id);
  favSet.delete(id);
  updateSelectionUI();
  renderRecordsList();
  showToast(`«${rec.name}» перемещена в корзину`);
}

function moveSelectedToTrash() {
  if (selectedIds.size === 0) return;
  const recs = loadRecords();
  const trash = loadTrash();
  const idsToMove = new Set(selectedIds);
  const remaining = recs.filter(r => !idsToMove.has(r.id));
  const moved = recs.filter(r => idsToMove.has(r.id)).map(r => ({...r, trashedAt: Date.now()}));
  saveRecords(remaining);
  moved.forEach(r => { favSet.delete(r.id); });
  trash.unshift(...moved);
  saveTrash(trash);
  const count = idsToMove.size;
  selectedIds.clear();
  updateSelectionUI();
  renderRecordsList();
  showToast(`${count} ${count===1?'запись перемещена':'записей перемещено'} в корзину`);
}

function restoreFromTrash(id) {
  const trash = loadTrash();
  const rec = trash.find(r => r.id === id);
  if (!rec) return;
  saveTrash(trash.filter(r => r.id !== id));
  const { trashedAt, ...original } = rec;
  const recs = loadRecords();
  recs.unshift(original);
  saveRecords(recs);
  renderTrashTab();
  renderRecordsList();
  showToast(`«${rec.name}» восстановлена`);
}

function deleteFromTrashPermanently(id, name) {
  if (!confirm(`Удалить «${name}» навсегда? Это действие необратимо.`)) return;
  saveTrash(loadTrash().filter(r => r.id !== id));
  renderTrashTab();
  showToast('Запись удалена навсегда');
}

function renderTrashTab() {
  const trashList = document.getElementById('trashRecordsList');
  if (!trashList) return;
  const trash = loadTrash();
  trashList.innerHTML = '';
  if (trash.length === 0) {
    trashList.innerHTML = `<div class="trash-empty"><div class="trash-empty-icon">🗑️</div><p>Корзина пуста. Удалённые записи появятся здесь.</p></div>`;
    return;
  }
  trash.forEach(rec => {
    const card = document.createElement('div');
    card.className = 'record-card';
    card.style.opacity = '0.82';
    card.innerHTML = `
      <div class="record-top">
        <div class="record-left">
          <div class="record-title-row">
            <span class="record-title">${escHtml(rec.name || 'Без названия')}</span>
          </div>
          <div class="record-sub">
            <span>${formatDate(rec.ts)}</span>
            ${rec.duration ? `<span>${formatDuration(rec.duration)}</span>` : ''}
            <span style="color:rgba(244,123,123,0.75);font-size:11px;">Корзина: ${formatDate(rec.trashedAt)}</span>
          </div>
        </div>
        <div class="record-right">
          ${rec.duration ? `<span class="record-duration">${formatDuration(rec.duration)}</span>` : ''}
        </div>
      </div>
      <div class="trash-actions">
        <button class="trash-btn restore">↩ Восстановить</button>
        <button class="trash-btn del-perm">✕ Удалить навсегда</button>
      </div>
    `;
    card.querySelector('.restore').addEventListener('click', () => restoreFromTrash(rec.id));
    card.querySelector('.del-perm').addEventListener('click', () => deleteFromTrashPermanently(rec.id, rec.name || 'Без названия'));
    trashList.appendChild(card);
  });
}

// ── Render records list ───────────────────────────────────
const allRecordsList = document.getElementById('allRecordsList');
let favSet = new Set();
let activeAudio = null;
let activePlayBtn = null;
let activePlayerDiv = null;

function formatDuration(sec) {
  const m = Math.floor(sec/60).toString().padStart(2,'0');
  const s = Math.floor(sec%60).toString().padStart(2,'0');
  return `${m}:${s}`;
}

function formatDate(ts) {
  const d = new Date(ts);
  return d.toLocaleDateString('ru-RU',{day:'2-digit',month:'2-digit',year:'numeric'});
}

function renderRecordsList() {
  const recs = loadRecords();
  const favRecsList = document.getElementById('favRecordsList');
  allRecordsList.innerHTML = '';

  if (currentFolderId) {
    // ── Inside a folder: show only records belonging to it ──
    const folderRecs = recs.filter(r => r.folderId === currentFolderId);
    const sorted = [...folderRecs].sort((a, b) => (favSet.has(a.id)?0:1) - (favSet.has(b.id)?0:1));
    if (sorted.length === 0) {
      allRecordsList.innerHTML = `<div class="empty-state"><div class="empty-state-icon">📁</div><p>Папка пуста. Переместите записи сюда через кнопку 📁 на карточке.</p></div>`;
    } else {
      sorted.forEach((rec, idx) => {
        const card = createRecordCard(rec, idx);
        allRecordsList.appendChild(card);
        if (favSet.has(rec.id)) {
          const star = card.querySelector('.record-star');
          if (star) { star.textContent = '★'; star.classList.add('starred'); card.classList.add('favorited'); }
        }
      });
    }
    renderFavTab();
    return;
  }

  // ── Root view: show folders, then root records ──
  const folders = loadFolders();
  folders.forEach(folder => {
    allRecordsList.appendChild(createFolderCard(folder, recs));
  });

  const rootRecs = recs.filter(r => !r.folderId);
  if (recs.length === 0 && folders.length === 0) {
    allRecordsList.innerHTML = `<div class="empty-state"><div class="empty-state-icon">🎙️</div><p>Нет записей. Нажмите на кнопку микрофона, чтобы создать первую!</p></div>`;
    if (favRecsList) favRecsList.innerHTML = `<div class="favorites-empty"><div class="favorites-empty-icon">★</div><p>Добавляйте записи в избранное нажав ☆</p></div>`;
    return;
  }

  const sorted = [...rootRecs].sort((a, b) => (favSet.has(a.id)?0:1) - (favSet.has(b.id)?0:1));
  sorted.forEach((rec, idx) => {
    const card = createRecordCard(rec, idx);
    allRecordsList.appendChild(card);
    if (favSet.has(rec.id)) {
      const star = card.querySelector('.record-star');
      if (star) { star.textContent = '★'; star.classList.add('starred'); card.classList.add('favorited'); }
    }
  });

  renderFavTab();
}

function createRecordCard(rec, idx) {
  const card = document.createElement('div');
  card.className = 'record-card';
  card.dataset.id = rec.id;

  // Restore selection state
  if (selectedIds.has(rec.id)) card.classList.add('card-sel');

  const hasAudio = !!rec.audioBase64;
  const hasTranscript = !!(rec.transcript && rec.transcript.trim());

  card.innerHTML = `
    <div class="record-top">
      <div class="record-left">
        <div class="record-title-row">
          <button class="record-select-circle${selectedIds.has(rec.id) ? ' sel' : ''}" aria-label="Выбрать"></button>
          <button class="record-star" aria-label="В избранное">☆</button>
          <span class="record-title">${escHtml(rec.name || 'Без названия')}</span>
        </div>
        <div class="record-sub">
          <span>${formatDate(rec.ts)}</span>
          ${rec.duration ? `<span>${formatDuration(rec.duration)}</span>` : ''}
          ${rec.transcMethod ? `<span style="color:var(--accent);font-size:11px;">${methodLabel(rec.transcMethod)}</span>` : ''}
        </div>
      </div>
      <div class="record-right">
        ${rec.duration ? `<span class="record-duration">${formatDuration(rec.duration)}</span>` : ''}
        ${hasAudio ? `<button class="record-play" aria-label="Воспроизвести"><svg viewBox="0 0 24 24"><polygon points="5 3 19 12 5 21 5 3"/></svg></button>` : ''}
        <button class="record-folder-btn${rec.folderId ? ' in-folder' : ''}" aria-label="Папка" title="Переместить в папку">
          <svg viewBox="0 0 24 24"><path d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"/></svg>
        </button>
        <button class="record-delete" aria-label="В корзину" title="Переместить в корзину" style="width:34px;height:34px;border-radius:50%;background:rgba(220,80,80,0.08);border:1px solid rgba(220,80,80,0.18);display:inline-flex;align-items:center;justify-content:center;cursor:pointer;transition:all 0.2s;">
          <svg viewBox="0 0 24 24" width="14" height="14" fill="none" stroke="#f47b7b" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="3 6 5 6 21 6"/><path d="M19 6l-1 14H6L5 6"/><path d="M10 11v6M14 11v6"/><path d="M9 6V4h6v2"/></svg>
        </button>
      </div>
    </div>
    ${hasAudio ? `
    <div class="record-player">
      <input type="range" class="player-progress" min="0" max="100" value="0" step="0.1">
      <div class="player-times"><span class="player-cur">0:00</span><span class="player-total">0:00</span></div>
    </div>` : ''}
    ${hasTranscript ? `
    <div class="record-transcript">
      <div class="transcript-engine-badge">${methodLabel(rec.transcMethod)}</div>
      ${escHtml(rec.transcript)}
    </div>` : ''}
  `;

  // Selection circle
  const selectCircle = card.querySelector('.record-select-circle');
  selectCircle.addEventListener('click', (e) => {
    e.stopPropagation();
    toggleSelection(rec.id, selectCircle, card);
  });

  // Star
  card.querySelector('.record-star').addEventListener('click', () => toggleFav(rec.id, card));

  // Delete -> move to trash
  card.querySelector('.record-delete').addEventListener('click', () => moveToTrash(rec.id));

  // Folder button -> open picker
  card.querySelector('.record-folder-btn').addEventListener('click', (e) => {
    e.stopPropagation();
    openFolderPicker(rec.id);
  });

  // Play
  if (hasAudio) {
    const playBtn = card.querySelector('.record-play');
    const playerDiv = card.querySelector('.record-player');
    const progress = card.querySelector('.player-progress');
    const curTime = card.querySelector('.player-cur');
    const totalTime = card.querySelector('.player-total');
    const transcriptDiv = card.querySelector('.record-transcript');

    playBtn.addEventListener('click', () => {
      if (activeAudio && activeAudio !== null) {
        activeAudio.pause(); activeAudio.currentTime = 0;
        if (activePlayBtn) {
          activePlayBtn.classList.remove('playing');
          activePlayBtn.innerHTML = `<svg viewBox="0 0 24 24"><polygon points="5 3 19 12 5 21 5 3"/></svg>`;
        }
        if (activePlayerDiv) activePlayerDiv.classList.remove('visible');
        if (activeAudio === card._audio) {
          activeAudio = null; activePlayBtn = null; activePlayerDiv = null; return;
        }
      }

      if (!card._audio) {
        const audio = new Audio(rec.audioBase64);
        card._audio = audio;
        audio._transcriptDiv = transcriptDiv;

        audio.addEventListener('loadedmetadata', () => {
          totalTime.textContent = formatDuration(audio.duration);
          if (progress) progress.max = audio.duration;
        });
        audio.addEventListener('timeupdate', () => {
          curTime.textContent = formatDuration(audio.currentTime);
          if (progress) progress.value = audio.currentTime;
        });
        audio.addEventListener('ended', () => {
          playBtn.classList.remove('playing');
          playBtn.innerHTML = `<svg viewBox="0 0 24 24"><polygon points="5 3 19 12 5 21 5 3"/></svg>`;
          playerDiv.classList.remove('visible');
        });
      }

      if (progress) {
        progress.addEventListener('input', () => { card._audio.currentTime = parseFloat(progress.value); });
      }

      activeAudio = card._audio; activePlayBtn = playBtn; activePlayerDiv = playerDiv;
      playerDiv.classList.add('visible');
      playBtn.classList.add('playing');
      playBtn.innerHTML = `<svg viewBox="0 0 24 24" fill="#25d4c8" stroke="none"><rect x="6" y="4" width="4" height="16"/><rect x="14" y="4" width="4" height="16"/></svg>`;
      card._audio.play();
    });
  }

  return card;
}

function methodLabel(m) {
  if (m === 'browser') return 'Browser API';
  if (m === 'yandex') return 'Yandex SpeechKit';
  return '';
}

function escHtml(s) {
  return (s||'').replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;');
}

// ── Favorites ─────────────────────────────────────────────
function toggleFav(id, card) {
  const isFav = favSet.has(id);
  if (isFav) {
    favSet.delete(id);
  } else {
    favSet.add(id);
  }
  // Re-render the full list so sorting updates immediately
  renderRecordsList();
  // Animate the star on the newly rendered card
  if (!isFav) {
    const newCard = allRecordsList.querySelector(`.record-card[data-id="${id}"]`);
    const star = newCard?.querySelector('.record-star');
    if (star) star.animate([{transform:'scale(1)'},{transform:'scale(1.5)'},{transform:'scale(1)'}],{duration:280,easing:'ease-out'});
  }
}

function renderFavTab() {
  let favRecsList = document.getElementById('favRecordsList');
  if (!favRecsList) {
    favRecsList = document.createElement('div');
    favRecsList.className = 'records-list';
    favRecsList.id = 'favRecordsList';
    favRecsList.style.display = 'none';
    allRecordsList.after(favRecsList);
  }
  favRecsList.innerHTML = '';
  if (favSet.size === 0) {
    favRecsList.innerHTML = `<div class="favorites-empty"><div class="favorites-empty-icon">★</div><p>Нажмите на звёздочку рядом с записью, чтобы добавить её в избранное</p></div>`;
    return;
  }
  const recs = loadRecords().filter(r => favSet.has(r.id));
  recs.forEach(rec => {
    const card = createRecordCard(rec);
    const star = card.querySelector('.record-star');
    if (star) { star.textContent='★'; star.classList.add('starred'); card.classList.add('favorited'); }
    favRecsList.appendChild(card);
  });
}

// ── Tab switching ─────────────────────────────────────────
document.querySelectorAll('.app-tab[data-tab]').forEach(tab => {
  tab.addEventListener('click', () => {
    document.querySelectorAll('.app-tab').forEach(t=>t.classList.remove('active'));
    tab.classList.add('active');
    const favRecsList = document.getElementById('favRecordsList');
    const trashList = document.getElementById('trashRecordsList');
    const fabMic = document.getElementById('fabMic');

    allRecordsList.style.display = 'none';
    if (favRecsList) favRecsList.style.display = 'none';
    if (trashList) trashList.style.display = 'none';

    if (tab.dataset.tab === 'all') {
      allRecordsList.style.display = '';
      currentFolderId = null;
      updateFolderBreadcrumb();
      renderRecordsList();
      updateSelectionUI();
    } else if (tab.dataset.tab === 'favorites') {
      if (favRecsList) { favRecsList.style.display = ''; renderFavTab(); }
      updateSelectionUI();
    } else if (tab.dataset.tab === 'trash') {
      if (trashList) { trashList.style.display = ''; renderTrashTab(); }
      // Hide both FABs on trash tab
      fabMic.classList.add('hidden');
      document.getElementById('fabTrash').classList.remove('visible');
    }
  });
});

// ── FAB: Trash ────────────────────────────────────────────
document.getElementById('fabTrash')?.addEventListener('click', () => {
  moveSelectedToTrash();
});

// ── Folder back button ────────────────────────────────────
document.getElementById('folderBackBtn')?.addEventListener('click', () => goToRoot());

// ── New folder button ─────────────────────────────────────
document.getElementById('newFolderBtn')?.addEventListener('click', () => {
  const name = prompt('Название папки:');
  if (!name || !name.trim()) return;
  createFolderEntry(name.trim());
  renderRecordsList();
  showToast(`Папка «${name.trim()}» создана`);
});

// ── Folder picker cancel ──────────────────────────────────
document.getElementById('folderPickerCancel')?.addEventListener('click', () => {
  document.getElementById('folderPickerModal').classList.remove('open');
});
document.getElementById('folderPickerModal')?.addEventListener('click', (e) => {
  if (e.target === e.currentTarget) e.currentTarget.classList.remove('open');
});

// ── Clear selection ───────────────────────────────────────
document.getElementById('clearSelectionBtn')?.addEventListener('click', () => {
  if (selectedIds.size === 0) return;
  selectedIds.clear();
  // Remove visual selection from all cards
  document.querySelectorAll('.record-card.card-sel').forEach(card => {
    card.classList.remove('card-sel');
    const circle = card.querySelector('.record-select-circle');
    if (circle) circle.classList.remove('sel');
  });
  updateSelectionUI();
});

// ── Recording modal ───────────────────────────────────────
const recordModal = document.getElementById('recordModal');
const modalStep1 = document.getElementById('modalStep1');
const modalStep2 = document.getElementById('modalStep2');
const modalTimer = document.getElementById('modalTimer');
const modalRecLabel = document.getElementById('modalRecLabel');
const modalTranscribing = document.getElementById('modalTranscribing');
const modalWaveformEl = document.getElementById('modalWaveform');
const yandexKeyField = document.getElementById('yandexKeyField');
const yandexKeyInput = document.getElementById('yandexKeyInput');

const mwBars = [];
for (let i=0; i<18; i++) {
  const b=document.createElement('div'); b.className='modal-wave-bar';
  modalWaveformEl.appendChild(b); mwBars.push(b);
}

document.querySelectorAll('input[name="transcMethod"]').forEach(r => {
  r.addEventListener('change', () => {
    yandexKeyField.classList.toggle('visible', r.value==='yandex' && r.checked);
  });
});

document.getElementById('fabMic')?.addEventListener('click', () => {
  const savedKey = localStorage.getItem(YANDEX_KEY_STORE)||'';
  if (savedKey) yandexKeyInput.value = savedKey;
  document.getElementById('recNameInput').value = '';
  modalStep1.style.display=''; modalStep2.style.display='none';
  recordModal.classList.add('open');
});

document.getElementById('modalCancelBtn')?.addEventListener('click', () => {
  recordModal.classList.remove('open');
});

let mediaRecorder = null;
let audioChunks = [];
let recInterval = null;
let recSeconds = 0;
let browserRecognition = null;
let liveTranscript = '';
let mwAnimFrame = null;
let analyserNode = null;
let audioCtx = null;

function updateModalTimer(s) {
  const m=Math.floor(s/60).toString().padStart(2,'0');
  const sec=(s%60).toString().padStart(2,'0');
  modalTimer.textContent=`${m}:${sec}`;
}

function animateModalWave(active, stream) {
  if (!active) {
    mwBars.forEach(b=>{b.style.height='8px';b.classList.remove('active');});
    if (mwAnimFrame) cancelAnimationFrame(mwAnimFrame);
    if (audioCtx) { audioCtx.close(); audioCtx=null; analyserNode=null; }
    return;
  }
  if (stream) {
    try {
      audioCtx = new AudioContext();
      const src = audioCtx.createMediaStreamSource(stream);
      analyserNode = audioCtx.createAnalyser();
      analyserNode.fftSize = 64;
      src.connect(analyserNode);
    } catch(e) {}
  }
  function draw() {
    if (analyserNode) {
      const data = new Uint8Array(analyserNode.frequencyBinCount);
      analyserNode.getByteFrequencyData(data);
      mwBars.forEach((b,i)=>{
        const v = data[i % data.length];
        const h = 4 + (v/255)*28;
        b.style.height=h+'px'; b.classList.add('active');
      });
    } else {
      mwBars.forEach(b=>{ const h=4+Math.random()*24; b.style.height=h+'px'; b.classList.add('active'); });
    }
    mwAnimFrame = requestAnimationFrame(draw);
  }
  draw();
}

document.getElementById('modalStartBtn')?.addEventListener('click', async () => {
  const name = document.getElementById('recNameInput').value.trim() || 'Запись';
  const method = document.querySelector('input[name="transcMethod"]:checked')?.value || 'none';
  let yandexKey = (method==='yandex') ? (yandexKeyInput.value.trim() || localStorage.getItem(YANDEX_KEY_STORE)||'') : '';

  if (method==='yandex' && !yandexKey) {
    alert('Введите Yandex API ключ для использования SpeechKit.'); return;
  }

  try {
    const stream = await navigator.mediaDevices.getUserMedia({audio:true});
    modalStep1.style.display='none'; modalStep2.style.display='';
    modalRecLabel.textContent='Запись…'; modalRecLabel.className='modal-rec-label recording';
    document.getElementById('modalStopBtn').style.display='';
    document.getElementById('modalSaveBtn').style.display='none';

    const mimeTypes = ['audio/webm;codecs=opus','audio/webm','audio/ogg;codecs=opus','audio/mp4',''];
    let mimeType = '';
    for (const mt of mimeTypes) {
      if (!mt || MediaRecorder.isTypeSupported(mt)) { mimeType=mt; break; }
    }
    mediaRecorder = new MediaRecorder(stream, mimeType ? {mimeType} : {});
    audioChunks = []; liveTranscript = ''; recSeconds = 0;
    const liveBox = document.getElementById('liveTranscriptBox');
    if (liveBox) { liveBox.innerHTML = ''; liveBox.classList.add('empty'); }

    mediaRecorder.ondataavailable = e => { if (e.data.size>0) audioChunks.push(e.data); };
    mediaRecorder.start(250);

    recInterval = setInterval(() => { recSeconds++; updateModalTimer(recSeconds); }, 1000);
    animateModalWave(true, stream);

    if (method === 'browser') {
      const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
      if (SpeechRecognition) {
        browserRecognition = new SpeechRecognition();
        browserRecognition.lang = 'ru-RU';
        browserRecognition.continuous = true;
        browserRecognition.interimResults = true;
        browserRecognition.onresult = e => {
          let newFinal = '', interim = '';
          for (let i = e.resultIndex; i < e.results.length; i++) {
            if (e.results[i].isFinal) newFinal += e.results[i][0].transcript + ' ';
            else interim += e.results[i][0].transcript;
          }
          liveTranscript += newFinal;
          // Update the live transcript box
          const box = document.getElementById('liveTranscriptBox');
          if (box) {
            box.classList.remove('empty');
            box.innerHTML =
              (liveTranscript ? `<span class="live-transcript-final">${escHtml(liveTranscript)}</span>` : '') +
              (interim ? `<span class="live-transcript-interim">${escHtml(interim)}</span>` : '') +
              `<span class="live-cursor"></span>`;
            box.scrollTop = box.scrollHeight;
          }
        };
        browserRecognition.onerror = () => {};
        browserRecognition.onend = () => {
          // Restart if still recording (recognition can cut off mid-session)
          if (mediaRecorder && mediaRecorder.state === 'recording') {
            try { browserRecognition.start(); } catch(e) {}
          }
        };
        browserRecognition.start();
      }
    } else {
      // For Yandex / none — show a placeholder in the box
      const box = document.getElementById('liveTranscriptBox');
      if (box && method === 'yandex') {
        box.classList.remove('empty');
        box.innerHTML = `<span style="color:var(--text-muted);font-size:13px;">Транскрибация Yandex SpeechKit будет доступна после остановки записи…</span>`;
      }
    }

    mediaRecorder._recName = name;
    mediaRecorder._method = method;
    mediaRecorder._yandexKey = yandexKey;
    mediaRecorder._stream = stream;

  } catch(err) {
    alert('Не удалось получить доступ к микрофону: '+err.message);
  }
});

document.getElementById('modalStopBtn')?.addEventListener('click', async () => {
  if (!mediaRecorder || mediaRecorder.state === 'inactive') return;
  clearInterval(recInterval);
  animateModalWave(false);
  if (browserRecognition) { try { browserRecognition.stop(); } catch(e){} browserRecognition=null; }

  const name = mediaRecorder._recName;
  const method = mediaRecorder._method;
  const yandexKey = mediaRecorder._yandexKey;
  const stream = mediaRecorder._stream;
  const duration = recSeconds;

  modalRecLabel.textContent = 'Обработка…';
  modalRecLabel.className = 'modal-rec-label';
  document.getElementById('modalStopBtn').style.display='none';

  await new Promise(resolve => {
    mediaRecorder.onstop = resolve;
    mediaRecorder.stop();
  });
  stream.getTracks().forEach(t=>t.stop());

  const blob = new Blob(audioChunks, {type: mediaRecorder.mimeType || 'audio/webm'});
  const audioBase64 = await blobToBase64(blob);

  let transcript = '';
  if (method === 'browser') {
    transcript = liveTranscript.trim();
    saveAndClose(name, method, duration, audioBase64, transcript);
  } else if (method === 'yandex') {
    modalTranscribing.classList.add('visible');
    transcript = await transcribeYandex(blob, yandexKey);
    modalTranscribing.classList.remove('visible');
    saveAndClose(name, method, duration, audioBase64, transcript);
  } else {
    saveAndClose(name, method, duration, audioBase64, '');
  }
});

function saveAndClose(name, method, duration, audioBase64, transcript) {
  const rec = {
    id: 'rec_'+(Date.now())+'_'+Math.random().toString(36).slice(2,8),
    name, method: method, transcMethod: method,
    duration, ts: Date.now(),
    audioBase64, transcript
  };
  const recs = loadRecords();
  recs.unshift(rec);
  saveRecords(recs);
  renderRecordsList();
  recordModal.classList.remove('open');
  modalStep1.style.display=''; modalStep2.style.display='none';
  showToast('Запись «'+name+'» сохранена ✓');
}

document.getElementById('modalDiscardBtn')?.addEventListener('click', () => {
  if (mediaRecorder && mediaRecorder.state !== 'inactive') {
    mediaRecorder.stop();
    mediaRecorder._stream?.getTracks().forEach(t=>t.stop());
  }
  clearInterval(recInterval);
  animateModalWave(false);
  if (browserRecognition) { try { browserRecognition.stop(); } catch(e){} browserRecognition=null; }
  mediaRecorder = null; audioChunks = [];
  recordModal.classList.remove('open');
  modalStep1.style.display=''; modalStep2.style.display='none';
});

function blobToBase64(blob) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onloadend = () => resolve(reader.result);
    reader.onerror = reject;
    reader.readAsDataURL(blob);
  });
}

// ── Yandex SpeechKit transcription ───────────────────────
async function transcribeYandex(blob, apiKey) {
  try {
    const arrayBuffer = await blob.arrayBuffer();
    let pcmBase64 = '';
    try {
      const audioCtxTmp = new AudioContext({sampleRate: 16000});
      const decoded = await audioCtxTmp.decodeAudioData(arrayBuffer.slice(0));
      await audioCtxTmp.close();
      const channel = decoded.getChannelData(0);
      const pcm = new Int16Array(channel.length);
      for (let i=0; i<channel.length; i++) {
        let s = Math.max(-1, Math.min(1, channel[i]));
        pcm[i] = s < 0 ? s*0x8000 : s*0x7FFF;
      }
      pcmBase64 = arrayBufferToBase64(pcm.buffer);
    } catch(e) {
      pcmBase64 = await blobToBase64RAW(blob);
    }

    const resp = await fetch('https://stt.api.cloud.yandex.net/speech/v1/stt:recognize?lang=ru-RU&format=lpcm&sampleRateHertz=16000', {
      method: 'POST',
      headers: {
        'Authorization': `Api-Key ${apiKey}`,
        'Content-Type': 'application/octet-stream'
      },
      body: base64ToArrayBuffer(pcmBase64)
    });

    if (!resp.ok) {
      const err = await resp.json().catch(()=>({}));
      throw new Error(err.message || `HTTP ${resp.status}`);
    }
    const data = await resp.json();
    return data.result || '';
  } catch(e) {
    showToast('Ошибка Yandex SpeechKit: '+e.message, 'error');
    return '';
  }
}

function arrayBufferToBase64(buffer) {
  const bytes = new Uint8Array(buffer);
  let binary = '';
  for (let i=0; i<bytes.byteLength; i++) binary += String.fromCharCode(bytes[i]);
  return btoa(binary);
}

function base64ToArrayBuffer(b64) {
  const bin = atob(b64);
  const bytes = new Uint8Array(bin.length);
  for (let i=0; i<bin.length; i++) bytes[i]=bin.charCodeAt(i);
  return bytes.buffer;
}

function blobToBase64RAW(blob) {
  return new Promise((res,rej)=>{
    const r=new FileReader();
    r.onloadend=()=>{ const b64=r.result.split(',')[1]; res(b64); };
    r.onerror=rej;
    r.readAsDataURL(blob);
  });
}

// ── Settings modal ────────────────────────────────────────
document.getElementById('settingsBtn')?.addEventListener('click', () => {
  document.getElementById('settingsYandexKey').value = localStorage.getItem(YANDEX_KEY_STORE)||'';
  document.getElementById('settingsModal').classList.add('open');
});
document.getElementById('settingsCancelBtn')?.addEventListener('click', () => {
  document.getElementById('settingsModal').classList.remove('open');
});
document.getElementById('settingsSaveBtn')?.addEventListener('click', () => {
  const key = document.getElementById('settingsYandexKey').value.trim();
  if (key) localStorage.setItem(YANDEX_KEY_STORE, key);
  else localStorage.removeItem(YANDEX_KEY_STORE);
  document.getElementById('settingsModal').classList.remove('open');
  showToast('Настройки сохранены');
});

document.getElementById('recordModal')?.addEventListener('click', e => {
  if (e.target === document.getElementById('recordModal')) {
    if (!mediaRecorder || mediaRecorder.state === 'inactive') {
      recordModal.classList.remove('open');
      modalStep1.style.display=''; modalStep2.style.display='none';
    }
  }
});
document.getElementById('settingsModal')?.addEventListener('click', e => {
  if (e.target === document.getElementById('settingsModal')) document.getElementById('settingsModal').classList.remove('open');
});
</script>
</body>
</html>
