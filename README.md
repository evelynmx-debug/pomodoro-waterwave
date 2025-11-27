<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Pomodoro WaterWave 45</title>
<style>
  body {
    margin: 0;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    background: #f5f6fa;
    font-family: Inter, system-ui;
  }

  .card {
    background: white;
    padding: 25px;
    border-radius: 22px;
    box-shadow: 0px 6px 20px rgba(0,0,0,0.08);
    text-align: center;
  }

  .time-text {
    margin-top: 8px;
    font-weight: 700;
    font-size: 32px;
  }

  .subtext {
    color: rgb(120,120,120);
    margin-bottom: 20px;
  }

  .circle-container {
    width: 260px;
    height: 260px;
    position: relative;
  }

  svg {
    width: 100%;
    height: 100%;
  }

  .controls {
    display: flex;
    justify-content: center;
    gap: 12px;
    margin-top: 22px;
  }

  button {
    padding: 10px 16px;
    border-radius: 10px;
    background: #4e8cff;
    color: white;
    border: none;
    cursor: pointer;
    font-weight: 600;
  }

  button.secondary {
    background: transparent;
    border: 1px solid rgba(0,0,0,0.2);
    color: black;
  }
</style>
</head>
<body>

<div class="card">
    <div class="time-text" id="time">45:00</div>
    <div class="subtext">Focus session</div>

    <div class="circle-container">
        <svg viewBox="0 0 200 200">
            <!-- Circle outline -->
            <circle cx="100" cy="100" r="95" fill="#f1f2f6"/>

            <!-- Clip for water -->
            <clipPath id="clipCircle">
                <circle cx="100" cy="100" r="95"/>
            </clipPath>

            <!-- Wave group -->
            <g clip-path="url(#clipCircle)">
                <path id="wave" fill="#4e8cffaa">
                    <!-- Wave path generated via JS -->
                </path>
            </g>

            <!-- Inner white circle for clean look -->
            <circle cx="100" cy="100" r="80" fill="transparent"/>
        </svg>
    </div>

    <div class="controls">
        <button id="start">Start</button>
        <button class="secondary" id="pause">Pause</button>
        <button class="secondary" id="reset">Reset</button>
    </div>
</div>

<script>
/* ---- TIMER LOGIC ---- */
let total = 45 * 60; 
let elapsed = 0;
let running = false;
let timer = null;

/* ---- ELEMENTS ---- */
const timeDisplay = document.getElementById("time");
const wave = document.getElementById("wave");

/* ---- FORMAT TIME ---- */
function fmt(s){
    const m = Math.floor(s/60);
    const sec = s%60;
    return `${String(m).padStart(2,'0')}:${String(sec).padStart(2,'0')}`;
}

/* ---- DRAW WAVE ---- */
function drawWave(progress, tick) {
    const height = 200 - (progress * 200); 
    const amplitude = 6;
    const length = 40;
    let d = `M 0 ${height}`;

    for (let x = 0; x <= 200; x++) {
        let y = height + Math.sin((x + tick) / length) * amplitude;
        d += ` L ${x} ${y}`;
    }
    d += ` L 200 200 L 0 200 Z`;

    wave.setAttribute("d", d);
}

/* ---- UPDATE UI ---- */
let tick = 0;
function update(){
    const remaining = Math.max(total - elapsed, 0);
    timeDisplay.textContent = fmt(remaining);

    const progress = elapsed / total;
    drawWave(progress, tick);
    tick += 2;
}

/* ---- TIMER TICK ---- */
function run(){
    elapsed++;
    if(elapsed >= total){
        clearInterval(timer);
        running = false;
    }
    update();
}

/* ---- BUTTONS ---- */
document.getElementById("start").onclick = () => {
    if(running) return;
    running = true;
    timer = setInterval(run, 1000);
};

document.getElementById("pause").onclick = () => {
    running = false;
    clearInterval(timer);
};

document.getElementById("reset").onclick = () => {
    running = false;
    clearInterval(timer);
    elapsed = 0;
    update();
};

update();
</script>

</body>
</html>
