<html lang="ko">
<head>
<meta charset="UTF-8"><meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>출결 시스템</title>
<style>
body{font-family:Arial;text-align:center;padding:20px}
#display{font-size:32px;border:2px solid #000;padding:15px;margin:20px auto;width:220px}
.grid{display:grid;grid-template-columns:repeat(3,80px);gap:10px;justify-content:center}
button{font-size:24px;padding:20px;text-align:center;display:flex;align-items:center;justify-content:center}table{margin:auto;border-collapse:collapse;width:95%}
td,th{border:1px solid #000;padding:10px}.late{background:red;color:white}
img{width:80px}.hidden{display:none}video{width:250px}#list{max-height:80vh;overflow-y:auto}
.bottom-buttons{position:fixed;bottom:10px;left:50%;transform:translateX(-50%);display:flex;gap:10px}
</style>
</head>
<body>
<button onclick="adminLogin()" style="position:absolute;top:10px;left:10px;font-size:12px;padding:6px 10px;">출결 리스트</button>
<h1>출결 시스템</h1>
<div id="main"><div id="display"></div>
<div class="grid">
<button onclick="press('1')">1</button><button onclick="press('2')">2</button><button onclick="press('3')">3</button>
<button onclick="press('4')">4</button><button onclick="press('5')">5</button><button onclick="press('6')">6</button>
<button onclick="press('7')">7</button><button onclick="press('8')">8</button><button onclick="press('9')">9</button>
<button onclick="submitID()">OK</button><button onclick="press('0')">0</button><button onclick="del()">DEL</button></div></div>
<div id="camera" class="hidden" style="display:flex;flex-direction:column;align-items:center;justify-content:center;"> <video id="video" autoplay></video><br> <button id="captureBtn" onclick="capture()" style="display:none;">촬영</button> <canvas id="canvas" class="hidden"></canvas> </div>
<div id="list" class="hidden"><h2>출결 기록</h2><table id="table"><tr><th>학번</th><th>시간</th><th>사진</th></tr></table>
<div class="bottom-buttons"><button onclick="goHome()">메인</button><button onclick="clearAll()">전체 삭제</button><button onclick="exportCSV()">내보내기</button></div></div>
<script>
let input='',currentID='',stream;let logs=JSON.parse(localStorage.getItem('logs')||'[]');
function update(){display.innerText=input}
function press(n){input+=n;update()} function del(){input=input.slice(0,-1);update()}
async function submitID(){if(!input)return;if(logs.find(l=>l.student_id===input)){alert('이미 출석함');input='';update();return;}currentID=input;input='';update();main.classList.add('hidden');camera.classList.remove('hidden');stream=await navigator.mediaDevices.getUserMedia({video:true});video.srcObject=stream; video.onloadedmetadata=()=>{captureBtn.style.display='flex';};}
function capture(){let c=canvas,ctx=c.getContext('2d');c.width=video.videoWidth;c.height=video.videoHeight;ctx.drawImage(video,0,0);let img=c.toDataURL();let now=new Date(),time=now.toTimeString().slice(0,5);logs.push({student_id:currentID,time,photo:img});logs.sort((a,b)=>a.student_id.localeCompare(b.student_id));localStorage.setItem('logs',JSON.stringify(logs));stream.getTracks().forEach(t=>t.stop());alert('출결 완료');location.reload();}
function adminLogin(){let pw=prompt('관리자 비밀번호');if(pw==='0331')showList();else alert('틀림');}
function showList(){main.classList.add('hidden');list.classList.remove('hidden');table.innerHTML='<tr><th>학번</th><th>시간</th><th>사진</th></tr>';logs.forEach(log=>{let tr=document.createElement('tr');let late=log.time>='07:41';tr.innerHTML=`<td class='${late?'late':''}'>${log.student_id}</td><td>${log.time}</td><td><img src='${log.photo}'></td>`;table.appendChild(tr);});}
function goHome(){list.classList.add('hidden');main.classList.remove('hidden');}
function clearAll(){if(confirm('삭제?')){localStorage.removeItem('logs');location.reload();}}
function exportCSV(){let csv='학번,시간,사진\n'+logs.map(l=>`${l.student_id},${l.time},${l.photo}`).join('\n');let a=document.createElement('a');a.href=URL.createObjectURL(new Blob([csv]));a.download='attendance.csv';a.click();}
update();
</script></body></html>
