<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Mini Water Sort (Levels)</title>
  <style>
    :root{
      --bg0:#0b1020;
      --text: rgba(255,255,255,.92);
      --muted: rgba(255,255,255,.68);
      --panel: rgba(255,255,255,.08);
      --tubeW: 86px;
      --tubeH: 220px;
      --segH: 34px;
    }

    html, body {
      height: 100%;
      margin: 0;
      color: var(--text);
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Arial;
      background: radial-gradient(1200px 800px at 20% 10%, #1a2b5a 0%, var(--bg0) 55%, #060912 100%);
      overflow-x: hidden;
    }

    body.celebrate { animation: bgFlash 900ms ease-in-out 0s 1; }
    @keyframes bgFlash {
      0% { filter: hue-rotate(0deg) saturate(1); }
      20% { filter: hue-rotate(50deg) saturate(1.6) brightness(1.1); }
      45% { filter: hue-rotate(140deg) saturate(1.8) brightness(1.15); }
      70% { filter: hue-rotate(220deg) saturate(1.7) brightness(1.08); }
      100% { filter: hue-rotate(0deg) saturate(1) brightness(1); }
    }

    header {
      max-width: 980px;
      margin: 20px auto 8px;
      padding: 0 16px;
      display: flex;
      gap: 14px;
      align-items: center;
      justify-content: space-between;
      flex-wrap: wrap;
    }
    .title{display:flex;flex-direction:column;gap:4px}
    .title h1{margin:0;font-size:18px}
    .title p{margin:0;font-size:13px;color:var(--muted)}
    .controls{display:flex;gap:10px;align-items:center;flex-wrap:wrap;justify-content:flex-end}
    button{
      border: 0;
      border-radius: 999px;
      padding: 10px 14px;
      color: var(--text);
      background: linear-gradient(180deg, rgba(255,255,255,.14), rgba(255,255,255,.08));
      box-shadow: 0 10px 30px rgba(0,0,0,.25);
      cursor:pointer;
      font-weight: 650;
    }
    button:hover { background: linear-gradient(180deg, rgba(255,255,255,.18), rgba(255,255,255,.10)); }
    button:active { transform: translateY(1px); }
    .pill{
      padding: 9px 12px;
      border-radius: 999px;
      background: var(--panel);
      color: var(--muted);
      font-size: 12px;
      border: 1px solid rgba(255,255,255,.10);
      user-select:none;
    }

    main{max-width: 980px;margin: 0 auto;padding: 10px 16px 28px;display:grid;gap:16px}
    .boardWrap{
      position: relative;
      border-radius: 22px;
      background: linear-gradient(180deg, rgba(255,255,255,.09), rgba(255,255,255,.05));
      border: 1px solid rgba(255,255,255,.10);
      box-shadow: 0 20px 60px rgba(0,0,0,.35);
      overflow: hidden;
    }
    .board{
      padding: 18px 16px 16px;
      display:flex;
      flex-wrap:wrap;
      gap: 14px;
      justify-content:center;
      align-items:flex-end;
      min-height: 340px;
    }
    .tube{
      width: var(--tubeW);
      height: var(--tubeH);
      border-radius: 18px 18px 24px 24px;
      border: 2px solid rgba(255,255,255,.22);
      background: linear-gradient(180deg, rgba(255,255,255,.10), rgba(255,255,255,.05));
      box-shadow: inset 0 0 0 2px rgba(0,0,0,.08), 0 18px 40px rgba(0,0,0,.35);
      position:relative;
      cursor:pointer;
      user-select:none;
      transition: transform 120ms ease, box-shadow 120ms ease, border-color 120ms ease;
      display:flex;
      flex-direction:column;
      justify-content:flex-end;
      padding: 10px 10px 12px;
      gap: 6px;
    }
    .tube:hover{transform: translateY(-2px);box-shadow: inset 0 0 0 2px rgba(0,0,0,.08), 0 22px 55px rgba(0,0,0,.42)}
    .tube.selected{
      border-color: rgba(124,247,255,.85);
      box-shadow: inset 0 0 0 2px rgba(0,0,0,.08), 0 0 0 4px rgba(124,247,255,.18), 0 22px 60px rgba(0,0,0,.45);
      transform: translateY(-6px) rotate(-0.4deg);
    }
    .tube.complete{border-color: rgba(255,255,255,.35)}
    .seg{
      height: var(--segH);
      border-radius: 12px;
      border: 1px solid rgba(255,255,255,.18);
      box-shadow: inset 0 10px 16px rgba(255,255,255,.08), inset 0 -10px 14px rgba(0,0,0,.12);
    }
    .seg.empty{
      background: rgba(255,255,255,.04);
      border-style:dashed;
      border-color: rgba(255,255,255,.12);
      box-shadow:none;
    }

    .footerRow{
      display:flex;
      gap:12px;
      align-items:center;
      justify-content:space-between;
      padding: 0 16px 16px;
      color: var(--muted);
      font-size: 12px;
      flex-wrap: wrap;
    }

    #fx { position:absolute; inset:0; width:100%; height:100%; pointer-events:none; }

    .dogDock{
      position:absolute;
      right: 16px;
      bottom: 14px;
      display:flex;
      gap:10px;
      align-items:flex-end;
      pointer-events:none;
      filter: drop-shadow(0 18px 24px rgba(0,0,0,.35));
    }
    .dogBubble{
      background: rgba(255,255,255,.10);
      border: 1px solid rgba(255,255,255,.14);
      color: rgba(255,255,255,.85);
      padding: 10px 12px;
      border-radius: 14px;
      font-size: 12px;
      max-width: 240px;
    }
    .dog{ width: 120px; height: 120px; transform-origin: 60px 104px; will-change: transform; }
    .dog.flip{ animation: backflip 900ms cubic-bezier(.2,.9,.15,1) 0s 1; }
    @keyframes backflip{
      0%   { transform: translateY(0) rotate(0deg) scale(1); }
      18%  { transform: translateY(-18px) rotate(-8deg) scale(1.02); }
      52%  { transform: translateY(-46px) rotate(-190deg) scale(1.03); }
      78%  { transform: translateY(-10px) rotate(-340deg) scale(1.01); }
      100% { transform: translateY(0) rotate(-360deg) scale(1); }
    }
    .srOnly{position:absolute;left:-9999px;width:1px;height:1px;overflow:hidden}
  </style>
</head>
<body>
  <header>
    <div class="title">
      <h1>Mini Water Sort</h1>
      <p>Click a tube, then click another to pour. Solve 4 puzzles to level up.</p>
    </div>
    <div class="controls">
      <div class="pill" id="statusPill">Loading…</div>
      <button id="newBtn">New Run</button>
      <button id="undoBtn">Undo</button>
    </div>
  </header>

  <main>
    <div class="boardWrap" id="wrap">
      <canvas id="fx" aria-hidden="true"></canvas>
      <div class="board" id="board" role="application" aria-label="Water sort board"></div>

      <div class="dogDock" aria-hidden="true">
        <div class="dogBubble" id="dogBubble">Complete tubes and I’ll backflip!</div>
        <svg class="dog" id="dog" viewBox="0 0 120 120">
          <ellipse cx="62" cy="108" rx="34" ry="8" fill="rgba(0,0,0,.25)"/>
          <path d="M28 78 C30 55, 48 48, 63 48 C82 48, 95 58, 96 75 C97 90, 88 100, 72 100 C52 100, 26 97, 28 78 Z"
                fill="#f2c27b" stroke="rgba(0,0,0,.18)" stroke-width="2"/>
          <path d="M43 82 C45 70, 57 64, 68 66 C80 69, 86 80, 80 92 C72 106, 44 102, 43 82 Z"
                fill="#f8e2bf" opacity=".9"/>
          <path d="M30 58 C30 43, 44 34, 58 34 C74 34, 86 45, 85 60 C84 73, 72 82, 58 80 C44 79, 30 72, 30 58 Z"
                fill="#f2c27b" stroke="rgba(0,0,0,.18)" stroke-width="2"/>
          <path d="M36 44 C30 38, 26 44, 28 54 C30 66, 40 64, 44 56 C47 50, 43 48, 36 44 Z"
                fill="#e7ab5b" stroke="rgba(0,0,0,.18)" stroke-width="2"/>
          <path d="M60 58 C64 55, 72 55, 76 60 C79 64, 76 70, 70 71 C63 72, 58 66, 60 58 Z"
                fill="#f8e2bf" stroke="rgba(0,0,0,.14)" stroke-width="2"/>
          <ellipse cx="75" cy="61" rx="4" ry="3" fill="#2b2b2b"/>
          <circle cx="52" cy="55" r="3.3" fill="#2b2b2b"/>
          <circle cx="64" cy="55" r="3.3" fill="#2b2b2b"/>
          <circle cx="51" cy="54" r="1" fill="rgba(255,255,255,.9)"/>
          <circle cx="63" cy="54" r="1" fill="rgba(255,255,255,.9)"/>
          <path d="M64 65 C66 67, 70 68, 73 66" fill="none" stroke="rgba(0,0,0,.35)" stroke-width="2" stroke-linecap="round"/>
          <rect x="44" y="96" width="10" height="14" rx="5" fill="#e7ab5b"/>
          <rect x="72" y="96" width="10" height="14" rx="5" fill="#e7ab5b"/>
          <path d="M96 78 C110 76, 110 92, 98 92" fill="none" stroke="#e7ab5b" stroke-width="7" stroke-linecap="round"/>
          <path d="M96 78 C110 76, 110 92, 98 92" fill="none" stroke="rgba(0,0,0,.18)" stroke-width="2" stroke-linecap="round"/>
        </svg>
      </div>

      <div class="footerRow">
        <div>Rule: Pour onto same-color top (or empty). Auto-pours as much as possible.</div>
        <div class="srOnly" id="ariaLive" aria-live="polite"></div>
      </div>
    </div>
  </main>

  <script>
    const CAP = 4;

    // Lots of colors so levels can grow.
    const COLORS = [
      "#ff5d8f","#7cf7ff","#ffd166","#9bff7a","#b28dff","#ff8a4c",
      "#66ffa6","#ff6b6b","#4d96ff","#f72585","#a8dadc","#ffbe0b",
      "#c77dff","#06d6a0","#ef476f","#8338ec","#3a86ff","#fb8500",
      "#8ecae6","#90be6d"
    ];

    // Level rules
    const PUZZLES_PER_LEVEL = 4;
    const LEVEL1_FILLED_TUBES = 5; // 5 filled + 2 empty = 7 total
    const LEVEL1_EMPTY_TUBES  = 2;
    const EMPTY_TUBES_EACH_LEVEL = 2;
    const FILLED_TUBES_GROWTH_PER_LEVEL = 1;

    const boardEl = document.getElementById("board");
    const statusPill = document.getElementById("statusPill");
    const newBtn = document.getElementById("newBtn");
    const undoBtn = document.getElementById("undoBtn");
    const ariaLive = document.getElementById("ariaLive");

    const wrap = document.getElementById("wrap");
    const canvas = document.getElementById("fx");
    const ctx = canvas.getContext("2d");

    const dog = document.getElementById("dog");
    const dogBubble = document.getElementById("dogBubble");

    let tubes = [];     // bottom->top
    let selected = -1;
    let history = [];

    let level = 1;
    let puzzlesSolvedThisLevel = 0;

    function resizeCanvas(){
      const r = wrap.getBoundingClientRect();
      const dpr = Math.max(1, Math.floor(window.devicePixelRatio || 1));
      canvas.width = Math.floor(r.width * dpr);
      canvas.height = Math.floor(r.height * dpr);
      canvas.style.width = r.width + "px";
      canvas.style.height = r.height + "px";
      ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
    }
    window.addEventListener("resize", resizeCanvas);

    function cloneState(state){ return state.map(t => t.slice()); }

    function topColor(t){
      for (let i = CAP - 1; i >= 0; i--) if (t[i] != null) return t[i];
      return null;
    }
    function spaceLeft(t){
      let s = 0;
      for (let i = 0; i < CAP; i++) if (t[i] == null) s++;
      return s;
    }
    function countTopRun(t){
      const c = topColor(t);
      if (c == null) return 0;
      let n = 0;
      for (let i = CAP - 1; i >= 0; i--){
        if (t[i] === c) n++;
        else if (t[i] == null) continue;
        else break;
      }
      return n;
    }
    function isCompleteTube(t){
      const c = t[0];
      if (c == null) return false;
      for (let i = 1; i < CAP; i++) if (t[i] !== c) return false;
      return true;
    }

    function canPour(from, to){
      if (from === to) return false;
      const A = tubes[from], B = tubes[to];
      const cA = topColor(A);
      if (cA == null) return false;
      if (spaceLeft(B) === 0) return false;
      const cB = topColor(B);
      return (cB == null || cB === cA);
    }

    function checkWin(){
      return tubes.every(t => {
        const empty = topColor(t) == null;
        return empty || isCompleteTube(t);
      });
    }

    function announce(msg){
      ariaLive.textContent = msg;
      statusPill.textContent = msg;
    }
    function levelStatusText(msg){
      return `Level ${level} — Puzzle ${puzzlesSolvedThisLevel + 1}/${PUZZLES_PER_LEVEL}: ${msg}`;
    }
    function filledTubeCountForLevel(lv){
      return LEVEL1_FILLED_TUBES + (lv - 1) * FILLED_TUBES_GROWTH_PER_LEVEL;
    }
    function emptyTubeCountForLevel(lv){
      return EMPTY_TUBES_EACH_LEVEL;
    }

    // --- Solvable generator: scramble from solved ---
    function topColorState(t){
      for (let i = CAP - 1; i >= 0; i--) if (t[i] != null) return t[i];
      return null;
    }
    function spaceLeftState(t){
      let s = 0; for (let i = 0; i < CAP; i++) if (t[i] == null) s++; return s;
    }
    function countTopRunState(t){
      const c = topColorState(t);
      if (c == null) return 0;
      let n = 0;
      for (let i = CAP - 1; i >= 0; i--){
        if (t[i] === c) n++;
        else if (t[i] == null) continue;
        else break;
      }
      return n;
    }
    function canPourState(state, from, to){
      if (from === to) return false;
      const A = state[from], B = state[to];
      const cA = topColorState(A);
      if (cA == null) return false;
      if (spaceLeftState(B) === 0) return false;
      const cB = topColorState(B);
      return (cB == null || cB === cA);
    }
    function applyPourState(state, from, to){
      const A = state[from], B = state[to];
      const cA = topColorState(A);
      const run = countTopRunState(A);
      const amt = Math.min(run, spaceLeftState(B));

      let removed = 0;
      for (let i = CAP - 1; i >= 0 && removed < amt; i--){
        if (A[i] === cA){ A[i] = null; removed++; }
      }
      let placed = 0;
      for (let i = 0; i < CAP && placed < amt; i++){
        if (B[i] == null){ B[i] = cA; placed++; }
      }
    }
    function listValidMoves(state){
      const moves = [];
      for (let a = 0; a < state.length; a++){
        for (let b = 0; b < state.length; b++){
          if (a === b) continue;
          if (canPourState(state, a, b)) moves.push([a, b]);
        }
      }
      return moves;
    }
    function buildSolvedTubes(filledCount, empties){
      if (filledCount > COLORS.length) filledCount = COLORS.length; // clamp
      const used = COLORS.slice(0, filledCount);
      const out = [];
      for (let i = 0; i < filledCount; i++){
        const c = used[i];
        out.push([c, c, c, c]);
      }
      for (let e = 0; e < empties; e++) out.push([null, null, null, null]);
      return out;
    }
    function scrambleFromSolved(filledCount, empties){
      const totalTubes = filledCount + empties;
      const steps = 40 + totalTubes * 12 + level * 10;

      let state = buildSolvedTubes(filledCount, empties);
      let lastMove = null;

      for (let i = 0; i < steps; i++){
        const moves = listValidMoves(state);
        const filtered = lastMove
          ? moves.filter(([a,b]) => !(a === lastMove[1] && b === lastMove[0]))
          : moves;
        const pool = filtered.length ? filtered : moves;
        if (!pool.length) break;
        const pick = pool[Math.floor(Math.random() * pool.length)];
        applyPourState(state, pick[0], pick[1]);
        lastMove = pick;
      }
      return state;
    }

    function newPuzzleForCurrentLevel(){
      selected = -1;
      history = [];

      const filledCount = filledTubeCountForLevel(level);
      const empties = emptyTubeCountForLevel(level);

      let tries = 0;
      do {
        tubes = scrambleFromSolved(filledCount, empties);
        tries++;
      } while (checkWin() && tries < 10);

      render();
      dogBubble.textContent = `Level ${level}: solve ${PUZZLES_PER_LEVEL} puzzles to level up.`;
      announce(levelStatusText("Select a tube."));
    }

    function startNewRun(){
      level = 1;
      puzzlesSolvedThisLevel = 0;
      newPuzzleForCurrentLevel();
    }

    function render(){
      boardEl.innerHTML = "";
      tubes.forEach((t, idx) => {
        const tubeEl = document.createElement("div");
        tubeEl.className = "tube" +
          (idx === selected ? " selected" : "") +
          (isCompleteTube(t) ? " complete" : "");
        tubeEl.setAttribute("role", "button");
        tubeEl.setAttribute("aria-label", "Tube " + (idx + 1));
        tubeEl.addEventListener("click", () => onTubeClick(idx));

        for (let i = CAP - 1; i >= 0; i--){
          const seg = document.createElement("div");
          const c = t[i];
          seg.className = "seg" + (c == null ? " empty" : "");
          if (c != null) seg.style.background = c;
          tubeEl.appendChild(seg);
        }
        boardEl.appendChild(tubeEl);
      });
    }

    function onTubeClick(i){
      if (selected === -1){
        selected = i;
        render();
        announce(levelStatusText("Selected tube " + (i + 1) + ". Choose a destination."));
        return;
      }
      if (selected === i){
        selected = -1;
        render();
        announce(levelStatusText("Selection cleared."));
        return;
      }

      const ok = pour(selected, i);
      if (!ok){
        selected = i;
        render();
        announce(levelStatusText("Can't pour there. Selected tube " + (i + 1) + " instead."));
      }
    }

    function pour(from, to){
      if (!canPour(from, to)) return false;

      history.push({ tubes: cloneState(tubes), selected });

      const A = tubes[from], B = tubes[to];
      const cA = topColor(A);
      const run = countTopRun(A);
      const amt = Math.min(run, spaceLeft(B));

      let removed = 0;
      for (let i = CAP - 1; i >= 0 && removed < amt; i--){
        if (A[i] === cA){ A[i] = null; removed++; }
      }
      let placed = 0;
      for (let i = 0; i < CAP && placed < amt; i++){
        if (B[i] == null){ B[i] = cA; placed++; }
      }

      selected = -1;
      render();

      const becameComplete = isCompleteTube(B);
      if (becameComplete) celebrate("Tube completed!");
      else if (spaceLeft(B) === 0) celebrate("Tube filled!");

      if (checkWin()){
        celebrate("Puzzle solved!");
        puzzlesSolvedThisLevel++;

        if (puzzlesSolvedThisLevel >= PUZZLES_PER_LEVEL){
          level++;
          puzzlesSolvedThisLevel = 0;
          dogBubble.textContent = `Level up! Welcome to Level ${level}.`;
          celebrate(`Level ${level}!`);
        }

        setTimeout(() => newPuzzleForCurrentLevel(), 650);
      }

      return true;
    }

    function undo(){
      const prev = history.pop();
      if (!prev){ announce(levelStatusText("Nothing to undo.")); return; }
      tubes = cloneState(prev.tubes);
      selected = prev.selected;
      render();
      announce(levelStatusText("Undid last move."));
    }

    // --- Celebration FX (sparkles) ---
    let particles = [];
    let fxUntil = 0;
    let rafOn = false;

    function celebrate(msg){
      announce(levelStatusText(msg));

      document.body.classList.remove("celebrate");
      void document.body.offsetWidth;
      document.body.classList.add("celebrate");

      dog.classList.remove("flip");
      void dog.offsetWidth;
      dog.classList.add("flip");

      const r = wrap.getBoundingClientRect();
      const cx = r.width * (0.35 + Math.random() * 0.3);
      const cy = r.height * (0.25 + Math.random() * 0.25);
      spawnSparkles(cx, cy, 90);

      fxUntil = performance.now() + 1100;
      if (!rafOn) startFX();
    }

    function spawnSparkles(x, y, n){
      for (let i = 0; i < n; i++){
        const a = Math.random() * Math.PI * 2;
        const sp = 1.6 + Math.random() * 4.4;
        const hue = Math.floor(Math.random() * 360);
        particles.push({
          x, y,
          vx: Math.cos(a) * sp,
          vy: Math.sin(a) * sp - (1.5 + Math.random() * 2.0),
          g: 0.08 + Math.random() * 0.06,
          life: 38 + Math.floor(Math.random() * 28),
          r: 1.5 + Math.random() * 2.5,
          hue,
          tw: Math.random() * Math.PI * 2
        });
      }
    }

    function startFX(){ rafOn = true; requestAnimationFrame(tick); }

    function tick(){
      const now = performance.now();
      const r = wrap.getBoundingClientRect();
      ctx.clearRect(0, 0, r.width, r.height);

      if (now < fxUntil){
        ctx.save();
        ctx.globalAlpha = 0.12;
        const grad = ctx.createRadialGradient(r.width/2, r.height/3, 30, r.width/2, r.height/3, 320);
        grad.addColorStop(0, "rgba(255,255,255,.8)");
        grad.addColorStop(1, "rgba(255,255,255,0)");
        ctx.fillStyle = grad;
        ctx.fillRect(0,0,r.width,r.height);
        ctx.restore();
      }

      for (let i = particles.length - 1; i >= 0; i--){
        const p = particles[i];
        p.vy += p.g;
        p.x += p.vx;
        p.y += p.vy;
        p.life--;

        const t = (Math.sin(p.tw += 0.35) + 1) / 2;
        const alpha = Math.max(0, Math.min(1, p.life / 40)) * (0.35 + 0.65 * t);

        ctx.save();
        ctx.globalAlpha = alpha;
        ctx.fillStyle = `hsla(${p.hue}, 95%, 70%, 1)`;
        drawStar(p.x, p.y, 4, p.r * (1.1 + t), p.r * 0.55);
        ctx.restore();

        if (p.life <= 0) particles.splice(i, 1);
      }

      if (particles.length > 0 || now < fxUntil) requestAnimationFrame(tick);
      else rafOn = false;
    }

    function drawStar(x, y, points, outerR, innerR){
      ctx.beginPath();
      let rot = Math.PI / 2 * 3;
      const step = Math.PI / points;
      ctx.moveTo(x, y - outerR);
      for (let i = 0; i < points; i++){
        ctx.lineTo(x + Math.cos(rot) * outerR, y + Math.sin(rot) * outerR);
        rot += step;
        ctx.lineTo(x + Math.cos(rot) * innerR, y + Math.sin(rot) * innerR);
        rot += step;
      }
      ctx.closePath();
      ctx.fill();
    }

    newBtn.addEventListener("click", startNewRun);
    undoBtn.addEventListener("click", undo);

    resizeCanvas();
    startNewRun();
  </script>
</body>
</html>
