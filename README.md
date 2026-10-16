
<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Traffic Monitor</title>
<style>
*{box-sizing:border-box}
body{margin:0;background:#0f1115;color:#d7dbe2;font:13px Arial,sans-serif}
header{height:54px;padding:18px 22px;border-bottom:1px solid #292d35;font-weight:600}
header span{color:#8da7ff}
main{max-width:900px;margin:25px auto;padding:0 16px}
h2{font-size:13px;font-weight:600;color:#aeb6c4;margin:25px 0 10px}
.stats{display:grid;grid-template-columns:repeat(3,1fr);gap:10px}
.stat,.section{background:#15181e;border:1px solid #292d35;border-radius:6px}
.stat{padding:14px}
.label{color:#929aa8;font-size:12px}
.value{margin-top:8px;font-size:20px}
.section{overflow-x:auto}
table{width:100%;border-collapse:collapse;text-align:left}
th,td{padding:11px 13px;border-bottom:1px solid #292d35}
th{font-size:11px;color:#8e98a8;font-weight:500}
tr:last-child td{border-bottom:0}
button{background:#20242c;color:#d7dbe2;border:1px solid #353b46;border-radius:4px;padding:7px 10px;cursor:pointer}
button:hover{background:#292f39}
footer{margin-top:18px;color:#777f8c;font-size:11px;line-height:1.7}
@media(max-width:500px){.stats{grid-template-columns:1fr}.value{font-size:18px}}
</style>
</head>
<body>
<header><span>●</span> Traffic Monitor</header>
<main>
  <div class="stats">
    <div class="stat">
      <div class="label">Oturum süresi</div>
      <div class="value" id="time">0 sn</div>
    </div>
    <div class="stat">
      <div class="label">Tıklamalar</div>
      <div class="value" id="clicks">0</div>
    </div>
    <div class="stat">
      <div class="label">Bağlantı</div>
      <div class="value" id="online">Online</div>
    </div>
  </div>

  <h2>Tarayıcı bilgileri</h2>
  <div class="section">
    <table><tbody id="info"></tbody></table>
  </div>

  <h2>Olay kayıtları</h2>
  <div style="display:flex;justify-content:flex-end;padding-bottom:9px">
    <button id="clear">Temizle</button>
  </div>
  <div class="section">
    <table>
      <thead><tr><th>Saat</th><th>Olay</th><th>Detay</th></tr></thead>
      <tbody id="events"></tbody>
    </table>
  </div>

  <footer>
    IP adresi bu panelde alınmaz veya gösterilmez.
    Veriler geçicidir ve yalnızca bu sekmede tutulur.
    Bu panel diğer ziyaretçilerin verilerini uzaktan toplamaz.
  </footer>
</main>

<script>
const $=id=>document.getElementById(id);
let clicks=0;
const start=Date.now();

const info=[
 ["Tarayıcı",navigator.userAgent],
 ["Dil",navigator.language],
 ["Platform",navigator.userAgentData?.platform||navigator.platform||"Bilinmiyor"],
 ["Ekran",screen.width+" × "+screen.height],
 ["Saat dilimi",Intl.DateTimeFormat().resolvedOptions().timeZone],
 ["Sayfa",location.pathname],
 ["Önceki sayfa",document.referrer||"Yok"],
 ["Bağlantı türü",navigator.connection?.effectiveType||"Bilinmiyor"]
];

info.forEach(([k,v])=>{
 const tr=document.createElement("tr");
 [k,v].forEach(t=>{
  const td=document.createElement("td");
  td.textContent=t;
  tr.appendChild(td);
 });
 $("info").appendChild(tr);
});

function log(event,detail){
 const tr=document.createElement("tr");
 [new Date().toLocaleTimeString("tr-TR"),event,detail].forEach(v=>{
  const td=document.createElement("td");
  td.textContent=v;
  tr.appendChild(td);
 });
 $("events").prepend(tr);
 while($("events").children.length>50)
  $("events").lastElementChild.remove();
}

function update(){
 $("time").textContent=Math.floor((Date.now()-start)/1000)+" sn";
 $("clicks").textContent=clicks;
 $("online").textContent=navigator.onLine?"Online":"Offline";
}

document.addEventListener("click",e=>{
 if(e.target.id==="clear")return;
 clicks++;
 log("Tıklama",e.target.tagName.toLowerCase());
 update();
});

window.addEventListener("online",()=>{
 log("Bağlantı","Online");update();
});
window.addEventListener("offline",()=>{
 log("Bağlantı","Offline");update();
});
document.addEventListener("visibilitychange",()=>{
 log("Sekme",document.hidden?"Arka planda":"Aktif");
});

$("clear").onclick=()=>{
 $("events").replaceChildren();
 log("Sistem","Kayıtlar temizlendi");
};

log("Sistem","Panel başlatıldı");
update();
setInterval(update,1000);
</script>
</body>
</html>
