<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Tic Tac Toe By Hridhan </title>
<link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Sans:wght@300;400;500;600&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0d0d0f;
    --surface: #16161a;
    --surface2: #1e1e24;
    --border: #2a2a35;
    --x-color: #f5a623;
    --o-color: #4fc3f7;
    --text: #f0f0f5;
    --muted: #6b6b80;
    --win-glow-x: 0 0 20px rgba(245,166,35,0.5);
    --win-glow-o: 0 0 20px rgba(79,195,247,0.5);
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'DM Sans', sans-serif;
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;
  }

  /* Animated background grid */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(255,255,255,0.02) 1px, transparent 1px),
      linear-gradient(90deg, rgba(255,255,255,0.02) 1px, transparent 1px);
    background-size: 60px 60px;
    z-index: 0;
    animation: gridShift 20s linear infinite;
  }
  @keyframes gridShift {
    0%   { transform: translate(0,0); }
    100% { transform: translate(60px,60px); }
  }

  .app {
    position: relative;
    z-index: 1;
    width: 100%;
    max-width: 480px;
    padding: 24px;
  }

  /* ── SETUP SCREEN ── */
  #setup-screen {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 20px;
    padding: 40px 36px;
    text-align: center;
    animation: fadeIn .4s ease;
  }

  .logo {
    font-family: 'Bebas Neue', sans-serif;
    font-size: 52px;
    letter-spacing: 4px;
    line-height: 1;
    background: linear-gradient(135deg, var(--x-color), var(--o-color));
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    margin-bottom: 6px;
  }

  .tagline {
    color: var(--muted);
    font-size: 13px;
    letter-spacing: 2px;
    text-transform: uppercase;
    margin-bottom: 36px;
  }

  .player-inputs {
    display: flex;
    flex-direction: column;
    gap: 16px;
    margin-bottom: 28px;
  }

  .input-group {
    display: flex;
    align-items: center;
    gap: 12px;
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 14px 18px;
    transition: border-color .2s;
  }
  .input-group:focus-within { border-color: var(--x-color); }
  .input-group.p2:focus-within { border-color: var(--o-color); }

  .player-badge {
    font-family: 'Bebas Neue', sans-serif;
    font-size: 22px;
    width: 32px;
    text-align: center;
    flex-shrink: 0;
  }
  .p1 .player-badge { color: var(--x-color); }
  .p2 .player-badge { color: var(--o-color); }

  .input-group input {
    background: none;
    border: none;
    outline: none;
    color: var(--text);
    font-family: 'DM Sans', sans-serif;
    font-size: 15px;
    font-weight: 500;
    width: 100%;
  }
  .input-group input::placeholder { color: var(--muted); }

  .btn-start {
    width: 100%;
    padding: 16px;
    background: linear-gradient(135deg, var(--x-color), #e8940f);
    border: none;
    border-radius: 12px;
    color: #0d0d0f;
    font-family: 'Bebas Neue', sans-serif;
    font-size: 20px;
    letter-spacing: 2px;
    cursor: pointer;
    transition: transform .15s, opacity .15s;
  }
  .btn-start:hover { transform: translateY(-2px); opacity: .92; }
  .btn-start:active { transform: translateY(0); }

  /* ── GAME SCREEN ── */
  #game-screen { display: none; animation: fadeIn .4s ease; }

  .scoreboard {
    display: flex;
    align-items: center;
    justify-content: space-between;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 16px 20px;
    margin-bottom: 18px;
  }

  .player-info {
    text-align: center;
    flex: 1;
  }
  .player-info .name {
    font-size: 13px;
    font-weight: 600;
    letter-spacing: 1px;
    text-transform: uppercase;
    margin-bottom: 4px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    max-width: 120px;
  }
  .player-info.px .name { color: var(--x-color); }
  .player-info.po .name { color: var(--o-color); }
  .player-info .score {
    font-family: 'Bebas Neue', sans-serif;
    font-size: 36px;
    line-height: 1;
  }
  .player-info.px .score { color: var(--x-color); }
  .player-info.po .score { color: var(--o-color); }

  .score-divider {
    font-family: 'Bebas Neue', sans-serif;
    font-size: 28px;
    color: var(--muted);
    padding: 0 12px;
  }

  /* Turn indicator */
  .turn-bar {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-bottom: 18px;
    font-size: 13px;
    letter-spacing: 1px;
    text-transform: uppercase;
    color: var(--muted);
    height: 28px;
  }
  .turn-name {
    font-weight: 600;
    transition: color .3s;
  }
  .turn-name.x { color: var(--x-color); }
  .turn-name.o { color: var(--o-color); }

  /* Board */
  .board {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
    margin-bottom: 18px;
  }

  .cell {
    aspect-ratio: 1;
    background: var(--surface);
    border: 1.5px solid var(--border);
    border-radius: 14px;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    transition: background .15s, border-color .15s, transform .1s;
    position: relative;
    overflow: hidden;
  }
  .cell:hover:not(.taken) {
    background: var(--surface2);
    border-color: #3a3a50;
    transform: scale(1.03);
  }
  .cell.taken { cursor: default; }

  /* X mark */
  .cell .x-mark, .cell .o-mark {
    width: 52%;
    height: 52%;
    animation: popIn .22s cubic-bezier(.34,1.56,.64,1);
  }
  @keyframes popIn {
    from { transform: scale(0) rotate(-15deg); opacity: 0; }
    to   { transform: scale(1) rotate(0deg);   opacity: 1; }
  }

  .x-mark svg { stroke: var(--x-color); }
  .o-mark svg { stroke: var(--o-color); }

  /* Win highlight */
  .cell.win-cell {
    background: var(--surface2);
    animation: winPulse .5s ease forwards;
  }
  .cell.win-cell.x-win { border-color: var(--x-color); box-shadow: var(--win-glow-x); }
  .cell.win-cell.o-win { border-color: var(--o-color); box-shadow: var(--win-glow-o); }
  @keyframes winPulse {
    0%   { transform: scale(1); }
    40%  { transform: scale(1.07); }
    100% { transform: scale(1); }
  }

  /* Action row */
  .action-row {
    display: flex;
    gap: 10px;
  }
  .btn-secondary {
    flex: 1;
    padding: 13px;
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 10px;
    color: var(--muted);
    font-family: 'DM Sans', sans-serif;
    font-size: 13px;
    font-weight: 600;
    letter-spacing: 1px;
    text-transform: uppercase;
    cursor: pointer;
    transition: background .15s, color .15s, border-color .15s;
  }
  .btn-secondary:hover { background: #252530; color: var(--text); border-color: #3a3a50; }

  /* ── WINNER OVERLAY ── */
  #winner-overlay {
    display: none;
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,.75);
    backdrop-filter: blur(6px);
    z-index: 100;
    align-items: center;
    justify-content: center;
    animation: fadeIn .3s ease;
  }
  #winner-overlay.show { display: flex; }

  .winner-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 24px;
    padding: 48px 40px;
    text-align: center;
    max-width: 340px;
    width: 90%;
    animation: slideUp .35s cubic-bezier(.34,1.3,.64,1);
  }
  @keyframes slideUp {
    from { transform: translateY(40px) scale(.95); opacity: 0; }
    to   { transform: translateY(0) scale(1);      opacity: 1; }
  }

  .winner-emoji {
    font-size: 56px;
    margin-bottom: 12px;
    display: block;
  }

  .winner-label {
    font-size: 12px;
    letter-spacing: 3px;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 8px;
  }

  .winner-name {
    font-family: 'Bebas Neue', sans-serif;
    font-size: 48px;
    letter-spacing: 3px;
    line-height: 1;
    margin-bottom: 6px;
  }
  .winner-name.x { color: var(--x-color); text-shadow: 0 0 30px rgba(245,166,35,.4); }
  .winner-name.o { color: var(--o-color); text-shadow: 0 0 30px rgba(79,195,247,.4); }
  .winner-name.draw { color: var(--text); }

  .winner-sub {
    font-size: 13px;
    color: var(--muted);
    margin-bottom: 32px;
  }

  .winner-actions {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  .btn-play-again {
    padding: 15px;
    border: none;
    border-radius: 12px;
    font-family: 'Bebas Neue', sans-serif;
    font-size: 20px;
    letter-spacing: 2px;
    cursor: pointer;
    transition: transform .15s, opacity .15s;
  }
  .btn-play-again:hover { transform: translateY(-2px); opacity: .9; }
  .btn-play-again.x { background: linear-gradient(135deg, var(--x-color), #e8940f); color: #0d0d0f; }
  .btn-play-again.o { background: linear-gradient(135deg, var(--o-color), #2ba8d8); color: #0d0d0f; }
  .btn-play-again.draw { background: linear-gradient(135deg, #5a5a7a, #3a3a55); color: var(--text); }

  .btn-new-game {
    padding: 13px;
    background: none;
    border: 1px solid var(--border);
    border-radius: 12px;
    color: var(--muted);
    font-family: 'DM Sans', sans-serif;
    font-size: 13px;
    font-weight: 600;
    letter-spacing: 1px;
    text-transform: uppercase;
    cursor: pointer;
    transition: background .15s, color .15s;
  }
  .btn-new-game:hover { background: var(--surface2); color: var(--text); }

  /* Confetti */
  .confetti-piece {
    position: fixed;
    width: 8px;
    height: 8px;
    border-radius: 2px;
    pointer-events: none;
    z-index: 200;
    animation: confettiFall linear forwards;
  }
  @keyframes confettiFall {
    0%   { transform: translateY(-20px) rotate(0deg); opacity: 1; }
    100% { transform: translateY(100vh) rotate(720deg); opacity: 0; }
  }

  @keyframes fadeIn {
    from { opacity: 0; transform: translateY(10px); }
    to   { opacity: 1; transform: translateY(0); }
  }

  /* Draws badge */
  .draws-badge {
    text-align: center;
    font-size: 11px;
    color: var(--muted);
    letter-spacing: 1px;
    text-transform: uppercase;
    margin-top: -8px;
    margin-bottom: 10px;
  }
  .draws-badge span { font-weight: 700; color: #5a5a7a; }
</style>
</head>
<body>

<div class="app">

  <!-- Setup Screen -->
  <div id="setup-screen">
    <div class="logo">TIC TAC TOE 
    niki
    </div>
    <div class="tagline">Enter Player Names to Begin</div>

    <div class="player-inputs">
      <div class="input-group p1">
        <span class="player-badge">X</span>
        <input type="text" id="p1-name" placeholder="Player One" maxlength="16" autocomplete="off">
      </div>
      <div class="input-group p2">
        <span class="player-badge">O</span>
        <input type="text" id="p2-name" placeholder="Player Two" maxlength="16" autocomplete="off">
      </div>
    </div>

    <button class="btn-start" onclick="startGame()">START GAME</button>
  </div>

  <!-- Game Screen -->
  <div id="game-screen">
    <div class="scoreboard">
      <div class="player-info px">
        <div class="name" id="p1-display">Player X</div>
        <div class="score" id="score-x">0</div>
      </div>
      <div class="score-divider">VS</div>
      <div class="player-info po">
        <div class="name" id="p2-display">Player O</div>
        <div class="score" id="score-o">0</div>
      </div>
    </div>

    <div class="draws-badge">Draws: <span id="draws-count">0</span></div>

    <div class="turn-bar">
      <span id="turn-text">Turn:</span>
      <span class="turn-name" id="turn-name">—</span>
    </div>

    <div class="board" id="board"></div>

    <div class="action-row">
      <button class="btn-secondary" onclick="resetBoard()">↺ Restart</button>
      <button class="btn-secondary" onclick="backToSetup()">✎ New Players</button>
    </div>
  </div>

</div>

<!-- Winner Overlay -->
<div id="winner-overlay">
  <div class="winner-card">
    <span class="winner-emoji" id="winner-emoji">🏆</span>
    <div class="winner-label" id="winner-label">Winner</div>
    <div class="winner-name" id="winner-name-display">Player</div>
    <div class="winner-sub" id="winner-sub">Congratulations!</div>
    <div class="winner-actions">
      <button class="btn-play-again" id="btn-play-again" onclick="playAgain()">PLAY AGAIN</button>
      <button class="btn-new-game" onclick="backToSetup()">Change Players</button>
    </div>
  </div>
</div>

<script>
  const WIN_COMBOS = [
    [0,1,2],[3,4,5],[6,7,8],
    [0,3,6],[1,4,7],[2,5,8],
    [0,4,8],[2,4,6]
  ];

  let board = Array(9).fill(null);
  let current = 'X';
  let gameOver = false;
  let scores = { X: 0, O: 0, D: 0 };
  let names = { X: 'Player X', O: 'Player O' };

  function startGame() {
    const p1 = document.getElementById('p1-name').value.trim() || 'Player X';
    const p2 = document.getElementById('p2-name').value.trim() || 'Player O';
    names.X = p1;
    names.O = p2;
    scores = { X: 0, O: 0, D: 0 };
    document.getElementById('p1-display').textContent = p1;
    document.getElementById('p2-display').textContent = p2;
    document.getElementById('score-x').textContent = '0';
    document.getElementById('score-o').textContent = '0';
    document.getElementById('draws-count').textContent = '0';
    document.getElementById('setup-screen').style.display = 'none';
    document.getElementById('game-screen').style.display = 'block';
    resetBoard();
  }

  function resetBoard() {
    board = Array(9).fill(null);
    current = 'X';
    gameOver = false;
    renderBoard();
    updateTurnUI();
  }

  function renderBoard() {
    const el = document.getElementById('board');
    el.innerHTML = '';
    board.forEach((val, i) => {
      const cell = document.createElement('div');
      cell.className = 'cell' + (val ? ' taken' : '');
      cell.onclick = () => handleClick(i);
      if (val === 'X') {
        cell.innerHTML = `<div class="x-mark"><svg viewBox="0 0 40 40" fill="none" stroke-width="5" stroke-linecap="round"><line x1="6" y1="6" x2="34" y2="34"/><line x1="34" y1="6" x2="6" y2="34"/></svg></div>`;
      } else if (val === 'O') {
        cell.innerHTML = `<div class="o-mark"><svg viewBox="0 0 40 40" fill="none" stroke-width="5" stroke-linecap="round"><circle cx="20" cy="20" r="13"/></svg></div>`;
      }
      el.appendChild(cell);
    });
  }

  function handleClick(idx) {
    if (gameOver || board[idx]) return;
    board[idx] = current;
    renderBoard();

    const win = checkWin();
    if (win) {
      gameOver = true;
      highlightWin(win);
      setTimeout(() => showWinner(current, win), 500);
      return;
    }
    if (board.every(v => v)) {
      gameOver = true;
      setTimeout(() => showDraw(), 400);
      return;
    }
    current = current === 'X' ? 'O' : 'X';
    updateTurnUI();
  }

  function checkWin() {
    for (const combo of WIN_COMBOS) {
      const [a,b,c] = combo;
      if (board[a] && board[a] === board[b] && board[b] === board[c]) return combo;
    }
    return null;
  }

  function highlightWin(combo) {
    const cells = document.querySelectorAll('.cell');
    combo.forEach(i => {
      cells[i].classList.add('win-cell', current === 'X' ? 'x-win' : 'o-win');
    });
  }

  function updateTurnUI() {
    const tn = document.getElementById('turn-name');
    tn.textContent = names[current];
    tn.className = 'turn-name ' + current.toLowerCase();
  }

  function showWinner(player, _combo) {
    scores[player]++;
    document.getElementById('score-x').textContent = scores.X;
    document.getElementById('score-o').textContent = scores.O;
    const overlay = document.getElementById('winner-overlay');
    const nameEl = document.getElementById('winner-name-display');
    const label  = document.getElementById('winner-label');
    const sub    = document.getElementById('winner-sub');
    const emoji  = document.getElementById('winner-emoji');
    const btn    = document.getElementById('btn-play-again');
    nameEl.textContent = names[player];
    nameEl.className   = 'winner-name ' + player.toLowerCase();
    label.textContent  = '🏆 Winner';
    sub.textContent    = player === 'X'
      ? `${names.X} wins this round!`
      : `${names.O} wins this round!`;
    emoji.textContent = '🏆';
    btn.className = 'btn-play-again ' + player.toLowerCase();
    overlay.classList.add('show');
    spawnConfetti(player === 'X' ? '#f5a623' : '#4fc3f7');
  }

  function showDraw() {
    scores.D++;
    document.getElementById('draws-count').textContent = scores.D;
    const overlay = document.getElementById('winner-overlay');
    document.getElementById('winner-name-display').textContent = "It's a Draw";
    document.getElementById('winner-name-display').className = 'winner-name draw';
    document.getElementById('winner-label').textContent = '🤝 No winner';
    document.getElementById('winner-sub').textContent = 'Great game! Play again?';
    document.getElementById('winner-emoji').textContent = '🤝';
    document.getElementById('btn-play-again').className = 'btn-play-again draw';
    overlay.classList.add('show');
  }

  function playAgain() {
    document.getElementById('winner-overlay').classList.remove('show');
    resetBoard();
  }

  function backToSetup() {
    document.getElementById('winner-overlay').classList.remove('show');
    document.getElementById('game-screen').style.display = 'none';
    document.getElementById('setup-screen').style.display = 'block';
  }

  function spawnConfetti(color) {
    const colors = [color, '#ffffff', '#ffdd44', '#ff6699', '#aaffaa'];
    for (let i = 0; i < 60; i++) {
      const piece = document.createElement('div');
      piece.className = 'confetti-piece';
      piece.style.cssText = `
        left: ${Math.random()*100}vw;
        background: ${colors[Math.floor(Math.random()*colors.length)]};
        animation-duration: ${1.2 + Math.random()*1.6}s;
        animation-delay: ${Math.random()*.6}s;
        width: ${6 + Math.random()*8}px;
        height: ${6 + Math.random()*8}px;
      `;
      document.body.appendChild(piece);
      piece.addEventListener('animationend', () => piece.remove());
    }
  }
</script>
</body>
</html>
