

<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>ระบบจดออเดอร์ไข่เป็ด</title>
<style>
*{box-sizing:border-box}
body{font-family:Arial,sans-serif;background:#f4f6f8;color:#202938;margin:0;padding:16px}
main{max-width:1000px;margin:auto}
h1{font-size:25px;margin-bottom:6px}
.sub{color:#64748b;margin-bottom:18px}
.panel,.stat{background:white;padding:16px;border-radius:14px;margin-bottom:14px;box-shadow:0 2px 8px #0000000a}
h2{font-size:18px;margin-top:0}
.grid{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:12px}
label{display:block;font-size:14px;font-weight:bold;margin-bottom:5px}
input,select,textarea,button{font:inherit}
input,select,textarea{width:100%;padding:11px;border:1px solid #cbd5e1;border-radius:8px;background:white}
textarea{resize:vertical}
button{border:0;border-radius:8px;padding:11px 14px;cursor:pointer;font-weight:bold}
.primary{background:#166534;color:white}
.secondary{background:#e2e8f0;color:#1e293b}
.danger{background:#fee2e2;color:#991b1b}
.price-buttons{display:flex;gap:8px}
.price-buttons button{flex:1;border:2px solid #cbd5e1;background:white}
.price-buttons button.active{border-color:#166534;background:#dcfce7;color:#166534}
.stats{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:10px}
.stat{margin:0;padding:12px}
.stat small{display:block;color:#64748b;font-size:12px}
.stat strong{display:block;font-size:19px;margin-top:6px;overflow-wrap:anywhere}
.actions{display:flex;gap:8px;flex-wrap:wrap;margin-top:14px}
.table-wrap{overflow-x:auto}
table{width:100%;border-collapse:collapse;font-size:13px}
th,td{text-align:left;padding:10px;border-bottom:1px solid #e2e8f0;vertical-align:top}
th{background:#f8fafc;white-space:nowrap}
td{min-width:90px}
td button{margin:2px;padding:7px;font-size:12px}
.status{display:inline-block;padding:4px 7px;border-radius:6px;background:#e2e8f0}
#message{margin:10px 0;color:#166534;font-size:14px}
.muted{color:#64748b;font-size:13px}
@media(max-width:520px){
 body{padding:10px}
 .grid{grid-template-columns:1fr}
 .stats{grid-template-columns:1fr}
 .stat strong{font-size:21px}
}
</style>
</head>
<body>
<main>
<h1>🥚 ระบบจดออเดอร์ไข่เป็ด</h1>
<div class="sub">บันทึกออเดอร์ ติดตามการส่ง และเช็กยอดรับเงิน</div>

<div class="stats">
 <div class="stat"><small>ยอดขายรวม</small><strong id="sales">0 บาท</strong></div>
 <div class="stat"><small>รับเงินแล้ว</small><strong id="received">0 บาท</strong></div>
 <div class="stat"><small>ยอดค้างชำระ</small><strong id="due">0 บาท</strong></div>
</div>

<section class="panel">
<h2 id="formTitle">เพิ่มออเดอร์ใหม่</h2>
<form id="orderForm">
 <div class="grid">
  <div>
   <label for="customer">ชื่อลูกค้า *</label>
   <input id="customer" required maxlength="100" placeholder="ชื่อผู้สั่งซื้อ">
  </div>
  <div>
   <label for="phone">เบอร์โทรศัพท์</label>
   <input id="phone" type="tel" maxlength="30" placeholder="เบอร์โทรลูกค้า">
  </div>
  <div>
   <label>ราคาต่อถาด *</label>
   <div class="price-buttons">
    <button type="button" data-price="125" class="active">125 บาท</button>
    <button type="button" data-price="135">135 บาท</button>
   </div>
  </div>
  <div>
   <label for="qty">จำนวนถาด *</label>
   <input id="qty" type="number" min="1" step="1" required value="1">
  </div>
  <div>
   <label for="orderDate">วันที่สั่งซื้อ *</label>
   <input id="orderDate" type="date" required>
  </div>
  <div>
   <label for="deliveryDate">วันที่นัดส่ง</label>
   <input id="deliveryDate" type="date">
  </div>
  <div>
   <label for="deliveryStatus">สถานะจัดส่ง</label>
   <select id="deliveryStatus">
    <option>รอจัดส่ง</option>
    <option>จัดส่งแล้ว</option>
    <option>ยกเลิก</option>
   </select>
  </div>
  <div>
   <label for="paid">รับเงินแล้ว (บาท)</label>
   <input id="paid" type="number" min="0" step="1" value="0">
  </div>
  <div style="grid-column:1/-1">
   <label for="note">หมายเหตุ</label>
   <textarea id="note" rows="2" maxlength="500" placeholder="รายละเอียดเพิ่มเติม (ถ้ามี)"></textarea>
  </div>
 </div>
 <p><strong>ยอดออเดอร์นี้: <span id="preview">125 บาท</span></strong></p>
 <div class="actions">
  <button class="primary" id="saveBtn" type="submit">บันทึกออเดอร์</button>
  <button class="secondary" id="cancelEdit" type="button" hidden>ยกเลิกการแก้ไข</button>
 </div>
 <div id="message" role="status"></div>
</form>
</section>

<section class="panel">
<h2>รายการออเดอร์</h2>
<div class="grid" style="margin-bottom:12px">
 <div><label for="search">ค้นหาลูกค้า / เบอร์โทร</label><input id="search" placeholder="พิมพ์คำค้นหา"></div>
 <div><label for="filter">กรองสถานะจัดส่ง</label>
  <select id="filter">
   <option value="">ทุกสถานะ</option>
   <option>รอจัดส่ง</option>
   <option>จัดส่งแล้ว</option>
   <option>ยกเลิก</option>
  </select>
 </div>
</div>
<div id="loading" class="muted">กำลังเชื่อมต่อฐานข้อมูล...</div>
<div class="table-wrap">
<table>
<thead><tr>
<th>ลูกค้า / เบอร์โทร</th><th>วันที่สั่ง</th><th>วันส่ง</th>
<th>จำนวน</th><th>ยอดขาย</th><th>รับแล้ว</th>
<th>ค้าง</th><th>สถานะ</th><th>จัดการ</th>
</tr></thead>
<tbody id="orderRows"></tbody>
</table>
</div>
<p id="empty" class="muted" hidden>ยังไม่มีออเดอร์</p>
</section>
<p class="muted">ข้อมูลบันทึกใน Firebase เพื่อให้เครื่องที่เปิดเว็บนี้เห็นรายการร่วมกัน</p>
</main>

<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-database-compat.js"></script>
<script>
const firebaseConfig = {
  apiKey: "AIzaSyBLkRBYsbCpZmKPuFlf4FSi50AELr46WKw",
  authDomain: "eggduckwaiya.firebaseapp.com",
  databaseURL: "https://eggduckwaiya-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "eggduckwaiya",
  storageBucket: "eggduckwaiya.firebasestorage.app",
  messagingSenderId: "932383285255",
  appId: "1:932383285255:web:0958dacc81cf366d688aa2",
  measurementId: "G-GKN05BJ84C"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.database();
const ordersRef = db.ref("orders");

let orders = [];
let selectedPrice = 125;
let editingId = null;
let connected = false;

const $ = id => document.getElementById(id);
const money = n => Number(n || 0).toLocaleString("th-TH") + " บาท";
const today = () => {
 const d = new Date();
 return d.getFullYear()+"-"+String(d.getMonth()+1).padStart(2,"0")+"-"+String(d.getDate()).padStart(2,"0");
};
const safe = value => String(value ?? "").replace(/[&<>"']/g, c => ({
 "&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"
}[c]));

function totalOf(o){return Number(o.price||0)*Number(o.qty||0)}
function paidOf(o){return Math.min(totalOf(o),Math.max(0,Number(o.paid||0)))}
function dueOf(o){return Math.max(0,totalOf(o)-paidOf(o))}
function showMessage(text,isError=false){
 $("message").textContent=text;
 $("message").style.color=isError?"#b91c1c":"#166534";
}

function updatePreview(){
 const qty=Math.max(0,Number($("qty").value||0));
 $("preview").textContent=money(selectedPrice*qty);
}
document.querySelectorAll("[data-price]").forEach(btn=>{
 btn.addEventListener("click",()=>{
  selectedPrice=Number(btn.dataset.price);
  document.querySelectorAll("[data-price]").forEach(b=>b.classList.toggle("active",b===btn));
  updatePreview();
 });
});
$("qty").addEventListener("input",updatePreview);

function resetForm(){
 editingId=null;
 $("orderForm").reset();
 $("orderDate").value=today();
 $("qty").value=1;
 $("paid").value=0;
 selectedPrice=125;
 document.querySelectorAll("[data-price]").forEach(b=>b.classList.toggle("active",b.dataset.price==="125"));
 $("formTitle").textContent="เพิ่มออเดอร์ใหม่";
 $("saveBtn").textContent="บันทึกออเดอร์";
 $("cancelEdit").hidden=true;
 updatePreview();
}
$("cancelEdit").addEventListener("click",()=>{resetForm();showMessage("");});

$("orderForm").addEventListener("submit",async e=>{
 e.preventDefault();
 if(!connected){showMessage("ยังเชื่อมต่อฐานข้อมูลไม่ได้ กรุณารอสักครู่แล้วลองใหม่",true);return}
 const qty=Number($("qty").value);
 const paid=Number($("paid").value||0);
 if(!Number.isInteger(qty)||qty<1){showMessage("กรุณาระบุจำนวนถาดอย่างน้อย 1 ถาด",true);return}
 if(!Number.isFinite(paid)||paid<0){showMessage("ยอดรับเงินต้องไม่ติดลบ",true);return}
 const record={
  customer:$("customer").value.trim(),
  phone:$("phone").value.trim(),
  price:selectedPrice,
  qty,
  orderDate:$("orderDate").value,
  deliveryDate:$("deliveryDate").value,
  deliveryStatus:$("deliveryStatus").value,
  paid,
  note:$("note").value.trim(),
  updatedAt:Date.now()
 };
 if(!record.customer){showMessage("กรุณากรอกชื่อลูกค้า",true);return}
 if(paid>record.price*qty){showMessage("ยอดรับเงินต้องไม่เกินยอดออเดอร์",true);return}
 const button=$("saveBtn");
 button.disabled=true;
 try{
  if(editingId){
   await ordersRef.child(editingId).update(record);
   showMessage("แก้ไขออเดอร์เรียบร้อย");
  }else{
   record.createdAt=Date.now();
   await ordersRef.push(record);
   showMessage("บันทึกออเดอร์เรียบร้อย");
  }
  resetForm();
 }catch(err){
  console.error(err);
  showMessage("บันทึกไม่สำเร็จ: "+err.message,true);
 }finally{button.disabled=false}
});

function render(){
 const active=orders.filter(o=>o.deliveryStatus!=="ยกเลิก");
 $("sales").textContent=money(active.reduce((s,o)=>s+totalOf(o),0));
 $("received").textContent=money(active.reduce((s,o)=>s+paidOf(o),0));
 $("due").textContent=money(active.reduce((s,o)=>s+dueOf(o),0));

 const term=$("search").value.trim().toLowerCase();
 const filter=$("filter").value;
 const list=orders.filter(o=>{
  const matchText=(o.customer+" "+o.phone).toLowerCase().includes(term);
  return matchText&&(!filter||o.deliveryStatus===filter);
 }).sort((a,b)=>(b.createdAt||0)-(a.createdAt||0));

 $("orderRows").innerHTML=list.map(o=>{
  const id=safe(o.id);
  return `<tr>
   <td><strong>${safe(o.customer)}</strong><br>${safe(o.phone)}</td>
   <td>${safe(o.orderDate||"-")}</td>
   <td>${safe(o.deliveryDate||"-")}</td>
   <td>${safe(o.qty)} ถาด<br><span class="muted">${safe(o.price)} บาท/ถาด</span></td>
   <td>${money(totalOf(o))}</td>
   <td>${money(paidOf(o))}</td>
   <td>${money(dueOf(o))}</td>
   <td><span class="status">${safe(o.deliveryStatus||"รอจัดส่ง")}</span></td>
   <td><button class="secondary" data-edit="${id}">แก้ไข</button>
   <button class="danger" data-delete="${id}">ลบ</button></td>
  </tr>`;
 }).join("");

 $("empty").hidden=list.length!==0;
}

$("orderRows").addEventListener("click",async e=>{
 const edit=e.target.closest("[data-edit]");
 const del=e.target.closest("[data-delete]");
 if(edit){
  const o=orders.find(x=>x.id===edit.dataset.edit);
  if(!o)return;
  editingId=o.id;
  $("customer").value=o.customer||"";
  $("phone").value=o.phone||"";
  selectedPrice=Number(o.price)===135?135:125;
  document.querySelectorAll("[data-price]").forEach(b=>b.classList.toggle("active",Number(b.dataset.price)===selectedPrice));
  $("qty").value=o.qty||1;
  $("orderDate").value=o.orderDate||today();
  $("deliveryDate").value=o.deliveryDate||"";
  $("deliveryStatus").value=o.deliveryStatus||"รอจัดส่ง";
  $("paid").value=o.paid||0;
  $("note").value=o.note||"";
  $("formTitle").textContent="แก้ไขออเดอร์";
  $("saveBtn").textContent="บันทึกการแก้ไข";
  $("cancelEdit").hidden=false;
  updatePreview();
  window.scrollTo({top:0,behavior:"smooth"});
 }
 if(del){
  const o=orders.find(x=>x.id===del.dataset.delete);
  if(!o)return;
  if(confirm("ยืนยันลบออเดอร์ของ "+o.customer+"? การลบนี้จะมีผลกับทุกเครื่อง")){
   try{await ordersRef.child(o.id).remove();showMessage("ลบออเดอร์แล้ว")}
   catch(err){showMessage("ลบไม่สำเร็จ: "+err.message,true)}
  }
 }
});
$("search").addEventListener("input",render);
$("filter").addEventListener("change",render);

db.ref(".info/connected").on("value",snap=>{
 connected=snap.val()===true;
 $("loading").textContent=connected?"เชื่อมต่อ Firebase แล้ว":"กำลังเชื่อมต่อ Firebase...";
});

ordersRef.on("value",snap=>{
 const data=snap.val()||{};
 orders=Object.entries(data).map(([id,o])=>({...o,id}));
 $("loading").textContent=connected?"เชื่อมต่อ Firebase แล้ว":"กำลังโหลดข้อมูล...";
 render();
},err=>{
 $("loading").textContent="โหลดข้อมูลไม่สำเร็จ: "+err.message;
 showMessage("อ่านข้อมูลไม่ได้ กรุณาตรวจสอบ Firebase Rules และการเชื่อมต่อ",true);
});

resetForm();
</script>
</body>
</html>
