<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Stick War Legacy</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            width: 100vw;
            height: 100vh;
            overflow: hidden;
            background: #1a1a2e;
            font-family: Arial, sans-serif;
            touch-action: manipulation;
            user-select: none;
            -webkit-user-select: none;
        }
        
        #game {
            position: relative;
            width: 100%;
            height: 100%;
        }
        
        canvas {
            display: block;
            width: 100%;
            height: 100%;
        }
        
        .screen {
            position: absolute;
            top: 0; left: 0;
            width: 100%; height: 100%;
            background: linear-gradient(135deg, #1a1a2e, #16213e);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 100;
        }
        
        .hidden { display: none !important; }
        
        .title {
            font-size: 24px;
            color: #fff;
            text-shadow: 0 0 20px #4a9eff;
            margin-bottom: 10px;
            text-align: center;
        }
        
        .subtitle {
            font-size: 13px;
            color: #aaa;
            margin-bottom: 25px;
        }
        
        .btn {
            padding: 12px 35px;
            margin: 6px;
            font-size: 15px;
            background: linear-gradient(135deg, #4a9eff, #0066cc);
            border: none;
            border-radius: 25px;
            color: white;
            min-width: 180px;
            cursor: pointer;
        }
        
        .btn:active { transform: scale(0.95); }
        .btn.red { background: linear-gradient(135deg, #ff4a4a, #cc0000); }
        .btn.green { background: linear-gradient(135deg, #2ecc71, #27ae60); }
        
        /* Control Panels */
        .panel {
            position: absolute;
            bottom: 5px;
            background: rgba(0,0,0,0.92);
            border-radius: 10px;
            padding: 6px;
            z-index: 50;
            touch-action: manipulation;
        }
        
        .panel.left {
            left: 5px;
            border: 2px solid #4a9eff;
        }
        
        .panel.right {
            right: 5px;
            border: 2px solid #ff4a4a;
        }
        
        .panel-title {
            text-align: center;
            color: white;
            font-size: 11px;
            font-weight: bold;
            padding: 4px;
            border-radius: 5px;
            margin-bottom: 4px;
        }
        
        .panel.left .panel-title { background: #4a9eff; }
        .panel.right .panel-title { background: #ff4a4a; }
        
        .resources {
            display: flex;
            justify-content: space-around;
            color: white;
            font-size: 11px;
            margin-bottom: 4px;
            background: rgba(255,255,255,0.1);
            padding: 4px;
            border-radius: 5px;
        }
        
        .units {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 3px;
            margin-bottom: 4px;
        }
        
        .unit-btn {
            width: 48px;
            height: 42px;
            border: 2px solid #555;
            border-radius: 8px;
            background: #2a2a2a;
            color: white;
            font-size: 8px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            touch-action: manipulation;
        }
        
        .unit-btn:active {
            background: #4a4a4a;
            transform: scale(0.92);
        }
        
        .unit-btn .icon { font-size: 15px; }
        .unit-btn .cost { color: gold; font-size: 9px; margin-top: 1px; }
        
        .commands {
            display: flex;
            gap: 3px;
        }
        
        .cmd {
            flex: 1;
            padding: 8px 4px;
            border: 2px solid transparent;
            border-radius: 6px;
            color: white;
            font-size: 13px;
            cursor: pointer;
            touch-action: manipulation;
        }
        
        .cmd.atk { background: #c0392b; }
        .cmd.def { background: #27ae60; }
        .cmd.gar { background: #f39c12; }
        .cmd.active { border-color: white; box-shadow: 0 0 8px white; }
        
        /* Pause */
        #pauseBtn {
            position: absolute;
            top: 5px;
            right: 5px;
            width: 36px;
            height: 36px;
            border: none;
            border-radius: 50%;
            background: rgba(0,0,0,0.8);
            color: white;
            font-size: 16px;
            z-index: 60;
            cursor: pointer;
        }
        
        .info-box {
            background: rgba(0,0,0,0.92);
            padding: 15px;
            border-radius: 12px;
            max-width: 90%;
            max-height: 55vh;
            overflow-y: auto;
            color: white;
            font-size: 12px;
            line-height: 1.5;
            margin-bottom: 15px;
        }
        
        .info-box h3 {
            color: #4a9eff;
            margin: 10px 0 5px;
            font-size: 13px;
        }
        
        #endScreen .title {
            font-size: 28px;
            margin-bottom: 20px;
        }
        
        @media (max-width: 400px) {
            .unit-btn { width: 42px; height: 38px; }
            .unit-btn .icon { font-size: 13px; }
            .panel { padding: 5px; }
        }
    </style>
</head>
<body>
    <div id="game">
        <canvas id="canvas"></canvas>
        
        <!-- MENU -->
        <div id="menuScreen" class="screen">
            <div class="title">⚔️ STICK WAR LEGACY ⚔️</div>
            <div class="subtitle">2 Người Chơi Cùng Màn Hình</div>
            <button class="btn" id="btnPlay">🎮 CHƠI NGAY</button>
            <button class="btn green" id="btnHelp">📖 HƯỚNG DẪN</button>
        </div>
        
        <!-- HELP -->
        <div id="helpScreen" class="screen hidden">
            <div class="info-box">
                <h2 style="text-align:center;color:#FFD700;">📖 HƯỚNG DẪN</h2>
                <h3>🎯 Mục tiêu:</h3>
                <p>Phá hủy tượng đài đối thủ!</p>
                <h3>⛏️ Đào vàng:</h3>
                <p>Miner đi mỏ → Đào → Về nhà → Nhận +50💰</p>
                <h3>⚔️ Quân đội:</h3>
                <p>⛏️Miner 100 | ⚔️Sword 150 | 🏹Archer 300<br>
                🛡️Spear 500 | 🔮Mage 800 | 👹Giant 1500</p>
                <h3>🎮 Điều khiển:</h3>
                <p>• P1 (Xanh): Bảng TRÁI<br>
                • P2 (Đỏ): Bảng PHẢI<br>
                • Cả 2 người có thể bấm cùng lúc!</p>
            </div>
            <button class="btn red" id="btnBack">⬅️ QUAY LẠI</button>
        </div>
        
        <!-- MODE -->
        <div id="modeScreen" class="screen hidden">
            <div class="title">🎮 CHỌN CHẾ ĐỘ</div>
            <button class="btn" id="btnPvP">👥 2 NGƯỜI CHƠI</button>
            <button class="btn" id="btnPvE">🤖 ĐẤU VỚI MÁY</button>
            <button class="btn red" id="btnBack2">⬅️ QUAY LẠI</button>
        </div>
        
        <!-- PAUSE -->
        <div id="pauseScreen" class="screen hidden">
            <div class="title">⏸️ TẠM DỪNG</div>
            <button class="btn" id="btnResume">▶️ TIẾP TỤC</button>
            <button class="btn red" id="btnQuit">🏠 VỀ MENU</button>
        </div>
        
        <!-- GAME OVER -->
        <div id="endScreen" class="screen hidden">
            <div class="title" id="winText">🎉 PLAYER 1 THẮNG!</div>
            <button class="btn" id="btnRestart">🔄 CHƠI LẠI</button>
            <button class="btn red" id="btnMenu">🏠 VỀ MENU</button>
        </div>
        
        <!-- GAME UI -->
        <button id="pauseBtn" class="hidden">⏸️</button>
        
        <div class="panel left hidden" id="panel1">
            <div class="panel-title">🔵 PLAYER 1</div>
            <div class="resources">
                <span>💰<span id="gold1">200</span></span>
                <span>👥<span id="pop1">0</span>/50</span>
            </div>
            <div class="units" id="units1"></div>
            <div class="commands" id="cmds1"></div>
        </div>
        
        <div class="panel right hidden" id="panel2">
            <div class="panel-title">PLAYER 2 🔴</div>
            <div class="resources">
                <span>💰<span id="gold2">200</span></span>
                <span>👥<span id="pop2">0</span>/50</span>
            </div>
            <div class="units" id="units2"></div>
            <div class="commands" id="cmds2"></div>
        </div>
    </div>

<script>
// ============ CONFIG ============
const WORLD_W = 1400;
const GROUND = 0.72;
const MAX_HP = 1000;

const UNITS_DATA = [
    { id: 'miner', icon: '⛏️', cost: 100, hp: 60, dmg: 8, spd: 1.8, range: 25, atkSpd: 1000, pop: 1, isMiner: true, h: 28, color: '#FFD700' },
    { id: 'sword', icon: '⚔️', cost: 150, hp: 100, dmg: 22, spd: 2.8, range: 28, atkSpd: 550, pop: 1, h: 32, color: '#888' },
    { id: 'archer', icon: '🏹', cost: 300, hp: 70, dmg: 30, spd: 2.2, range: 150, atkSpd: 1200, pop: 2, ranged: true, h: 32, color: '#8B4513' },
    { id: 'spear', icon: '🛡️', cost: 500, hp: 300, dmg: 38, spd: 1.8, range: 35, atkSpd: 900, pop: 3, shield: 0.45, h: 40, color: '#C0C0C0' },
    { id: 'mage', icon: '🔮', cost: 800, hp: 140, dmg: 60, spd: 1.5, range: 130, atkSpd: 1800, pop: 4, ranged: true, magic: true, splash: 50, h: 35, color: '#9400D3' },
    { id: 'giant', icon: '👹', cost: 1500, hp: 900, dmg: 100, spd: 1.0, range: 45, atkSpd: 1400, pop: 7, kb: 25, h: 60, color: '#654321' }
];

// ============ GAME STATE ============
let canvas, ctx;
let running = false, paused = false, mode = 'pvp';
let lastTime = 0;
let scale = 1;

let players, projectiles, effects, mines;

// ============ INIT ============
function init() {
    canvas = document.getElementById('canvas');
    ctx = canvas.getContext('2d');
    
    resize();
    window.addEventListener('resize', resize);
    
    setupMenuButtons();
    createUnitButtons();
    createCommandButtons();
}

function resize() {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
    scale = canvas.width / WORLD_W;
    if (scale > 1) scale = 1;
}

function setupMenuButtons() {
    addClick('btnPlay', () => { hide('menuScreen'); show('modeScreen'); });
    addClick('btnHelp', () => { hide('menuScreen'); show('helpScreen'); });
    addClick('btnBack', () => { hide('helpScreen'); show('menuScreen'); });
    addClick('btnBack2', () => { hide('modeScreen'); show('menuScreen'); });
    addClick('btnPvP', () => startGame('pvp'));
    addClick('btnPvE', () => startGame('pve'));
    addClick('btnResume', resumeGame);
    addClick('btnQuit', quitGame);
    addClick('btnRestart', () => startGame(mode));
    addClick('btnMenu', quitGame);
    addClick('pauseBtn', pauseGame);
}

function addClick(id, fn) {
    const el = document.getElementById(id);
    el.addEventListener('pointerdown', (e) => {
        e.stopPropagation();
        fn();
    });
}

function createUnitButtons() {
    for (let p of [1, 2]) {
        const container = document.getElementById('units' + p);
        container.innerHTML = '';
        for (let u of UNITS_DATA) {
            const btn = document.createElement('div');
            btn.className = 'unit-btn';
            btn.innerHTML = `<span class="icon">${u.icon}</span><span class="cost">${u.cost}</span>`;
            btn.addEventListener('pointerdown', (e) => {
                e.stopPropagation();
                e.preventDefault();
                spawnUnit(p, u.id);
            });
            container.appendChild(btn);
        }
    }
}

function createCommandButtons() {
    const cmds = [
        { id: 'attack', icon: '⚔️', cls: 'atk' },
        { id: 'defend', icon: '🛡️', cls: 'def' },
        { id: 'garrison', icon: '🏠', cls: 'gar' }
    ];
    
    for (let p of [1, 2]) {
        const container = document.getElementById('cmds' + p);
        container.innerHTML = '';
        for (let c of cmds) {
            const btn = document.createElement('div');
            btn.className = 'cmd ' + c.cls;
            btn.id = `cmd${p}_${c.id}`;
            btn.textContent = c.icon;
            btn.addEventListener('pointerdown', (e) => {
                e.stopPropagation();
                e.preventDefault();
                setCommand(p, c.id);
            });
            container.appendChild(btn);
        }
    }
}

function show(id) { document.getElementById(id).classList.remove('hidden'); }
function hide(id) { document.getElementById(id).classList.add('hidden'); }

// ============ GAME CONTROL ============
function startGame(m) {
    mode = m;
    resetGame();
    
    hide('modeScreen');
    hide('endScreen');
    show('pauseBtn');
    show('panel1');
    show('panel2');
    
    if (mode === 'pve') hide('panel2');
    
    running = true;
    paused = false;
    lastTime = performance.now();
    requestAnimationFrame(loop);
}

function resetGame() {
    players = {
        1: { 
            gold: 200, pop: 0, units: [], cmd: 'defend', 
            statue: MAX_HP, maxStatue: MAX_HP,
            spawnX: 70, color: '#4a9eff', dir: 1 
        },
        2: { 
            gold: 200, pop: 0, units: [], cmd: 'defend', 
            statue: MAX_HP, maxStatue: MAX_HP,
            spawnX: WORLD_W - 70, color: '#ff4a4a', dir: -1 
        }
    };
    
    mines = [
        { x: 250, owner: 1 },
        { x: 400, owner: 1 },
        { x: WORLD_W - 250, owner: 2 },
        { x: WORLD_W - 400, owner: 2 }
    ];
    
    projectiles = [];
    effects = [];
    
    updateCmdUI(1, 'defend');
    updateCmdUI(2, 'defend');
}

function pauseGame() {
    if (!running) return;
    paused = true;
    show('pauseScreen');
}

function resumeGame() {
    paused = false;
    hide('pauseScreen');
    lastTime = performance.now();
    requestAnimationFrame(loop);
}

function quitGame() {
    running = false;
    paused = false;
    hide('pauseScreen');
    hide('endScreen');
    hide('pauseBtn');
    hide('panel1');
    hide('panel2');
    show('menuScreen');
}

function endGame(winner) {
    running = false;
    const txt = document.getElementById('winText');
    txt.textContent = `🎉 PLAYER ${winner} THẮNG!`;
    txt.style.color = players[winner].color;
    show('endScreen');
    if (navigator.vibrate) navigator.vibrate([100, 50, 100, 50, 200]);
}

// ============ SPAWN & COMMANDS ============
function spawnUnit(pid, uid) {
    if (!running || paused) return;
    
    const data = UNITS_DATA.find(u => u.id === uid);
    const p = players[pid];
    
    if (p.gold >= data.cost && p.pop + data.pop <= 50) {
        p.gold -= data.cost;
        p.pop += data.pop;
        
        const groundY = canvas.height * GROUND;
        const unit = {
            ...data,
            player: pid,
            x: p.spawnX + (Math.random() - 0.5) * 30,
            y: groundY,
            hp: data.hp,
            maxHp: data.hp,
            state: 'idle',
            facing: pid === 1,
            lastAtk: 0,
            anim: Math.random() * 10,
            carrying: 0,
            mineTime: 0,
            target: null
        };
        
        p.units.push(unit);
        
        effects.push({
            type: 'spawn', x: unit.x, y: groundY - data.h/2,
            r: 8, alpha: 1, color: p.color
        });
        
        if (navigator.vibrate) navigator.vibrate(20);
    }
}

function setCommand(pid, cmd) {
    if (!running || paused) return;
    players[pid].cmd = cmd;
    updateCmdUI(pid, cmd);
    if (navigator.vibrate) navigator.vibrate(15);
}

function updateCmdUI(pid, cmd) {
    ['attack', 'defend', 'garrison'].forEach(c => {
        const el = document.getElementById(`cmd${pid}_${c}`);
        if (el) el.classList.toggle('active', c === cmd);
    });
}

// ============ GAME LOOP ============
function loop(time) {
    if (!running || paused) return;
    
    const dt = Math.min((time - lastTime) / 1000, 0.05);
    lastTime = time;
    
    update(dt);
    render();
    
    requestAnimationFrame(loop);
}

function update(dt) {
    const groundY = canvas.height * GROUND;
    
    for (let pid of [1, 2]) {
        const p = players[pid];
        const enemy = players[pid === 1 ? 2 : 1];
        
        for (let u of p.units) {
            u.anim += dt * 7;
            
            if (u.isMiner) {
                updateMiner(u, p, groundY, dt);
            } else {
                updateCombat(u, p, enemy, groundY, dt);
            }
        }
        
        p.units = p.units.filter(u => u.hp > 0);
        p.pop = p.units.reduce((s, u) => s + u.pop, 0);
    }
    
    // Projectiles
    for (let proj of projectiles) {
        const dx = proj.tx - proj.x;
        const dy = proj.ty - proj.y;
        const dist = Math.sqrt(dx * dx + dy * dy);
        
        if (dist < proj.spd) {
            const enemy = players[proj.pid === 1 ? 2 : 1];
            
            if (proj.splash) {
                for (let u of enemy.units) {
                    const d = Math.abs(u.x - proj.tx);
                    if (d < proj.splash) {
                        u.hp -= proj.dmg * (1 - d / proj.splash);
                    }
                }
                effects.push({ type: 'explode', x: proj.tx, y: proj.ty, r: 5, max: proj.splash, alpha: 1, color: '#9400D3' });
            } else {
                for (let u of enemy.units) {
                    if (Math.abs(u.x - proj.tx) < 20) {
                        u.hp -= proj.dmg;
                        effects.push({ type: 'hit', x: u.x, y: groundY - u.h - 5, text: '-' + proj.dmg, alpha: 1 });
                        break;
                    }
                }
            }
            proj.dead = true;
        } else {
            proj.x += (dx / dist) * proj.spd;
            proj.y += (dy / dist) * proj.spd;
        }
    }
    projectiles = projectiles.filter(p => !p.dead);
    
    // Effects
    for (let e of effects) {
        e.alpha -= dt * 2.5;
        if (e.type === 'hit' || e.type === 'gold') e.y -= dt * 35;
        if (e.type === 'explode') e.r = Math.min(e.r + dt * 80, e.max);
        if (e.type === 'spawn') e.r += dt * 30;
    }
    effects = effects.filter(e => e.alpha > 0);
    
    // AI
    if (mode === 'pve') updateAI(dt);
    
    // Win check
    if (players[1].statue <= 0) endGame(2);
    if (players[2].statue <= 0) endGame(1);
    
    updateHUD();
}

function updateMiner(u, p, groundY, dt) {
    const homeX = p.spawnX;
    
    if (!u.target) {
        const myMines = mines.filter(m => m.owner === u.player);
        let best = null, bestD = Infinity;
        for (let m of myMines) {
            const d = Math.abs(m.x - u.x);
            if (d < bestD) { bestD = d; best = m; }
        }
        u.target = best;
    }
    
    if (u.carrying > 0) {
        if (Math.abs(u.x - homeX) > 25) {
            u.x += (homeX > u.x ? 1 : -1) * u.spd;
            u.state = 'return';
            u.facing = homeX > u.x;
        } else {
            p.gold += u.carrying;
            effects.push({ type: 'gold', x: u.x, y: groundY - 45, text: '+' + u.carrying, alpha: 1 });
            u.carrying = 0;
            u.state = 'idle';
        }
    } else if (u.target) {
        if (Math.abs(u.x - u.target.x) > 20) {
            u.x += (u.target.x > u.x ? 1 : -1) * u.spd;
            u.state = 'walk';
            u.facing = u.target.x > u.x;
        } else {
            u.state = 'mine';
            u.mineTime += dt * 1000;
            if (u.mineTime >= 1200) {
                u.carrying = 50;
                u.mineTime = 0;
            }
        }
    }
}

function updateCombat(u, p, enemy, groundY, dt) {
    const cmd = p.cmd;
    const homeX = p.spawnX;
    const enemyX = enemy.spawnX;
    
    if (cmd === 'garrison') {
        if (Math.abs(u.x - homeX) > 50) {
            u.x += (homeX > u.x ? 1 : -1) * u.spd;
            u.state = 'move';
            u.facing = homeX > u.x;
        } else {
            u.state = 'idle';
        }
        return;
    }
    
    let target = null, minD = Infinity;
    for (let e of enemy.units) {
        const d = Math.abs(e.x - u.x);
        if (d < minD) { minD = d; target = e; }
    }
    
    if (target && minD <= u.range) {
        u.state = 'attack';
        u.facing = target.x > u.x;
        
        if (Date.now() - u.lastAtk > u.atkSpd) {
            doAttack(u, target, enemy, groundY);
            u.lastAtk = Date.now();
        }
    } else if (target && (cmd === 'attack' || minD < 160)) {
        u.x += (target.x > u.x ? 1 : -1) * u.spd;
        u.state = 'move';
        u.facing = target.x > u.x;
    } else if (cmd === 'attack') {
        if (Math.abs(u.x - enemyX) > u.range) {
            u.x += (enemyX > u.x ? 1 : -1) * u.spd;
            u.state = 'move';
            u.facing = enemyX > u.x;
        } else {
            u.state = 'attack';
            if (Date.now() - u.lastAtk > u.atkSpd) {
                enemy.statue -= u.dmg;
                u.lastAtk = Date.now();
                effects.push({ type: 'hit', x: enemyX, y: groundY - 80, text: '-' + u.dmg, alpha: 1 });
            }
        }
    } else {
        u.state = 'idle';
    }
}

function doAttack(u, target, enemy, groundY) {
    if (u.ranged) {
        projectiles.push({
            x: u.x, y: groundY - u.h/2,
            tx: target.x, ty: groundY - target.h/2,
            spd: 8, dmg: u.dmg, pid: u.player,
            splash: u.splash || 0, magic: u.magic
        });
    } else {
        let dmg = u.dmg;
        if (target.shield && Math.random() < target.shield) {
            dmg *= 0.25;
            effects.push({ type: 'hit', x: target.x, y: groundY - target.h - 8, text: '🛡️', alpha: 1 });
        }
        target.hp -= dmg;
        if (u.kb) target.x += (target.x > u.x ? 1 : -1) * u.kb;
        effects.push({ type: 'hit', x: target.x, y: groundY - target.h - 5, text: '-' + Math.floor(dmg), alpha: 1 });
        
        if (target.hp <= 0) {
            effects.push({ type: 'spawn', x: target.x, y: groundY - target.h/2, r: 5, alpha: 1, color: '#ff0000' });
        }
    }
}

function updateAI(dt) {
    const ai = players[2];
    
    if (Math.random() < 0.028) {
        const types = ['miner', 'sword', 'archer', 'spear', 'mage', 'giant'];
        const weights = [0.32, 0.26, 0.18, 0.13, 0.07, 0.04];
        let r = Math.random(), c = 0;
        for (let i = 0; i < types.length; i++) {
            c += weights[i];
            if (r < c) { spawnUnit(2, types[i]); break; }
        }
    }
    
    const combat = ai.units.filter(u => !u.isMiner).length;
    if (combat >= 5 && ai.statue > 350) {
        if (ai.cmd !== 'attack') setCommand(2, 'attack');
    } else if (ai.statue < 250) {
        if (ai.cmd !== 'defend') setCommand(2, 'defend');
    }
}

function updateHUD() {
    document.getElementById('gold1').textContent = Math.floor(players[1].gold);
    document.getElementById('pop1').textContent = players[1].pop;
    document.getElementById('gold2').textContent = Math.floor(players[2].gold);
    document.getElementById('pop2').textContent = players[2].pop;
}

// ============ RENDER ============
function render() {
    const groundY = canvas.height * GROUND;
    
    // Sky
    const sky = ctx.createLinearGradient(0, 0, 0, groundY);
    sky.addColorStop(0, '#87CEEB');
    sky.addColorStop(1, '#c8e6f5');
    ctx.fillStyle = sky;
    ctx.fillRect(0, 0, canvas.width, groundY);
    
    // Sun
    ctx.fillStyle = '#FFD700';
    ctx.beginPath();
    ctx.arc(canvas.width - 40, 35, 20, 0, Math.PI * 2);
    ctx.fill();
    
    // Clouds
    ctx.fillStyle = 'rgba(255,255,255,0.8)';
    const t = Date.now() * 0.003;
    for (let i = 0; i < 5; i++) {
        const cx = ((i * 250 + t) % (canvas.width + 100)) - 50;
        drawCloud(cx, 30 + i * 12, 15 + i * 2);
    }
    
    // Ground
    const gnd = ctx.createLinearGradient(0, groundY, 0, canvas.height);
    gnd.addColorStop(0, '#228B22');
    gnd.addColorStop(1, '#0d3d0d');
    ctx.fillStyle = gnd;
    ctx.fillRect(0, groundY, canvas.width, canvas.height - groundY);
    
    ctx.save();
    ctx.scale(scale, 1);
    
    // Mines
    for (let m of mines) {
        ctx.fillStyle = '#3d2817';
        ctx.fillRect(m.x - 18, groundY - 10, 36, 10);
        
        ctx.fillStyle = '#FFD700';
        ctx.beginPath();
        ctx.moveTo(m.x - 14, groundY - 10);
        ctx.lineTo(m.x, groundY - 28);
        ctx.lineTo(m.x + 14, groundY - 10);
        ctx.fill();
        
        if (Math.random() > 0.95) {
            ctx.fillStyle = '#FFF';
            ctx.beginPath();
            ctx.arc(m.x + (Math.random()-0.5)*20, groundY - 18, 2, 0, Math.PI*2);
            ctx.fill();
        }
        
        ctx.fillStyle = m.owner === 1 ? '#4a9eff' : '#ff4a4a';
        ctx.beginPath();
        ctx.arc(m.x, groundY - 35, 4, 0, Math.PI * 2);
        ctx.fill();
    }
    
    // Statues with Health Bars
    for (let pid of [1, 2]) {
        const p = players[pid];
        const x = p.spawnX;
        
        // Platform
        ctx.fillStyle = '#555';
        ctx.fillRect(x - 30, groundY - 12, 60, 12);
        ctx.fillRect(x - 24, groundY - 24, 48, 12);
        
        // Body
        ctx.fillStyle = p.color;
        ctx.fillRect(x - 15, groundY - 70, 30, 46);
        
        // Head
        ctx.beginPath();
        ctx.arc(x, groundY - 82, 14, 0, Math.PI * 2);
        ctx.fill();
        
        // Crown
        ctx.fillStyle = '#FFD700';
        ctx.beginPath();
        ctx.moveTo(x - 8, groundY - 92);
        ctx.lineTo(x, groundY - 105);
        ctx.lineTo(x + 8, groundY - 92);
        ctx.fill();
        
        // Eyes
        ctx.fillStyle = '#fff';
        ctx.beginPath();
        ctx.arc(x - 4, groundY - 84, 2.5, 0, Math.PI * 2);
        ctx.arc(x + 4, groundY - 84, 2.5, 0, Math.PI * 2);
        ctx.fill();
        
        // ========== HEALTH BAR ==========
        const barWidth = 70;
        const barHeight = 10;
        const barX = x - barWidth / 2;
        const barY = groundY - 125;
        const hpPercent = Math.max(0, p.statue / p.maxStatue);
        
        // Background
        ctx.fillStyle = '#333';
        ctx.fillRect(barX - 2, barY - 2, barWidth + 4, barHeight + 4);
        
        // Border
        ctx.strokeStyle = '#000';
        ctx.lineWidth = 2;
        ctx.strokeRect(barX - 2, barY - 2, barWidth + 4, barHeight + 4);
        
        // Health fill
        let hpColor;
        if (hpPercent > 0.6) {
            hpColor = '#2ecc71';
        } else if (hpPercent > 0.3) {
            hpColor = '#f39c12';
        } else {
            hpColor = '#e74c3c';
        }
        
        // Gradient health bar
        const hpGrad = ctx.createLinearGradient(barX, barY, barX, barY + barHeight);
        hpGrad.addColorStop(0, hpColor);
        hpGrad.addColorStop(0.5, hpColor);
        hpGrad.addColorStop(1, shadeColor(hpColor, -30));
        ctx.fillStyle = hpGrad;
        ctx.fillRect(barX, barY, barWidth * hpPercent, barHeight);
        
        // HP Text
        ctx.fillStyle = '#fff';
        ctx.font = 'bold 10px Arial';
        ctx.textAlign = 'center';
        ctx.strokeStyle = '#000';
        ctx.lineWidth = 2;
        const hpText = Math.floor(p.statue) + ' / ' + p.maxStatue;
        ctx.strokeText(hpText, x, barY + barHeight - 1);
        ctx.fillText(hpText, x, barY + barHeight - 1);
        
        // Player label
        ctx.font = 'bold 12px Arial';
        ctx.fillStyle = p.color;
        ctx.strokeStyle = '#000';
        ctx.lineWidth = 3;
        const label = pid === 1 ? '🔵 P1' : 'P2 🔴';
        ctx.strokeText(label, x, barY - 8);
        ctx.fillText(label, x, barY - 8);
    }
    
    // Units
    for (let pid of [1, 2]) {
        for (let u of players[pid].units) {
            drawUnit(u, players[pid].color, groundY);
        }
    }
    
    // Projectiles
    for (let p of projectiles) {
        ctx.fillStyle = p.magic ? '#9400D3' : '#8B4513';
        ctx.beginPath();
        ctx.arc(p.x, p.y, p.magic ? 5 : 3, 0, Math.PI * 2);
        ctx.fill();
        
        if (p.magic) {
            ctx.fillStyle = 'rgba(148,0,211,0.3)';
            ctx.beginPath();
            ctx.arc(p.x, p.y, 8, 0, Math.PI * 2);
            ctx.fill();
        }
    }
    
    // Effects
    for (let e of effects) {
        ctx.globalAlpha = e.alpha;
        if (e.type === 'hit') {
            ctx.fillStyle = '#FF3333';
            ctx.font = 'bold 11px Arial';
            ctx.textAlign = 'center';
            ctx.fillText(e.text, e.x, e.y);
        } else if (e.type === 'gold') {
            ctx.fillStyle = '#FFD700';
            ctx.font = 'bold 13px Arial';
            ctx.textAlign = 'center';
            ctx.fillText(e.text, e.x, e.y);
        } else if (e.type === 'spawn' || e.type === 'explode') {
            ctx.strokeStyle = e.color || '#9400D3';
            ctx.lineWidth = 2;
            ctx.beginPath();
            ctx.arc(e.x, e.y, e.r, 0, Math.PI * 2);
            ctx.stroke();
        }
        ctx.globalAlpha = 1;
    }
    
    ctx.restore();
}

function shadeColor(color, percent) {
    const num = parseInt(color.replace('#', ''), 16);
    const amt = Math.round(2.55 * percent);
    const R = (num >> 16) + amt;
    const G = (num >> 8 & 0x00FF) + amt;
    const B = (num & 0x0000FF) + amt;
    return '#' + (0x1000000 + 
        (R < 255 ? (R < 1 ? 0 : R) : 255) * 0x10000 + 
        (G < 255 ? (G < 1 ? 0 : G) : 255) * 0x100 + 
        (B < 255 ? (B < 1 ? 0 : B) : 255)
    ).toString(16).slice(1);
}

function drawCloud(x, y, s) {
    ctx.beginPath();
    ctx.arc(x, y, s, 0, Math.PI * 2);
    ctx.arc(x + s * 0.8, y - s * 0.3, s * 0.7, 0, Math.PI * 2);
    ctx.arc(x + s * 1.5, y, s * 0.8, 0, Math.PI * 2);
    ctx.fill();
}

function drawUnit(u, color, groundY) {
    const x = u.x;
    const unitScale = u.id === 'giant' ? 1.5 : 1;
    const flip = u.facing ? 1 : -1;
    const bob = Math.sin(u.anim) * 1.5;
    
    ctx.save();
    ctx.translate(x, groundY);
    ctx.scale(flip * unitScale, unitScale);
    
    // Carrying gold
    if (u.carrying > 0) {
        ctx.font = '10px Arial';
        ctx.textAlign = 'center';
        ctx.fillText('💰', 0, -u.h - 10);
    }
    
    // Mining bar
    if (u.state === 'mine' && u.mineTime > 0) {
        ctx.fillStyle = '#333';
        ctx.fillRect(-10, -u.h - 8, 20, 3);
        ctx.fillStyle = '#FFD700';
        ctx.fillRect(-10, -u.h - 8, 20 * (u.mineTime / 1200), 3);
    }
    
    const h = -u.h;
    
    // Shadow
    ctx.fillStyle = 'rgba(0,0,0,0.2)';
    ctx.beginPath();
    ctx.ellipse(0, -1, 8, 3, 0, 0, Math.PI * 2);
    ctx.fill();
    
    // Head
    ctx.fillStyle = '#FDB';
    ctx.beginPath();
    ctx.arc(0, h + 6 + bob, 5, 0, Math.PI * 2);
    ctx.fill();
    ctx.strokeStyle = '#000';
    ctx.lineWidth = 0.5;
    ctx.stroke();
    
    // Headband
    ctx.fillStyle = color;
    ctx.fillRect(-5, h + 3 + bob, 10, 3);
    
    // Body
    ctx.strokeStyle = u.color;
    ctx.lineWidth = 2.5;
    ctx.beginPath();
    ctx.moveTo(0, h + 11 + bob);
    ctx.lineTo(0, h + 24 + bob);
    ctx.stroke();
    
    // Arms
    const armY = u.state === 'attack' ? 8 : 4;
    ctx.beginPath();
    ctx.moveTo(0, h + 14 + bob);
    ctx.lineTo(7, h + 14 + armY + bob);
    ctx.moveTo(0, h + 14 + bob);
    ctx.lineTo(-7, h + 14 + armY + bob);
    ctx.stroke();
    
    // Legs
    const legOff = (u.state === 'move' || u.state === 'walk' || u.state === 'return') ? bob * 1.2 : 0;
    ctx.beginPath();
    ctx.moveTo(0, h + 24 + bob);
    ctx.lineTo(-4, -2 + legOff);
    ctx.moveTo(0, h + 24 + bob);
    ctx.lineTo(4, -2 - legOff);
    ctx.stroke();
    
    // Weapon
    ctx.lineWidth = 1.8;
    if (u.id === 'miner') {
        ctx.strokeStyle = '#654';
        ctx.beginPath();
        ctx.moveTo(7, h + 16 + bob);
        ctx.lineTo(13, h + 8 + bob);
        ctx.moveTo(11, h + 10 + bob);
        ctx.lineTo(16, h + 13 + bob);
        ctx.stroke();
    } else if (u.id === 'sword') {
        ctx.strokeStyle = '#CCC';
        ctx.beginPath();
        ctx.moveTo(7, h + 18 + bob);
        ctx.lineTo(16, h + 6 + bob);
        ctx.stroke();
    } else if (u.id === 'archer') {
        ctx.strokeStyle = '#654';
        ctx.lineWidth = 1.5;
        ctx.beginPath();
        ctx.arc(9, h + 16 + bob, 7, -1.4, 1.4);
        ctx.stroke();
    } else if (u.id === 'spear') {
        ctx.strokeStyle = '#654';
        ctx.beginPath();
        ctx.moveTo(7, h + 12 + bob);
        ctx.lineTo(18, h - 2 + bob);
        ctx.stroke();
        ctx.fillStyle = color;
        ctx.beginPath();
        ctx.ellipse(-9, h + 18 + bob, 4, 8, 0, 0, Math.PI * 2);
        ctx.fill();
    } else if (u.id === 'mage') {
        ctx.strokeStyle = '#93D';
        ctx.lineWidth = 2;
        ctx.beginPath();
        ctx.moveTo(7, h + 20 + bob);
        ctx.lineTo(11, h + 2 + bob);
        ctx.stroke();
        ctx.fillStyle = '#93D';
        ctx.beginPath();
        ctx.arc(11, h - 2 + bob, 4, 0, Math.PI * 2);
        ctx.fill();
    } else if (u.id === 'giant') {
        ctx.strokeStyle = '#432';
        ctx.lineWidth = 5;
        ctx.beginPath();
        ctx.moveTo(10, h + 18 + bob);
        ctx.lineTo(26, h + 2 + bob);
        ctx.stroke();
    }
    
    ctx.restore();
    
    // HP bar
    if (u.hp < u.maxHp) {
        const barW = 18 * unitScale * scale;
        const pct = u.hp / u.maxHp;
        const barX = x * scale - barW / 2;
        const barY = groundY - u.h * unitScale - 10;
        
        ctx.fillStyle = '#333';
        ctx.fillRect(barX, barY, barW, 3);
        ctx.fillStyle = pct > 0.5 ? '#4C5' : pct > 0.25 ? '#FC0' : '#F44';
        ctx.fillRect(barX, barY, barW * pct, 3);
    }
}

window.onload = init;
</script>
</body>
</html>
