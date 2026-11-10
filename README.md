from pathlib import Path

items = [
("أَنَا","ذَهَبْتُ","أَذْهَبُ","أَذْهَبُ","تَذْهَبُ","يَذْهَبُ","نَذْهَبُ","من رفتم ← من می‌روم"),
("أَنْتَ","كَتَبْتَ","تَكْتُبُ","أَكْتُبُ","تَكْتُبُ","يَكْتُبُ","نَكْتُبُ","تو (مذکر) نوشتی ← می‌نویسی"),
("أَنْتِ","قَرَأْتِ","تَقْرَئِينَ","أَقْرَأُ","تَقْرَئِينَ","يَقْرَأُ","نَقْرَأُ","تو (مونث) خواندی ← می‌خوانی"),
("هُوَ","شَرِبَ","يَشْرَبُ","أَشْرَبُ","تَشْرَبُ","يَشْرَبُ","نَشْرَبُ","او (مذکر) نوشید ← می‌نوشد"),
("هِيَ","لَعِبَتْ","تَلْعَبُ","أَلْعَبُ","تَلْعَبُ","يَلْعَبُ","نَلْعَبُ","او (مونث) بازی کرد ← بازی می‌کند"),
("نَحْنُ","جَلَسْنَا","نَجْلِسُ","أَجْلِسُ","تَجْلِسُ","يَجْلِسُ","نَجْلِسُ","ما نشستیم ← می‌نشینیم"),
("أَنْتُمْ","فَهِمْتُمْ","تَفْهَمُونَ","أَفْهَمُ","تَفْهَمُونَ","يَفْهَمُونَ","نَفْهَمُ","شما فهمیدید ← می‌فهمید"),
("هُمْ","رَجَعُوا","يَرْجِعُونَ","أَرْجِعُ","تَرْجِعُونَ","يَرْجِعُونَ","نَرْجِعُ","آن‌ها برگشتند ← برمی‌گردند"),
("هُنَّ","كَتَبْنَ","يَكْتُبْنَ","أَكْتُبُ","تَكْتُبِينَ","يَكْتُبْنَ","نَكْتُبُ","آن‌ها (مونث) نوشتند ← می‌نویسند"),
("أَنْتُمَا","ذَهَبْتُمَا","تَذْهَبَانِ","أَذْهَبُ","تَذْهَبَانِ","يَذْهَبَانِ","نَذْهَبُ","شما دو نفر رفتید ← می‌روید"),
("هُمَا (مذکر)","لَعِبَا","يَلْعَبَانِ","أَلْعَبُ","تَلْعَبَانِ","يَلْعَبَانِ","نَلْعَبُ","آن دو (مذکر) بازی کردند ← بازی می‌کنند"),
("هُمَا (مونث)","جَلَسَتَا","تَجْلِسَانِ","أَجْلِسُ","تَجْلِسَانِ","يَجْلِسَانِ","نَجْلِسُ","آن دو (مونث) نشستند ← می‌نشینند"),
("أَنَا","شَرِبْتُ","أَشْرَبُ","أَشْرَبُ","تَشْرَبُ","يَشْرَبُ","نَشْرَبُ","من نوشیدم ← می‌نوشم"),
("أَنْتَ","فَتَحْتَ","تَفْتَحُ","أَفْتَحُ","تَفْتَحُ","يَفْتَحُ","نَفْتَحُ","تو (مذکر) باز کردی ← باز می‌کنی"),
("هُوَ","سَمِعَ","يَسْمَعُ","أَسْمَعُ","تَسْمَعُ","يَسْمَعُ","نَسْمَعُ","او (مذکر) شنید ← می‌شنود"),
("هِيَ","زَرَعَتْ","تَزْرَعُ","أَزْرَعُ","تَزْرَعُ","يَزْرَعُ","نَزْرَعُ","او (مونث) کاشت ← می‌کارد"),
("نَحْنُ","تَعَلَّمْنَا","نَتَعَلَّمُ","أَتَعَلَّمُ","تَتَعَلَّمُ","يَتَعَلَّمُ","نَتَعَلَّمُ","ما یاد گرفتیم ← یاد می‌گیریم"),
("أَنْتِ","سَمِعْتِ","تَسْمَعِينَ","أَسْمَعُ","تَسْمَعِينَ","يَسْمَعُ","نَسْمَعُ","تو (مونث) شنیدی ← می‌شنوی"),
("هُمْ","خَرَجُوا","يَخْرُجُونَ","أَخْرُجُ","تَخْرُجُونَ","يَخْرُجُونَ","نَخْرُجُ","آن‌ها خارج شدند ← خارج می‌شوند"),
("أَنْتُنَّ","كَتَبْتُنَّ","تَكْتُبْنَ","أَكْتُبُ","تَكْتُبِينَ","تَكْتُبْنَ","نَكْتُبُ","شما (مونث) نوشتید ← می‌نویسید"),
]
# Correct distractor arrays: option order is أنا، أنت/أنتِ، هو/هي/هم, نحن, with exact correct index.
import json
data=[]
for pron,past,pres,a,b,c,d,meaning in items:
    # Put correct answer in a fixed varied position while retaining plausible forms.
    opts=[a,b,c,d]
    ci=opts.index(pres)
    data.append(dict(pron=pron,past=past,present=pres,opts=opts,correct=ci,meaning=meaning))
jsdata=json.dumps(data, ensure_ascii=False)

html=f'''<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8"><meta name="viewport" content="width=device-width,initial-scale=1">
<title>ماجراجویی در سرزمین فعل‌ها</title>
<style>
*{{box-sizing:border-box}}body{{margin:0;min-height:100vh;font-family:Tahoma,Arial,sans-serif;color:white;background:#090d27 url("Moonlit%20Princess%20Over%20the%20Enchanted%20Gorge.png") center/cover fixed no-repeat}}
body:before{{content:"";position:fixed;inset:0;background:linear-gradient(135deg,#05081e66,#160d3799);z-index:-1}}
header,#scene,#game{{max-width:920px;margin:18px auto;padding:20px;background:rgba(13,17,52,.88);border:1px solid #ffffff44;border-radius:22px;box-shadow:0 12px 35px #0007}}
header{{text-align:center}}h1{{color:#ffdc86;margin:8px;font-size:clamp(24px,5vw,36px)}}.stats{{display:flex;justify-content:center;gap:12px;flex-wrap:wrap}}.info{{padding:9px 16px;border-radius:12px;background:#ffffff18}}
#scene{{text-align:center}}#princess{{font-size:38px}}#bridge{{height:8px;max-width:500px;margin:12px auto;background:linear-gradient(90deg,#9c6b39,#f5cf7c,#9c6b39);border-radius:20px}}#bridgeMessage{{color:#ffe6a8}}
.top{{display:flex;justify-content:space-between;gap:10px;flex-wrap:wrap;color:#dedaff}}#progress{{height:10px;background:#ffffff25;border-radius:20px;margin:14px 0;overflow:hidden}}#bar{{height:100%;width:0;background:linear-gradient(90deg,#8b77ff,#ffda7d);transition:.3s}}
.instruction{{text-align:center;color:#dedaff;margin:15px 0}}.verbbox{{display:grid;grid-template-columns:1fr 1fr;gap:12px;margin:15px 0}}.verb{{text-align:center;padding:18px 10px;border-radius:15px;background:#ffffff16;border:1px solid #ffffff35}}.verb small{{display:block;color:#e0d9ff;margin-bottom:9px}}.verb strong{{font-size:clamp(23px,5vw,32px);color:#ffdc86}}#question{{text-align:center;font-size:19px;margin:18px 0}}#meaning{{text-align:center;color:#ddd8ff;margin-bottom:16px}}
#answers{{display:grid;grid-template-columns:repeat(2,1fr);gap:11px}}.answer{{font: bold 20px Tahoma;padding:15px;border-radius:13px;border:1px solid #ffffff44;background:#ffffff17;color:white;cursor:pointer}}.answer:disabled{{cursor:default;opacity:.95}}.correct{{background:#176b50!important;border-color:#70e7b8!important}}.wrong{{background:#81384b!important;border-color:#ff9eae!important}}
#feedback{{min-height:32px;text-align:center;font-weight:bold;margin:16px 0}}.controls{{display:flex;justify-content:center;gap:10px;flex-wrap:wrap}}button.action{{font: bold 15px Tahoma;padding:12px 18px;border:0;border-radius:12px;background:linear-gradient(135deg,#ffdc86,#e9a94e);color:#342044;cursor:pointer}}button:disabled{{opacity:.5}}#music{{background:#7770b8;color:white}}#restart{{background:#77c8bd}}footer{{text-align:center;padding:0 12px 24px;color:#eee5ff;font-size:13px}}
@media(max-width:560px){{header,#scene,#game{{margin:12px;padding:16px}}#answers{{grid-template-columns:1fr}}.answer{{font-size:19px}}}}
</style></head>
<body>
<header><div>🏰 ✨ 🌙</div><h1>ماجراجویی در سرزمین فعل‌ها</h1><p>فعل ماضی را ببین و مضارع درستِ همان ضمیر را پیدا کن!</p><div class="stats"><div class="info">⭐ امتیاز: <b id="score">۰</b></div><div class="info">📜 سؤال: <b id="counter">۱ از ۲۰</b></div></div></header>
<section id="scene"><div id="princess">👸</div><div id="bridge"></div><div id="bridgeMessage">با پاسخ‌های درست، شاهزاده را به قصر برسان!</div></section>
<main id="game"><div class="top"><span>ماضی ← مضارع</span><span id="stage">مرحله ۱</span></div><div id="progress"><div id="bar"></div></div>
<p class="instruction">ضمیر و فعل ماضی را بخوان؛ سپس مضارع مناسب را انتخاب کن.</p>
<div class="verbbox"><div class="verb"><small>ضمیر</small><strong id="pronoun">أَنَا</strong></div><div class="verb"><small>فعل ماضی</small><strong id="past">ذَهَبْتُ</strong></div></div>
<div id="question">مضارعِ این فعل برای این ضمیر کدام است؟</div><div id="meaning"></div><div id="answers"></div><div id="feedback" aria-live="polite"></div>
<div class="controls"><button class="action" id="next" disabled>سؤال بعدی ←</button><button class="action" id="music">🎵 روشن کردن موسیقی</button><button class="action" id="restart" hidden>شروع دوباره</button></div></main>
<footer>هر پاسخ درست، یک قدم به قصر نزدیک‌ترت می‌کند! 🌟</footer>
<script>
const questions={jsdata};
let index=0,score=0,answered=false,musicOn=false,ctx=null,timer=null,note=0;
const el=id=>document.getElementById(id),fa=n=>String(n).replace(/\\\\d/g,d=>"۰۱۲۳۴۵۶۷۸۹"[d]);
function show(){{
 answered=false;let q=questions[index];el("counter").textContent=fa(index+1)+" از "+fa(questions.length);el("stage").textContent="مرحله "+fa(index+1);el("pronoun").textContent=q.pron;el("past").textContent=q.past;el("meaning").textContent=q.meaning;el("feedback").textContent="";el("next").disabled=true;el("bar").style.width=(index/questions.length*100)+"%";el("answers").innerHTML="";
 q.opts.forEach((v,i)=>{{let b=document.createElement("button");b.className="answer";b.textContent=v;b.onclick=()=>choose(i,b);el("answers").appendChild(b)}});
}}
function choose(i,button){{
 if(answered)return;answered=true;let q=questions[index];document.querySelectorAll(".answer").forEach((b,j)=>{{b.disabled=true;if(j===q.correct)b.classList.add("correct")}});
 if(i===q.correct){{score+=10;el("score").textContent=fa(score);el("feedback").textContent="آفرین! مضارع را درست پیدا کردی. ✨";el("feedback").style.color="#83f2bd";el("bridgeMessage").textContent="یک قدم دیگر به قصر نزدیک شدی!";}}
 else{{button.classList.add("wrong");el("feedback").textContent="پاسخ درست: "+q.present;el("feedback").style.color="#ffb1c0";el("bridgeMessage").textContent="اشکالی ندارد؛ شکل درست را یاد گرفتی."}}
 el("next").disabled=false;el("bar").style.width=((index+1)/questions.length*100)+"%";
}}
function next(){{if(!answered)return;index++;if(index===questions.length){{el("pronoun").textContent="🏆";el("past").textContent="أَحْسَنْتَ";el("question").textContent="به پایان ماجراجویی رسیدی!";el("meaning").textContent="امتیاز نهایی: "+fa(score)+" از "+fa(questions.length*10);el("answers").innerHTML="";el("feedback").textContent="آفرین که ماضی و مضارع را تمرین کردی!";el("next").hidden=true;el("restart").hidden=false;el("bridgeMessage").textContent="دروازه قصر باز شد! 🏰";el("counter").textContent="پایان";return}}show()}}
el("next").onclick=next;el("restart").onclick=()=>{{index=0;score=0;el("score").textContent="۰";el("next").hidden=false;el("restart").hidden=true;el("princess").textContent="👸";el("bridgeMessage").textContent="با پاسخ‌های درست، شاهزاده را به قصر برسان!";show()}};
function tone(f){{if(!ctx)return;let o=ctx.createOscillator(),g=ctx.createGain();o.type="sine";o.frequency.value=f;g.gain.setValueAtTime(.0001,ctx.currentTime);g.gain.exponentialRampToValueAtTime(.06,ctx.currentTime+.03);g.gain.exponentialRampToValueAtTime(.0001,ctx.currentTime+.25);o.connect(g);g.connect(ctx.destination);o.start();o.stop(ctx.currentTime+.26)}}
el("music").onclick=async()=>{{if(!musicOn){{let AC=window.AudioContext||window.webkitAudioContext;if(!AC)return;ctx=new AC();if(ctx.state==="suspended")await ctx.resume();let notes=[392,440,523,440,349,392,466,392];timer=setInterval(()=>tone(notes[note++%notes.length]),380);musicOn=true;el("music").textContent="🔇 خاموش کردن موسیقی"}}else{{clearInterval(timer);await ctx.suspend();musicOn=false;el("music").textContent="🎵 روشن کردن موسیقی"}}}};
show();
</script></body></html>'''

out = Path("/mnt/data/arabic-game-past-present.html")
out.write_text(html, encoding="utf-8")
print(f"فایل کامل آماده شد: {len(html.splitlines())} خط")
