<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Anomaly — Calistenia (iOS / Sporty UI)</title>
<style>
  :root{ --bg:#071019; --card:#0f1720; --muted:#9fb0bd; --accent:#ff4757; --glass: rgba(255,255,255,0.04); }
  *{box-sizing:border-box}
  html,body{height:100%;margin:0;font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Arial;color:#fff;background:linear-gradient(180deg,#061018 0%,#071019 100%);}
  .app{max-width:980px;margin:12px auto;padding:14px}
  header{display:flex;align-items:center;justify-content:space-between;gap:12px}
  .brand{display:flex;flex-direction:column}
  .title{font-size:20px;font-weight:800}
  .subtitle{font-size:12px;color:var(--muted)}
  .topBtns{display:flex;gap:8px}
  .btn{background:var(--accent);border:none;color:white;padding:8px 12px;border-radius:12px;font-weight:700;cursor:pointer}
  .btn.ghost{background:transparent;border:1px solid rgba(255,255,255,0.05)}
  nav{display:flex;gap:8px;margin-top:12px;flex-wrap:wrap}
  nav button{background:transparent;border:none;color:var(--muted);padding:8px 10px;border-radius:12px}
  nav button.active{background:rgba(255,255,255,0.03);color:#fff}
  main{margin-top:12px}
  .card{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));padding:12px;border-radius:14px;border:1px solid var(--glass);}
  .grid2{display:grid;grid-template-columns:1fr 1fr;gap:12px}
  .weekGrid{display:grid;grid-template-columns:repeat(7,1fr);gap:8px;margin-top:10px}
  .day{min-height:80px;border-radius:12px;padding:8px;border:1px dashed rgba(255,255,255,0.03);background:linear-gradient(180deg,rgba(255,255,255,0.01),transparent)}
  .routineChip{background:rgba(255,255,255,0.03);padding:6px;border-radius:10px;margin-top:6px;font-size:12px;cursor:pointer}
  .timerBig{font-size:36px;font-weight:800;text-align:center}
  .timerControls{display:flex;gap:8px;justify-content:center;margin-top:8px}
  input,select{padding:8px;border-radius:8px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:white}
  table{width:100%;border-collapse:collapse;margin-top:10px}
  th,td{border-bottom:1px solid rgba(255,255,255,0.04);padding:8px;text-align:left}
  .small{font-size:13px;color:var(--muted)}
  .legend{display:flex;gap:8px;margin-top:8px}
  .badge{background:rgba(255,255,255,0.03);padding:6px;border-radius:8px}
  @media (max-width:820px){ .grid2{grid-template-columns:1fr} .weekGrid{grid-template-columns:repeat(4,1fr)} }
</style>
</head>
<body>
<div class="app">
  <header>
    <div class="brand">
      <div class="title">ANOMALY</div>
      <div class="subtitle">Calistenia — deportivo & adictivo</div>
    </div>
    <div class="topBtns">
      <button id="btnProfile" class="btn ghost">Perfil</button>
      <button id="btnLogin" class="btn">Login</button>
    </div>
  </header>

  <nav id="mainNav">
    <button class="active" data-view="inicio">Inicio</button>
    <button data-view="planner">Planificador</button>
    <button data-view="rutinas">Rutinas</button>
    <button data-view="body">Body</button>
    <button data-view="timers">Cronos</button>
    <button data-view="progs">Progresiones</button>
    <button data-view="guided">Guiado</button>
    <button data-view="hist">Historial</button>
    <button data-view="test">Test nivel</button>
  </nav>

  <main id="mainContent"></main>
</div>

<script>
/* ------------------ Datos (mantenidos) ------------------ */
const ejercicios = [
  {id:'pull',name:'Dominadas',zone:'espalda'},
  {id:'push',name:'Flexiones',zone:'pecho'},
  {id:'diamante',name:'Flexiones diamante',zone:'brazos'},
  {id:'fondos',name:'Fondos',zone:'brazos'},
  {id:'muscle',name:'Muscle-Ups',zone:'espalda'},
  {id:'squat',name:'Sentadillas',zone:'piernas'},
  {id:'plank',name:'Plank',zone:'core'},
  {id:'lunges',name:'Jump Lunges',zone:'piernas'}
];
const rutinas = [
  {nombre:'Brazos Avanzado', zone:'brazos', ejercicios:[{name:'Flexiones diamante', reps:12, series:3, descanso:60},{name:'Fondos en paralelas', reps:10, series:3, descanso:60},{name:'Flexiones normales', reps:15, series:3, descanso:60},{name:'Tríceps banco', reps:12, series:3, descanso:60},{name:'Curl isométrico', reps:20, series:3, descanso:45}]},
  {nombre:'Hombros & Core', zone:'hombros', ejercicios:[{name:'Pike push-ups', reps:10, series:3, descanso:60},{name:'Handstand hold', reps:40, series:3, descanso:60},{name:'Elevaciones laterales (banda)', reps:15, series:3, descanso:45},{name:'Plank', reps:45, series:3, descanso:45}]},
  {nombre:'Espalda Fuerte', zone:'espalda', ejercicios:[{name:'Pull-Ups', reps:6, series:3, descanso:90},{name:'Chin-Ups', reps:6, series:3, descanso:90},{name:'Inverted Rows', reps:12, series:3, descanso:60},{name:'Remo con banda', reps:10, series:3, descanso:60}]},
  {nombre:'Piernas Potentes', zone:'piernas', ejercicios:[{name:'Sentadillas', reps:20, series:3, descanso:60},{name:'Pistol Squats', reps:6, series:3, descanso:90},{name:'Jump Lunges', reps:12, series:3, descanso:60},{name:'Puente glúteo', reps:15, series:3, descanso:45}]},
  {nombre:'Core Trabajo', zone:'core', ejercicios:[{name:'Plank', reps:60, series:3, descanso:60},{name:'Hollow Hold', reps:40, series:3, descanso:60},{name:'Leg Raises', reps:15, series:3, descanso:45},{name:'Russian Twists', reps:30, series:3, descanso:45}]},
  {nombre:'Full Body Explosivo', zone:'full', ejercicios:[{name:'Burpees', reps:12, series:3, descanso:60},{name:'Muscle-Ups', reps:5, series:3, descanso:90},{name:'Dips', reps:12, series:3, descanso:60},{name:'Jumping Jacks', reps:40, series:3, descanso:45}]}
];

/* ------------------ Storage & helpers ------------------ */
const USERS_KEY = 'anomaly_usersData';
function getUsers(){ return JSON.parse(localStorage.getItem(USERS_KEY)||'{}'); }
function saveUsers(u){ localStorage.setItem(USERS_KEY, JSON.stringify(u)); }
function getCurrentUser(){ return localStorage.getItem('anomaly_user') || null; }

/* ------------------ Navigation wiring (fix: buttons now work) ------------------ */
const nav = document.getElementById('mainNav');
nav.addEventListener('click', (e)=>{ const b = e.target.closest('button'); if(!b) return; const view = b.dataset.view; if(!view) return; // visual
  document.querySelectorAll('#mainNav button').forEach(x=>x.classList.remove('active')); b.classList.add('active'); // show
  switch(view){
    case 'inicio': showInicio(); break;
    case 'planner': showPlanner(); break;
    case 'rutinas': showRutinas(); break;
    case 'body': showBody(); break;
    case 'timers': showTimers(); break;
    case 'progs': showProgressions(); break;
    case 'guided': showGuided(); break;
    case 'hist': showHistory(); break;
    case 'test': showTest(); break;
  }
});

/* ------------------ HEADER actions ------------------ */
document.getElementById('btnLogin').addEventListener('click', ()=>{ const name = prompt('Usuario (demo):'); const pass = prompt('Contraseña (demo):'); if(!name || !pass) return; demoRegister(name,pass); alert('Login/demo registrado.'); showInicio(); });
document.getElementById('btnProfile').addEventListener('click', ()=>{ const u = getCurrentUser(); alert('Usuario: '+(u||'—')+' (local demo)'); });

/* ------------------ ORIGINAL VIEWS RESTORED ------------------ */
function showInicio(){ const cur = getCurrentUser(); const name = cur || 'Invitado'; const level = estimateLevel(cur); const next = nextPlanned(cur) || 'Sin plan'; const minutes = weeklyMinutes(cur);
  document.getElementById('mainContent').innerHTML = `
    <div class="card">
      <div style="display:flex;justify-content:space-between;align-items:center"><div><div style="font-weight:800">Bienvenido, ${name}</div><div class="small">Nivel: ${level}</div></div><div class="badge">${next}</div></div>
      <div style="margin-top:10px" class="grid2">
        <div class="card"><div style="font-weight:800">Accesos rápidos</div><div style="margin-top:8px"><button class="btn" onclick="showPlanner()">Planificador</button> <button class="btn ghost" onclick="showTimers()">Cronos</button> <button class="btn" onclick="showGuided()">Guiado</button></div></div>
        <div class="card"><div style="font-weight:800">Estadísticas</div><div style="margin-top:8px" class="small">Tiempo esta semana: ${minutes} min</div></div>
      </div>
    </div>
    <div class="card" style="margin-top:12px"><div style="font-weight:800">Rutinas</div><div style="margin-top:8px;display:flex;flex-wrap:wrap;gap:8px">${rutinas.map(r=>`<div class="routineChip" data-rutina="${encodeURIComponent(r.nombre)}">${r.nombre}</div>`).join('')}</div></div>
  `;
}

function showCuenta(){ const cur = getCurrentUser()||'—'; document.getElementById('mainContent').innerHTML = `<div class="card"><h3>Cuenta</h3><p>Usuario: ${cur}</p><div style="margin-top:8px"><button class="btn ghost" onclick="deleteAccount()">Eliminar cuenta (local)</button></div></div>`; }
function deleteAccount(){ if(!confirm('Eliminar cuenta local?')) return; const cur=getCurrentUser(); if(cur){ const users=getUsers(); delete users[cur]; saveUsers(users); localStorage.removeItem('anomaly_user'); localStorage.removeItem('anomaly_pass'); alert('Cuenta eliminada'); location.reload(); } }

function showRangos(){ const cur=getCurrentUser(); const users=getUsers(); let html=`<div class="card"><h3>Rangos por ejercicio</h3>`; ejercicios.forEach(e=>{ const val = cur && users[cur] && users[cur].reps? users[cur].reps[e.id]||0 : parseInt(localStorage.getItem('reps_'+e.id)||0); const niveles=[5,10,15,25,40,60]; let rango='Bronce'; if(val>=niveles[5]) rango='Anomalía'; else if(val>=niveles[4]) rango='Élite'; else if(val>=niveles[3]) rango='Platino'; else if(val>=niveles[2]) rango='Diamante'; else if(val>=niveles[1]) rango='Oro'; else if(val>=niveles[0]) rango='Plata'; html+=`<p>${e.name}: <b>${rango}</b> — ${val} reps</p>`; }); html+=`</div>`; document.getElementById('mainContent').innerHTML=html; }

function showRutinas(){ let html=`<div class="card"><h3>Rutinas</h3><div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(160px,1fr));gap:8px;margin-top:8px">`; rutinas.forEach(r=> html += `<div class="card" style="cursor:pointer" onclick="viewRoutine('${encodeURIComponent(r.nombre)}')"><div style="font-weight:800">${r.nombre}</div><div class="small">${r.ejercicios.length} ejercicios</div></div>`); html+=`</div><div id="rutinaDetalle" style="margin-top:12px"></div></div>`; document.getElementById('mainContent').innerHTML = html; }
function viewRoutine(encodedName){ const name = decodeURIComponent(encodedName); const r = rutinas.find(x=>x.nombre===name); if(!r) return; let html = `<div class="card"><h4>${r.nombre}</h4><table><tr><th>Ejercicio</th><th>Series</th><th>Reps</th><th>Descanso</th></tr>`; r.ejercicios.forEach(e=> html+=`<tr><td>${e.name}</td><td>${e.series}</td><td>${e.reps}</td><td>${e.descanso}s</td></tr>`); html+=`</table><div style="margin-top:8px"><button class="btn" onclick="markRoutine('${encodeURIComponent(r.nombre)}')">Hecho</button></div></div>`; document.getElementById('rutinaDetalle').innerHTML = html; }
function markRoutine(encName){ const name=decodeURIComponent(encName); const cur=getCurrentUser(); if(!cur) return alert('Inicia sesión'); const users=getUsers(); if(!users[cur]) users[cur]={reps:{},rutinas:{}}; users[cur].rutinas[name] = (users[cur].rutinas[name]||0)+1; if(users[cur].rutinas[name]>100) users[cur].rutinas[name]=100; saveUsers(users); alert('Rutina marcada'); showRutinas(); }

function showBody(){ // simplified visual body + coloring using stored rutinas
  const cur = getCurrentUser(); const users = getUsers(); const counts = {brazos:0,hombros:0,pecho:0,espalda:0,core:0,piernas:0,full:0}; if(cur && users[cur]){ for(const r of rutinas){ const cnt = users[cur].rutinas && users[cur].rutinas[r.nombre] ? users[cur].rutinas[r.nombre] : 0; counts[r.zone] = (counts[r.zone]||0) + cnt; if(r.zone==='full'){ counts.brazos += cnt*0.5; counts.pecho += cnt*0.5; counts.espalda += cnt*0.5; counts.piernas += cnt*0.5; counts.core += cnt*0.5; } } }
  const colorFor=(n)=>{ const v = Math.min(100,Math.round(n)); if(v<33) return 'linear-gradient(180deg, #2ecc71,#27ae60)'; if(v<66) return 'linear-gradient(180deg,#ffb347,#ff7b50)'; return 'linear-gradient(180deg,#8a2be2,#6a0dad)'; };
  document.getElementById('mainContent').innerHTML = `<div class="card"><h3>Body</h3><div style="display:flex;gap:12px;align-items:center"><div style="width:220px;height:420px;background:#0b1114;border-radius:14px;position:relative;padding:12px">`+
    `<div style="position:absolute;left:40px;top:40px;width:140px;height:60px;border-radius:12px;background:${colorFor(counts.hombros)}"></div>`+
    `<div style="position:absolute;left:60px;top:120px;width:100px;height:80px;border-radius:12px;background:${colorFor(counts.pecho)}"></div>`+
    `<div style="position:absolute;left:60px;top:220px;width:100px;height:60px;border-radius:12px;background:${colorFor(counts.core)}"></div>`+
    `<div style="position:absolute;left:40px;top:300px;width:60px;height:120px;border-radius:12px;background:${colorFor(counts.piernas)}"></div>`+
    `<div style="position:absolute;left:140px;top:300px;width:60px;height:120px;border-radius:12px;background:${colorFor(counts.piernas)}"></div>`+
    `</div><div style="flex:1">`+
    `<div class="small">Distribución por zona (basado en rutinas hechas)</div><div style="margin-top:8px">`+Object.keys(counts).map(k=>`<div style="display:flex;justify-content:space-between;margin-top:6px"><div>${k}</div><div>${Math.round(counts[k])}</div></div>`).join('')+`</div></div></div></div>`;
}

function showRankings(){ const users = getUsers(); const arr=[]; for(const u in users){ let total=0; ejercicios.forEach(e=> total += Number(users[u].reps && users[u].reps[e.id]? users[u].reps[e.id]:0)); let rutCount=0; for(const r in (users[u].rutinas||{})) rutCount+=users[u].rutinas[r]||0; const score = total + rutCount*5; arr.push({user:u,score, totalReps:total, rutinas:rutCount}); } arr.sort((a,b)=>b.score-a.score); let html=`<div class="card"><h3>Ranking Top 3</h3><table><tr><th>Puesto</th><th>Usuario</th><th>Puntuación</th><th>Reps</th><th>Rutinas</th></tr>`; for(let i=0;i<3;i++){ if(arr[i]) html+=`<tr><td>${i+1}</td><td>${arr[i].user}</td><td>${arr[i].score}</td><td>${arr[i].totalReps}</td><td>${arr[i].rutinas}</td></tr>`; } html+=`</table></div>`; document.getElementById('mainContent').innerHTML = html; }

/* ------------------ NEW FEATURES (Timely + Planner + Progressions + Guided + History + Test) ------------------ */
// Planner
function showPlanner(){ const cur=getCurrentUser(); const plan = JSON.parse(localStorage.getItem('anomaly_planner')||'{}'); if(!plan[cur]) plan[cur]={}; const p = plan[cur]; const today = new Date(); const days = [...Array(7)].map((_,i)=>{ const d=new Date(today); d.setDate(today.getDate()+i); return d; });
  let html = `<div class="card"><h3>Planificador semanal</h3><div class="weekGrid">`;
  days.forEach(d=>{ const iso=d.toISOString().slice(0,10); const label=d.toLocaleDateString(undefined,{weekday:'short',day:'numeric'}); const n = p[iso]||''; html+=`<div class="day"><div style="font-weight:700">${label}</div><div id="slot_${iso}">${n?`<div class='routineChip' data-rutina='${encodeURIComponent(n)}'>${n}</div>`:''}</div><div style='margin-top:8px'><button class='btn ghost' onclick="promptAssign('${iso}')">Asignar</button></div></div>`; });
  html += `</div><div style="margin-top:8px"><input id="plannerName" placeholder="Nombre rutina" style="padding:8px;border-radius:8px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:white" /><button class="btn" onclick="assignToday()">Asignar hoy</button></div></div>`;
  document.getElementById('mainContent').innerHTML = html;
}
function promptAssign(iso){ const val = prompt('Asignar rutina para '+iso+' (nombre)'); if(val===null) return; const cur=getCurrentUser(); if(!cur){ alert('Inicia sesión para usar el planificador'); return; } const plan=JSON.parse(localStorage.getItem('anomaly_planner')||'{}'); if(!plan[cur]) plan[cur]={}; if(val.trim()==='') delete plan[cur][iso]; else plan[cur][iso]=val.trim(); localStorage.setItem('anomaly_planner', JSON.stringify(plan)); showPlanner(); }
function assignToday(){ const val=document.getElementById('plannerName').value.trim(); if(!val) return alert('Escribe nombre'); const cur=getCurrentUser(); if(!cur) return alert('Inicia sesión'); const plan=JSON.parse(localStorage.getItem('anomaly_planner')||'{}'); if(!plan[cur]) plan[cur]={}; const iso=new Date().toISOString().slice(0,10); plan[cur][iso]=val; localStorage.setItem('anomaly_planner', JSON.stringify(plan)); showPlanner(); }

// Timers (Cronómetro, EMOM, AMRAP)
let inter=null; let swStartTs=0; let swElapsed=0; function showTimers(){ document.getElementById('mainContent').innerHTML = `<div class='grid2'><div class='card'><div style='font-weight:800'>Cronómetro</div><div class='timerBig' id='swDisp'>00:00:00</div><div class='timerControls'><button class='btn' id='swStartBtn'>Iniciar</button><button class='btn ghost' id='swStopBtn'>Pausar</button><button class='btn ghost' id='swResetBtn'>Reset</button></div></div><div class='card'><div style='font-weight:800'>EMOM / AMRAP</div><div style='margin-top:8px'><label class='small'>EMOM (seg)</label><input id='emomVal' type='number' value='60' /></div><div style='margin-top:8px'><label class='small'>AMRAP (seg)</label><input id='amrapVal' type='number' value='300' /></div><div style='margin-top:8px'><button class='btn' id='startEmom'>Iniciar EMOM</button><button class='btn ghost' id='startAmrap'>Iniciar AMRAP</button></div><div id='emamDisp' style='margin-top:8px'></div></div></div>`;
  document.getElementById('swStartBtn').addEventListener('click', ()=>{ if(inter) return; swStartTs = Date.now() - swElapsed; inter = setInterval(()=>{ swElapsed = Date.now()-swStartTs; document.getElementById('swDisp').innerText = formatTime(swElapsed); },200); });
  document.getElementById('swStopBtn').addEventListener('click', ()=>{ if(inter) clearInterval(inter); inter=null; });
  document.getElementById('swResetBtn').addEventListener('click', ()=>{ if(inter) clearInterval(inter); inter=null; swElapsed=0; document.getElementById('swDisp').innerText='00:00:00'; });
  document.getElementById('startEmom').addEventListener('click', ()=>{ startEMOM(Number(document.getElementById('emomVal').value||60)); });
  document.getElementById('startAmrap').addEventListener('click', ()=>{ startAMRAP(Number(document.getElementById('amrapVal').value||300)); });
}
function formatTime(ms){ const s=Math.floor(ms/1000); const hh=Math.floor(s/3600); const mm=Math.floor((s%3600)/60); const ss=s%60; return `${String(hh).padStart(2,'0')}:${String(mm).padStart(2,'0')}:${String(ss).padStart(2,'0')}`; }
let emomInterval=null; function startEMOM(sec){ if(emomInterval) clearInterval(emomInterval); let next = Date.now(); let round=0; emomInterval = setInterval(()=>{ const now=Date.now(); if(now>=next){ round++; notify('EMOM: ronda '+round); next += sec*1000; } const rem=Math.max(0,Math.round((next-now)/1000)); document.getElementById('emamDisp').innerText = `Ronda ${round} · restante ${rem}s`; },200); }
let amrapInterval=null; function startAMRAP(dur){ if(amrapInterval) clearInterval(amrapInterval); const start=Date.now(); amrapInterval = setInterval(()=>{ const now=Date.now(); const el=Math.floor((now-start)/1000); const rem=Math.max(0,dur-el); document.getElementById('emamDisp').innerText = `AMRAP · ${el}s / ${dur}s`; if(rem===0){ clearInterval(amrapInterval); amrapInterval=null; notify('AMRAP finalizado'); recordSession('amrap', dur); } },200); }

// Progressions
const progressions = [ {id:'muscle',title:'Muscle-Up',steps:['Dominadas 10-12','Pull overs','Transición asistida','Muscle-up completo']}, {id:'planche',title:'Planche',steps:['Tuck 20s','Advanced tuck','Straddle','Full']}, {id:'handstand',title:'Handstand',steps:['Pike','Wall 30s','Asistido','Free 60s']} ];
function showProgressions(){ document.getElementById('mainContent').innerHTML = `<div class='card'><h3>Progresiones</h3><div id='progList'></div></div>`; const el=document.getElementById('progList'); progressions.forEach(p=>{ const d=document.createElement('div'); d.className='card'; d.style.marginTop='8px'; d.innerHTML = `<div style='display:flex;justify-content:space-between;align-items:center'><div><div style='font-weight:800'>${p.title}</div><div class='small'>${p.steps[0]} → ${p.steps[p.steps.length-1]}</div></div><div><button class='btn ghost' data-prog='${p.id}'>Ver</button></div></div>`; el.appendChild(d); }); el.addEventListener('click',(e)=>{ const b=e.target.closest('button'); if(!b) return; const id=b.dataset.prog; openProgress(id); }); }
function openProgress(id){ const p = progressions.find(x=>x.id===id); if(!p) return; showModal(p.title, `<div>${p.steps.map((s,i)=>`<div style='margin-top:8px'>Paso ${i+1}: ${s} <button class='btn ghost' onclick="markProg('${id}',${i})">Hecho</button></div>`).join('')}</div>`); }
function markProg(id,step){ const cur=getCurrentUser(); if(!cur) return alert('Inicia sesión'); const key='anomaly_progress'; const all=JSON.parse(localStorage.getItem(key)||'{}'); if(!all[cur]) all[cur]={}; all[cur][id]=Math.max(all[cur][id]||0, step+1); localStorage.setItem(key, JSON.stringify(all)); notify('Paso registrado'); }

// Guided (voice + vibrate)
let guide={running:false,steps:[],idx:0};
function showGuided(){ document.getElementById('mainContent').innerHTML=`<div class='card'><h3>Entrenamiento guiado</h3><div class='small'>Elige una rutina y la app te guiará por series (voz + vibración)</div><div style='margin-top:8px;display:flex;flex-wrap:wrap;gap:8px'>${rutinas.map(r=>`<button class='btn ghost' data-rut='${encodeURIComponent(r.nombre)}'>${r.nombre}</button>`).join('')}</div><div id='guidedArea' style='margin-top:12px'></div></div>`; document.querySelectorAll('[data-rut]').forEach(b=>b.addEventListener('click',()=>startGuided(decodeURIComponent(b.dataset.rut)))); }
function startGuided(name){ const r = rutinas.find(x=>x.nombre===name); if(!r) return; guide.running=true; guide.steps=[]; r.ejercicios.forEach(ex=>{ for(let s=0;s<ex.series;s++) guide.steps.push({text:`${ex.name} — Serie ${s+1}: ${ex.reps} reps`, rest:ex.descanso}); }); guide.idx=0; renderGuideStep(); }
function renderGuideStep(){ if(!guide.running) return; const s=guide.steps[guide.idx]; document.getElementById('guidedArea').innerHTML = `<div class='card'><div style='font-weight:800'>${s.text}</div><div class='small'>Descanso: ${s.rest}s</div><div style='margin-top:8px'><button class='btn' id='doneBtn'>Completé</button> <button class='btn ghost' id='stopBtn'>Detener</button></div></div>`; speak(s.text); vibrate(200); document.getElementById('doneBtn').addEventListener('click', ()=>{ recordSession('routine_piece', s.rest); guide.idx++; if(guide.idx>=guide.steps.length){ notify('Completado'); guide.running=false; document.getElementById('guidedArea').innerHTML=`<div class='small'>Entrenamiento finalizado 🎉</div>`; } else countdown(s.rest, ()=>{ notify('Siguiente'); renderGuideStep(); }); }); document.getElementById('stopBtn').addEventListener('click', ()=>{ guide.running=false; document.getElementById('guidedArea').innerHTML=''; }); }
function countdown(sec,cb){ let left=sec; document.getElementById('guidedArea').querySelector('.card').innerHTML += `<div style='margin-top:8px' class='small'>Descanso: <span id='cd'>${left}</span>s</div>`; const it=setInterval(()=>{ left--; const sp=document.getElementById('cd'); if(sp) sp.innerText=left; if(left<=0){ clearInterval(it); cb(); } },1000); }

// History & stats
function recordSession(type,duration){ const cur=getCurrentUser()||'guest'; const h = JSON.parse(localStorage.getItem('anomaly_history')||'[]'); h.push({user:cur,type,duration:duration||0,ts:Date.now()}); localStorage.setItem('anomaly_history', JSON.stringify(h)); }
function showHistory(){ const h = JSON.parse(localStorage.getItem('anomaly_history')||'[]'); let html=`<div class='card'><h3>Historial</h3><table><tr><th>Fecha</th><th>Usuario</th><th>Tipo</th><th>Duración</th></tr>`; h.slice().reverse().forEach(it=> html+=`<tr><td>${new Date(it.ts).toLocaleString()}</td><td>${it.user}</td><td>${it.type}</td><td>${it.duration||0}</td></tr>`); html+=`</table></div>`; document.getElementById('mainContent').innerHTML=html; }

// Test
const testQ=[{q:'Dominadas (completas)?', opts:['0-2','3-6','7+']},{q:'Handstand (pared)?',opts:['0s','10-30s','30s+']},{q:'Muscle-up?',opts:['No','Asistido','Sí'] }];
function showTest(){ document.getElementById('mainContent').innerHTML = `<div class='card'><h3>Test nivel</h3><div id='testArea'></div></div>`; const el=document.getElementById('testArea'); el.innerHTML = testQ.map((t,i)=>`<div style='margin-top:8px'><div class='small'>${t.q}</div>${t.opts.map((o,j)=>`<div><label><input type='radio' name='q${i}' value='${j}' /> ${o}</label></div>`).join('')}</div>`).join('') + `<div style='margin-top:8px'><button class='btn' id='finishTest'>Finalizar</button></div>`; document.getElementById('finishTest').addEventListener('click', finishTest); }
function finishTest(){ const answers=[]; for(let i=0;i<testQ.length;i++){ const v=document.querySelector('input[name=q'+i+']:checked'); answers.push(v? v.value : null); } let score=0; if(answers[0]==='1') score+=1; if(answers[0]==='2') score+=2; if(answers[1]==='1') score+=1; if(answers[1]==='2') score+=2; if(answers[2]==='1') score+=1; if(answers[2]==='2') score+=2; const level = score<=2? 'Principiante': score<=4? 'Intermedio':'Avanzado'; const rec = level==='Avanzado'? 'Brazos Avanzado' : level==='Intermedio'? 'Espalda Fuerte' : 'Core Trabajo'; const cur=getCurrentUser(); if(cur){ const users=getUsers(); if(!users[cur]) users[cur]={reps:{},rutinas:{}}; users[cur].recommended = rec; saveUsers(users); } showModal('Resultado', `<div style='font-weight:800'>Nivel: ${level}</div><div class='small' style='margin-top:6px'>Rutina recomendada: ${rec}</div>`); }

/* ------------------ Utilities: modals, voice, vibrate, notify ------------------ */
function showModal(title,html){ const m=document.createElement('div'); m.className='card'; m.style.position='fixed'; m.style.left='50%'; m.style.top='50%'; m.style.transform='translate(-50%,-50%)'; m.style.zIndex=999; m.style.width='92%'; m.innerHTML = `<div style='display:flex;justify-content:space-between;align-items:center'><div style='font-weight:800'>${title}</div><button class='btn ghost' id='closeModal'>Cerrar</button></div><div style='margin-top:8px'>${html}</div>`; document.body.appendChild(m); document.getElementById('closeModal').addEventListener('click', ()=>{ if(m.parentElement) m.parentElement.removeChild(m); }); }
function speak(t){ if('speechSynthesis' in window){ const msg=new SpeechSynthesisUtterance(t); msg.lang='es-ES'; speechSynthesis.speak(msg); } }
function vibrate(ms){ if(navigator.vibrate) navigator.vibrate(ms); }
function notify(t){ speak(t); vibrate(150); const s=document.createElement('div'); s.className='card'; s.style.position='fixed'; s.style.right='18px'; s.style.bottom='18px'; s.style.zIndex=999; s.style.padding='8px'; s.innerText=t; document.body.appendChild(s); setTimeout(()=>{ if(s.parentElement) s.parentElement.removeChild(s); },2200); }

/* ------------------ Small helpers & demo account ------------------ */
function estimateLevel(user){ if(!user) return '—'; const users=getUsers(); const u=users[user]; if(!u) return '—'; let tot=0; ejercicios.forEach(e=> tot+= (u.reps && u.reps[e.id])? Number(u.reps[e.id]) : 0); if(tot>200) return 'Avanzado'; if(tot>50) return 'Intermedio'; return 'Principiante'; }
function nextPlanned(user){ const plan=JSON.parse(localStorage.getItem('anomaly_planner')||'{}'); if(!user) return null; const p=plan[user]; if(!p) return null; const today=new Date().toISOString().slice(0,10); return p[today]||null; }
function weeklyMinutes(user){ const h=JSON.parse(localStorage.getItem('anomaly_history')||'[]'); if(!user) return 0; const now=Date.now(); const week=7*24*3600*1000; return h.filter(x=>x.user===user && (now-x.ts)<week).reduce((s,x)=>s+(x.duration||0),0); }
function formatDate(ts){ return new Date(ts).toLocaleString(); }

function demoRegister(user,pass){ let users=getUsers(); if(users[user]) return; users[user]={reps:{},rutinas:{}}; ejercicios.forEach(e=>users[user].reps[e.id]=0); rutinas.forEach(r=>users[user].rutinas[r.nombre]=0); saveUsers(users); localStorage.setItem('anomaly_user', user); localStorage.setItem('anomaly_pass', pass); notify('Cuenta demo creada: '+user); }
if(!localStorage.getItem('anomaly_user')) demoRegister('demo','demo');

/* ------------------ event delegation for rutina chips ------------------ */
document.addEventListener('click', (e)=>{ const chip = e.target.closest('[data-rutina]'); if(chip){ const name = decodeURIComponent(chip.dataset.rutina); viewRoutine(encodeURIComponent(name)); } });

/* ------------------ Init ------------------ */
showInicio();
</script>
</body>
</html>

Login Registrarse Login Admin


Crear cuenta
Cancelar Continuar
Las credenciales se guardan localmente (demo). Admi
