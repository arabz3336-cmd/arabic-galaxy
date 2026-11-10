from pathlib import Path

html = r'''<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ماجراجویی در سرزمین فعل‌ها</title>
<style>
*{box-sizing:border-box}
:root{--gold:#ffd875;--purple:#17183f;--glass:rgba(13,17,52,.82)}
body{
  margin:0;min-height:100vh;font-family:Tahoma,Arial,sans-serif;color:#fff;
  background:#090d27 url("Moonlit%20Princess%20Over%20the%20Enchanted%20Gorge.png") center/cover fixed no-repeat;
}
body::before{content:"";position:fixed;inset:0;background:linear-gradient(135deg,rgba(5,8,30,.38),rgba(22,13,55,.58));z-index:-1}
header{max-width:980px;margin:22px auto 14px;padding:18px 22px;text-align:center;background:var(--glass);border:1px solid #ffffff35;border-radius:22px;box-shadow:0 12px 35px #0006}
h1{margin:8px 0;color:var(--gold);font-size:clamp(24px,5vw,38px);text-shadow:0 3px 12px #000}
.subtitle{color:#e6e4ff;margin:8px 0 14px}
.stats{display:flex;justify-content:center;gap:12px;flex-wrap:wrap}
.info{background:#ffffff16;border:1px solid #ffffff30;border-radius:12px;padding:9px 16px}
#scene{max-width:980px;margin:14px auto;padding:22px;background:var(--glass);border:1px solid #ffffff35;border-radius:22px;text-align:center}
.castle{font-size:42px;margin-bottom:5px}
#princess{font-size:40px;display:inline-block;transition:transform .4s}
#bridge{height:9px;max-width:520px;margin:13px auto;background:linear-gradient(90deg,#9c6b39,#f5cf7c,#9c6b39);border-radius:20px;box-shadow:0 0 18px #f6cf7b88}
#bridgeMessage{display:inline-block;padding:9px 15px;background:#ffffff18;border-radius:12px;color:#fff2c8}
#game{max-width:980px;margin:14px auto 35px;padding:24px;background:rgba(17,19,51,.88);border:1px solid #ffffff35;border-radius:22px;box-shadow:0 15px 40px #0008}
.question-top{display:flex;justify-content:space-between;gap:10px;flex-wrap:wrap;color:#ddd9ff}
#progress{height:10px;background:#ffffff22;border-radius:20px;overflow:hidden;margin:14px 0 22px}
#progressBar{height:100%;width:0;background:linear-gradient(90deg,#8b77ff,#ffda7d);transition:width .3s}
#question{font-size:clamp(22px,4vw,30px);text-align:center;margin:18px 0;color:var(--gold)}
#translation{text-align:center;color:#d9d5ff;margin-bottom:18px}
.answers{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:12px}
.answer{font-family:inherit;font-size:18px;padding:15px 12px;border-radius:14px;border:1px solid #ffffff35;background:#ffffff13;color:white;cursor:pointer;transition:.2s}
.answer:hover:not(:disabled){background:#ffffff29;transform:translateY(-2px)}
.answer:disabled{cursor:default;opacity:.9}
.answer.correct{background:#176b50;border-color:#70e7b8}
.answer.wrong{background:#81384b;border-color:#ff9eae}
#feedback{min-height:34px;text-align:center;font-weight:bold;margin:16px 0 8px}
.controls{display:flex;justify-content:center;gap:10px;flex-wrap:wrap}
button.action{font-family:inherit;border:0;border-radius:13px;padding:12px 20px;background:linear-gradient(135deg,#ffdc86,#e9a94e);color:#342044;font-weight:bold;font-size:16px;cursor:pointer}
button.action:disabled{opacity:.45;cursor:not-allowed}
#musicButton{background:#7770b8;color:white}
#restart{background:#77c8bd;color:#102c35}
footer{text-align:center;color:#e2ddff;padding:0 12px 24px;font-size:13px}
@media(max-width:560px){header,#scene,#game{margin:12px;padding:17px}.answers{grid-template-columns:1fr}.answer{font-size:17px}}
</style>
</head>
<body>
<header>
  <div class="castle">🏰 ✨ 🌙</div>
  <h1>ماجراجویی در سرزمین فعل‌ها</h1>
  <p class="subtitle">به شاهزاده کمک کن از پل جادویی عبور کند و به قصر برسد!</p>
  <div class="stats">
    <div class="info">⭐ امتیاز: <strong id="score">۰</strong></div>
    <div class="info">📜 سؤال: <strong id="counter">۱ از ۲۰</strong></div>
  </div>
</header>

<section id="scene">
  <div id="princess">👸</div>
  <div id="bridge"></div>
  <div id="bridgeMessage">برای باز شدن پل، پاسخ درست را انتخاب کن!</div>
</section>

<main id="game">
  <div class="question-top"><span>تمرین فعل‌ها و ضمیرها</span><span id="level">مرحله ۱</span></div>
  <div id="progress"><div id="progressBar"></div></div>
  <div id="question">...</div>
  <div id="translation"></div>
  <div class="answers" id="answers"></div>
  <div id="feedback" aria-live="polite"></div>
  <div class="controls">
    <button class="action" id="nextButton" disabled>سؤال بعدی ←</button>
    <button class="action" id="musicButton">🎵 روشن کردن موسیقی</button>
    <button class="action" id="restart" hidden>شروع دوباره</button>
  </div>
</main>
<footer>با هر پاسخ درست، یک قدم به قصر نزدیک‌تر می‌شوی! 🌟</footer>

<script>
const questions = [
 {q:"أَنَا ... إِلَى الْمَدْرَسَةِ",t:"من به مدرسه می‌روم.",a:["أَذْهَبُ","تَذْهَبُ","يَذْهَبُ","نَذْهَبُ"],c:0},
 {q:"هُوَ ... الدَّرْسَ",t:"او (مذکر) درس را می‌نویسد.",a:["أَكْتُبُ","تَكْتُبُ","يَكْتُبُ","نَكْتُبُ"],c:2},
 {q:"هِيَ ... الْقُرْآنَ",t:"او (مونث) قرآن می‌خواند.",a:["أَقْرَأُ","تَقْرَأُ","يَقْرَأُ","نَقْرَأُ"],c:1},
 {q:"نَحْنُ ... فِي الْحَدِيقَةِ",t:"ما در باغ بازی می‌کنیم.",a:["أَلْعَبُ","تَلْعَبُ","يَلْعَبُ","نَلْعَبُ"],c:3},
 {q:"أَنْتَ ... الْمَاءَ",t:"تو (مذکر) آب می‌نوشی.",a:["أَشْرَبُ","تَشْرَبُ","يَشْرَبُ","نَشْرَبُ"],c:1},
 {q:"أَنْتِ ... الرِّسَالَةَ",t:"تو (مونث) نامه را می‌خوانی.",a:["تَقْرَئِينَ","يَقْرَأُ","أَقْرَأُ","نَقْرَأُ"],c:0},
 {q:"هُمْ ... إِلَى الْبَيْتِ",t:"آن‌ها به خانه می‌روند.",a:["أَرْجِعُ","تَرْجِعُ","يَرْجِعُونَ","نَرْجِعُ"],c:2},
 {q:"أَنْتُمَا ... الْكُرَةَ",t:"شما دو نفر توپ را می‌بینید.",a:["تَرَيَانِ","يَرَى","أَرَى","نَرَى"],c:0},
 {q:"هُنَّ ... الطَّعَامَ",t:"آن‌ها (مونث) غذا می‌پزند.",a:["يَطْبُخْنَ","تَطْبُخِينَ","أَطْبُخُ","نَطْبُخُ"],c:0},
 {q:"أَنَا ... كِتَابًا",t:"من کتابی می‌خرم.",a:["تَشْتَرِي","يَشْتَرِي","أَشْتَرِي","نَشْتَرِي"],c:2},
 {q:"أَنْتُمْ ... الدَّرْسَ",t:"شما درس را می‌فهمید.",a:["أَفْهَمُ","تَفْهَمُونَ","يَفْهَمُ","نَفْهَمُ"],c:1},
 {q:"هِيَ ... الْبَابَ",t:"او (مونث) در را باز می‌کند.",a:["أَفْتَحُ","تَفْتَحُ","يَفْتَحُ","نَفْتَحُ"],c:1},
 {q:"نَحْنُ ... إِلَى الْمُعَلِّمِ",t:"ما به معلم گوش می‌دهیم.",a:["أَسْمَعُ","تَسْمَعُ","يَسْمَعُ","نَسْمَعُ"],c:3},
 {q:"هُوَ ... فِي الْمَسَاءِ",t:"او (مذکر) در عصر می‌خوابد.",a:["أَنَامُ","تَنَامُ","يَنَامُ","نَنَامُ"],c:2},
 {q:"أَنْتِ ... إِلَى الْمُوسِيقَى",t:"تو (مونث) به موسیقی گوش می‌دهی.",a:["تَسْمَعِينَ","يَسْمَعُ","أَسْمَعُ","نَسْمَعُ"],c:0},
 {q:"أَنَا ... الرِّيَاضَةَ",t:"من ورزش را دوست دارم.",a:["يُحِبُّ","تُحِبُّ","أُحِبُّ","نُحِبُّ"],c:2},
 {q:"هُمْ ... فِي الصَّفِّ",t:"آن‌ها در کلاس می‌نشینند.",a:["أَجْلِسُ","تَجْلِسُ","يَجْلِسُونَ","نَجْلِسُ"],c:2},
 {q:"أَنْتَ ... الْوَاجِبَ",t:"تو (مذکر) تکلیف را انجام می‌دهی.",a:["أَعْمَلُ","تَعْمَلُ","يَعْمَلُ","نَعْمَلُ"],c:1},
 {q:"هِيَ ... الزَّهْرَةَ",t:"او (مونث) گل را می‌کارد.",a:["أَزْرَعُ","تَزْرَعُ","يَزْرَعُ","نَزْرَعُ"],c:1},
 {q:"نَحْنُ ... اللُّغَةَ الْعَرَبِيَّةَ",t:"ما زبان عربی را یاد می‌گیریم.",a:["أَتَعَلَّمُ","تَتَعَلَّمُ","يَتَعَلَّمُ","نَتَعَلَّمُ"],c:3}
];

let current=0,score=0,answered=false,musicOn=false,audioContext=null,melodyTimer=null,noteIndex=0;
const $=id=>document.getElementById(id);
const toPersian=n=>String(n).replace(/\d/g,d=>"۰۱۲۳۴۵۶۷۸۹"[d]);

function renderQuestion(){
 answered=false;
 const item=questions[current];
 $("counter").textContent=`${toPersian(current+1)} از ${toPersian(questions.length)}`;
 $("level").textContent=`مرحله ${toPersian(current+1)}`;
 $("question").textContent=item.q;
 $("translation").textContent=item.t;
 $("feedback").textContent="";
 $("nextButton").disabled=true;
 $("progressBar").style.width=`${current/questions.length*100}%`;
 $("answers").innerHTML="";
 item.a.forEach((answer,index)=>{
   const button=document.createElement("button");
   button.className="answer";button.textContent=answer;
   button.addEventListener("click",()=>chooseAnswer(index,button));
   $("answers").appendChild(button);
 });
}
function chooseAnswer(index,button){
 if(answered)return;
 answered=true;
 const item=questions[current];
 document.querySelectorAll(".answer").forEach((b,i)=>{
   b.disabled=true;
   if(i===item.c)b.classList.add("correct");
 });
 if(index===item.c){
   score+=10;$("score").textContent=toPersian(score);
   $("feedback").textContent="آفرین! پاسخ درست است. ✨";
   $("feedback").style.color="#83f2bd";
   $("princess").style.transform="translateX(-18px)";
   $("bridgeMessage").textContent="یک قدم دیگر به قصر نزدیک شدی! 🌟";
 }else{
   button.classList.add("wrong");
   $("feedback").textContent=`پاسخ درست: ${item.a[item.c]}`;
   $("feedback").style.color="#ffb1c0";
   $("bridgeMessage").textContent="اشکالی ندارد؛ پاسخ درست را یاد گرفتی! 💜";
 }
 $("nextButton").disabled=false;
 $("progressBar").style.width=`${(current+1)/questions.length*100}%`;
}
function nextQuestion(){
 if(!answered)return;
 current++;
 if(current>=questions.length){finishGame();return;}
 renderQuestion();
}
function finishGame(){
 $("question").textContent="🎉 به قصر جادویی رسیدی!";
 $("translation").textContent=`بازی تمام شد! امتیاز تو ${toPersian(score)} از ${toPersian(questions.length*10)} است.`;
 $("answers").innerHTML="";
 $("feedback").textContent=score>=150?"عالی بود! تو استاد سرزمین فعل‌هایی! 👑":"آفرین که تا پایان تلاش کردی! برای بهتر شدن دوباره بازی کن.";
 $("nextButton").hidden=true;$("restart").hidden=false;
 $("counter").textContent="پایان";
 $("bridgeMessage").textContent="دروازه قصر باز شد! 🏰";
 $("princess").textContent="👸🏰";
}
$("nextButton").addEventListener("click",nextQuestion);
$("restart").addEventListener("click",()=>{
 current=0;score=0;$("score").textContent="۰";
 $("nextButton").hidden=false;$("restart").hidden=true;
 $("princess").textContent="👸";$("princess").style.transform="";
 $("bridgeMessage").textContent="برای باز شدن پل، پاسخ درست را انتخاب کن!";
 renderQuestion();
});
function playTone(freq,duration=0.22){
 if(!audioContext)return;
 const oscillator=audioContext.createOscillator(),gain=audioContext.createGain();
 oscillator.type="sine";oscillator.frequency.value=freq;
 gain.gain.setValueAtTime(.0001,audioContext.currentTime);
 gain.gain.exponentialRampToValueAtTime(.07,audioContext.currentTime+.03);
 gain.gain.exponentialRampToValueAtTime(.0001,audioContext.currentTime+duration);
 oscillator.connect(gain);gain.connect(audioContext.destination);
 oscillator.start();oscillator.stop(audioContext.currentTime+duration);
}
function startMusic(){
 const notes=[392,440,523.25,440,349.23,392,466.16,392];
 noteIndex=0;
 melodyTimer=setInterval(()=>{
   playTone(notes[noteIndex%notes.length],.28);noteIndex++;
 },380);
}
$("musicButton").addEventListener("click",async()=>{
 if(!musicOn){
   const AudioCtx=window.AudioContext||window.webkitAudioContext;
   if(!AudioCtx){$("musicButton").textContent="موسیقی پشتیبانی نمی‌شود";return;}
   audioContext=new AudioCtx();
   if(audioContext.state==="suspended")await audioContext.resume();
   startMusic();musicOn=true;$("musicButton").textContent="🔇 خاموش کردن موسیقی";
 }else{
   clearInterval(melodyTimer);musicOn=false;
   if(audioContext)audioContext.suspend();
   $("musicButton").textContent="🎵 روشن کردن موسیقی";
 }
});
renderQuestion();
</script>
</body>
</html>
'''

path = Path("/mnt/data/arabic-game-complete.html")
path.write_text(html, encoding="utf-8")
print(f"فایل کامل ساخته شد: {path.name} — {len(html.splitlines())} خط")
