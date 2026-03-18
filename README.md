<html lang="ar">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
<title>لعبة رومانسية بين الحبيبين</title>
<style>
body { margin:0; font-family:Arial,sans-serif; background:linear-gradient(to bottom,#ffc1cc,#ffe6f0,#ffffff); text-align:center; }
.container { max-width:700px; margin:0 auto; padding:15px; }
h1 { color:#ff4f79; font-size:2em; margin-bottom:10px; }
input { padding:8px; width:140px; border-radius:8px; border:none; text-align:center; font-weight:bold; }
button { padding:10px 25px; border-radius:10px; border:none; font-weight:bold; cursor:pointer; background-color:#ff99aa; color:white; margin:10px; }
.timer { font-size:22px; margin-top:10px; color:#ff3366; }
.question-box, .punishment-box { display:none; background:#fff0f5; color:black; margin:20px auto; padding:15px; border-radius:12px; }
.answers button { width:70%; margin:5px 0; }
.correct { background-color:#00cc44 !important; color:white; }
.wrong { background-color:#ff3333 !important; color:white; }
.message { font-size:18px; margin-top:10px; font-weight:bold; color:#ff3366; }
.home-btn { padding:8px 15px; border-radius:8px; border:none; background-color:#ffffff; font-weight:bold; cursor:pointer; position:fixed; top:10px; left:50%; transform:translateX(-50%); }
</style>
</head>
<body>
<div class="container">

<!-- الصفحة الأولى: إدخال الأسماء -->
<div id="pageStart">
<h1>💖 لعبة بين الحبيبين</h1>
<input type="text" id="player1Name" placeholder="اسم الشخص الأول"><br><br>
<input type="text" id="player2Name" placeholder="اسم الشخص الثاني"><br><br>
<button onclick="showInstructions()">ابدأ اللعبة 💕</button>
</div>

<!-- الصفحة الثانية: شرح اللعبة -->
<div id="pageInstructions" style="display:none;">
<h2>شرح اللعبة 📝</h2>
<p>مرحباً بكم! ❤️</p>
<ul style="text-align:right;">
<li>مدة كل دور 30 ثانية ⏱</li>
<li>عند الإجابة الصحيحة تحصل على نقطة ✅</li>
<li>يمكنك تخطي السؤال بالضغط على زر "تخطي السؤال بعقاب" واختيار عقاب ممتع للطرف الآخر 😏</li>
<li>اللاعب الذي يصل إلى 10 نقاط أولاً يفوز! 🏆</li>
</ul>
<button onclick="startGame()">ابدأ اللعبة الآن 🎮</button>
</div>

<!-- الصفحة الثالثة: اللعبة -->
<div id="pageGame" style="display:none;">
<div class="players">
  <div class="player">
    <span id="player1Display"></span><br>
    نقاط: <span id="score1">0</span>
  </div>
  <div class="player player2">
    <span id="player2Display"></span><br>
    نقاط: <span id="score2">0</span>
  </div>
</div>

<div class="timer" id="timer"></div>

<div class="question-box" id="questionBox">
  <h2 id="question"></h2>
  <div class="answers" id="answers"></div>
  <button onclick="skipWithPunishment()">تخطي السؤال بعقاب ⚡</button>
  <div class="message" id="message"></div>
</div>

<div class="punishment-box" id="punishmentBox">
  <h3>اختر العقاب 🎯</h3>
  <div id="punishmentOptions"></div>
</div>

</div>

<audio id="correctSound" src="https://www.soundjay.com/human/applause-8.mp3"></audio>
<audio id="wrongSound" src="https://www.soundjay.com/button/beep-10.mp3"></audio>

<script>
let questions = [
  "أرسل رسالة حب غريبة للشخص الثاني 💌",
  "قل للشخص الثاني شيء جريء لم تقله من قبل 😏",
  "ماذا ستفعل لو كنت مع الشخص الثاني الآن؟ 🥰",
  "ما أكثر شيء يعجبك فيه؟ 💖",
  "شارك سر صغير للشخص الثاني 🤫",
  "هل يمكنك وصف شعور قلبك الآن؟ ❤️",
  "قل كلمة رومانسية بطريقة مضحكة 😂💘",
  "ما الشيء الذي تريد أن تجربه مع الشخص الآخر؟ 🌹"
];
let punishments = ["أكل فلفل 🌶️","رقصة مضحكة 💃","قول جملة رومانسية جريئة 😏","اغلق عينيك 10 ثواني 😵"];
let currentPlayer = 1;
let timer, time = 30;
let score1=0, score2=0;
let lastQuestionIndex=-1;

function showInstructions(){
  let p1=document.getElementById("player1Name").value.trim();
  let p2=document.getElementById("player2Name").value.trim();
  if(!p1 || !p2){ alert("ادخل اسم اللاعبين!"); return;}
  document.getElementById("player1Display").innerText=p1;
  document.getElementById("player2Display").innerText=p2;
  document.getElementById("pageStart").style.display="none";
  document.getElementById("pageInstructions").style.display="block";
}

function startGame(){
  document.getElementById("pageInstructions").style.display="none";
  document.getElementById("pageGame").style.display="block";
  nextQuestion();
}

function nextQuestion(){
  if(score1>=10){ alert(`${document.getElementById("player1Display").innerText} فاز! 😏`); restartGame(); return; }
  if(score2>=10){ alert(`${document.getElementById("player2Display").innerText} فاز! 😏`); restartGame(); return; }

  document.getElementById("questionBox").style.display="block";
  document.getElementById("punishmentBox").style.display="none";

  let qIndex; do{ qIndex=Math.floor(Math.random()*questions.length); }while(qIndex===lastQuestionIndex);
  lastQuestionIndex=qIndex;
  document.getElementById("question").innerText=questions[qIndex];

  let answersDiv=document.getElementById("answers");
  answersDiv.innerHTML="";
  ["💖 أوافق","💔 لا أوافق"].forEach(opt=>{
    let btn=document.createElement("button");
    btn.innerText=opt;
    btn.onclick=()=> checkAnswer(opt==="💖 أوافق");
    answersDiv.appendChild(btn);
  });

  time=30; updateTimer();
  clearInterval(timer);
  timer=setInterval(()=>{
    time--;
    updateTimer();
    if(time<=0){
      clearInterval(timer);
      document.getElementById("message").innerText="انتهى الوقت ⏰";
      setTimeout(switchPlayer,1500);
    }
  },1000);
}

function updateTimer(){
  let name=currentPlayer===1?document.getElementById("player1Display").innerText:document.getElementById("player2Display").innerText;
  document.getElementById("timer").innerText=`⏱ الدور الحالي: ${name} - ${time} ثانية`;
}

function checkAnswer(isCorrect){
  clearInterval(timer);
  let audio=isCorrect?document.getElementById("correctSound"):document.getElementById("wrongSound");
  audio.pause(); audio.currentTime=0; audio.play();

  if(isCorrect){
    if(currentPlayer===1){ score1++; document.getElementById("score1").innerText=score1;}
    else{ score2++; document.getElementById("score2").innerText=score2;}
    document.getElementById("message").innerText="أحسنت! ✅";
  }else{ document.getElementById("message").innerText="خطأ ❌"; }

  setTimeout(switchPlayer,1500);
}

function switchPlayer(){
  currentPlayer=currentPlayer===1?2:1;
  document.getElementById("message").innerText="";
  nextQuestion();
}

function skipWithPunishment(){
  clearInterval(timer);
  document.getElementById("questionBox").style.display="none";
  document.getElementById("punishmentBox").style.display="block";
  let container=document.getElementById("punishmentOptions");
  container.innerHTML="";
  punishments.forEach(p=>{
    let btn=document.createElement("button");
    btn.innerText=p;
    btn.onclick=()=>{
      alert(`💥 العقاب: ${p}`);
      switchPlayer();
    };
    container.appendChild(btn);
  });
}

function restartGame(){ location.reload(); }
</script>
</body>
</html>
