!doctype html>
<html lang="es">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Museo interactivo: evolución de los relojes de bolsillo</title>
<style>
*{box-sizing:border-box}body{margin:0;font-family:system-ui,Arial,sans-serif;background:#f4ead7;color:#382714}
main{max-width:1000px;margin:auto;padding:20px}.hero{background:#fff9ec;border:1px solid #d9c49a;border-radius:24px;padding:20px;text-align:center}
h1{margin:0 0 6px}.sub{color:#746552}.years{display:flex;gap:8px;overflow:auto;margin:18px 0}.years button{border:1px solid #cdb786;background:#fffaf0;border-radius:12px;padding:12px 16px;font-weight:800;white-space:nowrap}.years button.active{background:#d7aa4f}
.viewer{height:520px;background:radial-gradient(circle,#fffdf6,#eadfc8);border-radius:22px;display:flex;align-items:center;justify-content:center;perspective:1100px;overflow:hidden;touch-action:none}
.model{width:330px;height:360px;position:relative;transform-style:preserve-3d;cursor:grab}.model:active{cursor:grabbing}
.clock{position:absolute;left:50%;top:50%;transform:translate(-50%,-50%);transform-style:preserve-3d;transition:.35s}
.face,.back{position:absolute;inset:0;border-radius:50%;backface-visibility:hidden;transform-style:preserve-3d}.back{transform:rotateY(180deg);display:grid;place-items:center;text-align:center;font-weight:900;color:#fff1c9;background:#76501c;border:12px solid #51340d}
.hand{position:absolute;left:50%;top:50%;width:7px;height:34%;background:#3a2919;border-radius:8px;transform-origin:50% 100%;transform:translate(-50%,-100%) rotate(35deg);z-index:4}.hand.b{height:25%;width:9px;transform:translate(-50%,-100%) rotate(125deg)}
.pin{position:absolute;left:50%;top:50%;width:18px;height:18px;border-radius:50%;background:#b77a1e;transform:translate(-50%,-50%);z-index:5}
.chain{position:absolute;border-right:9px dotted #80602d;border-radius:50%}
.ring{position:absolute;border:10px solid #9b6a21;border-radius:50%}
.cover{position:absolute;inset:-3px;border-radius:50%;background:#a36d20;border:12px solid #5d3b0d;transform:translateZ(9px)}
.info{margin-top:15px;background:#fff9ec;border:1px solid #dbc79f;border-radius:18px;padding:16px}.info h2{margin:0 0 6px}.info p{margin:0;color:#695d4c;line-height:1.5}
.controls{display:flex;justify-content:center;gap:8px;margin-top:12px;flex-wrap:wrap}.controls button{border:1px solid #cdb786;background:#fffaf0;border-radius:12px;padding:11px 16px;font-weight:800}
.note{font-size:13px;color:#766a59;text-align:center;margin-top:12px}
</style>
</head>
<body><main>
<section class="hero">
<h1>⌚ Museo interactivo de los relojes de bolsillo</h1>
<div class="sub">Selecciona una época y arrastra el reloj para girarlo en 3D.</div>
<div class="years">
<button class="active" data-i="0">1500s</button><button data-i="1">1700s</button><button data-i="2">1800s</button><button data-i="3">1900s</button><button data-i="4">Actualidad</button>
</div>
<div class="viewer" id="viewer"><div class="model" id="model"><div class="clock" id="clock"></div></div></div>
<div class="controls"><button id="left">↶ Girar</button><button id="right">Girar ↷</button><button id="front">⟳ Frente</button></div>
<div class="info"><h2 id="title"></h2><p id="desc"></p></div>
<div class="note">👆 En el celular: toca y arrastra sobre el reloj. Cada época usa un diseño diferente.</div>
</section>
</main>
<script>
const data=[
 {title:"1500s — Reloj de tambor",desc:"Uno de los primeros tipos de relojes portátiles: grande, grueso y redondeado, pensado para llevarse encima.",kind:"drum"},
 {title:"1700s — Reloj con tapa ornamentada",desc:"Caja decorada y tapa protectora para cubrir la esfera. La cadena y los adornos también formaban parte del diseño.",kind:"cover"},
 {title:"1800s — Reloj de bolsillo clásico",desc:"Caja metálica redonda, esfera clara, números y cadena: el estilo clásico que se volvió muy reconocible.",kind:"classic"},
 {title:"1900s — Reloj ferroviario",desc:"Diseño enfocado en una lectura clara y precisa de la hora, asociado a los relojes utilizados en el ámbito ferroviario.",kind:"rail"},
 {title:"Actualidad — Reloj de colección",desc:"Los relojes de bolsillo actuales pueden ser piezas de colección, recuerdos familiares o diseños contemporáneos inspirados en modelos antiguos.",kind:"modern"}
];
let rx=-8,ry=-25,drag=false,px=0,py=0;
const model=document.querySelector("#model"); let clock=document.querySelector("#clock"); const viewer=document.querySelector("#viewer"),title=document.querySelector("#title"),desc=document.querySelector("#desc");
function draw(){model.style.transform=`rotateX(${rx}deg) rotateY(${ry}deg)`}
function makeClock(kind){
 let c=document.createElement("div"); c.className="clock";
 let size=kind==="drum"?250:kind==="cover"?220:kind==="classic"?205:kind==="rail"?190:165;
 c.style.width=size+"px";c.style.height=size+"px";
 let face=document.createElement("div");face.className="face";
 let back=document.createElement("div");back.className="back";back.innerHTML="RELOJ<br>DE BOLSILLO";
 let hand=document.createElement("div");hand.className="hand";
 let hand2=document.createElement("div");hand2.className="hand b";
 let pin=document.createElement("div");pin.className="pin";
 face.append(hand,hand2,pin);
 if(kind==="drum"){
   face.style.cssText+=";background:radial-gradient(circle,#fff7ce,#d8a63f 65%,#875313);border:15px solid #68430b;box-shadow:inset 0 0 0 7px #f0ce78,0 20px 35px #0003";
   let r=document.createElement("div");r.className="ring";r.style.cssText="width:55px;height:48px;left:50%;top:-48px;transform:translateX(-50%)";c.append(r);
 } else if(kind==="cover"){
   face.style.cssText+=";background:radial-gradient(circle,#fff8df,#d8b46b);border:10px solid #69450f;box-shadow:inset 0 0 0 4px #ead18d,0 20px 35px #0003";
   let cover=document.createElement("div");cover.className="cover";c.append(cover);
   let r=document.createElement("div");r.className="ring";r.style.cssText="width:48px;height:42px;left:50%;top:-42px;transform:translateX(-50%)";c.append(r);
 } else if(kind==="classic"){
   face.style.cssText+=";background:#f6f0dc;border:9px solid #8b8b83;box-shadow:inset 0 0 0 3px #c7c2ad,0 20px 35px #0003";
 } else if(kind==="rail"){
   face.style.cssText+=";background:#fff;border:12px solid #555;box-shadow:inset 0 0 0 3px #aaa,0 20px 35px #0003";
   hand.style.height="31%";hand2.style.height="22%";
 } else {
   face.style.cssText+=";background:radial-gradient(circle,#fff,#d8d2c5);border:6px solid #333;box-shadow:inset 0 0 0 3px #999,0 20px 35px #0003";
 }
 let chain=document.createElement("div");chain.className="chain";
 chain.style.cssText=`right:-${kind==="modern"?45:85}px;top:28%;width:${kind==="modern"?55:105}px;height:${kind==="drum"?180:145}px`;
 c.append(face,back,chain);
 return c;
}
function select(i){
 document.querySelectorAll(".years button").forEach((b,j)=>b.classList.toggle("active",i===j));
 clock.replaceWith(makeClock(data[i].kind)); clock=document.querySelector("#model .clock");
 title.textContent=data[i].title;desc.textContent=data[i].desc;rx=-8;ry=-25;draw();
}
title.textContent=data[0].title;desc.textContent=data[0].desc;
document.querySelectorAll(".years button").forEach((b,i)=>b.onclick=()=>select(i));
viewer.onpointerdown=e=>{drag=true;px=e.clientX;py=e.clientY;viewer.setPointerCapture(e.pointerId)};
viewer.onpointermove=e=>{if(!drag)return;ry+=(e.clientX-px)*.8;rx-=(e.clientY-py)*.7;rx=Math.max(-65,Math.min(65,rx));px=e.clientX;py=e.clientY;draw()};
viewer.onpointerup=viewer.onpointercancel=()=>drag=false;
document.querySelector("#left").onclick=()=>{ry-=35;draw()};document.querySelector("#right").onclick=()=>{ry+=35;draw()};document.querySelector("#front").onclick=()=>{rx=-8;ry=-25;draw()};
select(0);
</script></body></html># Museo-de-relojes-de-bolsillo-
