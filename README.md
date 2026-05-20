<!--Prueba para tener un tamagotchi básico -->
[tamagotchi.html](https://github.com/user-attachments/files/28061166/tamagotchi.html)
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Frijolito — tu compañero digital</title>
<link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&family=Nunito:wght@400;700;900&display=swap" rel="stylesheet">
<style>
  :root {
    --cream: #FFF8E7;
    --peach: #FFD9B0;
    --coral: #FF7F5C;
    --brown: #5C3D2E;
    --green: #7BC67E;
    --green-dark: #3D8B40;
    --sky: #B8E4FF;
    --yellow: #FFE566;
    --red: #FF4757;
    --gray: #C0B8A8;
    --shadow: rgba(92,61,46,0.18);
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    background: var(--cream);
    font-family: 'Nunito', sans-serif;
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;
  }
  /* Polka dot background */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image: radial-gradient(circle, var(--peach) 2px, transparent 2px);
    background-size: 32px 32px;
    opacity: 0.5;
    pointer-events: none;
    z-index: 0;
  }

  .device {
    position: relative;
    z-index: 1;
    background: var(--brown);
    border-radius: 48px 48px 60px 60px;
    width: 280px;
    padding: 18px 18px 28px;
    box-shadow: 0 12px 40px rgba(92,61,46,0.35), inset 0 2px 0 rgba(255,255,255,0.1);
  }
  .device-top {
    display: flex;
    justify-content: center;
    gap: 8px;
    margin-bottom: 12px;
  }
  .led {
    width: 8px; height: 8px;
    border-radius: 50%;
    background: var(--coral);
    box-shadow: 0 0 6px var(--coral);
  }
  .led:nth-child(2) { background: var(--yellow); box-shadow: 0 0 6px var(--yellow); }
  .led:nth-child(3) { background: var(--green); box-shadow: 0 0 6px var(--green); }

  .screen-shell {
    background: #3a2519;
    border-radius: 20px;
    padding: 6px;
    box-shadow: inset 0 4px 12px rgba(0,0,0,0.5);
  }
  .screen {
    background: #d4e8a0;
    border-radius: 14px;
    height: 200px;
    position: relative;
    overflow: hidden;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: flex-end;
  }
  /* scanlines */
  .screen::after {
    content: '';
    position: absolute;
    inset: 0;
    background: repeating-linear-gradient(
      0deg,
      transparent,
      transparent 3px,
      rgba(0,0,0,0.04) 3px,
      rgba(0,0,0,0.04) 4px
    );
    pointer-events: none;
    border-radius: 14px;
  }

  /* Sky & ground */
  .bg-sky {
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 120px;
    background: linear-gradient(180deg, #c8eaff 0%, #d4e8a0 100%);
  }
  .bg-ground {
    position: absolute;
    bottom: 0; left: 0; right: 0;
    height: 36px;
    background: #8db860;
    border-radius: 0 0 14px 14px;
  }
  .bg-ground::before {
    content: '';
    position: absolute;
    top: -6px; left: 0; right: 0;
    height: 12px;
    background: #a0cc70;
    border-radius: 50% 50% 0 0 / 8px 8px 0 0;
  }

  /* Sun */
  .sun {
    position: absolute;
    top: 14px; right: 22px;
    width: 28px; height: 28px;
    background: #FFE566;
    border-radius: 50%;
    box-shadow: 0 0 12px #FFE566;
  }

  /* Clouds */
  .cloud {
    position: absolute;
    background: white;
    border-radius: 20px;
    opacity: 0.85;
  }
  .cloud1 { top: 18px; left: 12px; width: 44px; height: 16px; }
  .cloud1::before { content: ''; position: absolute; top: -8px; left: 8px; width: 22px; height: 22px; background: white; border-radius: 50%; }
  .cloud1::after  { content: ''; position: absolute; top: -5px; left: 20px; width: 16px; height: 16px; background: white; border-radius: 50%; }
  .cloud2 { top: 30px; left: 80px; width: 36px; height: 12px; }
  .cloud2::before { content: ''; position: absolute; top: -7px; left: 6px; width: 18px; height: 18px; background: white; border-radius: 50%; }

  /* Creature */
  .creature-wrap {
    position: absolute;
    bottom: 28px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    flex-direction: column;
    align-items: center;
    transition: all 0.3s ease;
  }
  .creature-wrap.bouncing { animation: bounce 0.5s ease infinite alternate; }
  .creature-wrap.fainting { animation: faint 1s ease forwards; }

  @keyframes bounce {
    from { transform: translateX(-50%) translateY(0); }
    to   { transform: translateX(-50%) translateY(-6px); }
  }
  @keyframes faint {
    0%   { transform: translateX(-50%) rotate(0deg); opacity: 1; }
    50%  { transform: translateX(-50%) rotate(20deg); opacity: 0.7; }
    100% { transform: translateX(-50%) rotate(90deg) translateY(10px); opacity: 0.5; }
  }
  @keyframes fed-anim {
    0%   { transform: translateX(-50%) scale(1); }
    30%  { transform: translateX(-50%) scale(1.25) rotate(-5deg); }
    60%  { transform: translateX(-50%) scale(0.95) rotate(3deg); }
    100% { transform: translateX(-50%) scale(1); }
  }
  .creature-wrap.just-fed { animation: fed-anim 0.6s ease; }

  /* SVG creature */
  .creature-svg { width: 60px; height: 60px; }

  /* Status bar */
  .status-bar {
    position: absolute;
    top: 8px; left: 8px; right: 8px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    z-index: 2;
  }
  .hunger-bar-wrap {
    display: flex;
    align-items: center;
    gap: 3px;
  }
  .hunger-icon { font-size: 9px; }
  .hunger-pips { display: flex; gap: 2px; }
  .pip {
    width: 7px; height: 7px;
    border-radius: 2px;
    background: #a0cc70;
    border: 1px solid #6a9940;
    transition: background 0.3s;
  }
  .pip.filled { background: #ff7043; border-color: #bf360c; }

  /* Message bubble */
  .bubble {
    position: absolute;
    top: 32px;
    left: 50%;
    transform: translateX(-50%);
    background: white;
    border: 2px solid var(--brown);
    border-radius: 10px;
    padding: 4px 8px;
    font-size: 7px;
    font-family: 'Press Start 2P', monospace;
    color: var(--brown);
    white-space: nowrap;
    opacity: 0;
    pointer-events: none;
    transition: opacity 0.3s;
    z-index: 3;
  }
  .bubble::after {
    content: '';
    position: absolute;
    bottom: -7px;
    left: 50%;
    transform: translateX(-50%);
    border: 4px solid transparent;
    border-top-color: var(--brown);
  }
  .bubble.visible { opacity: 1; }

  /* Zzz when fainted */
  .zzz {
    position: absolute;
    top: 20px; right: 30px;
    font-family: 'Press Start 2P', monospace;
    font-size: 8px;
    color: #7599d4;
    opacity: 0;
    pointer-events: none;
    animation: zzz-float 2s ease-in-out infinite;
  }
  .zzz.visible { opacity: 1; }
  @keyframes zzz-float {
    0%   { transform: translateY(0) scale(0.8); opacity: 0.3; }
    50%  { transform: translateY(-8px) scale(1); opacity: 1; }
    100% { transform: translateY(-16px) scale(1.1); opacity: 0; }
  }

  /* Hearts when fed */
  .hearts {
    position: absolute;
    top: 20px; left: 50%; transform: translateX(-50%);
    font-size: 12px;
    opacity: 0;
    pointer-events: none;
    transition: opacity 0.3s;
    animation: hearts-float 1s ease forwards;
  }
  .hearts.visible { opacity: 1; }
  @keyframes hearts-float {
    0%   { transform: translateX(-50%) translateY(0); opacity: 1; }
    100% { transform: translateX(-50%) translateY(-30px); opacity: 0; }
  }

  /* Controls */
  .controls {
    margin-top: 16px;
    display: flex;
    flex-direction: column;
    gap: 10px;
    align-items: center;
  }
  .name-display {
    font-family: 'Press Start 2P', monospace;
    font-size: 8px;
    color: var(--peach);
    letter-spacing: 1px;
    text-align: center;
  }
  .btn-row { display: flex; gap: 12px; justify-content: center; }
  .btn {
    background: var(--coral);
    color: white;
    border: none;
    border-radius: 24px;
    font-family: 'Nunito', sans-serif;
    font-weight: 900;
    font-size: 13px;
    padding: 10px 18px;
    cursor: pointer;
    box-shadow: 0 4px 0 #c05030, 0 6px 12px rgba(0,0,0,0.2);
    transition: transform 0.1s, box-shadow 0.1s;
    letter-spacing: 0.3px;
  }
  .btn:active { transform: translateY(3px); box-shadow: 0 1px 0 #c05030; }
  .btn.green { background: var(--green); box-shadow: 0 4px 0 var(--green-dark); }
  .btn.green:active { box-shadow: 0 1px 0 var(--green-dark); }
  .btn:disabled { opacity: 0.5; cursor: not-allowed; transform: none; }

  /* Name input overlay */
  .name-overlay {
    position: fixed;
    inset: 0;
    background: rgba(92,61,46,0.7);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 100;
  }
  .name-card {
    background: var(--cream);
    border-radius: 24px;
    padding: 28px 24px;
    text-align: center;
    box-shadow: 0 16px 48px rgba(0,0,0,0.3);
    width: 260px;
  }
  .name-card h2 {
    font-family: 'Press Start 2P', monospace;
    font-size: 10px;
    color: var(--brown);
    margin-bottom: 8px;
    line-height: 1.6;
  }
  .name-card p {
    font-size: 13px;
    color: #8a6a5a;
    margin-bottom: 16px;
    line-height: 1.5;
  }
  .name-input {
    width: 100%;
    border: 2px solid var(--brown);
    border-radius: 12px;
    padding: 10px 14px;
    font-family: 'Nunito', sans-serif;
    font-size: 15px;
    font-weight: 700;
    color: var(--brown);
    background: white;
    margin-bottom: 14px;
    outline: none;
    text-align: center;
  }
  .name-input:focus { border-color: var(--coral); }

  /* Stats panel */
  .stats {
    background: rgba(255,255,255,0.12);
    border-radius: 12px;
    padding: 8px 14px;
    font-size: 11px;
    color: var(--peach);
    line-height: 1.8;
    width: 100%;
    text-align: center;
  }
  .stats span { color: white; font-weight: 700; }
</style>
</head>
<body>

<!-- Name overlay -->
<div class="name-overlay" id="nameOverlay">
  <div class="name-card">
    <h2>¡Hola!<br>Name your<br>creature</h2>
    <p>Choose a name for your new digital friend 🌱</p>
    <input class="name-input" id="nameInput" maxlength="12" placeholder="e.g. Frijolito" />
    <button class="btn" style="width:100%" onclick="saveName()">Let's go! →</button>
  </div>
</div>

<!-- Device -->
<div class="device">
  <div class="device-top">
    <div class="led"></div>
    <div class="led"></div>
    <div class="led"></div>
  </div>

  <div class="screen-shell">
    <div class="screen" id="screen">
      <div class="bg-sky"></div>
      <div class="sun"></div>
      <div class="cloud cloud1"></div>
      <div class="cloud cloud2"></div>
      <div class="bg-ground"></div>

      <!-- Status bar -->
      <div class="status-bar">
        <div class="hunger-bar-wrap">
          <span class="hunger-icon">🍎</span>
          <div class="hunger-pips" id="hungerPips">
            <div class="pip" id="p0"></div>
            <div class="pip" id="p1"></div>
            <div class="pip" id="p2"></div>
            <div class="pip" id="p3"></div>
            <div class="pip" id="p4"></div>
          </div>
        </div>
        <div id="dayDisplay" style="font-family:'Press Start 2P',monospace;font-size:6px;color:#3a5a1a;">DAY 1</div>
      </div>

      <!-- Bubble -->
      <div class="bubble" id="bubble">Hungry!</div>

      <!-- Zzz -->
      <div class="zzz" id="zzz">z z z</div>

      <!-- Creature -->
      <div class="creature-wrap" id="creature">
        <svg class="creature-svg" viewBox="0 0 60 60" id="creatureSVG">
          <!-- Body -->
          <ellipse cx="30" cy="36" rx="20" ry="18" fill="#FF9E7A"/>
          <!-- Belly -->
          <ellipse cx="30" cy="40" rx="12" ry="10" fill="#FFCBA8"/>
          <!-- Head -->
          <circle cx="30" cy="20" r="16" fill="#FF9E7A"/>
          <!-- Ears -->
          <ellipse cx="16" cy="10" rx="6" ry="8" fill="#FF9E7A"/>
          <ellipse cx="44" cy="10" rx="6" ry="8" fill="#FF9E7A"/>
          <ellipse cx="16" cy="10" rx="3.5" ry="5" fill="#FFD0B8"/>
          <ellipse cx="44" cy="10" rx="3.5" ry="5" fill="#FFD0B8"/>
          <!-- Eyes (normal) -->
          <g id="eyesNormal">
            <circle cx="23" cy="19" r="4.5" fill="white"/>
            <circle cx="37" cy="19" r="4.5" fill="white"/>
            <circle cx="24" cy="19" r="2.5" fill="#3D2010"/>
            <circle cx="38" cy="19" r="2.5" fill="#3D2010"/>
            <circle cx="25" cy="18" r="1" fill="white"/>
            <circle cx="39" cy="18" r="1" fill="white"/>
          </g>
          <!-- Eyes (fainted) -->
          <g id="eyesFainted" style="display:none">
            <line x1="20" y1="16" x2="26" y2="22" stroke="#3D2010" stroke-width="2" stroke-linecap="round"/>
            <line x1="26" y1="16" x2="20" y2="22" stroke="#3D2010" stroke-width="2" stroke-linecap="round"/>
            <line x1="34" y1="16" x2="40" y2="22" stroke="#3D2010" stroke-width="2" stroke-linecap="round"/>
            <line x1="40" y1="16" x2="34" y2="22" stroke="#3D2010" stroke-width="2" stroke-linecap="round"/>
          </g>
          <!-- Eyes (happy) -->
          <g id="eyesHappy" style="display:none">
            <path d="M19 21 Q23 15 27 21" fill="none" stroke="#3D2010" stroke-width="2.5" stroke-linecap="round"/>
            <path d="M33 21 Q37 15 41 21" fill="none" stroke="#3D2010" stroke-width="2.5" stroke-linecap="round"/>
          </g>
          <!-- Mouth (normal) -->
          <path id="mouthNormal" d="M25 28 Q30 32 35 28" fill="none" stroke="#C0694A" stroke-width="2" stroke-linecap="round"/>
          <!-- Mouth (sad) -->
          <path id="mouthSad" d="M25 31 Q30 27 35 31" fill="none" stroke="#C0694A" stroke-width="2" stroke-linecap="round" style="display:none"/>
          <!-- Cheeks -->
          <ellipse id="cheeks" cx="18" cy="26" rx="5" ry="3.5" fill="#FFB8A0" opacity="0.7"/>
          <ellipse id="cheeks2" cx="42" cy="26" rx="5" ry="3.5" fill="#FFB8A0" opacity="0.7"/>
          <!-- Feet -->
          <ellipse cx="22" cy="52" rx="7" ry="5" fill="#FF9E7A"/>
          <ellipse cx="38" cy="52" rx="7" ry="5" fill="#FF9E7A"/>
          <!-- Tiny tail -->
          <path d="M49 36 Q58 28 54 38" fill="#FF9E7A" stroke="#FF9E7A" stroke-width="1"/>
        </svg>
      </div>

    </div>
  </div>

  <!-- Controls -->
  <div class="controls">
    <div class="name-display" id="nameDisplay">FRIJOLITO</div>
    <div class="stats" id="statsPanel">
      Last fed: <span id="lastFedDisplay">never</span>
    </div>
    <div class="btn-row">
      <button class="btn green" id="feedBtn" onclick="feed()">🍎 Feed</button>
      <button class="btn" onclick="nudge()">👋 Nudge</button>
    </div>
  </div>
</div>

<script>
  // ── State ──────────────────────────────────────────────────────────
  const HUNGER_MAX = 5;
  const HOURS_TO_HUNGRY = 8;   // starts getting hungry after 8h
  const HOURS_TO_FAINT  = 24;  // faints after 24h without food

  let state = loadState();

  function defaultState() {
    return {
      name: '',
      lastFed: null,   // ISO string or null
      dayCount: 1,
      createdAt: new Date().toISOString()
    };
  }

  function loadState() {
    try {
      const raw = localStorage.getItem('tamagotchi_v1');
      if (raw) return JSON.parse(raw);
    } catch(e) {}
    return defaultState();
  }

  function saveState() {
    localStorage.setItem('tamagotchi_v1', JSON.stringify(state));
  }

  // ── Hunger calculation ─────────────────────────────────────────────
  function hoursSinceLastFed() {
    if (!state.lastFed) return 999;
    const diff = Date.now() - new Date(state.lastFed).getTime();
    return diff / (1000 * 60 * 60);
  }

  function getHungerLevel() {
    // 5 = full, 0 = empty/fainted
    const hours = hoursSinceLastFed();
    if (hours >= HOURS_TO_FAINT) return 0;
    if (hours < HOURS_TO_HUNGRY) return 5;
    // Linear decay from HOURS_TO_HUNGRY to HOURS_TO_FAINT
    const ratio = (hours - HOURS_TO_HUNGRY) / (HOURS_TO_FAINT - HOURS_TO_HUNGRY);
    return Math.max(0, Math.round(5 * (1 - ratio)));
  }

  function isFainted() { return getHungerLevel() === 0; }

  // ── UI updates ─────────────────────────────────────────────────────
  function updateUI() {
    const hunger = getHungerLevel();
    const fainted = isFainted();

    // Pips
    for (let i = 0; i < HUNGER_MAX; i++) {
      const pip = document.getElementById('p' + i);
      pip.classList.toggle('filled', i < hunger);
    }

    // Creature expression
    const creature = document.getElementById('creature');
    creature.className = 'creature-wrap';

    const eyesNormal  = document.getElementById('eyesNormal');
    const eyesHappy   = document.getElementById('eyesHappy');
    const eyesFainted = document.getElementById('eyesFainted');
    const mouthNormal = document.getElementById('mouthNormal');
    const mouthSad    = document.getElementById('mouthSad');
    const zzz         = document.getElementById('zzz');
    const bubble      = document.getElementById('bubble');

    eyesNormal.style.display  = 'none';
    eyesHappy.style.display   = 'none';
    eyesFainted.style.display = 'none';
    mouthNormal.style.display = 'none';
    mouthSad.style.display    = 'none';
    zzz.classList.remove('visible');
    bubble.classList.remove('visible');

    if (fainted) {
      eyesFainted.style.display = 'block';
      mouthSad.style.display    = 'block';
      creature.classList.add('fainting');
      zzz.classList.add('visible');
      bubble.textContent = 'So hungry...';
      bubble.classList.add('visible');
    } else if (hunger >= 4) {
      eyesHappy.style.display = 'block';
      mouthNormal.style.display = 'block';
      creature.classList.add('bouncing');
    } else if (hunger <= 1) {
      eyesNormal.style.display = 'block';
      mouthSad.style.display = 'block';
      bubble.textContent = 'Feed me! 🍎';
      bubble.classList.add('visible');
    } else {
      eyesNormal.style.display = 'block';
      mouthNormal.style.display = 'block';
    }

    // Feed button
    document.getElementById('feedBtn').disabled = !fainted && hunger === HUNGER_MAX;

    // Day counter
    const days = Math.floor((Date.now() - new Date(state.createdAt).getTime()) / (1000*60*60*24)) + 1;
    document.getElementById('dayDisplay').textContent = 'DAY ' + days;

    // Last fed display
    const lf = document.getElementById('lastFedDisplay');
    if (!state.lastFed) {
      lf.textContent = 'never';
    } else {
      const h = Math.floor(hoursSinceLastFed());
      const m = Math.floor((hoursSinceLastFed() - h) * 60);
      if (h === 0) lf.textContent = m + 'min ago';
      else if (h < 24) lf.textContent = h + 'h ' + m + 'm ago';
      else lf.textContent = Math.floor(h/24) + ' day(s) ago';
    }

    // Name
    document.getElementById('nameDisplay').textContent = (state.name || 'FRIJOLITO').toUpperCase();
  }

  // ── Actions ────────────────────────────────────────────────────────
  function feed() {
    state.lastFed = new Date().toISOString();
    saveState();

    const creature = document.getElementById('creature');
    creature.className = 'creature-wrap just-fed';
    setTimeout(() => updateUI(), 50);

    // Hearts
    const hearts = document.createElement('div');
    hearts.className = 'hearts visible';
    hearts.textContent = '❤️ ❤️ ❤️';
    document.getElementById('screen').appendChild(hearts);
    setTimeout(() => hearts.remove(), 1000);

    // Happy bubble
    const bubble = document.getElementById('bubble');
    bubble.textContent = ['Yummy!','Thank you!','Mmm ♥','So good!'][Math.floor(Math.random()*4)];
    bubble.classList.add('visible');
    setTimeout(() => { bubble.classList.remove('visible'); updateUI(); }, 1800);
  }

  function nudge() {
    if (isFainted()) {
      const bubble = document.getElementById('bubble');
      bubble.textContent = '...feed me first';
      bubble.classList.add('visible');
      setTimeout(() => bubble.classList.remove('visible'), 1500);
      return;
    }
    const creature = document.getElementById('creature');
    creature.style.animation = 'none';
    creature.offsetHeight;
    creature.style.animation = 'fed-anim 0.4s ease';
    setTimeout(() => { creature.style.animation = ''; updateUI(); }, 500);

    const phrases = ['Hehe ♥','Hi!','(^‿^)','*wiggles*','Hello!'];
    const bubble = document.getElementById('bubble');
    bubble.textContent = phrases[Math.floor(Math.random()*phrases.length)];
    bubble.classList.add('visible');
    setTimeout(() => bubble.classList.remove('visible'), 1500);
  }

  // ── Name setup ────────────────────────────────────────────────────
  function saveName() {
    const val = document.getElementById('nameInput').value.trim();
    state.name = val || 'Frijolito';
    if (!state.createdAt) state.createdAt = new Date().toISOString();
    saveState();
    document.getElementById('nameOverlay').style.display = 'none';
    updateUI();
  }

  document.getElementById('nameInput').addEventListener('keydown', e => {
    if (e.key === 'Enter') saveName();
  });

  // ── Init ──────────────────────────────────────────────────────────
  if (state.name) {
    document.getElementById('nameOverlay').style.display = 'none';
  }
  updateUI();
  setInterval(updateUI, 30000); // refresh every 30s
</script>
</body>
</html>
