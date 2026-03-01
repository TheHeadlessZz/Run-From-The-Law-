<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <title>Parkour Runner</title>
    <style>
        *{margin:0;padding:0;box-sizing:border-box}
        body{display:flex;justify-content:center;align-items:center;min-height:100vh;background:linear-gradient(135deg,#0a0a1a,#1a1a3a);font-family:Arial,sans-serif;overflow:hidden}
        .container{position:relative}
        canvas{border:4px solid #e94560;border-radius:15px;box-shadow:0 0 50px rgba(233,69,96,0.4)}
        .hud{position:absolute;top:10px;left:10px;right:10px;display:none;justify-content:space-between;padding:10px 15px;background:rgba(0,0,0,0.85);border-radius:12px;color:#fff;font-weight:bold;font-size:14px}
        .hud.show{display:flex}
        .coins{color:#ffd700}.score{color:#e94560}.dist{color:#00ff88}
        .badge{padding:4px 12px;border-radius:15px;font-size:11px}
        .easy{background:#00ff88;color:#000}.medium{background:#ffcc00;color:#000}.hard{background:#ff4444;color:#fff}
        .health-bar{position:absolute;top:65px;left:10px;right:10px;height:8px;background:rgba(0,0,0,0.8);border-radius:10px;display:none}
        .health-bar.show{display:block}
        .health-fill{height:100%;border-radius:10px;transition:width 0.3s;background:linear-gradient(90deg,#ff4444,#ffcc00,#00ff88)}
        .copbar{position:absolute;bottom:10px;left:10px;right:10px;height:25px;background:rgba(0,0,0,0.8);border-radius:12px;display:none;overflow:hidden}
        .copbar.show{display:block}
        .copfill{height:100%;background:linear-gradient(90deg,#00ff88,#ffcc00,#ff4444);transition:width 0.2s}
        .coptxt{position:absolute;width:100%;text-align:center;line-height:25px;color:#fff;font-size:11px;font-weight:bold}
        .screen{position:absolute;top:0;left:0;width:100%;height:100%;display:none;flex-direction:column;justify-content:center;align-items:center;background:rgba(10,10,30,0.97);border-radius:15px;color:#fff}
        .screen.show{display:flex}
        .title{font-size:38px;color:#e94560;margin-bottom:10px}
        .sub{color:#888;margin-bottom:20px;text-align:center;font-size:14px}
        .logo{font-size:60px;margin-bottom:15px}
        .wallet{display:flex;gap:8px;padding:12px 30px;background:rgba(255,215,0,0.15);border:2px solid #ffd700;border-radius:25px;color:#ffd700;font-size:20px;margin-bottom:20px}
        .keys{display:flex;gap:10px;margin-bottom:20px;flex-wrap:wrap;justify-content:center}
        .key{padding:8px 15px;background:#2a2a4a;border:2px solid #4a4a6a;border-radius:8px;font-size:12px}
        .btns{display:flex;flex-direction:column;gap:10px}
        .btn{padding:14px 45px;font-size:16px;font-weight:bold;border:none;border-radius:25px;cursor:pointer;transition:transform 0.2s}
        .btn:hover{transform:scale(1.05)}
        .btn-play{background:linear-gradient(135deg,#e94560,#ff6b8a);color:#fff}
        .btn-shop{background:linear-gradient(135deg,#ffd700,#ff9900);color:#000}
        .btn-back{background:#3a3a5a;color:#fff}
        .skins{display:grid;grid-template-columns:repeat(4,1fr);gap:10px;max-height:200px;overflow-y:auto;padding:10px;margin-bottom:15px}
        .skin{background:rgba(255,255,255,0.05);border:2px solid rgba(255,255,255,0.1);border-radius:10px;padding:12px;text-align:center;cursor:pointer}
        .skin:hover{border-color:#e94560}
        .skin.sel{border-color:#00ff88;box-shadow:0 0 15px rgba(0,255,136,0.4)}
        .skinprev{width:35px;height:50px;margin:0 auto 8px;position:relative}
        .skinhead{width:18px;height:18px;border-radius:50%;position:absolute;top:0;left:50%;transform:translateX(-50%)}
        .skinbody{width:20px;height:28px;border-radius:5px;position:absolute;bottom:0;left:50%;transform:translateX(-50%)}
        .skinname{font-size:10px}.skinprice{font-size:9px;color:#ffd700;margin:3px 0}
        .skinbtn{padding:5px 12px;font-size:9px;border:none;border-radius:10px;cursor:pointer}
        .skinbtn.buy{background:#ffd700;color:#000}.skinbtn.equip{background:#3a3a5a;color:#fff}.skinbtn.eqd{background:#00ff88;color:#000}
        .skinbtn:disabled{background:#333;color:#666}
        .stats{display:grid;grid-template-columns:1fr 1fr;gap:12px;margin-bottom:20px}
        .stat{background:rgba(255,255,255,0.05);padding:15px;border-radius:10px;text-align:center}
        .stv{font-size:24px;font-weight:bold}.stl{font-size:9px;color:#888}
        .msg,.story{position:absolute;left:50%;transform:translateX(-50%);padding:15px 30px;background:rgba(0,0,0,0.9);border:2px solid #e94560;border-radius:15px;font-size:18px;text-align:center;opacity:0;transition:opacity 0.3s;pointer-events:none}
        .msg{top:35%}.story{bottom:55px}
        .msg.show,.story.show{opacity:1}
        .flash{position:absolute;top:0;left:0;width:100%;height:100%;border-radius:15px;opacity:0;pointer-events:none;transition:opacity 0.1s}
        .flash.show{opacity:1;background:rgba(255,0,0,0.3)}
        .heal.show{opacity:1;background:rgba(0,255,100,0.2)}
        .regen{position:absolute;top:75px;right:15px;color:#00ff88;font-size:12px;opacity:0}
        .regen.show{opacity:1}
        .record{margin-top:15px;color:#ffd700;font-size:12px}
    </style>
</head>
<body>
<div class="container">
    <canvas id="c" width="800" height="500"></canvas>
    <div class="flash" id="flash"></div>
    <div class="flash heal" id="healFlash"></div>
    <div class="regen" id="regen">💚 Regenerando...</div>
    
    <div class="hud" id="hud">
        <span class="coins">💰<span id="hc">0</span></span>
        <span class="score">⭐<span id="hs">0</span></span>
        <span class="dist">📏<span id="hd">0</span>m</span>
        <span id="hh">❤️❤️❤️</span>
        <span class="badge easy" id="hdif">Fácil</span>
        <span id="henv">🌆 Cidade</span>
    </div>
    
    <div class="health-bar" id="healthBar"><div class="health-fill" id="healthFill"></div></div>
    <div class="copbar" id="copbar"><div class="copfill" id="copfill"></div><div class="coptxt" id="coptxt">👮 Distante</div></div>
    <div class="msg" id="msg"></div>
    <div class="story" id="story"></div>
    
    <div class="screen show" id="menu">
        <div class="logo">🏃‍♂️💨👮</div>
        <h1 class="title">PARKOUR RUNNER</h1>
        <p class="sub">Fuja do policial! Sua vida regenera com o tempo!</p>
        <div class="wallet">💰 <span id="mc">0</span></div>
        <div class="keys">
            <span class="key">← → Mover</span>
            <span class="key">ESPAÇO Pular</span>
            <span class="key">↓ Deslizar</span>
        </div>
        <div class="btns">
            <button class="btn btn-play" onclick="startGame()">▶ JOGAR</button>
            <button class="btn btn-shop" onclick="showShop()">🛒 LOJA</button>
        </div>
        <div class="record">🏆 Recorde: <span id="record">0</span>m</div>
    </div>
    
    <div class="screen" id="shop">
        <h2 style="color:#ffd700;margin-bottom:15px">🛒 LOJA</h2>
        <div class="wallet">💰 <span id="sc">0</span></div>
        <div class="skins" id="skins"></div>
        <button class="btn btn-back" onclick="showMenu()">⬅ VOLTAR</button>
    </div>
    
    <div class="screen" id="over">
        <h1 class="title" id="overt">💀 GAME OVER</h1>
        <p class="sub" id="overs"></p>
        <div class="stats">
            <div class="stat"><div class="stv" id="fc" style="color:#ffd700">0</div><div class="stl">MOEDAS</div></div>
            <div class="stat"><div class="stv" id="fs" style="color:#e94560">0</div><div class="stl">PONTOS</div></div>
            <div class="stat"><div class="stv" id="fd" style="color:#00ff88">0</div><div class="stl">METROS</div></div>
            <div class="stat"><div class="stv" id="ft" style="color:#ff9900">0</div><div class="stl">TOTAL💰</div></div>
        </div>
        <div class="btns">
            <button class="btn btn-play" onclick="startGame()">🔄 JOGAR</button>
            <button class="btn btn-back" onclick="showMenu()">🏠 MENU</button>
        </div>
    </div>
    
    <div class="screen" id="pause">
        <h1 class="title">⏸ PAUSADO</h1>
        <div class="btns" style="margin-top:20px">
            <button class="btn btn-play" onclick="resume()">▶ CONTINUAR</button>
            <button class="btn btn-back" onclick="showMenu()">🏠 SAIR</button>
        </div>
    </div>
</div>

<script>
const cv=document.getElementById('c'),cx=cv.getContext('2d'),W=800,H=500;

const SKINS=[
    {id:'default',name:'Clássico',price:0,body:'#e94560',head:'#ffddcc'},
    {id:'ninja',name:'Ninja',price:100,body:'#222',head:'#333'},
    {id:'robot',name:'Robô',price:150,body:'#4a90d9',head:'#6ab0f3'},
    {id:'fire',name:'Fogo',price:200,body:'#ff4400',head:'#ffaa00'},
    {id:'ice',name:'Gelo',price:200,body:'#00ccff',head:'#aaeeff'},
    {id:'gold',name:'Dourado',price:500,body:'#ffd700',head:'#ffee88'},
    {id:'rainbow',name:'Arco-Íris',price:800,body:'rainbow',head:'rainbow'}
];

const ENVS=[
    {id:0,name:'Cidade',icon:'🌆',sky1:'#1a1a2e',sky2:'#16213e',ground:'#4a4a6a',plat:'#6a6a8a'},
    {id:1,name:'Floresta',icon:'🌲',sky1:'#0d3320',sky2:'#1a5c3a',ground:'#3d2817',plat:'#5a4020'},
    {id:2,name:'Praia',icon:'🏖️',sky1:'#87CEEB',sky2:'#e0f4ff',ground:'#f4d03f',plat:'#deb887'},
    {id:3,name:'Neve',icon:'❄️',sky1:'#b8d4e8',sky2:'#e8f4fc',ground:'#ffffff',plat:'#c8dce8'},
    {id:4,name:'Vulcão',icon:'🌋',sky1:'#1a0a0a',sky2:'#3d1515',ground:'#2d1a1a',plat:'#5a3030'},
    {id:5,name:'Espaço',icon:'🚀',sky1:'#000005',sky2:'#0a0a1a',ground:'#1a1a2a',plat:'#3a3a5a'}
];

const DIFFS={easy:{name:'Fácil',cls:'easy',spd:4.5,cop:0.02,obs:0.12,heal:0.12},medium:{name:'Médio',cls:'medium',spd:6,cop:0.04,obs:0.2,heal:0.08},hard:{name:'Difícil',cls:'hard',spd:7.5,cop:0.06,obs:0.28,heal:0.04}};

const MAX_HP=100;

let G={run:false,pause:false,cut:false,cutP:0,cutT:0,score:0,dist:0,coins:0,hp:MAX_HP,inv:0,regenT:0,
    total:+localStorage.getItem('coins')||0,record:+localStorage.getItem('record')||0,
    skin:localStorage.getItem('skin')||'default',owned:JSON.parse(localStorage.getItem('owned')||'["default"]'),
    env:0,diff:'easy',spd:4.5,envC:0,difC:0,t:0};

let P={x:350,y:370,w:30,h:50,vx:0,vy:0,gr:true,sl:false,dbl:true,hue:0,fr:0};
let C={x:-100,y:375,w:34,h:54,tired:false,tiredT:0,fr:0};

let plats=[],obs=[],coins=[],parts=[],stars=[],envObjs=[];
let keys={};

function init(){
    plats=[{x:0,y:430,w:W+200,h:70,type:'ground'}];
    obs=[];coins=[];parts=[];stars=[];envObjs=[];
    P={x:350,y:370,w:30,h:50,vx:0,vy:0,gr:true,sl:false,dbl:true,hue:0,fr:0};
    C={x:-100,y:375,w:34,h:54,tired:false,tiredT:0,fr:0};
    G.score=0;G.dist=0;G.coins=0;G.hp=MAX_HP;G.inv=0;G.regenT=0;
    G.spd=4.5;G.env=0;G.diff='easy';G.envC=0;G.difC=0;G.t=0;
    G.cut=true;G.cutP=0;G.cutT=0;
    
    for(let i=0;i<80;i++)stars.push({x:Math.random()*W,y:Math.random()*350,s:Math.random()*2+0.5,t:Math.random()*6.28});
    genEnvObjs();
    updHP();
}

function genEnvObjs(){
    envObjs=[];
    for(let i=0;i<8;i++){
        envObjs.push({x:i*120,v:Math.floor(Math.random()*3),h:80+Math.random()*100,sp:0.25+Math.random()*0.15});
    }
}

function genPlat(x){
    const d=DIFFS[G.diff],w=90+Math.random()*100,y=200+Math.random()*140;
    plats.push({x,y,w,h:18,type:Math.random()>0.8?'mov':'norm',oy:y,dir:1});
    
    if(Math.random()<d.obs){
        const t=['spike','barrel','bee'][Math.floor(Math.random()*3)];
        let o={x:x+w/2,type:t,tim:0};
        if(t==='spike'){o.y=405;o.w=26;o.h=26;o.dmg=18;}
        else if(t==='barrel'){o.y=390;o.w=30;o.h=40;o.dmg=15;}
        else{o.y=260+Math.random()*80;o.w=26;o.h=26;o.dmg=20;o.fly=true;}
        obs.push(o);
    }
    
    if(Math.random()>0.25){
        const cy=150+Math.random()*150;
        for(let i=0;i<5;i++)coins.push({x:x+i*30,y:cy+Math.sin(i)*20,r:11,v:5,bob:Math.random()*6.28});
    }
}

document.addEventListener('keydown',e=>{
    keys[e.code]=true;
    if(G.cut)return;
    if(e.code==='Escape'){if(G.run&&!G.pause)pause();else if(G.pause)resume();}
    if((e.code==='Space'||e.code==='ArrowUp')&&G.run&&!G.pause){jump();e.preventDefault();}
    if(e.code==='ArrowDown'&&G.run&&!G.pause&&P.gr&&!P.sl){P.sl=true;P.h=26;P.y+=24;}
});

document.addEventListener('keyup',e=>{
    keys[e.code]=false;
    if(e.code==='ArrowDown'&&P.sl){P.sl=false;P.y-=24;P.h=50;}
});

function jump(){
    if(P.gr){P.vy=-14;P.gr=false;P.dbl=true;spawn(P.x+P.w/2,P.y+P.h,'#fff',8);}
    else if(P.dbl){P.vy=-12;P.dbl=false;spawn(P.x+P.w/2,P.y+P.h,'#ffd700',10);}
}

function spawn(x,y,col,n){for(let i=0;i<n;i++)parts.push({x,y,vx:(Math.random()-0.5)*8,vy:(Math.random()-0.5)*8,life:1,col,s:3+Math.random()*3});}

function damage(d){
    if(G.inv>0)return;
    G.hp-=d;G.inv=50;G.regenT=0;
    document.getElementById('flash').classList.add('show');
    setTimeout(()=>document.getElementById('flash').classList.remove('show'),120);
    spawn(P.x+P.w/2,P.y+P.h/2,'#ff0000',15);
    updHP();
    if(G.hp<=0){G.hp=0;gameOver('morreu');}
}

function heal(a){
    if(G.hp>=MAX_HP)return;
    G.hp=Math.min(MAX_HP,G.hp+a);
    updHP();
}

function updHP(){
    document.getElementById('healthFill').style.width=(G.hp/MAX_HP*100)+'%';
    let h=G.hp>66?'❤️❤️❤️':G.hp>33?'❤️❤️🖤':G.hp>0?'❤️🖤🖤':'🖤🖤🖤';
    document.getElementById('hh').innerHTML=h;
}

function updCut(){
    G.cutT++;
    if(G.cutP===0){P.fr+=0.12;if(G.cutT===1)showStory("🎵 Dançando na rua... 🎵");if(G.cutT>80){G.cutP=1;G.cutT=0;}}
    else if(G.cutP===1){C.x+=3;C.fr+=0.2;if(G.cutT===1)showStory("👮 PARADO AÍ!");if(C.x>130){G.cutP=2;G.cutT=0;}}
    else if(G.cutP===2){
        if(G.cutT===1)showStory("😱 CORRE!!!");
        if(P.x>160)P.x-=4;
        if(G.cutT>60){
            G.cut=false;G.run=true;P.x=160;C.x=-70;hideStory();
            for(let i=1;i<8;i++)genPlat(400+i*170);
            document.getElementById('hud').classList.add('show');
            document.getElementById('healthBar').classList.add('show');
            document.getElementById('copbar').classList.add('show');
        }
    }
}

function showStory(t){document.getElementById('story').textContent=t;document.getElementById('story').classList.add('show');}
function hideStory(){document.getElementById('story').classList.remove('show');}
function showMsg(t){const e=document.getElementById('msg');e.innerHTML=t;e.classList.add('show');setTimeout(()=>e.classList.remove('show'),2000);}

function update(){
    if(G.cut){updCut();return;}
    if(!G.run||G.pause)return;
    
    G.t++;
    const d=DIFFS[G.diff];
    if(G.inv>0)G.inv--;
    
    // Regeneração de vida
    G.regenT++;
    if(G.regenT>180&&G.hp<MAX_HP){
        heal(d.heal);
        document.getElementById('regen').classList.add('show');
        if(G.t%30===0){spawn(P.x+P.w/2,P.y,'#00ff88',2);}
    }else{
        document.getElementById('regen').classList.remove('show');
    }
    
    // Movimento
    if(keys['ArrowLeft']||keys['KeyA'])P.vx=-7;
    else if(keys['ArrowRight']||keys['KeyD'])P.vx=7;
    else P.vx*=0.85;
    
    P.vy+=0.55;P.x+=P.vx;P.y+=P.vy;
    P.x=Math.max(60,Math.min(W-70,P.x));
    P.fr+=0.2;P.hue=(P.hue+3)%360;
    
    // Colisão plataformas
    P.gr=false;
    for(let p of plats){
        if(p.type==='mov'){p.y+=p.dir*1.2;if(Math.abs(p.y-p.oy)>30)p.dir*=-1;}
        if(P.x+P.w>p.x+5&&P.x<p.x+p.w-5&&P.y+P.h>p.y&&P.y+P.h<p.y+20&&P.vy>=0){
            P.y=p.y-P.h;P.vy=0;P.gr=true;P.dbl=true;
        }
    }
    
    // Policial
    C.fr+=0.15;C.tiredT++;
    const dist=P.x-C.x;
    if(C.tiredT>500)C.tired=true;
    if(C.tired){C.x-=1.5;if(C.tiredT>650){C.tired=false;C.tiredT=0;}}
    else{let sp=d.cop;if(dist>400)sp*=1.3;else if(dist<150)sp*=0.25;C.x+=sp;}
    
    if(C.x+C.w>=P.x-5){gameOver('pego');return;}
    
    const bar=Math.max(0,Math.min(100,(1-dist/450)*100));
    document.getElementById('copfill').style.width=bar+'%';
    let txt=C.tired?'👮😮‍💨 Cansou!':dist<100?'👮😡 QUASE!':dist<200?'👮🏃 Perto!':'👮 Distante';
    document.getElementById('coptxt').textContent=txt;
    
    // Plataformas
    for(let i=plats.length-1;i>=0;i--){
        let p=plats[i];
        if(p.type!=='ground'){p.x-=G.spd;if(p.x+p.w<-50)plats.splice(i,1);}
    }
    
    // Obstáculos
    for(let i=obs.length-1;i>=0;i--){
        let o=obs[i];o.x-=G.spd;o.tim++;
        if(o.fly)o.y+=Math.sin(o.tim*0.1)*2;
        if(P.x+5<o.x+o.w&&P.x+P.w-5>o.x&&P.y+5<o.y+o.h&&P.y+P.h>o.y){
            if(P.sl&&o.type==='barrel'){}
            else{damage(o.dmg);obs.splice(i,1);C.x+=35;continue;}
        }
        if(o.x<-50){obs.splice(i,1);G.score+=25;}
    }
    
    // Moedas
    for(let i=coins.length-1;i>=0;i--){
        let c=coins[i];c.x-=G.spd;c.bob+=0.1;
        const by=Math.sin(c.bob)*5;
        if(P.x<c.x+15&&P.x+P.w>c.x-15&&P.y<c.y+by+15&&P.y+P.h>c.y+by-15){
            G.coins+=c.v;G.score+=c.v*2;spawn(c.x,c.y+by,'#ffd700',10);coins.splice(i,1);continue;
        }
        if(c.x<-20)coins.splice(i,1);
    }
    
    // Partículas
    for(let i=parts.length-1;i>=0;i--){
        let p=parts[i];p.x+=p.vx;p.y+=p.vy;p.vy+=0.2;p.life-=0.025;
        if(p.life<=0)parts.splice(i,1);
    }
    
    // Objetos do ambiente
    for(let o of envObjs){o.x-=G.spd*o.sp;if(o.x<-120){o.x=W+50;o.v=Math.floor(Math.random()*3);}}
    
    // Gerar plataformas
    const last=plats.filter(p=>p.type!=='ground').pop();
    if(!last||last.x<W)genPlat(W+100+Math.random()*80);
    
    // Queda
    if(P.y>H+50){gameOver('caiu');return;}
    
    // Score e distância
    G.dist+=G.spd/10;G.score++;
    
    // Dificuldade
    const dl=Math.floor(G.dist/500);
    if(dl>G.difC){
        G.difC=dl;
        if(G.diff==='easy'&&dl>=1){G.diff='medium';showMsg("⚠️ MÉDIO!");}
        else if(G.diff==='medium'&&dl>=2){G.diff='hard';showMsg("🔥 DIFÍCIL!");}
    }
    
    // Ambiente
    const newEnv=Math.floor(G.dist/600)%ENVS.length;
    if(newEnv!==G.env){G.env=newEnv;genEnvObjs();showMsg(ENVS[G.env].icon+" "+ENVS[G.env].name);}
    
    // Velocidade
    G.spd=DIFFS[G.diff].spd+G.dist/1500;
    G.spd=Math.min(G.spd,9);
    
    // Estrelas
    for(let s of stars)s.t+=0.03;
    
    updHUD();
}

function updHUD(){
    document.getElementById('hc').textContent=G.coins;
    document.getElementById('hs').textContent=Math.floor(G.score);
    document.getElementById('hd').textContent=Math.floor(G.dist);
    const d=DIFFS[G.diff],e=document.getElementById('hdif');
    e.textContent=d.name;e.className='badge '+d.cls;
    document.getElementById('henv').textContent=ENVS[G.env].icon+' '+ENVS[G.env].name;
}

function render(){
    const env=ENVS[G.env];
    
    // Céu
    const sky=cx.createLinearGradient(0,0,0,H);
    sky.addColorStop(0,env.sky1);sky.addColorStop(1,env.sky2);
    cx.fillStyle=sky;cx.fillRect(0,0,W,H);
    
    // Renderizar ambiente específico
    renderEnv();
    
    // Plataformas
    for(let p of plats){
        if(p.type==='ground'){
            cx.fillStyle=env.ground;cx.fillRect(0,p.y,W,p.h);
            cx.strokeStyle='#e94560';cx.lineWidth=3;cx.beginPath();cx.moveTo(0,p.y);cx.lineTo(W,p.y);cx.stroke();
        }else{
            cx.fillStyle=env.plat;cx.fillRect(p.x,p.y,p.w,p.h);
            cx.fillStyle='rgba(255,255,255,0.15)';cx.fillRect(p.x,p.y,p.w,4);
        }
    }
    
    // Obstáculos
    for(let o of obs){
        if(o.type==='spike'){
            cx.fillStyle='#ff4444';cx.beginPath();cx.moveTo(o.x,o.y+o.h);cx.lineTo(o.x+o.w/2,o.y);cx.lineTo(o.x+o.w,o.y+o.h);cx.fill();
        }else if(o.type==='barrel'){
            cx.fillStyle='#8B4513';cx.fillRect(o.x,o.y,o.w,o.h);
            cx.strokeStyle='#5a2a00';cx.lineWidth=2;
            cx.beginPath();cx.moveTo(o.x,o.y+o.h*0.33);cx.lineTo(o.x+o.w,o.y+o.h*0.33);cx.stroke();
            cx.beginPath();cx.moveTo(o.x,o.y+o.h*0.66);cx.lineTo(o.x+o.w,o.y+o.h*0.66);cx.stroke();
        }else{
            cx.fillStyle='#ffcc00';cx.beginPath();cx.arc(o.x+o.w/2,o.y+o.h/2,o.w/2,0,6.28);cx.fill();
            cx.fillStyle='#222';cx.fillRect(o.x+4,o.y+o.h/2-2,o.w-8,4);
        }
    }
    
    // Moedas
    for(let c of coins){
        const by=Math.sin(c.bob)*5;
        cx.fillStyle='rgba(255,215,0,0.3)';cx.beginPath();cx.arc(c.x,c.y+by,c.r+4,0,6.28);cx.fill();
        cx.fillStyle='#ffd700';cx.beginPath();cx.arc(c.x,c.y+by,c.r,0,6.28);cx.fill();
        cx.fillStyle='#b8860b';cx.font='bold 10px Arial';cx.textAlign='center';cx.fillText('$',c.x,c.y+by+4);
    }
    
    // Partículas
    for(let p of parts){cx.globalAlpha=p.life;cx.fillStyle=p.col;cx.beginPath();cx.arc(p.x,p.y,p.s*p.life,0,6.28);cx.fill();}
    cx.globalAlpha=1;
    
    drawCop();
    drawPlayer();
}

function renderEnv(){
    const e=G.env;
    
    // === CIDADE ===
    if(e===0){
        // Estrelas
        for(let s of stars){
            if(s.y<250){
                cx.fillStyle=`rgba(255,255,255,${0.3+Math.sin(s.t)*0.3})`;
                cx.beginPath();cx.arc(s.x,s.y,s.s,0,6.28);cx.fill();
            }
        }
        // Lua
        cx.fillStyle='#ffffcc';cx.shadowColor='#ffffcc';cx.shadowBlur=20;
        cx.beginPath();cx.arc(680,70,30,0,6.28);cx.fill();cx.shadowBlur=0;
        // Prédios
        for(let o of envObjs){
            const bh=o.h+60,bw=45+o.v*12;
            cx.fillStyle='#2a2a4a';cx.fillRect(o.x,430-bh,bw,bh);
            // Janelas
            for(let r=0;r<Math.floor(bh/22);r++){
                for(let c=0;c<3;c++){
                    cx.fillStyle=Math.sin(G.t*0.02+r+c+o.x)>0?'rgba(255,220,100,0.8)':'rgba(50,50,80,0.5)';
                    cx.fillRect(o.x+5+c*12,430-bh+8+r*22,8,14);
                }
            }
        }
    }
    
    // === FLORESTA ===
    else if(e===1){
        // Sol através das árvores
        cx.fillStyle='#ffdd44';cx.shadowColor='#ffdd44';cx.shadowBlur=50;
        cx.beginPath();cx.arc(650,80,35,0,6.28);cx.fill();cx.shadowBlur=0;
        // Névoa
        cx.fillStyle='rgba(200,255,200,0.15)';cx.fillRect(0,300,W,130);
        // Árvores
        for(let o of envObjs){
            // Tronco
            cx.fillStyle='#4a3020';cx.fillRect(o.x+18,350,18,80);
            // Copa
            for(let l=0;l<3;l++){
                cx.fillStyle=l===0?'#1a5a1a':'#228B22';
                cx.beginPath();cx.moveTo(o.x+27,300-l*35);cx.lineTo(o.x-5+l*5,350-l*30);cx.lineTo(o.x+59-l*5,350-l*30);cx.fill();
            }
        }
        // Borboletas
        for(let i=0;i<3;i++){
            const bx=(80+i*220+G.t*0.4)%W,by=280+Math.sin(G.t*0.04+i)*25;
            cx.fillStyle=['#ff69b4','#87ceeb','#ffd700'][i];
            cx.beginPath();cx.ellipse(bx-4,by,4,3,Math.sin(G.t*0.15)*0.5,0,6.28);cx.ellipse(bx+4,by,4,3,-Math.sin(G.t*0.15)*0.5,0,6.28);cx.fill();
        }
    }
    
    // === PRAIA ===
    else if(e===2){
        // Mar
        cx.fillStyle='#0088cc';cx.fillRect(0,0,W,180);
        // Ondas
        cx.fillStyle='rgba(255,255,255,0.25)';
        for(let w=0;w<3;w++){
            cx.beginPath();cx.moveTo(0,160+w*10);
            for(let x=0;x<=W;x+=15)cx.lineTo(x,160+w*10+Math.sin((x+G.t*2+w*40)*0.02)*6);
            cx.lineTo(W,200);cx.lineTo(0,200);cx.fill();
        }
        // Sol
        cx.fillStyle='#FFD700';cx.shadowColor='#FFD700';cx.shadowBlur=40;
        cx.beginPath();cx.arc(650,70,40,0,6.28);cx.fill();cx.shadowBlur=0;
        // Nuvens
        for(let i=0;i<3;i++){
            const nx=(i*280+G.t*0.25)%(W+150)-50;
            cx.fillStyle='rgba(255,255,255,0.85)';
            cx.beginPath();cx.arc(nx,50+i*20,20,0,6.28);cx.arc(nx+20,45+i*20,25,0,6.28);cx.arc(nx+45,50+i*20,20,0,6.28);cx.fill();
        }
        // Palmeiras e guarda-sóis
        for(let o of envObjs){
            if(o.v===0){
                // Palmeira
                cx.strokeStyle='#8B5A2B';cx.lineWidth=10;cx.beginPath();cx.moveTo(o.x+25,430);cx.quadraticCurveTo(o.x+35,350,o.x+30,280);cx.stroke();
                for(let f=0;f<6;f++){
                    cx.save();cx.translate(o.x+30,280);cx.rotate((f-2.5)*0.4+Math.sin(G.t*0.02)*0.05);
                    cx.fillStyle='#228B22';cx.beginPath();cx.ellipse(0,-35,6,40,0,0,6.28);cx.fill();cx.restore();
                }
            }else if(o.v===1){
                // Guarda-sol
                cx.fillStyle='#f4d03f';cx.fillRect(o.x+23,385,4,45);
                cx.fillStyle='#e74c3c';cx.beginPath();cx.arc(o.x+25,385,30,Math.PI,0);cx.fill();
                cx.fillStyle='#fff';
                for(let s=0;s<4;s++){cx.beginPath();cx.moveTo(o.x+25,385);cx.arc(o.x+25,385,30,Math.PI+s*0.35,Math.PI+s*0.35+0.15);cx.fill();}
            }else{
                // Castelo de areia
                cx.fillStyle='#daa520';cx.fillRect(o.x+5,405,45,25);
                cx.beginPath();cx.arc(o.x+15,405,8,Math.PI,0);cx.arc(o.x+28,405,10,Math.PI,0);cx.arc(o.x+42,405,8,Math.PI,0);cx.fill();
            }
        }
        // Gaivotas
        cx.strokeStyle='#333';cx.lineWidth=2;
        for(let i=0;i<3;i++){
            const gx=(i*200+G.t*0.6)%W,gy=90+Math.sin(G.t*0.04+i)*15;
            cx.beginPath();cx.moveTo(gx-8,gy);cx.quadraticCurveTo(gx,gy-4,gx+8,gy);cx.stroke();
        }
    }
    
    // === NEVE ===
    else if(e===3){
        // Aurora boreal
        const au=cx.createLinearGradient(0,0,W,100);
        au.addColorStop(0,'rgba(0,255,100,0.1)');au.addColorStop(0.5,'rgba(100,200,255,0.12)');au.addColorStop(1,'rgba(200,100,255,0.1)');
        cx.fillStyle=au;cx.fillRect(0,0,W,150);
        // Montanhas
        cx.fillStyle='#d8e8f0';
        cx.beginPath();cx.moveTo(0,300);cx.lineTo(120,180);cx.lineTo(250,270);cx.lineTo(400,140);cx.lineTo(550,250);cx.lineTo(700,120);cx.lineTo(W,220);cx.lineTo(W,430);cx.lineTo(0,430);cx.fill();
        // Picos de neve
        cx.fillStyle='#fff';
        cx.beginPath();cx.moveTo(115,185);cx.lineTo(120,180);cx.lineTo(125,185);cx.fill();
        cx.beginPath();cx.moveTo(395,145);cx.lineTo(400,140);cx.lineTo(405,145);cx.fill();
        // Pinheiros com neve
        for(let o of envObjs){
            cx.fillStyle='#3a2010';cx.fillRect(o.x+18,400,12,30);
            for(let l=0;l<4;l++){
                const lw=50-l*10,ly=400-l*30;
                cx.fillStyle='#0d5c0d';cx.beginPath();cx.moveTo(o.x+24,ly-35);cx.lineTo(o.x+24-lw/2,ly);cx.lineTo(o.x+24+lw/2,ly);cx.fill();
                cx.fillStyle='#fff';cx.beginPath();cx.moveTo(o.x+24,ly-35);cx.lineTo(o.x+24-lw/4,ly-22);cx.lineTo(o.x+24+lw/4,ly-22);cx.fill();
            }
            // Boneco de neve
            if(o.v===1){
                cx.fillStyle='#fff';cx.beginPath();cx.arc(o.x+55,415,10,0,6.28);cx.arc(o.x+55,398,7,0,6.28);cx.arc(o.x+55,385,5,0,6.28);cx.fill();
                cx.fillStyle='#000';cx.beginPath();cx.arc(o.x+53,383,1.5,0,6.28);cx.arc(o.x+57,383,1.5,0,6.28);cx.fill();
                cx.fillStyle='#ff6600';cx.beginPath();cx.moveTo(o.x+55,385);cx.lineTo(o.x+62,387);cx.lineTo(o.x+55,388);cx.fill();
            }
        }
        // Flocos de neve
        cx.fillStyle='#fff';
        for(let i=0;i<35;i++){
            const fx=(i*25+G.t*0.4)%W,fy=(i*35+G.t)%380;
            cx.beginPath();cx.arc(fx,fy,2+Math.sin(i)*0.8,0,6.28);cx.fill();
        }
    }
    
    // === VULCÃO ===
    else if(e===4){
        // Brilho vermelho
        const glow=cx.createRadialGradient(400,100,0,400,100,250);
        glow.addColorStop(0,'rgba(255,80,0,0.25)');glow.addColorStop(1,'rgba(255,40,0,0)');
        cx.fillStyle=glow;cx.fillRect(0,0,W,H);
        // Vulcão
        cx.fillStyle='#2d1a1a';cx.beginPath();cx.moveTo(180,430);cx.lineTo(330,100);cx.lineTo(470,100);cx.lineTo(620,430);cx.fill();
        // Lava na cratera
        cx.fillStyle='#ff4400';cx.shadowColor='#ff4400';cx.shadowBlur=25;
        cx.beginPath();cx.ellipse(400,100,35+Math.sin(G.t*0.08)*5,12,0,0,6.28);cx.fill();cx.shadowBlur=0;
        // Lava escorrendo
        cx.fillStyle='#ff6600';
        cx.beginPath();cx.moveTo(385,100);cx.quadraticCurveTo(375,200,365,300);cx.quadraticCurveTo(360,350,355,430);cx.lineTo(375,430);cx.quadraticCurveTo(380,350,390,300);cx.quadraticCurveTo(398,200,400,100);cx.fill();
        // Rochas
        for(let o of envObjs){
            cx.fillStyle='#1a0808';
            cx.beginPath();cx.moveTo(o.x,430);cx.lineTo(o.x+15,430-o.h*0.25);cx.lineTo(o.x+35,430-o.h*0.35);cx.lineTo(o.x+55,430-o.h*0.2);cx.lineTo(o.x+65,430);cx.fill();
            if(o.v===0){
                cx.fillStyle='#ff4400';cx.shadowColor='#ff4400';cx.shadowBlur=8;
                cx.beginPath();cx.arc(o.x+28,418,3+Math.sin(G.t*0.1+o.x)*1.5,0,6.28);cx.fill();cx.shadowBlur=0;
            }
        }
        // Cinzas subindo
        cx.fillStyle='#ff6600';
        for(let i=0;i<12;i++){
            const px=340+Math.sin(i*2+G.t*0.04)*70,py=100-(G.t+i*25)%130;
            cx.globalAlpha=Math.max(0,1-py/100);cx.beginPath();cx.arc(px,py+40,2+Math.random()*2,0,6.28);cx.fill();
        }
        cx.globalAlpha=1;
        // Fumaça
        cx.fillStyle='rgba(50,50,50,0.35)';
        for(let i=0;i<4;i++){
            const sx=400+Math.sin(G.t*0.015+i)*25,sy=40-i*18-(G.t*0.4%80);
            cx.beginPath();cx.arc(sx,sy,15+i*8,0,6.28);cx.fill();
        }
    }
    
    // === ESPAÇO ===
    else if(e===5){
        // Estrelas brilhantes
        for(let s of stars){
            const a=0.4+Math.sin(s.t)*0.4,sz=s.s*(1+Math.sin(s.t*2)*0.25);
            cx.fillStyle=`rgba(255,255,255,${a})`;cx.beginPath();cx.arc(s.x,s.y,sz,0,6.28);cx.fill();
            if(s.s>1.5){
                cx.strokeStyle=`rgba(255,255,255,${a*0.3})`;cx.lineWidth=1;
                cx.beginPath();cx.moveTo(s.x-4,s.y);cx.lineTo(s.x+4,s.y);cx.moveTo(s.x,s.y-4);cx.lineTo(s.x,s.y+4);cx.stroke();
            }
        }
        // Nebulosa
        const neb=cx.createRadialGradient(180,130,0,180,130,180);
        neb.addColorStop(0,'rgba(140,40,180,0.18)');neb.addColorStop(0.5,'rgba(40,80,180,0.1)');neb.addColorStop(1,'rgba(0,0,0,0)');
        cx.fillStyle=neb;cx.fillRect(0,0,W,H);
        // Planeta
        const pl=cx.createRadialGradient(620,110,0,650,110,55);
        pl.addColorStop(0,'#6a8caf');pl.addColorStop(0.7,'#3d5a80');pl.addColorStop(1,'#1d2d44');
        cx.fillStyle=pl;cx.beginPath();cx.arc(650,110,50,0,6.28);cx.fill();
        // Anéis
        cx.strokeStyle='rgba(200,200,220,0.45)';cx.lineWidth=4;cx.beginPath();cx.ellipse(650,110,80,18,-0.2,0.1,Math.PI-0.1);cx.stroke();
        // Lua
        cx.fillStyle='#aaa';cx.beginPath();cx.arc(540,75,14,0,6.28);cx.fill();
        cx.fillStyle='#888';cx.beginPath();cx.arc(535,73,3,0,6.28);cx.arc(545,78,2,0,6.28);cx.fill();
        // Objetos espaciais
        for(let o of envObjs){
            if(o.v===0){
                // Asteroide
                cx.fillStyle='#555';cx.beginPath();cx.arc(o.x+25,360,18,0,6.28);cx.fill();
                cx.fillStyle='#444';cx.beginPath();cx.arc(o.x+20,355,4,0,6.28);cx.arc(o.x+32,365,3,0,6.28);cx.fill();
            }else if(o.v===1){
                // Estação espacial
                cx.fillStyle='#4a4a6a';cx.fillRect(o.x+8,365,35,18);
                cx.fillStyle='#3498db';cx.fillRect(o.x-12,370,20,8);cx.fillRect(o.x+43,370,20,8);
                cx.fillStyle=Math.sin(G.t*0.08)>0?'#ff0':'#f00';cx.beginPath();cx.arc(o.x+25,365,3,0,6.28);cx.fill();
            }else{
                // Satélite
                cx.fillStyle='#888';cx.fillRect(o.x+15,375,18,12);
                cx.fillStyle='#3498db';cx.fillRect(o.x-5,378,20,6);cx.fillRect(o.x+33,378,20,6);
            }
        }
        // Estrela cadente
        if(Math.sin(G.t*0.015)>0.92){
            cx.strokeStyle='#fff';cx.lineWidth=2;cx.beginPath();
            const sx=(G.t*2.5)%W;cx.moveTo(sx,40);cx.lineTo(sx+40,85);cx.stroke();
        }
    }
}

function drawCop(){
    cx.save();cx.translate(C.x+C.w/2,C.y+C.h/2);
    const b=Math.sin(C.fr)*3;
    
    // Sombra
    cx.fillStyle='rgba(0,0,0,0.3)';cx.beginPath();cx.ellipse(0,C.h/2-2,C.w/2,5,0,0,6.28);cx.fill();
    
    // Pernas
    cx.fillStyle='#0a1a3e';
    const lL=Math.sin(C.fr)*6,lR=-Math.sin(C.fr)*6;
    cx.fillRect(-8,C.h/2-18+lL,8,16);cx.fillRect(0,C.h/2-18+lR,8,16);
    cx.fillStyle='#111';cx.fillRect(-9,C.h/2-3+lL,10,5);cx.fillRect(-1,C.h/2-3+lR,10,5);
    
    // Corpo
    cx.fillStyle='#1a4a8e';cx.fillRect(-C.w/2+2,-C.h/2+15+b,C.w-4,C.h-32);
    cx.fillStyle='#ffd700';cx.beginPath();cx.arc(7,-C.h/2+28+b,4,0,6.28);cx.fill();
    
    // Cabeça
    cx.fillStyle='#e8c8a8';cx.beginPath();cx.arc(0,-C.h/2+10+b,12,0,6.28);cx.fill();
    cx.fillStyle='#0a2040';cx.fillRect(-13,-C.h/2-2+b,26,9);cx.fillRect(-9,-C.h/2-9+b,18,9);
    cx.fillStyle='#ffd700';cx.beginPath();cx.arc(0,-C.h/2-4+b,3,0,6.28);cx.fill();
    
    // Olhos
    cx.fillStyle='#fff';cx.beginPath();cx.ellipse(-3,-C.h/2+8+b,4,4.5,0,0,6.28);cx.ellipse(3,-C.h/2+8+b,4,4.5,0,0,6.28);cx.fill();
    cx.fillStyle=C.tired?'#666':'#200';cx.beginPath();cx.arc(-2,-C.h/2+8+b,2,0,6.28);cx.arc(4,-C.h/2+8+b,2,0,6.28);cx.fill();
    
    if(C.tired){cx.fillStyle='#66ccff';cx.beginPath();cx.arc(-12,-C.h/2+b,4,0,6.28);cx.fill();}
    
    const dist=P.x-C.x;
    if(dist<100&&G.run){cx.fillStyle='#ff0000';cx.font='bold 18px Arial';cx.textAlign='center';cx.fillText('!',0,-C.h/2-15+b);}
    
    cx.restore();
}

function drawPlayer(){
    const skin=SKINS.find(s=>s.id===G.skin)||SKINS[0];
    cx.save();cx.translate(P.x+P.w/2,P.y+P.h/2);
    
    if(G.inv>0&&Math.floor(G.inv/4)%2===0)cx.globalAlpha=0.4;
    
    let body=skin.body,head=skin.head;
    if(skin.id==='rainbow'){body=`hsl(${P.hue},100%,50%)`;head=`hsl(${(P.hue+60)%360},100%,60%)`;}
    
    const dance=G.cut&&G.cutP===0,b=G.run?Math.sin(P.fr)*3:(dance?Math.sin(P.fr*1.5)*5:0);
    
    // Barra de vida
    if(G.run){
        const hw=P.w+8;
        cx.fillStyle='#333';cx.fillRect(-hw/2,-P.h/2-10,hw,5);
        cx.fillStyle=G.hp>60?'#00ff88':G.hp>30?'#ffcc00':'#ff4444';
        cx.fillRect(-hw/2,-P.h/2-10,hw*G.hp/MAX_HP,5);
        if(G.regenT>180){cx.strokeStyle='#00ff88';cx.lineWidth=1;cx.strokeRect(-hw/2,-P.h/2-10,hw,5);}
    }
    
    // Sombra
    cx.fillStyle='rgba(0,0,0,0.3)';cx.beginPath();cx.ellipse(0,P.h/2-2,P.w/2+2,4,0,0,6.28);cx.fill();
    
    if(dance)cx.rotate(Math.sin(P.fr*2)*0.1);
    
    // Pernas
    cx.fillStyle=body;
    const lL=G.run?Math.sin(P.fr)*6:0,lR=G.run?-Math.sin(P.fr)*6:0;
    cx.fillRect(-6,P.h/2-16+lL,7,14);cx.fillRect(-1,P.h/2-16+lR,7,14);
    cx.fillStyle='#222';cx.fillRect(-7,P.h/2-3+lL,9,4);cx.fillRect(-2,P.h/2-3+lR,9,4);
    
    // Corpo
    cx.fillStyle=body;cx.fillRect(-P.w/2+2,-P.h/2+12+b,P.w-4,P.h-28);
    
    // Cabeça
    cx.fillStyle=head;cx.beginPath();cx.arc(0,-P.h/2+10+b,11,0,6.28);cx.fill();
    cx.fillStyle='#4a3020';cx.beginPath();cx.arc(0,-P.h/2+4+b,11,3.5,6,false);cx.fill();
    
    // Olho
    cx.fillStyle='#fff';cx.beginPath();cx.ellipse(4,-P.h/2+9+b,4,5,0,0,6.28);cx.fill();
    cx.fillStyle='#222';cx.beginPath();cx.arc(5,-P.h/2+9+b,2,0,6.28);cx.fill();
    
    // Boca
    if(dance){cx.strokeStyle='#222';cx.lineWidth=2;cx.beginPath();cx.arc(2,-P.h/2+15+b,4,0.2,2.9);cx.stroke();}
    else if(G.run){cx.fillStyle='#222';cx.beginPath();cx.ellipse(2,-P.h/2+16+b,3,2,0,0,6.28);cx.fill();}
    
    if(!P.dbl&&!P.gr){cx.strokeStyle='rgba(255,200,0,0.5)';cx.lineWidth=2;cx.beginPath();cx.arc(0,0,20,0,6.28);cx.stroke();}
    
    if(dance){cx.fillStyle='#ffcc00';cx.font='14px Arial';cx.fillText('♪',-18,-P.h/2-8+Math.sin(G.t/10)*4);cx.fillText('♫',14,-P.h/2-12+Math.sin(G.t/10+1)*4);}
    
    cx.restore();
}

function loop(){update();render();requestAnimationFrame(loop);}

function show(id){document.querySelectorAll('.screen').forEach(s=>s.classList.remove('show'));if(id)document.getElementById(id).classList.add('show');}

function showMenu(){
    show('menu');
    ['hud','healthBar','copbar','regen'].forEach(x=>document.getElementById(x).classList.remove('show'));
    document.getElementById('mc').textContent=G.total;
    document.getElementById('record').textContent=G.record;
    G.run=false;G.pause=false;G.cut=false;hideStory();
}

function showShop(){show('shop');document.getElementById('sc').textContent=G.total;renderShop();}

function startGame(){
    show(null);
    ['hud','healthBar','copbar'].forEach(x=>document.getElementById(x).classList.remove('show'));
    init();
}

function pause(){G.pause=true;show('pause');}
function resume(){G.pause=false;show(null);}

function gameOver(r){
    G.run=false;G.total+=G.coins;
    if(Math.floor(G.dist)>G.record)G.record=Math.floor(G.dist);
    localStorage.setItem('coins',G.total);localStorage.setItem('record',G.record);
    
    document.getElementById('fc').textContent=G.coins;
    document.getElementById('fs').textContent=Math.floor(G.score);
    document.getElementById('fd').textContent=Math.floor(G.dist)+'m';
    document.getElementById('ft').textContent=G.total;
    
    if(r==='pego'){document.getElementById('overt').textContent='🚔 PEGO!';document.getElementById('overs').textContent='O policial te pegou!';}
    else if(r==='morreu'){document.getElementById('overt').textContent='💀 MORREU!';document.getElementById('overs').textContent='Você perdeu toda a vida!';}
    else{document.getElementById('overt').textContent='💀 CAIU!';document.getElementById('overs').textContent='Você caiu no abismo!';}
    
    show('over');
    ['hud','healthBar','copbar','regen'].forEach(x=>document.getElementById(x).classList.remove('show'));
}

function renderShop(){
    const g=document.getElementById('skins');g.innerHTML='';
    for(let s of SKINS){
        const own=G.owned.includes(s.id),eq=G.skin===s.id;
        let bs=s.body,hs=s.head;
        if(s.id==='rainbow'){bs='linear-gradient(135deg,red,orange,yellow,green,blue,purple)';hs=bs;}
        const d=document.createElement('div');d.className='skin'+(eq?' sel':'');
        d.innerHTML=`<div class="skinprev"><div class="skinhead" style="background:${hs}"></div><div class="skinbody" style="background:${bs}"></div></div><div class="skinname">${s.name}</div><div class="skinprice">${own?'✓':'💰'+s.price}</div>${own?`<button class="skinbtn ${eq?'eqd':'equip'}" ${eq?'disabled':''} data-id="${s.id}" data-a="eq">${eq?'✓':'Usar'}</button>`:`<button class="skinbtn buy" ${G.total<s.price?'disabled':''} data-id="${s.id}" data-a="buy">Comprar</button>`}`;
        g.appendChild(d);
    }
    g.onclick=e=>{
        const b=e.target.closest('[data-a]');if(!b)return;
        const id=b.dataset.id,a=b.dataset.a,s=SKINS.find(x=>x.id===id);
        if(a==='buy'&&s&&G.total>=s.price){G.total-=s.price;G.owned.push(id);localStorage.setItem('coins',G.total);localStorage.setItem('owned',JSON.stringify(G.owned));document.getElementById('sc').textContent=G.total;renderShop();}
        else if(a==='eq'){G.skin=id;localStorage.setItem('skin',id);renderShop();}
    };
}

document.getElementById('mc').textContent=G.total;
document.getElementById('record').textContent=G.record;
loop();
</script>
</body>
</html>
