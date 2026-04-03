<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>TSI Strategy Trainer</title>
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #f6f7f9;
    --surface: #ffffff;
    --surface-alt: #f0f1f4;
    --text: #1a1d23;
    --text-muted: #6b7084;
    --border: #e2e4ea;
    --accent: #4361ee;
    --accent-light: #eef0ff;
    --success: #10b981;
    --success-light: #ecfdf5;
    --warning: #f59e0b;
    --warning-light: #fffbeb;
    --error: #ef4444;
    --error-light: #fef2f2;
    --qr: #4361ee;
    --ar: #8b5cf6;
    --gms: #06b6d4;
    --pr: #f97316;
    --radius: 12px;
    --shadow: 0 1px 3px rgba(0,0,0,0.06), 0 1px 2px rgba(0,0,0,0.04);
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    font-family: 'DM Sans', sans-serif;
    background: var(--bg);
    color: var(--text);
    line-height: 1.6;
    min-height: 100vh;
  }

  .container {
    max-width: 720px;
    margin: 0 auto;
    padding: 20px 16px;
  }

  .header { text-align: center; margin-bottom: 24px; }
  .header h1 { font-size: 1.5rem; font-weight: 700; letter-spacing: -0.02em; }
  .header p { color: var(--text-muted); font-size: 0.9rem; margin-top: 4px; }

  /* STATS BAR */
  .stats-bar { display: flex; gap: 8px; margin-bottom: 20px; flex-wrap: wrap; }
  .stat-chip {
    background: var(--surface); border: 1px solid var(--border); border-radius: 8px;
    padding: 8px 14px; font-size: 0.82rem; font-weight: 500;
    display: flex; align-items: center; gap: 6px; flex: 1; min-width: 100px; justify-content: center;
  }
  .stat-chip .num { font-family: 'JetBrains Mono', monospace; font-weight: 600; font-size: 1rem; }

  /* CATEGORY FILTER */
  .filter-row { display: flex; gap: 6px; margin-bottom: 20px; flex-wrap: wrap; }
  .filter-btn {
    border: 1.5px solid var(--border); background: var(--surface); border-radius: 8px;
    padding: 6px 14px; font-size: 0.8rem; font-weight: 600; cursor: pointer;
    transition: all 0.15s ease; font-family: inherit; color: var(--text-muted);
  }
  .filter-btn:hover { border-color: var(--accent); color: var(--accent); }
  .filter-btn.active { background: var(--accent); color: #fff; border-color: var(--accent); }
  .filter-btn[data-cat="QR"].active { background: var(--qr); border-color: var(--qr); }
  .filter-btn[data-cat="AR"].active { background: var(--ar); border-color: var(--ar); }
  .filter-btn[data-cat="GMS"].active { background: var(--gms); border-color: var(--gms); }
  .filter-btn[data-cat="PR"].active { background: var(--pr); border-color: var(--pr); }

  .card {
    background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius);
    box-shadow: var(--shadow); padding: 24px; margin-bottom: 16px;
  }

  .problem-meta { display: flex; justify-content: space-between; align-items: center; margin-bottom: 16px; }
  .category-tag {
    font-size: 0.75rem; font-weight: 700; text-transform: uppercase;
    letter-spacing: 0.06em; padding: 4px 10px; border-radius: 6px; color: #fff;
  }
  .category-tag.QR { background: var(--qr); }
  .category-tag.AR { background: var(--ar); }
  .category-tag.GMS { background: var(--gms); }
  .category-tag.PR { background: var(--pr); }

  .timer-display {
    font-family: 'JetBrains Mono', monospace; font-size: 1.1rem; font-weight: 600;
    color: var(--text-muted); display: flex; align-items: center; gap: 6px;
  }
  .timer-display.caution { color: var(--warning); }
  .timer-display.slow { color: var(--error); }

  .topic-label { font-size: 0.82rem; color: var(--text-muted); margin-bottom: 12px; font-weight: 500; }

  .problem-text { font-size: 1.05rem; line-height: 1.7; margin-bottom: 20px; white-space: pre-wrap; }

  .step-label {
    font-size: 0.78rem; font-weight: 700; text-transform: uppercase;
    letter-spacing: 0.08em; color: var(--accent); margin-bottom: 10px;
  }

  .strategy-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; margin-bottom: 8px; }
  .strategy-btn {
    border: 2px solid var(--border); background: var(--surface); border-radius: 10px;
    padding: 12px 14px; text-align: left; cursor: pointer; transition: all 0.15s ease; font-family: inherit;
  }
  .strategy-btn:hover { border-color: var(--accent); background: var(--accent-light); }
  .strategy-btn.selected { border-color: var(--accent); background: var(--accent-light); }
  .strategy-btn .strat-name { font-weight: 600; font-size: 0.88rem; display: block; margin-bottom: 2px; }
  .strategy-btn .strat-desc { font-size: 0.75rem; color: var(--text-muted); line-height: 1.3; }

  .choices { display: flex; flex-direction: column; gap: 8px; }
  .choice-btn {
    border: 2px solid var(--border); background: var(--surface); border-radius: 10px;
    padding: 14px 16px; text-align: left; cursor: pointer; transition: all 0.15s ease;
    font-family: inherit; font-size: 0.95rem; display: flex; align-items: center; gap: 12px;
  }
  .choice-btn:hover { border-color: var(--accent); background: var(--accent-light); }
  .choice-btn.selected { border-color: var(--accent); background: var(--accent-light); }
  .choice-btn.correct { border-color: var(--success); background: var(--success-light); }
  .choice-btn.incorrect { border-color: var(--error); background: var(--error-light); }
  .choice-letter {
    width: 28px; height: 28px; border-radius: 50%; background: var(--surface-alt);
    display: flex; align-items: center; justify-content: center; font-weight: 700; font-size: 0.82rem; flex-shrink: 0;
  }
  .choice-btn.correct .choice-letter { background: var(--success); color: #fff; }
  .choice-btn.incorrect .choice-letter { background: var(--error); color: #fff; }

  .btn-primary {
    background: var(--accent); color: #fff; border: none; border-radius: 10px;
    padding: 14px 28px; font-size: 0.95rem; font-weight: 600; cursor: pointer;
    font-family: inherit; transition: all 0.15s ease; width: 100%;
  }
  .btn-primary:hover { filter: brightness(1.08); transform: translateY(-1px); }
  .btn-primary:disabled { opacity: 0.4; cursor: default; transform: none; }

  .btn-secondary {
    background: var(--surface); color: var(--text); border: 2px solid var(--border);
    border-radius: 10px; padding: 12px 24px; font-size: 0.9rem; font-weight: 600;
    cursor: pointer; font-family: inherit; transition: all 0.15s ease;
  }
  .btn-secondary:hover { border-color: var(--accent); color: var(--accent); }

  /* FEEDBACK */
  .feedback-card { border-radius: var(--radius); padding: 20px; margin-top: 16px; border: 1.5px solid; }
  .feedback-card.correct { background: var(--success-light); border-color: var(--success); }
  .feedback-card.incorrect { background: var(--error-light); border-color: var(--error); }
  .feedback-card h3 { font-size: 1rem; margin-bottom: 8px; display: flex; align-items: center; gap: 8px; }
  .feedback-card p { font-size: 0.88rem; line-height: 1.6; color: var(--text); }

  /* FAST PATH BOX */
  .fast-path {
    margin-top: 14px; padding: 14px; border-radius: 10px;
    background: #f0f4ff; border: 1.5px solid #c7d4ff;
  }
  .fast-path-label {
    font-size: 0.72rem; font-weight: 700; text-transform: uppercase; letter-spacing: 0.08em;
    color: var(--accent); margin-bottom: 6px; display: flex; align-items: center; gap: 5px;
  }
  .fast-path p { font-size: 0.85rem; line-height: 1.6; color: var(--text); }
  .fast-path .compare {
    display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-top: 10px;
  }
  .fast-path .path-option {
    padding: 10px; border-radius: 8px; font-size: 0.8rem; line-height: 1.5;
  }
  .fast-path .path-slow { background: #fff3f3; border: 1px solid #fecaca; }
  .fast-path .path-fast { background: #ecfdf5; border: 1px solid #a7f3d0; }
  .fast-path .path-label {
    font-weight: 700; font-size: 0.7rem; text-transform: uppercase; letter-spacing: 0.06em;
    margin-bottom: 4px;
  }
  .fast-path .path-slow .path-label { color: var(--error); }
  .fast-path .path-fast .path-label { color: var(--success); }

  .strategy-note {
    margin-top: 12px; padding-top: 12px; border-top: 1px solid rgba(0,0,0,0.08);
    font-size: 0.85rem; color: var(--text-muted);
  }
  .strategy-note strong { color: var(--text); }

  .time-note {
    font-size: 0.82rem; color: var(--text-muted); font-family: 'JetBrains Mono', monospace;
  }

  /* WELCOME */
  .welcome { text-align: center; padding: 40px 20px; }
  .welcome h2 { font-size: 1.3rem; margin-bottom: 12px; }
  .welcome p { color: var(--text-muted); margin-bottom: 24px; max-width: 480px; margin-left: auto; margin-right: auto; font-size: 0.92rem; }
  .welcome .how-it-works { text-align: left; max-width: 440px; margin: 0 auto 28px; }
  .welcome .step-item { display: flex; gap: 12px; margin-bottom: 14px; align-items: flex-start; }
  .welcome .step-num {
    width: 28px; height: 28px; border-radius: 50%; background: var(--accent); color: #fff;
    display: flex; align-items: center; justify-content: center; font-weight: 700;
    font-size: 0.8rem; flex-shrink: 0; margin-top: 2px;
  }
  .welcome .step-text { font-size: 0.88rem; line-height: 1.5; }
  .welcome .step-text strong { display: block; margin-bottom: 1px; }

  .phase { display: none; }
  .phase.active { display: block; animation: fadeIn 0.2s ease; }
  @keyframes fadeIn { from { opacity: 0; transform: translateY(6px); } to { opacity: 1; transform: translateY(0); } }

  .progress-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin-top: 16px; }
  .progress-item { background: var(--surface-alt); border-radius: 8px; padding: 12px; }
  .progress-item .cat-name {
    font-size: 0.78rem; font-weight: 700; text-transform: uppercase; letter-spacing: 0.06em; margin-bottom: 6px;
  }
  .progress-item .cat-name.QR { color: var(--qr); }
  .progress-item .cat-name.AR { color: var(--ar); }
  .progress-item .cat-name.GMS { color: var(--gms); }
  .progress-item .cat-name.PR { color: var(--pr); }
  .progress-bar { height: 6px; background: var(--border); border-radius: 3px; overflow: hidden; }
  .progress-fill { height: 100%; border-radius: 3px; transition: width 0.4s ease; }
  .progress-fill.QR { background: var(--qr); }
  .progress-fill.AR { background: var(--ar); }
  .progress-fill.GMS { background: var(--gms); }
  .progress-fill.PR { background: var(--pr); }
  .progress-label { font-size: 0.78rem; color: var(--text-muted); margin-top: 4px; font-family: 'JetBrains Mono', monospace; }

  .btn-row { display: flex; gap: 8px; margin-top: 16px; }
  .btn-row .btn-primary, .btn-row .btn-secondary { flex: 1; }

  @media (max-width: 500px) {
    .strategy-grid { grid-template-columns: 1fr; }
    .stats-bar { gap: 6px; }
    .stat-chip { padding: 6px 10px; font-size: 0.78rem; min-width: 80px; }
    .stat-chip .num { font-size: 0.9rem; }
    .filter-row { gap: 4px; }
    .filter-btn { padding: 5px 10px; font-size: 0.75rem; }
    .card { padding: 18px; }
    .progress-grid { grid-template-columns: 1fr; }
    .fast-path .compare { grid-template-columns: 1fr; }
  }
  sup { font-size: 0.7em; }
</style>
</head>
<body>
<div class="container">
  <div class="header">
    <h1>TSI Strategy Trainer</h1>
    <p>Learn when to solve it straight — and when there's a faster way.</p>
  </div>

  <div class="stats-bar">
    <div class="stat-chip"><span>Streak</span> <span class="num" id="streak">0</span></div>
    <div class="stat-chip"><span>Correct</span> <span class="num" id="totalCorrect">0</span></div>
    <div class="stat-chip"><span>Total</span> <span class="num" id="totalAttempted">0</span></div>
    <div class="stat-chip"><span>Avg Time</span> <span class="num" id="avgTime">--</span></div>
  </div>

  <div class="filter-row">
    <button class="filter-btn active" data-cat="ALL" onclick="setFilter('ALL')">All</button>
    <button class="filter-btn" data-cat="QR" onclick="setFilter('QR')">Quantitative</button>
    <button class="filter-btn" data-cat="AR" onclick="setFilter('AR')">Algebraic</button>
    <button class="filter-btn" data-cat="GMS" onclick="setFilter('GMS')">Geo/Measure</button>
    <button class="filter-btn" data-cat="PR" onclick="setFilter('PR')">Probabilistic</button>
  </div>

  <div id="content">
    <!-- WELCOME -->
    <div id="phase-welcome" class="phase active">
      <div class="card welcome">
        <h2>Build Smarter Test Instincts</h2>
        <p>Most TSI problems CAN be solved directly — but sometimes there's a shortcut that saves you a minute. This trainer helps you spot those moments.</p>
        <div class="how-it-works">
          <div class="step-item">
            <div class="step-num">1</div>
            <div class="step-text"><strong>Read the problem & the choices</strong>Look at the answers BEFORE you start solving. They're clues.</div>
          </div>
          <div class="step-item">
            <div class="step-num">2</div>
            <div class="step-text"><strong>Pick how you'll attack it</strong>Direct solve? Plug in answers? Eliminate? Estimate? Back-solve?</div>
          </div>
          <div class="step-item">
            <div class="step-num">3</div>
            <div class="step-text"><strong>Solve, then see the fast path</strong>After you answer, we'll show the quickest way — even if you got it right, you might learn a faster route.</div>
          </div>
        </div>
        <button class="btn-primary" onclick="startTraining()">Let's Go</button>
      </div>
    </div>

    <!-- STRATEGY PHASE -->
    <div id="phase-strategy" class="phase">
      <div class="card">
        <div class="problem-meta">
          <span class="category-tag" id="catTag"></span>
          <div class="timer-display" id="timer">⏱ 0:00</div>
        </div>
        <div class="topic-label" id="topicLabel"></div>
        <div class="problem-text" id="problemText"></div>
        <div class="choices" id="previewChoices"></div>
      </div>
      <div class="card">
        <div class="step-label">Step 1 — How will you attack this?</div>
        <div class="strategy-grid" id="strategyGrid"></div>
        <button class="btn-primary" id="confirmStrategy" onclick="confirmStrategy()" disabled>Lock In Strategy →</button>
      </div>
    </div>

    <!-- SOLVE PHASE -->
    <div id="phase-solve" class="phase">
      <div class="card">
        <div class="problem-meta">
          <span class="category-tag" id="catTag2"></span>
          <div class="timer-display" id="timer2">⏱ 0:00</div>
        </div>
        <div class="topic-label" id="topicLabel2"></div>
        <div class="problem-text" id="problemText2"></div>
        <div class="step-label">Step 2 — Solve It</div>
        <div style="font-size:0.82rem;color:var(--text-muted);margin-bottom:12px;">Your strategy: <strong id="chosenStratLabel"></strong></div>
        <div class="choices" id="answerChoices"></div>
        <button class="btn-primary" id="submitAnswer" onclick="submitAnswer()" disabled style="margin-top:12px;">Submit Answer</button>
      </div>
    </div>

    <!-- FEEDBACK PHASE -->
    <div id="phase-feedback" class="phase">
      <div class="card">
        <div class="problem-meta">
          <span class="category-tag" id="catTag3"></span>
          <div class="time-note" id="finalTime"></div>
        </div>
        <div class="topic-label" id="topicLabel3"></div>
        <div class="problem-text" id="problemText3"></div>
        <div class="choices" id="reviewChoices"></div>

        <div class="feedback-card" id="feedbackCard">
          <h3 id="feedbackTitle"></h3>
          <p id="feedbackExplain"></p>
        </div>

        <div class="fast-path" id="fastPathBox">
          <div class="fast-path-label">⚡ Strategy Breakdown</div>
          <div id="fastPathContent"></div>
        </div>

        <div class="btn-row">
          <button class="btn-secondary" onclick="showProgress()">Progress</button>
          <button class="btn-primary" onclick="nextProblem()">Next Problem →</button>
        </div>
      </div>
    </div>

    <!-- PROGRESS -->
    <div id="phase-progress" class="phase">
      <div class="card">
        <h3 style="margin-bottom:4px;">Your Progress</h3>
        <p style="font-size:0.85rem;color:var(--text-muted);margin-bottom:16px;">Accuracy by CRC category</p>
        <div class="progress-grid" id="progressGrid"></div>
        <div style="margin-top:16px;padding:14px;background:var(--surface-alt);border-radius:10px;">
          <div style="font-size:0.78rem;font-weight:700;text-transform:uppercase;letter-spacing:0.06em;color:var(--accent);margin-bottom:6px;">Strategy Usage</div>
          <div id="stratUsage" style="font-size:0.85rem;color:var(--text-muted);line-height:1.7;"></div>
        </div>
        <div class="btn-row">
          <button class="btn-primary" onclick="nextProblem()">Keep Practicing →</button>
        </div>
      </div>
    </div>
  </div>
</div>

<script>
const STRATEGIES = [
  { id: 'eliminate', name: 'Eliminate', desc: 'Rule out wrong answers first' },
  { id: 'plugin', name: 'Plug In', desc: 'Test the answer choices or pick easy numbers' },
  { id: 'backsolve', name: 'Back-Solve', desc: 'Start from the answers, work backward' },
  { id: 'estimate', name: 'Estimate', desc: 'Approximate to narrow it down fast' },
  { id: 'direct', name: 'Direct Solve', desc: 'Set up equations and solve' }
];

// ═══════════════════════════════════════════
// QUESTION BANK v2 — DESIGNED FOR STRATEGY VARIETY
// Each question has:
//   fastStrat: the FASTEST strategy for this problem
//   fastPath: step-by-step of the fast approach
//   slowPath: what direct solve looks like (to contrast)
//   directOk: true if direct solve is perfectly fine (no real shortcut)
// ═══════════════════════════════════════════

const QUESTIONS = [

// ─── BACKSOLVE PROBLEMS ───
// These are MUCH faster when you plug the answer choices back in

{
  cat: 'AR', topic: 'Linear Equations',
  text: 'Solve for x:\n  4(x + 3) − 2x = 3x − 1',
  choices: ['11', '−13', '13', '−11'],
  answer: 2,
  fastStrat: 'backsolve',
  directOk: false,
  fastPath: '<p>Try C: x = 13</p><p>Left: 4(16) − 26 = 64 − 26 = 38</p><p>Right: 3(13) − 1 = 38 ✓</p><p>Done in ~15 seconds. No algebra needed.</p>',
  slowPath: '<p>Distribute: 4x + 12 − 2x = 3x − 1</p><p>Combine: 2x + 12 = 3x − 1</p><p>Subtract 2x: 12 = x − 1</p><p>x = 13</p><p>Works, but more steps = more places for sign errors.</p>',
  explain: 'x = 13. Back-solving is faster because you skip the algebra entirely — just substitute and check.'
},
{
  cat: 'AR', topic: 'Quadratic Equations',
  text: 'Which value of x satisfies  x² + 3x − 28 = 0 ?',
  choices: ['7', '4', '−7', '−4'],
  answer: 1,
  fastStrat: 'backsolve',
  directOk: false,
  fastPath: '<p>Try B: x = 4</p><p>16 + 12 − 28 = 0 ✓</p><p>First try. Done.</p>',
  slowPath: '<p>Factor x² + 3x − 28...</p><p>Need two numbers that multiply to −28 and add to 3...</p><p>(x + 7)(x − 4) = 0</p><p>x = −7 or x = 4</p><p>Factoring works but requires mental search for the right pair.</p>',
  explain: 'Just plug 4 into the equation: 16 + 12 − 28 = 0. When answer choices are simple numbers, try them before factoring.'
},
{
  cat: 'QR', topic: 'Ratios & Percents',
  text: 'A number increased by 20% equals 96. What is the original number?',
  choices: ['76.8', '84', '80', '90'],
  answer: 2,
  fastStrat: 'backsolve',
  directOk: false,
  fastPath: '<p>Try C: 80</p><p>80 × 1.20 = 96 ✓</p><p>Done. One multiplication.</p>',
  slowPath: '<p>Let n = original number</p><p>1.20n = 96</p><p>n = 96 ÷ 1.20 = 80</p><p>Division by 1.20 is awkward mental math.</p>',
  explain: 'Multiplying forward (80 × 1.20) is way easier than dividing backward (96 ÷ 1.20). That\'s why back-solving wins here.'
},
{
  cat: 'AR', topic: 'Systems of Equations',
  text: 'A theater sold 200 tickets: adult tickets at $8 and child tickets at $5. Total revenue was $1,300. How many adult tickets were sold?',
  choices: ['100', '80', '120', '150'],
  answer: 0,
  fastStrat: 'backsolve',
  directOk: false,
  fastPath: '<p>Try A: 100 adults → 100 children</p><p>100(8) + 100(5) = 800 + 500 = $1,300 ✓</p><p>One check. Done.</p>',
  slowPath: '<p>Let a = adults, c = children</p><p>a + c = 200</p><p>8a + 5c = 1300</p><p>Substitute c = 200 − a</p><p>8a + 5(200 − a) = 1300</p><p>8a + 1000 − 5a = 1300</p><p>3a = 300, a = 100</p><p>Six steps of algebra vs. one multiplication check.</p>',
  explain: 'Word problems with nice answer choices are back-solve gold. Try each answer — you skip the entire system setup.'
},
{
  cat: 'AR', topic: 'Linear Equations',
  text: 'If  (2x + 5)/3 = 9, what is x?',
  choices: ['6', '11', '12', '7'],
  answer: 1,
  fastStrat: 'backsolve',
  directOk: true,
  fastPath: '<p>Try B: x = 11</p><p>(22 + 5)/3 = 27/3 = 9 ✓</p><p>Instant.</p>',
  slowPath: '<p>Multiply both sides by 3: 2x + 5 = 27</p><p>Subtract 5: 2x = 22</p><p>Divide: x = 11</p><p>Straightforward, but back-solve is still faster.</p>',
  explain: 'Direct solve is fine here, but back-solving is just one substitution. When the equation has fractions, plugging in avoids dealing with them.'
},
{
  cat: 'QR', topic: 'Ratios & Percents',
  text: 'After a 15% discount, a laptop costs $680. What was the original price?',
  choices: ['$800', '$782', '$750', '$720'],
  answer: 0,
  fastStrat: 'backsolve',
  directOk: false,
  fastPath: '<p>Try A: $800</p><p>15% of 800 = $120</p><p>800 − 120 = $680 ✓</p><p>Done. Clean numbers, easy check.</p>',
  slowPath: '<p>0.85p = 680</p><p>p = 680/0.85 = ?</p><p>That division is rough without a calculator.</p>',
  explain: 'Dividing by 0.85 is painful. Multiplying to check a nice answer choice is easy. Always scan the choices first on percent problems.'
},

// ─── PLUG IN PROBLEMS ───
// These reward substituting your own easy numbers

{
  cat: 'AR', topic: 'Expressions & Polynomials',
  text: 'Which expression is equivalent to  (x² − 16)/(x − 4)  for x ≠ 4?',
  choices: ['x + 16', 'x − 4', 'x + 4', 'x² − 4'],
  answer: 2,
  fastStrat: 'plugin',
  directOk: true,
  fastPath: '<p>Pick x = 5 (easy, not 4):</p><p>Original: (25 − 16)/(5 − 4) = 9/1 = 9</p><p>C: 5 + 4 = 9 ✓</p><p>B: 5 − 4 = 1 ✗</p><p>Done without factoring.</p>',
  slowPath: '<p>Factor numerator: (x+4)(x−4)/(x−4)</p><p>Cancel: x + 4</p><p>Requires recognizing difference of squares.</p>',
  explain: 'Plug in a number for x and evaluate both the original and each answer choice. Whichever answer matches is correct — no factoring needed.'
},
{
  cat: 'AR', topic: 'Expressions & Polynomials',
  text: 'If a = 3 and b = −2, what is the value of  2a²b − 3ab² ?',
  choices: ['−18', '0', '72', '−72'],
  answer: 3,
  fastStrat: 'plugin',
  directOk: true,
  fastPath: '<p>Just substitute carefully:</p><p>2(9)(−2) − 3(3)(4)</p><p>= −36 − 36 = −72</p><p>This IS direct substitution — the strategy here is going slow and watching signs.</p>',
  slowPath: '<p>Same process — no shortcut exists. The trap is rushing the signs.</p>',
  explain: 'This is a pure substitution problem. The "strategy" is discipline: write out each piece separately, don\'t combine in your head, and double-check the signs.'
},
{
  cat: 'AR', topic: 'Expressions & Polynomials',
  text: 'Which is equivalent to  (3x + 2)² ?',
  choices: ['9x² + 4', '9x² + 12x + 4', '9x² + 6x + 4', '3x² + 12x + 4'],
  answer: 1,
  fastStrat: 'plugin',
  directOk: true,
  fastPath: '<p>Let x = 1:</p><p>Original: (3 + 2)² = 25</p><p>B: 9 + 12 + 4 = 25 ✓</p><p>A: 9 + 4 = 13 ✗</p><p>C: 9 + 6 + 4 = 19 ✗</p><p>B wins without FOILing.</p>',
  slowPath: '<p>FOIL: (3x+2)(3x+2) = 9x² + 6x + 6x + 4 = 9x² + 12x + 4</p><p>Works, but the middle term is where most students mess up.</p>',
  explain: 'Plugging in x = 1 turns everything into arithmetic. You don\'t even need to know the binomial square formula. Try it on ANY "which is equivalent" problem.'
},
{
  cat: 'AR', topic: 'Exponents & Roots',
  text: 'Which is equivalent to  x^(−2/3)?',
  choices: ['1 / ∛(x²)', '∛(x²)', '−∛(x²)', '1 / x²'],
  answer: 0,
  fastStrat: 'plugin',
  directOk: false,
  fastPath: '<p>Let x = 8:</p><p>8^(−2/3) = 1/(8^(2/3)) = 1/(∛8)² = 1/4</p><p>A: 1/∛(64) = 1/4 ✓</p><p>B: ∛64 = 4 ✗</p><p>Done. No exponent rules needed.</p>',
  slowPath: '<p>Negative exponent → reciprocal</p><p>Fractional exponent → root and power</p><p>x^(−2/3) = 1/(x^(2/3)) = 1/∛(x²)</p><p>Requires knowing multiple exponent rules.</p>',
  explain: 'Negative fractional exponents confuse everyone. Plugging in x = 8 (a perfect cube) turns it into basic arithmetic. Let the numbers do the work.'
},

// ─── ELIMINATE PROBLEMS ───
// These have choices you can rule out without fully solving

{
  cat: 'QR', topic: 'Number Concepts',
  text: 'Which of the following is an irrational number?',
  choices: ['√49', '0.75', '√10', '−3/8'],
  answer: 2,
  fastStrat: 'eliminate',
  directOk: true,
  fastPath: '<p>Scan each choice:</p><p>A: √49 = 7 (integer) → rational ✗</p><p>B: 0.75 = 3/4 → rational ✗</p><p>D: already a fraction → rational ✗</p><p>C: √10... 10 isn\'t a perfect square → irrational ✓</p><p>Three instant eliminations.</p>',
  slowPath: '<p>Define irrational, test each... same result but without the systematic elimination mindset, students second-guess √10.</p>',
  explain: 'Elimination is the power move on "which of the following" questions. Cross off what you KNOW is wrong. What\'s left is your answer, even if you\'re not 100% sure why.'
},
{
  cat: 'GMS', topic: 'Perimeter, Area & Volume',
  text: 'A cylinder has radius 5 cm and height 12 cm. What is its volume? (Use π ≈ 3.14)',
  choices: ['942 cm³', '188.4 cm³', '376.8 cm³', '94.2 cm³'],
  answer: 0,
  fastStrat: 'estimate',
  directOk: true,
  fastPath: '<p>Quick estimate: V = π × 25 × 12</p><p>π × 25 ≈ 75-ish</p><p>75 × 12 = 900-ish</p><p>Only A is near 900. Done.</p>',
  slowPath: '<p>V = π(5²)(12) = 3.14 × 25 × 12 = 3.14 × 300 = 942</p><p>Same answer but requires precise multiplication.</p>',
  explain: 'Before multiplying anything precisely, estimate. π × 25 × 12 is roughly 75 × 12 ≈ 900. Only one answer is close. Save your precise arithmetic for when choices are close together.'
},
{
  cat: 'PR', topic: 'Statistical Measures',
  text: 'A class of 30 students has a mean test score of 76. One student who scored 98 transfers out. What is the new mean? (Round to the nearest tenth.)',
  choices: ['73.5', '74.0', '76.8', '75.2'],
  answer: 3,
  fastStrat: 'eliminate',
  directOk: true,
  fastPath: '<p>Think: removing a score ABOVE the mean will pull the mean DOWN.</p><p>Eliminate C (higher than 76).</p><p>The student scored 22 above the mean. Spread across 29 remaining students, the drop is small: 22/29 ≈ 0.76.</p><p>76 − 0.76 ≈ 75.2. Answer D.</p>',
  slowPath: '<p>Sum = 30 × 76 = 2280</p><p>Remove 98: 2280 − 98 = 2182</p><p>New mean: 2182/29 ≈ 75.24</p><p>Correct, but that long division is rough.</p>',
  explain: 'Ask: will the mean go up or down? That eliminates choices instantly. Then estimate the size of the change to narrow further.'
},
{
  cat: 'AR', topic: 'Quadratic Equations',
  text: 'A ball is launched with height h(t) = −16t² + 64t + 5. What is the maximum height?',
  choices: ['37 ft', '133 ft', '69 ft', '64 ft'],
  answer: 2,
  fastStrat: 'direct',
  directOk: true,
  fastPath: '<p>This one IS fastest with direct solve:</p><p>t = −b/(2a) = −64/(−32) = 2</p><p>h(2) = −16(4) + 64(2) + 5 = −64 + 128 + 5 = 69</p><p>Vertex formula is the right tool here.</p>',
  slowPath: '<p>Same — there\'s no shortcut. This is a "know the formula" problem.</p>',
  explain: 'Sometimes direct solve IS the fast path. Max/min problems = vertex formula. t = −b/(2a), then substitute back. Have this formula cold for test day.'
},
{
  cat: 'GMS', topic: 'Coordinate Geometry',
  text: 'What is the distance between (−2, 3) and (4, −5)?',
  choices: ['10', '6', '8', '√52'],
  answer: 0,
  fastStrat: 'direct',
  directOk: true,
  fastPath: '<p>d = √((6)² + (−8)²) = √(36 + 64) = √100 = 10</p><p>Distance formula is the only path here.</p>',
  slowPath: '<p>Same — no shortcut. But notice the 6-8-10 right triangle (scaled 3-4-5). Recognizing triples can skip the square root step.</p>',
  explain: 'Distance formula is direct solve, but the tip is recognizing Pythagorean triples: 3-4-5, 5-12-13, 8-15-17. The legs here are 6 and 8 → triple → 10.'
},

// ─── ESTIMATE PROBLEMS ───
{
  cat: 'QR', topic: 'Rational Numbers & Operations',
  text: 'What is the value of  (4.97 × 8.03) ÷ 2.01 ?',
  choices: ['10.2', '39.9', '19.8', '24.5'],
  answer: 2,
  fastStrat: 'estimate',
  directOk: false,
  fastPath: '<p>Round: (5 × 8) ÷ 2 = 40 ÷ 2 = 20</p><p>Closest answer: C (19.8)</p><p>Done in 5 seconds.</p>',
  slowPath: '<p>4.97 × 8.03 = 39.9091</p><p>39.9091 ÷ 2.01 = 19.856...</p><p>Brutal decimal arithmetic.</p>',
  explain: 'When the numbers are "almost round," round them and estimate. If the answer choices are spread apart, estimation gets you there instantly.'
},
{
  cat: 'QR', topic: 'Ratios & Percents',
  text: 'What is 33% of 91?',
  choices: ['27.3', '45.5', '30.03', '60.06'],
  answer: 2,
  fastStrat: 'estimate',
  directOk: true,
  fastPath: '<p>33% ≈ 1/3. One-third of 91 ≈ 30.</p><p>Only C is near 30. Done.</p>',
  slowPath: '<p>0.33 × 91 = 30.03</p><p>Works, but decimal multiplication isn\'t fun by hand.</p>',
  explain: '33% ≈ 1/3 is a conversion you should know cold. 1/3 of 90 = 30. Close enough to pick the right answer when choices are spread out.'
},
{
  cat: 'GMS', topic: 'Perimeter, Area & Volume',
  text: 'A circle has diameter 14 cm. What is its area? (Use π ≈ 3.14)',
  choices: ['87.92 cm²', '43.96 cm²', '615.44 cm²', '153.86 cm²'],
  answer: 3,
  fastStrat: 'estimate',
  directOk: true,
  fastPath: '<p>r = 7, so r² = 49</p><p>π × 49 ≈ 3 × 49 = 147-ish</p><p>Only D is near 150. Done.</p>',
  slowPath: '<p>D = π(7²) = 3.14 × 49 = 153.86</p><p>Same answer, more multiplication.</p>',
  explain: 'Estimating π as "about 3" is often enough when the answer choices are far apart. 3 × 49 ≈ 147, and only one answer is in that neighborhood.'
},

// ─── ELIMINATE + CONCEPT PROBLEMS ───
{
  cat: 'PR', topic: 'Scatter Plots & Regression',
  text: 'A scatter plot shows points trending downward from left to right. Which correlation coefficient is most likely?',
  choices: ['r = 0.89', 'r = −0.89', 'r = −0.12', 'r = 0.12'],
  answer: 1,
  fastStrat: 'eliminate',
  directOk: true,
  fastPath: '<p>Downward trend = negative r → eliminate A and D.</p><p>"Trending" implies strong → closer to −1 → eliminate C (−0.12 is weak).</p><p>Answer: B.</p>',
  slowPath: '<p>Same logic — this IS an elimination problem. There\'s nothing to "solve."</p>',
  explain: 'Two-step elimination: direction (positive or negative?) cuts half the choices. Then strength (strong or weak?) picks the winner.'
},
{
  cat: 'PR', topic: 'Data & Statistics',
  text: 'Which measure of center is LEAST affected by an outlier of 500 in the data set {12, 14, 15, 15, 18, 500}?',
  choices: ['Range', 'Mean', 'Median', 'Standard deviation'],
  answer: 2,
  fastStrat: 'eliminate',
  directOk: true,
  fastPath: '<p>Range uses the outlier directly → affected. Eliminate A.</p><p>SD measures spread from mean → affected. Eliminate D.</p><p>Mean includes all values → pulled up. Eliminate B.</p><p>Median = middle value, barely changed. Answer C.</p>',
  slowPath: '<p>Same — conceptual elimination IS the strategy. No computation needed.</p>',
  explain: 'You don\'t need to calculate anything. Just ask: "Does this measure USE the outlier in its formula?" Cross off the ones that do.'
},
{
  cat: 'PR', topic: 'Statistical Measures',
  text: 'In a normal distribution, approximately what percentage of data falls within 2 standard deviations of the mean?',
  choices: ['50%', '68%', '99.7%', '95%'],
  answer: 3,
  fastStrat: 'direct',
  directOk: true,
  fastPath: '<p>Empirical rule: 68-95-99.7</p><p>2 SD → 95%. Instant recall.</p>',
  slowPath: '<p>Same — this is pure memorization. No shortcut, no elimination needed.</p>',
  explain: 'Memorize 68-95-99.7 (1-2-3 standard deviations). This is a free point on test day if you know it.'
},
{
  cat: 'AR', topic: 'Systems of Equations',
  text: 'A system of two linear equations has no solution. What must be true about the lines?',
  choices: ['They are the same line', 'They intersect at one point', 'They are parallel', 'They are perpendicular'],
  answer: 2,
  fastStrat: 'eliminate',
  directOk: true,
  fastPath: '<p>One intersection = one solution → eliminate B.</p><p>Same line = infinite solutions → eliminate A.</p><p>Perpendicular = they definitely intersect → eliminate D.</p><p>Parallel = never meet = no solution. Answer C.</p>',
  slowPath: '<p>Same reasoning. Concept questions ARE elimination questions.</p>',
  explain: 'For concept questions, don\'t try to "prove" the right answer. Eliminate the wrong ones. It\'s faster and builds confidence.'
},

// ─── MORE BACKSOLVE ───
{
  cat: 'QR', topic: 'Ratios & Percents',
  text: 'The ratio of boys to girls in a class is 3:5. If there are 40 students, how many boys are there?',
  choices: ['12', '18', '24', '15'],
  answer: 3,
  fastStrat: 'backsolve',
  directOk: true,
  fastPath: '<p>Try D: 15 boys → 25 girls → 40 total ✓</p><p>Ratio: 15/25 = 3/5 ✓</p><p>Done.</p>',
  slowPath: '<p>3 + 5 = 8 parts. Boys = (3/8)(40) = 15.</p><p>Fine, but back-solve is just as fast here.</p>',
  explain: 'Both approaches work equally well here. The key is having back-solve as a tool so you\'re never stuck on ratio algebra.'
},
{
  cat: 'AR', topic: 'Linear Equations',
  text: 'A plumber charges $45 for a house call plus $30/hour. If the total was $165, how many hours?',
  choices: ['3', '5', '4', '3.5'],
  answer: 2,
  fastStrat: 'backsolve',
  directOk: true,
  fastPath: '<p>Try C: 4 hours</p><p>$45 + $30(4) = $45 + $120 = $165 ✓</p>',
  slowPath: '<p>45 + 30h = 165 → 30h = 120 → h = 4</p><p>Both fast — but back-solve requires zero equation setup.</p>',
  explain: 'Simple word problems with clean answers: back-solve. You skip translating words into equations, which is where most errors happen.'
},

// ─── MORE PLUG-IN ───
{
  cat: 'AR', topic: 'Linear Functions',
  text: 'Which equation represents a line perpendicular to y = (2/3)x − 1 that passes through (4, 1)?',
  choices: ['y = (2/3)x − 1', 'y = −(3/2)x + 7', 'y = −(3/2)x + 1', 'y = (3/2)x − 5'],
  answer: 1,
  fastStrat: 'plugin',
  directOk: true,
  fastPath: '<p>Perpendicular slope = −3/2. Eliminate A and D.</p><p>Now plug (4, 1) into remaining:</p><p>B: −(3/2)(4) + 7 = −6 + 7 = 1 ✓</p><p>C: −(3/2)(4) + 1 = −6 + 1 = −5 ✗</p><p>Answer B. Combined eliminate + plug in.</p>',
  slowPath: '<p>Perpendicular slope = −3/2</p><p>Point-slope: y − 1 = −(3/2)(x − 4)</p><p>y = −(3/2)x + 6 + 1 = −(3/2)x + 7</p><p>Works, but distributing fractions is error-prone.</p>',
  explain: 'Combine strategies: eliminate by slope first, then plug in the point to pick between survivors. Two quick checks beats fraction algebra.'
},
{
  cat: 'AR', topic: 'Expressions & Polynomials',
  text: 'Simplify:  (2x + 3)(x − 5) − (x² − x)',
  choices: ['x² − 8x − 15', 'x² − 6x − 15', '3x² − 8x − 15', 'x² − 6x + 15'],
  answer: 1,
  fastStrat: 'plugin',
  directOk: true,
  fastPath: '<p>Let x = 0:</p><p>Original: (3)(−5) − (0) = −15</p><p>B: 0 − 0 − 15 = −15 ✓</p><p>A: 0 − 0 − 15 = −15 ✓ (tie!)</p><p>Try x = 1:</p><p>Original: (5)(−4) − (0) = −20</p><p>B: 1 − 6 − 15 = −20 ✓</p><p>A: 1 − 8 − 15 = −22 ✗</p><p>Answer B.</p>',
  slowPath: '<p>FOIL: 2x² − 10x + 3x − 15 = 2x² − 7x − 15</p><p>Subtract: 2x² − 7x − 15 − x² + x = x² − 6x − 15</p><p>Multiple distribution and sign changes — easy to mess up.</p>',
  explain: 'When plug-in gives a tie, try a second number. Still faster (and safer) than distributing, combining, and subtracting across two expressions.'
},

// ─── MORE ESTIMATE ───
{
  cat: 'QR', topic: 'Rational Numbers & Operations',
  text: 'What is  (7/8) × (15/16) closest to?',
  choices: ['13/16', '1/2', '2', '1'],
  answer: 3,
  fastStrat: 'estimate',
  directOk: true,
  fastPath: '<p>7/8 ≈ 0.875 (almost 1)</p><p>15/16 ≈ 0.9375 (almost 1)</p><p>Product ≈ almost 1 × almost 1 ≈ just under 1</p><p>Answer D.</p>',
  slowPath: '<p>(7×15)/(8×16) = 105/128 ≈ 0.82</p><p>Closest to 1. Same answer, harder math.</p>',
  explain: 'Both fractions are close to 1, so their product is close to 1. No multiplication needed. Train yourself to "feel" fraction sizes.'
},
{
  cat: 'GMS', topic: 'Perimeter, Area & Volume',
  text: 'A sphere has radius 6 cm. What is its volume? (V = 4/3 πr³)',
  choices: ['1,356.5 cm³', '452.2 cm³', '113.0 cm³', '904.3 cm³'],
  answer: 3,
  fastStrat: 'estimate',
  directOk: true,
  fastPath: '<p>r³ = 216</p><p>4/3 × 3 × 216 = 4 × 216 = 864</p><p>Using π ≈ 3 gives ~864. Only D (904.3) is close.</p>',
  slowPath: '<p>V = (4/3)(3.14)(216) = (4/3)(678.24) = 904.32</p><p>Precise but slow.</p>',
  explain: 'Replace π with 3 for quick estimates. 4/3 × 3 = 4, so the estimate is just 4 × r³. When choices are spread apart, that\'s enough.'
},

// ─── DIRECT SOLVE (these genuinely require it) ───
{
  cat: 'AR', topic: 'Quadratic Equations',
  text: 'Use the discriminant to determine: how many real solutions does 2x² − 4x + 5 = 0 have?',
  choices: ['No real solutions', 'Exactly one', 'Exactly two', 'Cannot be determined'],
  answer: 0,
  fastStrat: 'direct',
  directOk: true,
  fastPath: '<p>D = b² − 4ac = 16 − 40 = −24</p><p>Negative → no real solutions.</p><p>There\'s no shortcut — you need the discriminant.</p>',
  slowPath: '<p>Same. This is a formula question.</p>',
  explain: 'Discriminant problems are always direct: b² − 4ac, check the sign. Negative = 0, zero = 1, positive = 2. Quick formula, quick answer.'
},
{
  cat: 'AR', topic: 'Linear Functions',
  text: 'A line passes through (−1, 5) and (3, −3). What is the equation of the line?',
  choices: ['y = 2x + 7', 'y = −2x + 3', 'y = −2x + 7', 'y = −(1/2)x + 3'],
  answer: 1,
  fastStrat: 'plugin',
  directOk: true,
  fastPath: '<p>Plug (−1, 5) into each choice:</p><p>B: −2(−1) + 3 = 5 ✓</p><p>Check (3, −3): −2(3) + 3 = −3 ✓</p><p>B works for both points.</p>',
  slowPath: '<p>m = (−3−5)/(3−(−1)) = −8/4 = −2</p><p>y − 5 = −2(x + 1) → y = −2x + 3</p><p>Standard but requires slope formula + point-slope.</p>',
  explain: 'When given points and answer choices, plug the points INTO the choices. If both points satisfy an equation, that\'s your line. Faster than calculating slope and y-intercept.'
},
{
  cat: 'GMS', topic: 'Angles & Triangles',
  text: 'In a right triangle, one leg is 5 and the hypotenuse is 13. What is the other leg?',
  choices: ['8', '12', '√194', '√144'],
  answer: 1,
  fastStrat: 'direct',
  directOk: true,
  fastPath: '<p>Recognize 5-12-13 Pythagorean triple → 12</p><p>Or: b² = 169 − 25 = 144, b = 12</p><p>Knowing common triples saves the computation.</p>',
  slowPath: '<p>Same — but if you recognize 5-12-13, it\'s instant.</p>',
  explain: 'Pythagorean triples to memorize: 3-4-5, 5-12-13, 8-15-17, 7-24-25. Recognizing them turns a calculation into a recall.'
},
{
  cat: 'GMS', topic: 'Transformations',
  text: 'Point (3, −2) is reflected over the y-axis, then translated 4 units up. What are the final coordinates?',
  choices: ['(3, −6)', '(3, 2)', '(−3, −6)', '(−3, 2)'],
  answer: 3,
  fastStrat: 'direct',
  directOk: true,
  fastPath: '<p>y-axis reflection: negate x → (−3, −2)</p><p>Translate up 4: add 4 to y → (−3, 2)</p><p>Two quick steps.</p>',
  slowPath: '<p>Same — straightforward application of rules.</p>',
  explain: 'Transformation problems are step-by-step. Do each transformation in order. The trap is doing them simultaneously or in the wrong order.'
},
{
  cat: 'GMS', topic: 'Circles',
  text: 'A circle has equation (x − 3)² + (y + 1)² = 36. What is its radius?',
  choices: ['36', '6', '18', '√6'],
  answer: 1,
  fastStrat: 'eliminate',
  directOk: true,
  fastPath: '<p>Standard form: r² = 36, so r = 6.</p><p>Eliminate A (that\'s r², a very common trap).</p><p>Eliminate C and D (not related to √36).</p><p>Answer B.</p>',
  slowPath: '<p>Same — but naming the trap (r² vs r) is the key insight.</p>',
  explain: 'The #1 trap on circle problems: the equation gives you r², not r. Always take the square root. If you see 36 as an answer AND 6 as an answer, the test is testing this exact trap.'
},

// ─── PR PROBLEMS ───
{
  cat: 'PR', topic: 'Counting & Probability',
  text: 'A bag has 4 red, 3 blue, and 5 green marbles. If you draw one marble, what is P(not green)?',
  choices: ['3/4', '5/12', '1/3', '7/12'],
  answer: 3,
  fastStrat: 'direct',
  directOk: true,
  fastPath: '<p>Total = 12. Not green = 4 + 3 = 7.</p><p>P(not green) = 7/12.</p><p>Faster than calculating P(green) and subtracting from 1.</p>',
  slowPath: '<p>P(green) = 5/12. P(not green) = 1 − 5/12 = 7/12.</p><p>Works but adds a subtraction step.</p>',
  explain: 'For "not" probabilities, count favorable outcomes directly if it\'s easy. Here, counting non-green (7) is just as fast as the complement method.'
},
{
  cat: 'PR', topic: 'Counting & Probability',
  text: 'How many different 3-letter "words" can be made from A, B, C, D, E if no letter repeats?',
  choices: ['120', '125', '10', '60'],
  answer: 3,
  fastStrat: 'direct',
  directOk: true,
  fastPath: '<p>5 choices × 4 remaining × 3 remaining = 60</p><p>That\'s P(5,3) = 60.</p>',
  slowPath: '<p>Same — permutation formula. P(n,r) = n!/(n−r)! = 120/2 = 60.</p>',
  explain: 'Permutation: order matters (ABC ≠ BAC). Multiply the decreasing choices: 5 × 4 × 3. Combination would be if order didn\'t matter.'
},
{
  cat: 'PR', topic: 'Data & Statistics',
  text: 'Data set: 3, 7, 7, 8, 10, 12, 15. What is the interquartile range?',
  choices: ['5', '8', '12', '7'],
  answer: 0,
  fastStrat: 'direct',
  directOk: true,
  fastPath: '<p>Median = 8 (middle of 7 values)</p><p>Q1 = median of {3,7,7} = 7</p><p>Q3 = median of {10,12,15} = 12</p><p>IQR = 12 − 7 = 5</p>',
  slowPath: '<p>Same process — IQR is always direct.</p>',
  explain: 'IQR: find the median, split the data, find Q1 and Q3, subtract. No shortcut, but the process is quick once you\'ve practiced it.'
},
{
  cat: 'PR', topic: 'Scatter Plots & Regression',
  text: 'A regression line is ŷ = 1.5x + 20. A data point is (10, 40). What is the residual?',
  choices: ['35', '−5', '5', '0'],
  answer: 2,
  fastStrat: 'direct',
  directOk: true,
  fastPath: '<p>Predicted: ŷ = 1.5(10) + 20 = 35</p><p>Residual = actual − predicted = 40 − 35 = 5</p>',
  slowPath: '<p>Same — residual = actual − predicted. No shortcut.</p>',
  explain: 'Residual = actual − predicted. Positive residual means the point is ABOVE the regression line. Quick formula, just don\'t flip the subtraction order.'
},

// ─── ADDITIONAL VARIETY ───
{
  cat: 'AR', topic: 'Exponents & Roots',
  text: 'Simplify:  (4x⁶y²)^(1/2)',
  choices: ['2x³y', '4x³y', '2x⁶y²', '16x³y'],
  answer: 0,
  fastStrat: 'plugin',
  directOk: true,
  fastPath: '<p>Let x = 1, y = 1:</p><p>Original: (4·1·1)^(1/2) = √4 = 2</p><p>A: 2·1·1 = 2 ✓</p><p>B: 4·1·1 = 4 ✗</p><p>Answer A.</p>',
  slowPath: '<p>√4 = 2, √(x⁶) = x³, √(y²) = y → 2x³y</p><p>Correct but requires knowing each root rule.</p>',
  explain: 'Setting x = y = 1 collapses everything to just the coefficient. If that narrows it to one answer, you\'re done without touching exponent rules.'
},
{
  cat: 'QR', topic: 'Ratios & Percents',
  text: 'A car that costs $24,000 depreciates 20% in the first year and 15% in the second year. What is it worth after 2 years?',
  choices: ['$14,400', '$15,600', '$16,320', '$19,200'],
  answer: 2,
  fastStrat: 'eliminate',
  directOk: true,
  fastPath: '<p>After year 1: $24,000 × 0.80 = $19,200</p><p>Eliminate D (that\'s only 1 year).</p><p>After year 2: $19,200 × 0.85 = ?</p><p>Must be less than $19,200 but more than $14,400 (which would be 40% off).</p><p>$19,200 × 0.85 = $16,320. Answer C.</p>',
  slowPath: '<p>Same computation — but the elimination mindset catches the year-1 trap (D) and the "just subtract 35%" trap (A).</p>',
  explain: 'Percent decreases compound — you can\'t add them. 20% then 15% is NOT 35% total. Multiply by each factor: 0.80 × 0.85 = 0.68, so it\'s a 32% total loss.'
},
{
  cat: 'AR', topic: 'Linear Equations',
  text: 'Solve:  |3x − 6| = 12',
  choices: ['x = 6 or x = −2', 'x = 6', 'x = −6 or x = 2', 'x = 6 or x = 2'],
  answer: 0,
  fastStrat: 'backsolve',
  directOk: true,
  fastPath: '<p>Scan choices — most have x = 6. Check it:</p><p>|3(6) − 6| = |12| = 12 ✓</p><p>Now check the second value in A: x = −2</p><p>|3(−2) − 6| = |−12| = 12 ✓</p><p>A has both working solutions.</p>',
  slowPath: '<p>3x − 6 = 12 → x = 6</p><p>3x − 6 = −12 → x = −2</p><p>Standard, but students often forget the negative case.</p>',
  explain: 'Absolute value means two cases. Back-solve by checking each answer choice\'s values — it confirms both solutions without needing to remember the two-case setup.'
},
{
  cat: 'GMS', topic: 'Coordinate Geometry',
  text: 'What is the midpoint of (−4, 9) and (6, −3)?',
  choices: ['(1, 3)', '(5, 6)', '(−5, 6)', '(2, 6)'],
  answer: 0,
  fastStrat: 'direct',
  directOk: true,
  fastPath: '<p>Average x: (−4+6)/2 = 1</p><p>Average y: (9+(−3))/2 = 3</p><p>Midpoint: (1, 3)</p>',
  slowPath: '<p>Same — midpoint is always just averaging.</p>',
  explain: 'Midpoint = average the x\'s, average the y\'s. No shortcut needed — it\'s already fast. Just don\'t mix up your signs.'
},
{
  cat: 'GMS', topic: 'Angles & Triangles',
  text: 'Two similar triangles have a scale factor of 2:5. If the smaller triangle has an area of 12 cm², what is the area of the larger triangle?',
  choices: ['30 cm²', '75 cm²', '48 cm²', '120 cm²'],
  answer: 1,
  fastStrat: 'eliminate',
  directOk: true,
  fastPath: '<p>Area scales by the SQUARE of the ratio: (5/2)² = 25/4</p><p>12 × 25/4 = 75</p><p>Key trap: A (30) is what you get if you just multiply by 5/2 without squaring. Eliminate it.</p>',
  slowPath: '<p>Same formula — but recognizing the "forgot to square" trap helps you avoid it.</p>',
  explain: 'Similar figures: lengths scale by the ratio, but AREAS scale by the ratio SQUARED. This is a trap the TSI loves. 2:5 ratio → area ratio is 4:25.'
},
{
  cat: 'PR', topic: 'Counting & Probability',
  text: 'Two fair coins are flipped. What is P(at least one head)?',
  choices: ['1/2', '3/4', '1/4', '1'],
  answer: 1,
  fastStrat: 'eliminate',
  directOk: true,
  fastPath: '<p>"At least one" = easier to find the complement.</p><p>P(no heads) = P(TT) = 1/4</p><p>P(at least one) = 1 − 1/4 = 3/4</p><p>Or just eliminate: P = 1/2 would mean heads and tails equally likely outcomes, but there are 3 favorable (HH, HT, TH) out of 4.</p>',
  slowPath: '<p>List outcomes: HH, HT, TH, TT. Three have at least one head. P = 3/4.</p><p>Both approaches work.</p>',
  explain: 'For "at least one" problems, the complement trick (1 minus P of none) is almost always the fastest approach. Train yourself to flip it.'
},
{
  cat: 'QR', topic: 'Number Concepts',
  text: 'What is the least common multiple (LCM) of 12 and 18?',
  choices: ['72', '36', '6', '216'],
  answer: 1,
  fastStrat: 'eliminate',
  directOk: true,
  fastPath: '<p>LCM must be ≥ 18 → eliminate C (6).</p><p>LCM must be divisible by both 12 and 18.</p><p>36 ÷ 12 = 3 ✓, 36 ÷ 18 = 2 ✓</p><p>36 is smallest that works. Answer B.</p>',
  slowPath: '<p>12 = 2² × 3, 18 = 2 × 3². LCM = 2² × 3² = 36.</p><p>Prime factorization works but takes longer.</p>',
  explain: 'LCM shortcut: start with the bigger number and check if the smaller divides evenly. 18 ÷ 12? No. 36 ÷ 12 = 3 and 36 ÷ 18 = 2. Yes! That\'s the LCM.'
},
{
  cat: 'AR', topic: 'Linear Functions',
  text: 'Which equation represents a line with slope −3 and y-intercept 7?',
  choices: ['y = −3x + 7', 'y = 7x − 3', 'y = 3x + 7', 'y = −3x − 7'],
  answer: 0,
  fastStrat: 'direct',
  directOk: true,
  fastPath: '<p>y = mx + b → y = −3x + 7. Done.</p><p>Pure recall of slope-intercept form.</p>',
  slowPath: '<p>Same — no faster way than just knowing y = mx + b.</p>',
  explain: 'Slope-intercept form: y = mx + b. m = slope, b = y-intercept. This should be automatic by test day — zero thinking required.'
},
{
  cat: 'AR', topic: 'Expressions & Polynomials',
  text: 'Factor completely:  2x² + 7x + 3',
  choices: ['(2x + 1)(x + 3)', '(2x + 3)(x + 1)', '(x + 1)(x + 3)', '2(x² + 3x + 1)'],
  answer: 0,
  fastStrat: 'backsolve',
  directOk: true,
  fastPath: '<p>FOIL each choice mentally:</p><p>A: 2x² + 6x + x + 3 = 2x² + 7x + 3 ✓</p><p>Done on first try.</p>',
  slowPath: '<p>Factor 2x² + 7x + 3: find two numbers that multiply to 2×3=6 and add to 7: 6 and 1.</p><p>Split: 2x² + 6x + x + 3 = 2x(x+3) + 1(x+3) = (2x+1)(x+3)</p><p>AC method works but is multi-step.</p>',
  explain: 'When factoring with a leading coefficient, FOIL the answer choices instead. It\'s faster than the AC method, and you\'re verifying rather than creating — much harder to make errors.'
},
{
  cat: 'QR', topic: 'Rational Numbers & Operations',
  text: 'Evaluate:  |−8| − |3 − 11|',
  choices: ['8', '16', '−16', '0'],
  answer: 3,
  fastStrat: 'direct',
  directOk: true,
  fastPath: '<p>|−8| = 8</p><p>|3 − 11| = |−8| = 8</p><p>8 − 8 = 0</p>',
  slowPath: '<p>Same — evaluate each absolute value separately. The trap is doing 8 − (3−11) = 8 + 8 = 16.</p>',
  explain: 'Absolute value problems: evaluate INSIDE the bars first, THEN take the absolute value. Don\'t distribute the bars across subtraction — that\'s not how they work.'
},
];

// ═══════════════════════════════════════════
// STATE
// ═══════════════════════════════════════════
let state = {
  filter: 'ALL',
  current: null,
  selectedStrategy: null,
  selectedAnswer: null,
  timerStart: null,
  timerInterval: null,
  elapsed: 0,
  stats: {
    attempted: 0, correct: 0, streak: 0, bestStreak: 0, times: [],
    byCategory: { QR:{a:0,c:0}, AR:{a:0,c:0}, GMS:{a:0,c:0}, PR:{a:0,c:0} },
    stratPicks: {}
  },
  seen: new Set()
};

// ═══════════════════════════════════════════
// HELPERS
// ═══════════════════════════════════════════
function setFilter(cat) {
  state.filter = cat;
  document.querySelectorAll('.filter-btn').forEach(b => b.classList.toggle('active', b.dataset.cat === cat));
}

function getPool() {
  let pool = state.filter === 'ALL' ? QUESTIONS : QUESTIONS.filter(q => q.cat === state.filter);
  let unseen = pool.filter(q => !state.seen.has(QUESTIONS.indexOf(q)));
  if (unseen.length === 0) { pool.forEach(q => state.seen.delete(QUESTIONS.indexOf(q))); unseen = pool; }
  return unseen;
}

function pickQuestion() {
  const pool = getPool();
  const q = pool[Math.floor(Math.random() * pool.length)];
  state.seen.add(QUESTIONS.indexOf(q));
  return q;
}

function showPhase(phase) {
  document.querySelectorAll('.phase').forEach(p => p.classList.remove('active'));
  document.getElementById('phase-' + phase).classList.add('active');
}

function formatTime(seconds) {
  const m = Math.floor(seconds / 60);
  const s = seconds % 60;
  return m + ':' + (s < 10 ? '0' : '') + s;
}

function startTimer() {
  state.timerStart = Date.now(); state.elapsed = 0;
  updateTimerDisplay();
  state.timerInterval = setInterval(() => { state.elapsed = Math.floor((Date.now() - state.timerStart) / 1000); updateTimerDisplay(); }, 1000);
}
function stopTimer() { clearInterval(state.timerInterval); return state.elapsed; }
function updateTimerDisplay() {
  const t = formatTime(state.elapsed);
  const cls = state.elapsed > 120 ? 'slow' : state.elapsed > 60 ? 'caution' : '';
  ['timer','timer2'].forEach(id => { const el = document.getElementById(id); if(el){el.textContent='⏱ '+t;el.className='timer-display '+cls;} });
}
function updateStats() {
  document.getElementById('streak').textContent = state.stats.streak;
  document.getElementById('totalCorrect').textContent = state.stats.correct;
  document.getElementById('totalAttempted').textContent = state.stats.attempted;
  if (state.stats.times.length > 0) {
    const avg = Math.round(state.stats.times.reduce((a,b)=>a+b,0) / state.stats.times.length);
    document.getElementById('avgTime').textContent = formatTime(avg);
  }
}

// ═══════════════════════════════════════════
// PHASES
// ═══════════════════════════════════════════
function startTraining() { nextProblem(); }

function nextProblem() {
  state.current = pickQuestion();
  state.selectedStrategy = null;
  state.selectedAnswer = null;
  renderStrategyPhase();
  showPhase('strategy');
  startTimer();
}

function renderStrategyPhase() {
  const q = state.current;
  const catNames = {QR:'Quantitative Reasoning',AR:'Algebraic Reasoning',GMS:'Geometry & Measurement',PR:'Probabilistic Reasoning'};
  const letters = ['A','B','C','D'];
  ['catTag','catTag2','catTag3'].forEach(id => { const el=document.getElementById(id); el.className='category-tag '+q.cat; el.textContent=catNames[q.cat]; });
  ['topicLabel','topicLabel2','topicLabel3'].forEach(id => { document.getElementById(id).textContent=q.topic; });
  ['problemText','problemText2','problemText3'].forEach(id => { document.getElementById(id).textContent=q.text; });
  document.getElementById('previewChoices').innerHTML = q.choices.map((c,i) =>
    `<div class="choice-btn" style="cursor:default;"><span class="choice-letter">${letters[i]}</span><span>${c}</span></div>`
  ).join('');
  document.getElementById('strategyGrid').innerHTML = STRATEGIES.map(s =>
    `<button class="strategy-btn" data-strat="${s.id}" onclick="selectStrategy('${s.id}')">
      <span class="strat-name">${s.name}</span><span class="strat-desc">${s.desc}</span>
    </button>`
  ).join('');
  document.getElementById('confirmStrategy').disabled = true;
}

function selectStrategy(id) {
  state.selectedStrategy = id;
  document.querySelectorAll('.strategy-btn').forEach(b => b.classList.toggle('selected', b.dataset.strat === id));
  document.getElementById('confirmStrategy').disabled = false;
}

function confirmStrategy() {
  const strat = STRATEGIES.find(s => s.id === state.selectedStrategy);
  document.getElementById('chosenStratLabel').textContent = strat.name + ' — ' + strat.desc;
  const q = state.current;
  const letters = ['A','B','C','D'];
  document.getElementById('answerChoices').innerHTML = q.choices.map((c,i) =>
    `<button class="choice-btn" data-idx="${i}" onclick="selectAnswer(${i})"><span class="choice-letter">${letters[i]}</span><span>${c}</span></button>`
  ).join('');
  document.getElementById('submitAnswer').disabled = true;
  showPhase('solve');
}

function selectAnswer(idx) {
  state.selectedAnswer = idx;
  document.querySelectorAll('#answerChoices .choice-btn').forEach(b =>
    b.classList.toggle('selected', parseInt(b.dataset.idx) === idx)
  );
  document.getElementById('submitAnswer').disabled = false;
}

function submitAnswer() {
  const elapsed = stopTimer();
  const q = state.current;
  const isCorrect = state.selectedAnswer === q.answer;

  // Stats
  state.stats.attempted++;
  state.stats.byCategory[q.cat].a++;
  state.stats.stratPicks[state.selectedStrategy] = (state.stats.stratPicks[state.selectedStrategy] || 0) + 1;
  if (isCorrect) { state.stats.correct++; state.stats.streak++; state.stats.byCategory[q.cat].c++; if(state.stats.streak>state.stats.bestStreak) state.stats.bestStreak=state.stats.streak; }
  else { state.stats.streak = 0; }
  state.stats.times.push(elapsed);
  updateStats();

  // Review choices
  const letters = ['A','B','C','D'];
  document.getElementById('reviewChoices').innerHTML = q.choices.map((c,i) => {
    let cls = 'choice-btn';
    if (i === q.answer) cls += ' correct';
    else if (i === state.selectedAnswer && !isCorrect) cls += ' incorrect';
    return `<div class="${cls}"><span class="choice-letter">${letters[i]}</span><span>${c}</span></div>`;
  }).join('');

  // Feedback card
  const fc = document.getElementById('feedbackCard');
  fc.className = 'feedback-card ' + (isCorrect ? 'correct' : 'incorrect');
  document.getElementById('feedbackTitle').innerHTML = isCorrect
    ? '✓ Correct!' : '✗ Not quite — the answer is ' + letters[q.answer] + ': ' + q.choices[q.answer];
  document.getElementById('feedbackExplain').textContent = q.explain;

  // Fast path comparison
  const stratName = STRATEGIES.find(s => s.id === state.selectedStrategy).name;
  const fastName = STRATEGIES.find(s => s.id === q.fastStrat).name;
  const userPickedFast = state.selectedStrategy === q.fastStrat;

  let fpHTML = '';
  if (q.directOk && state.selectedStrategy === 'direct') {
    // Direct is fine and they picked it
    fpHTML = `<p style="margin-bottom:8px;"><strong>Direct solve works well here.</strong> No faster shortcut on this one — knowing when to solve straight through is a skill too.</p>`;
    if (q.fastStrat !== 'direct') {
      fpHTML += `<p style="font-size:0.82rem;color:var(--text-muted);">That said, <strong>${fastName}</strong> is also quick here:</p>${q.fastPath}`;
    }
  } else if (userPickedFast) {
    fpHTML = `<p><strong>Great call picking ${fastName}!</strong> That's the fastest route on this one.</p>`;
    fpHTML += q.fastPath;
    if (q.slowPath && !q.directOk) {
      fpHTML += `<div class="compare"><div class="path-option path-fast"><div class="path-label">⚡ ${fastName} (your pick)</div>${q.fastPath}</div><div class="path-option path-slow"><div class="path-label">🐢 Direct Solve</div>${q.slowPath}</div></div>`;
    }
  } else {
    // They didn't pick the fast one
    if (q.directOk) {
      fpHTML = `<p style="margin-bottom:8px;"><strong>${stratName}</strong> works fine here. But check out how <strong>${fastName}</strong> could also work:</p>${q.fastPath}`;
    } else {
      fpHTML = `<p style="margin-bottom:10px;"><strong>${stratName}</strong> can work, but <strong>${fastName}</strong> is notably faster here:</p>`;
      fpHTML += `<div class="compare"><div class="path-option path-fast"><div class="path-label">⚡ ${fastName}</div>${q.fastPath}</div><div class="path-option path-slow"><div class="path-label">🐢 ${stratName}</div>${q.slowPath}</div></div>`;
    }
  }

  document.getElementById('fastPathContent').innerHTML = fpHTML;

  // Time
  let timeMsg = '⏱ ' + formatTime(elapsed);
  if (elapsed <= 45) timeMsg += ' — quick!';
  else if (elapsed <= 90) timeMsg += ' — solid pace';
  else if (elapsed <= 120) timeMsg += ' — try to find the shortcut faster';
  else timeMsg += ' — over 2 min. A faster strategy could help.';
  document.getElementById('finalTime').textContent = timeMsg;

  showPhase('feedback');
}

function showProgress() {
  const cats = ['QR','AR','GMS','PR'];
  const catNames = {QR:'Quantitative Reasoning',AR:'Algebraic Reasoning',GMS:'Geometry & Measurement',PR:'Probabilistic Reasoning'};
  document.getElementById('progressGrid').innerHTML = cats.map(cat => {
    const d = state.stats.byCategory[cat];
    const pct = d.a > 0 ? Math.round((d.c/d.a)*100) : 0;
    return `<div class="progress-item"><div class="cat-name ${cat}">${catNames[cat]}</div><div class="progress-bar"><div class="progress-fill ${cat}" style="width:${pct}%"></div></div><div class="progress-label">${d.c}/${d.a} correct (${pct}%)</div></div>`;
  }).join('');

  // Strategy usage
  const total = state.stats.attempted || 1;
  const usage = STRATEGIES.map(s => {
    const count = state.stats.stratPicks[s.id] || 0;
    const pct = Math.round((count/total)*100);
    return `<strong>${s.name}:</strong> ${count} times (${pct}%)`;
  }).join(' &nbsp;·&nbsp; ');
  document.getElementById('stratUsage').innerHTML = usage;

  showPhase('progress');
}

updateStats();
</script>
</body>
</html>
