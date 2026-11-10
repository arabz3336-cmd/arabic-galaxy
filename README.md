
<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ماجراجویی در سرزمین فعل‌ها</title>

<style>
*{box-sizing:border-box}
body{
  margin:0;
  min-height:100vh;
  font-family:Tahoma,Arial,sans-serif;
  color:white;
  background:#090d27 url("castle-bg.png") center/cover fixed no-repeat;
}
body::before{
  content:"";
  position:fixed;
  inset:0;
  background:linear-gradient(180deg,rgba(3,5,25,.25),rgba(3,5,25,.55) 48%,rgba(3,5,25,.9));
  z-index:-1;
}
header{
  display:flex;
  justify-content:space-between;
  align-items:center;
  flex-wrap:wrap;
  gap:12px;
  padding:18px;
}
h1{
  font-size:clamp(20px,4vw,32px);
  color:#ffe3a1;
  text-shadow:0 0 18px #b57bff;
  text-align:center;
}
.info{
  background:rgba(10,12,44,.85);
  border:1px solid #d9a64d;
  border-radius:16px;
  padding:12px 18px;
  box-shadow:0 0 15px #8b54bb;
}
#scene{
  position:relative;
  height:44vh;
  min-height:260px;
}
#marker{
  position:absolute;
  left:35%;
  top:56%;
  font-size:35px;
  filter:drop-shadow(0 0 12px gold);
  transition:all 1s ease;
}
#bridgeMessage{
  position:absolute;
  bottom:12px;
  left:50%;
  transform:translateX(-50%);
  background:#100e35dd;
  border:1px solid #d8ad61;
  border-radius:20px;
  padding:10px 18px;
  white-space:nowrap;
}
#game{
  width:min(900px,94%);
  margin:0 auto 25px;
  padding:22px;
  border:2px solid #d6a64c;
  border-radius:24px;
  background:linear-gradient(145deg,#111333f5,#211044f5);
  box-shadow:0 0 35px #9b5bce77;
  text-align:center;
}
.small{
  color:#e9c986;
  margin:8px;
}
#question{
  font-size:clamp(19px,3vw,27px);
  line-height:2;
  margin:15px 0 22px;
}
.answers{
  display:grid;
  grid-template-columns:repeat(2,minmax(0,1fr));
  gap:12px;
}
.answer{
  background:linear-gradient(130deg,#25205e,#17133d);
  color:white;
  border:1px solid #d7a34c;
  border-radius:15px;
  padding:16px 8px;
  font-family:inherit;
  font-size:23px;
  cursor:pointer;
  transition:.2s;
}
.answer:hover{
  transform:scale(1.03);
  box-shadow:0 0 18px #d8a64d;
}
.answer:disabled{cursor:default;opacity:.85}
.correct{background:#176b4b!important;border-color:#7fffc0}
.wrong{background:#762c43!important;border-color:#ff9aaf}
#feedback{
  min-height:34px;
  margin:16px;
  font-size:18px;
  color:#ffe5a5;
}
button.control{
  background:#34205d;
  border:1px solid #e4bd70;
  color:white;
  border-radius:12px;
  padding:10px 18px;
  font-family:inherit;
  cursor:pointer;
  margin:5px;
}
#next{display:none;background:#7250a5}
#endScreen{display:none;line-height:2}
@media(max-width:500px){
  .answers{grid-template-columns:1fr}
  #scene{height:35vh}
  header{justify-content:center}
}
</style>
</head>

<body>

<header>
  <div class="info">⭐ امتیاز: <span id="score">۰</span></div>
  <h1>🏰 ماجراجویی در سرزمین فعل‌ها</h1>
  <div class="info">سؤال: <span id="number">۱</span> از ۲۰</div>
</header>

<section id="scene">
  <div id="marker">✨</div>
  <div id="bridgeMessage">راه رسیدن به قصر از پل جادویی می‌گذرد...</div>
</section>

<main id="game">
  <div class="small" id="verbInfo">زمان: ماضی</div>
  <div class="small" id="progress">تخته‌های باقی‌مانده: ۱۲</div>

  <div id="question">در حال آماده‌سازی سؤال...</div>

  <div class="answers" id="answers"></div>

  <div id="feedback"></div>

  <button class="control" id="next" onclick="nextQuestion()">
    سؤال بعدی ←
  </button>

  <button class="control" onclick="toggleMusic()" id="musicButton">
    ♫ روشن / خاموش کردن موسیقی
  </button>

  <div id="endScreen">
    <h2>🏰 ماجراجویی به پایان رسید!</h2>
    <p id="finalScore"></p>
    <button class="control" onclick="restart()">شروع دوباره</button>
  </div>
</main>

<script>
const verbs = [
  {name:"ذَهَبَ", past:"ذَهَب", present:"ذْهَب", meaning:"رفتن"},
  {name:"كَتَبَ", past:"كَتَب", present:"كْتُب", meaning:"نوشتن"},
  {name:"جَلَسَ", past:"جَلَس", present:"جْلِس", meaning:"نشستن"},
  {name:"فَتَحَ", past:"فَتَح", present:"فْتَح", meaning:"باز کردن"},
  {name:"نَصَرَ", past:"نَصَر", present:"نْصُر", meaning:"یاری کردن"},
  {name:"شَرِبَ", past:"شَرِب", present:"شْرَب", meaning:"نوشیدن"},
  {name:"سَمِعَ", past:"سَمِع", present:"سْمَع", meaning:"شنیدن"},
  {name:"دَخَلَ", past:"دَخَل", present:"دْخُل", meaning:"وارد شدن"}
];

const pronouns = [
  {name:"هُوَ", past:"", presentPrefix:"يَ", presentEnd:"ُ"},
  {name:"هِيَ", past:"تْ", presentPrefix:"تَ", presentEnd:"ُ"},
  {name:"هُمَا (مذکر)", past:"ا", presentPrefix:"يَ", presentEnd:"انِ"},
  {name:"هُمَا (مؤنث)", past:"تَا", presentPrefix:"تَ", presentEnd:"انِ"},
  {name:"هُمْ", past:"وا", presentPrefix:"يَ", presentEnd:"ونَ"},
  {name:"هُنَّ", past:"ْنَ", presentPrefix:"يَ", presentEnd:"ْنَ"},
  {name:"أَنْتَ", past:"تَ", presentPrefix:"تَ", presentEnd:"ُ"},
  {name:"أَنْتِ", past:"تِ", presentPrefix:"تَ", presentEnd:"ينَ"},
  {name:"أَنْتُمَا", past:"تُمَا", presentPrefix:"تَ", presentEnd:"انِ"},
  {name:"أَنْتُمْ", past:"تُمْ", presentPrefix:"تَ", presentEnd:"ونَ"},
  {name:"أَنْتُنَّ", past:"تُنَّ", presentPrefix:"تَ", presentEnd:"ْنَ"},
  {name:"أَنَا", past:"تُ", presentPrefix:"أَ", presentEnd:"ُ"},
  {name:"نَحْنُ", past:"نَا", presentPrefix:"نَ", presentEnd:"ُ"}
];

// صرف فعل برای هر ضمیر
function conjugate(verb, pronoun, tense){
  if(tense === "ماضی"){
    return verb.past + pronoun.past;
  }
  return pronoun.presentPrefix + verb.present + pronoun.presentEnd;
}

let score = 0;
let questionCount = 0;
let planks = 12;
let currentQuestion = null;
let answered = false;
let musicOn = false;
let audioContext = null;

const totalQuestions = 20;

function randomItem(arr){
  return arr[Math.floor(Math.random()*arr.length)];
}

function newQuestion(){
  if(questionCount >= totalQuestions){
    finishGame();
    return;
  }

  answered = false;
  questionCount++;

  const verb = randomItem(verbs);
  const pronoun = randomItem(pronouns);
  const tense = Math.random() < .5 ? "ماضی" : "مضارع";

  const correct = conjugate(verb,pronoun,tense);

  const allAnswers = pronouns
    .map(p => conjugate(verb,p,tense))
    .filter(a => a !== correct);

  const wrongAnswers = [...new Set(allAnswers)];
  const choices = [correct];

  while(choices.length < 4 && wrongAnswers.length){
    const index = Math.floor(Math.random()*wrongAnswers.length);
    choices.push(wrongAnswers.splice(index,1)[0]);
  }

  while(choices.length < 4){
    choices.push(randomItem(allAnswers));
  }

  choices.sort(()=>Math.random()-.5);

  currentQuestion = {verb,pronoun,tense,correct};

  document.getElementById("number").textContent = questionCount;
  document.getElementById("score").textContent = score;
  document.getElementById("verbInfo").textContent =
    "زمان: " + tense + " | معنی فعل: " + verb.meaning;

  document.getElementById("question").innerHTML =
    "صرف فعل <strong>«" + verb.name +
    "»</strong> برای ضمیر <strong>«" +
    pronoun.name + "»</strong> چیست؟";

  document.getElementById("feedback").textContent = "";
  document.getElementById("next").style.display = "none";

  const box = document.getElementById("answers");
  box.innerHTML = "";

  choices.forEach(choice=>{
    const button = document.createElement("button");
    button.className = "answer";
    button.textContent = choice;
    button.onclick = ()=>checkAnswer(button,choice);
    box.appendChild(button);
  });

  document.getElementById("progress").textContent =
    "تخته‌های باقی‌مانده: " + planks;
}

function checkAnswer(button,choice){
  if(answered) return;
  answered = true;

  const buttons = document.querySelectorAll(".answer");
  buttons.forEach(b=>b.disabled=true);

  const feedback = document.getElementById("feedback");

  if(choice === currentQuestion.correct){
    score += 10;
    button.classList.add("correct");
    feedback.textContent = "✨ آفرین! درست جواب دادی. شاهزاده به قصر نزدیک‌تر شد!";
    moveMarker();
  }else{
    button.classList.add("wrong");
    feedback.textContent =
      "این بار درست نبود! جواب درست: " + currentQuestion.correct;
    planks = Math.max(0,planks-1);
    document.getElementById("bridgeMessage").textContent =
      "⚠️ یک تخته از پل فرو ریخت!";
    document.getElementById("progress").textContent =
      "تخته‌های باقی‌مانده: " + planks;
    shakeScene();
  }

  document.getElementById("score").textContent = score;
  document.getElementById("next").style.display = "inline-block";

  if(questionCount >= totalQuestions){
    document.getElementById("next").textContent = "دیدن نتیجه";
  }
}

function moveMarker(){
  const marker = document.getElementById("marker");
  const percent = Math.min(35 + score/200*45,80);
  marker.style.left = percent + "%";
  marker.style.top = Math.max(25,56-score/10) + "%";
  document.getElementById("bridgeMessage").textContent =
    "✨ شاهزاده یک قدم به قصر نزدیک‌تر شد!";
}

function shakeScene(){
  const scene = document.getElementById("scene");
  scene.animate([
    {transform:"translateX(0)"},
    {transform:"translateX(-8px)"},
    {transform:"translateX(8px)"},
    {transform:"translateX(0)"}
  ],{duration:350});
}

function nextQuestion(){
  newQuestion();
}

function finishGame(){
  document.getElementById("game").querySelector(".answers").style.display="none";
  document.getElementById("question").style.display="none";
  document.getElementById("verbInfo").style.display="none";
  document.getElementById("feedback").style.display="none";
  document.getElementById("next").style.display="none";
  document.getElementById("endScreen").style.display="block";

  document.getElementById("finalScore").textContent =
    "امتیاز تو: " + score + " از " + (totalQuestions*10);

  document.getElementById("bridgeMessage").textContent =
    "🏰 ماجراجویی تو به پایان رسید!";
}

function restart(){
  score=0;
  questionCount=0;
  planks=12;

  document.getElementById("game").querySelector(".answers").style.display="grid";
  document.getElementById("question").style.display="block";
  document.getElementById("verbInfo").style.display="block";
  document.getElementById("feedback").style.display="block";
  document.getElementById("endScreen").style.display="none";

  document.getElementById("marker").style.left="35%";
  document.getElementById("marker").style.top="56%";
  document.getElementById("bridgeMessage").textContent=
    "راه رسیدن به قصر از پل جادویی می‌گذرد...";

  newQuestion();
}

// موسیقی ساده و جادویی با صدای ساخته‌شده در مرورگر
function toggleMusic(){
  if(!audioContext){
    audioContext = new (window.AudioContext || window.webkitAudioContext)();
  }

  musicOn = !musicOn;
  document.getElementById("musicButton").textContent =
    musicOn ? "♫ خاموش کردن موسیقی" : "♫ روشن کردن موسیقی";

  if(musicOn){
    playMagicNotes();
  }
}

function playMagicNotes(){
  if(!musicOn) return;

  const notes=[261.63,329.63,392,523.25,392,329.63];
  let i=0;

  function playNext(){
    if(!musicOn) return;

    const osc=audioContext.createOscillator();
    const gain=audioContext.createGain();

    osc.type="sine";
    osc.frequency.value=notes[i%notes.length];

    gain.gain.setValueAtTime(.0001,audioContext.currentTime);
    gain.gain.exponentialRampToValueAtTime(.06,audioContext.currentTime+.1);
    gain.gain.exponentialRampToValueAtTime(.0001,audioContext.currentTime+.8);

    osc.connect(gain);
    gain.connect(audioContext.destination);

    osc.start();
    osc.stop(audioContext.currentTime+.85);

    i++;
    setTimeout(playNext,650);
  }

  playNext();
}

newQuestion();
</script>
</body>
</html>
