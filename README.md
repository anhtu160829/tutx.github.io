<html lang="vi">
<head>
<meta charset="UTF-8">
<title>anhtubantool</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@600;800&display=swap" rel="stylesheet">
<style>
body{
  margin:0;
  font-family:Segoe UI,Arial;
  background:linear-gradient(270deg,red,orange,yellow,green,cyan,blue,violet);
  background-size:1400% 1400%;
  animation:rainbow 18s ease infinite;
}
@keyframes rainbow{
  0%{background-position:0% 50%}
  50%{background-position:100% 50%}
  100%{background-position:0% 50%}
}
h1{
  text-align:center;
  font-family:'Cinzel',serif;
  font-size:34px;
  margin:14px 0;
  letter-spacing:2px;
  color:#000;
}
.tabs{display:flex;justify-content:center;gap:10px;margin-bottom:10px}
.tab{padding:10px 16px;border-radius:12px;background:#fff;border:2px solid #000;cursor:pointer;font-weight:bold;color:#000}
.tab.active{background:#ffd700}
.card{
  max-width:520px;
  margin:0 auto 20px;
  background:#fff;
  border-radius:18px;
  padding:18px;
  border:2px solid #000;
  display:none;
  color:#000;
}
.card.active{display:block}
.row{margin:6px 0;font-size:15px}
.label{font-weight:bold}
.value{margin-left:6px}
input,button{
  width:100%;
  padding:10px;
  border-radius:10px;
  border:2px solid #000;
  margin-top:6px;
}
button{
  background:#ffd700;
  font-weight:bold;
  cursor:pointer;
}
.history{
  margin-top:10px;
  font-size:12px;
  max-height:200px;
  overflow:auto;
}
.history div{
  border-bottom:1px dotted #000;
  padding:2px 0;
}
</style>
</head>

<body>
<h1>anhtubantool</h1>

<div class="tabs">
  <div class="tab active" onclick="openTab('sicbo')">SICBO</div>
  <div class="tab" onclick="openTab('chanle')">CHẴN LẺ</div>
  <div class="tab" onclick="openTab('sunwin')">SUNWIN</div>
</div>

<!-- SICBO -->
<div id="sicbo" class="card active">
<h2>🎲 SICBO AI</h2>
<input id="sicbo_md5" placeholder="Dán MD5 32 ký tự hex">
<button onclick="analyzeSicbo()">PHÂN TÍCH</button>

<div class="row"><span class="label">Dự đoán:</span><span class="value" id="sb_pred">--</span></div>
<div class="row"><span class="label">Vị lót:</span><span class="value" id="sb_lot">--</span></div>
<div class="row"><span class="label">Entropy:</span><span class="value" id="sb_ent">--</span></div>
<div class="row"><span class="label">Deep:</span><span class="value" id="sb_deep">--</span></div>

<div class="history" id="sb_history"></div>
</div>

<!-- CHẴN LẺ -->
<div id="chanle" class="card">
<h2>🔐 CHẴN / LẺ AI</h2>
<input id="cl_md5" placeholder="Dán MD5 32 ký tự hex">
<button onclick="analyzeChanLe()">PHÂN TÍCH</button>

<div class="row"><span class="label">Kết quả:</span><span class="value" id="cl_res">--</span></div>
<div class="row"><span class="label">Vị lót:</span><span class="value" id="cl_lot">--</span></div>
<div class="row"><span class="label">Tỉ lệ:</span><span class="value" id="cl_conf">--</span></div>
<div class="row"><span class="label">Lời khuyên:</span><span class="value" id="cl_adv">--</span></div>

<div class="history" id="cl_history"></div>
</div>

<!-- SUNWIN -->
<div id="sunwin" class="card">
<h2>🎰 SUNWIN AI</h2>

<div class="row"><span class="label">Phiên hiện tại:</span><span class="value" id="phien">--</span></div>
<div class="row"><span class="label">Phiên dự đoán:</span><span class="value" id="phien_next">--</span></div>
<div class="row"><span class="label">Xúc xắc:</span><span class="value" id="xx">--</span></div>
<div class="row"><span class="label">Tổng:</span><span class="value" id="tong">--</span></div>
<div class="row"><span class="label">Kết quả:</span><span class="value" id="ketqua">--</span></div>
<div class="row"><span class="label">Dự đoán:</span><span class="value" id="dudoan">--</span></div>
<div class="row"><span class="label">Cầu:</span><span class="value" id="sw_cau">--</span></div>
<div class="row"><span class="label">Đánh giá:</span><span class="value" id="sw_rs">--</span></div>

<div class="history" id="sw_history"></div>
</div>

<script>
function openTab(id){
  document.querySelectorAll('.tab').forEach(t=>t.classList.remove('active'));
  document.querySelectorAll('.card').forEach(c=>c.classList.remove('active'));
  event.target.classList.add('active');
  document.getElementById(id).classList.add('active');
}

let swHistory=[], lastPhien=null;

async function loadSunwin(){
  try{
    const r = await fetch("https://sunwinsaygex-tzz9.onrender.com/api/sun");
    const d = await r.json();
    if(d.phien === lastPhien) return;
    lastPhien = d.phien;

    phien.innerText = d.phien;
    phien_next.innerText = d.phien + 1;
    sw_cau.innerText = d.cau || "--";
    xx.innerText = `${d.xuc_xac_1}-${d.xuc_xac_2}-${d.xuc_xac_3}`;
    tong.innerText = d.tong;
    ketqua.innerText = d.ket_qua;
    dudoan.innerText = d.du_doan;

    const rs = (d.ket_qua === d.du_doan) ? "THẮNG" : "THUA";
    sw_rs.innerText = rs;

    swHistory.unshift(`#${d.phien} | ${d.du_doan} → ${rs}`);
    if(swHistory.length>20) swHistory.pop();
    sw_history.innerHTML = swHistory.map(x=>`<div>${x}</div>`).join("");
  }catch{}
}
setInterval(loadSunwin,3000);
loadSunwin();
</script>
</body>
</html>
