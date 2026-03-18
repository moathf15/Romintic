<html lang="ar">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
<title>لعبة رومانسية بين الحبيبين</title>
<style>
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: linear-gradient(to bottom, #ffc1cc, #ffe6f0, #ffffff);
  text-align: center;
}

/* الحاوية */
.container {
  max-width: 700px;
  margin: 0 auto;
  padding: 15px;
}

/* العنوان */
h1 {
  color: #ff4f79;
  font-size: 2em;
  margin-bottom: 10px;
}

/* رسالة رومانسية في البداية */
#intro {
  font-size: 1.2em;
  color: #ff3366;
  margin-bottom: 20px;
}

/* اللاعبين */
.players {
  display: flex;
  justify-content: space-between;
  margin-bottom: 15px;
}

.player {
  background-color: #ff99aa;
  padding: 10px;
  border-radius: 12px;
  color: white;
  font-weight: bold;
  width: 45%;
}

.player2 { background-color: #ff6699; }

.score {
  margin-top: 5px;
}

input {
  padding: 8px;
  width: 120px;
  border-radius: 8px;
  border: none;
  text-align: center;
  font-weight: bold;
}

/* الأزرار */
.start-btn, .answers button {
  padding: 10px 25px;
  border-radius: 10px;
  border: none;
  font-weight: bold;
  cursor: pointer;
  background-color: #ff99aa;
  color: white;
  margin-top: 10px;
}

.answers button { width: 70%; }

.correct { background-color: #00cc44 !important; color: white; }
.wrong { background-color: #ff3333 !important; color: white; }

/* زر العودة */
.home-btn {
  padding: 8px 15px;
  border-radius: 8px;
  border: none;
  background-color: #ffffff;
  font-weight: bold;
  cursor: pointer;
  position: fixed;
  top: 10px;
  left: 50%;
  transform: translateX(-50%);
}

/* المؤقت */
.timer { font-size: 22px; margin-top: 10px; color: #ff3366; }

/* صندوق السؤال */
.question-box {
  display: none;
  background: #fff0f5;
  color: black;
  margin: 20px auto;
  padding: 15px;
  border-radius: 12px;
}

/* الرسالة */
.message { font-size: 18px; margin-top: 10px; font-weight: bold; color: #ff3366; }
</style>
</head>

<body>
<div class="container">
<h1>💖 لعبة بين الحبيبين</h1>
<div id="intro">ابدأ اللعبة مع من تحب ❤️ و حاول الإجابة قبل أن يتحول الدور للطرف الآخر!</div>

<button class="home-btn" onclick="restartGame()">🏠 الرجوع للبداية</button>

<div class="players">
  <div class="player">
    <input type="text" id="player1Name" placeholder="اسم الشخص الأول"><br>
    نقاط: <span id="score1">0</span>
  </div>
  <div class="player player2">
    <div style="text-align:right;">
      نقاط: <span id="score2">0</span>
      <input type="text" id="player2Name" placeholder="اسم الشخص الثاني">
    </div>
  </div>
</div>

<button class="start-btn" id="startBtn" onclick="startGame()">ابدأ اللعبة 💕</button>

<div class="timer" id="timer"></div>

<div class="question-box" id="questionBox">
  <h2 id="question"></h2>
  <div class="answers" id="answers"></div>
  <div class="message" id="message"></div>
</div>
</div>

<audio id="correctSound" src="https://www.soundjay.com/human/applause-8.mp3"></audio>
<audio id="wrongSound" src="https://www.soundjay.com/button/beep-10.mp3"></audio>

<script>
let questions = [
  "أرسل رسالة حب غريبة للشخص الثاني",
  "قل للشخص الثاني شيء جريء لم تقله من قبل",
  "ماذا ستفعل لو كنت مع الشخص الثاني الآن؟",
  "ما أكثر شيء يعجبك فيه؟",
  "شارك سر صغير للشخص الثاني",
  "هل يمكنك وصف شعور قلبك الآن؟",
  "قل كلمة رومانسية بطريقة مضحكة",
  "ما الشيء الذي تريد أن تجربه مع الشخص الآخر؟"
];

let currentPlayer = 1;
let timer;
let time = 30;
let score1=0, score2=0;

function startGame(){
  let p1=document.getElementById("player1Name").value.trim();
  let p2=document.getElementById("player2Name").value.trim();
  if(!p1 || !p2){ alert("ادخل اسم اللاعبين!"); return;}
  document.getElementById("player1Name").disabled=true;
  document.getElementById("player2Name").disabled=true;
  document.getElementById("startBtn").disabled=true;
  document.getElementById("intro").style.display="none";
  nextQuestion();
}

function nextQuestion(){
  if(score1>=10){ alert(document.getElementById("player1Name").value + " فاز! يمكنك الحكم على الشخص الثاني 😏"); restartGame(); return; }
  if(score2>=10){ alert(document.getElementById("player2Name").value + " فاز! يمكنك الحكم على الشخص الأول 😏"); restartGame(); return; }

  document.getElementById("questionBox").style.display="block";
  let qIndex=Math.floor(Math.random()*questions.length);
  document.getElementById("question").innerText=questions[qIndex];
  let answersDiv=document.getElementById("answers");
  answersDiv.innerHTML="";

  ["صحيح","خطأ"].forEach(opt=>{
    let btn=document.createElement("button");
    btn.innerText=opt;
    btn.onclick=()=> checkAnswer(opt==="صحيح");
    answersDiv.appendChild(btn);
  });

  time=30; updateTimer();
  clearInterval(timer);
  timer=setInterval(()=>{
    time--;
    updateTimer();
    if(time<=0){
      clearInterval(timer);
      switchPlayer();
    }
  },1000);
}

function updateTimer(){
  document.getElementById("timer").innerText=`⏱ الدور الحالي: ${currentPlayer==1?document.getElementById("player1Name").value:document.getElementById("player2Name").value} - ${time} ثانية`;
}

function checkAnswer(isCorrect){
  clearInterval(timer);
  if(isCorrect){
    if(currentPlayer===1){ score1++; document.getElementById("score1").innerText=score1;}
    else{ score2++; document.getElementById("score2").innerText=score2;}
    document.getElementById("message").innerText="أحسنت! ✅";
    document.getElementById("correctSound").play();
  } else{
    document.getElementById("message").innerText="خطأ ❌";
    document.getElementById("wrongSound").play();
  }
  setTimeout(switchPlayer,1500);
}

function switchPlayer(){
  currentPlayer=currentPlayer===1?2:1;
  document.getElementById("message").innerText="";
  nextQuestion();
}

function restartGame(){ location.reload(); }
</script>
</body>
</html>
