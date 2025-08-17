<!DOCTYPE html><html lang="fa" dir="rtl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Al.VIPZEXNET - چت هوش مصنوعی (با تنظیمات شخصی‌سازی)</title>
  <style>
    :root{
      --bg:#0d1117; --panel:#161b22; --muted:#30363d; --brand:#58a6ff; --user:#238636; --bot:#21262d; --text:#ffffff;
    }
    *{box-sizing:border-box}
    body{margin:0;font-family:sans-serif;background:var(--bg);color:var(--text);display:flex;flex-direction:column;height:100vh}
    /* Header */
    header{background:var(--panel);padding:10px 12px;display:flex;align-items:center;justify-content:space-between;border-bottom:1px solid var(--muted);position:relative}
    header h1{margin:0;font-size:18px;color:var(--brand)}
    #menuBtn{background:none;border:none;color:var(--brand);font-size:20px;cursor:pointer}
    #menu{position:absolute;top:48px;right:10px;background:var(--panel);border:1px solid var(--muted);border-radius:10px;display:none;flex-direction:column;min-width:220px;z-index:50;max-height:60vh;overflow:auto}
    #menu button{width:100%;text-align:right;background:none;border:none;color:var(--text);padding:10px;border-radius:8px;cursor:pointer}
    #menu button:hover{background:#21262d}/* Chat */
#chatWindow{flex:1;overflow:auto;padding:16px;display:flex;flex-direction:column;gap:12px;scroll-behavior:smooth;scrollbar-width:thin;scrollbar-color:var(--brand) var(--bg)}
#chatWindow::-webkit-scrollbar{width:6px}
#chatWindow::-webkit-scrollbar-thumb{background:var(--brand);border-radius:3px}

.message{display:flex;align-items:flex-end;gap:10px}
.avatar{width:36px;height:36px;border-radius:50%;object-fit:cover;border:1px solid var(--muted)}
.bubble{max-width:75%;padding:10px 12px;border-radius:14px;line-height:1.6;white-space:pre-wrap;word-break:break-word;position:relative}
.user{justify-content:flex-end}
.bot{justify-content:flex-start}
.user .bubble{background:var(--user);border-bottom-right-radius:6px}
.bot .bubble{background:var(--bot);border-bottom-left-radius:6px}
.time{font-size:11px;opacity:.7;margin-top:4px;display:block}

/* Input */
#inputBar{display:flex;gap:10px;padding:10px;border-top:1px solid var(--muted);background:var(--panel)}
#userInput{flex:1;padding:12px;border:none;border-radius:10px;background:#0b1320;color:#fff}
#sendBtn{padding:12px 18px;border:none;border-radius:10px;background:var(--brand);color:#fff;cursor:pointer}
#sendBtn:hover{filter:brightness(1.1)}

/* Typing */
.typing::after{content:"...";animation:dots 1s steps(3,end) infinite}
@keyframes dots{0%{content:""}33%{content:"."}66%{content:".."}100%{content:"..."}}

/* Settings Modal */
.modal{position:fixed;inset:0;display:none;align-items:center;justify-content:center;background:rgba(0,0,0,.45);z-index:100}
.card{background:var(--panel);border:1px solid var(--muted);border-radius:12px;padding:16px;min-width:320px;max-width:92vw}
.row{display:flex;gap:8px}
.field{display:flex;flex-direction:column;margin-bottom:10px}
.field label{font-size:12px;opacity:.8;margin-bottom:4px}
.field input,.field select{padding:8px;border-radius:8px;border:1px solid var(--muted);background:#0b1320;color:#fff}
.actions{display:flex;gap:8px;justify-content:flex-end;margin-top:8px}
.btn{border:none;border-radius:8px;padding:8px 12px;cursor:pointer}
.btn-primary{background:var(--brand);color:#fff}
.btn-muted{background:#3a3f45;color:#fff}

@media (max-width:600px){.bubble{max-width:86%}}

  </style>
</head>
<body>
  <header>
    <h1>🤖 Al.VIPZEXNET</h1>
    <button id="menuBtn">⋮</button>
    <div id="menu">
      <button id="newChatBtn">🆕 چت جدید</button>
      <button id="settingsBtn">⚙️ تنظیمات</button>
      <button id="downloadTxtBtn">📥 دانلود TXT</button>
      <button id="downloadJsonBtn">📤 دانلود JSON</button>
      <button id="deleteLastBtn">🗑 حذف آخرین پیام</button>
      <button id="searchBtn">🔍 جستجو</button>
    </div>
  </header>  <div id="chatWindow"></div>  <div id="inputBar">
    <input id="userInput" type="text" placeholder="پیامت را بنویس…" />
    <button id="sendBtn">ارسال</button>
  </div>  <!-- Settings Modal -->  <div id="settingsModal" class="modal">
    <div class="card">
      <h3 style="margin-top:0">⚙️ تنظیمات شخصی‌سازی</h3>
      <div class="field"><label>نام</label><input id="setName" placeholder="مثلاً: علی"/></div>
      <div class="field"><label>ایمیل</label><input id="setEmail" type="email" placeholder="you@example.com"/></div>
      <div class="field"><label>آواتار (URL تصویر)</label><input id="setAvatar" placeholder="https://..."/></div>
      <div class="row" style="gap:12px">
        <div class="field" style="flex:1"><label>رنگ برند</label><input id="setBrand" type="color"/></div>
        <div class="field" style="flex:1"><label>رنگ پیام کاربر</label><input id="setUserColor" type="color"/></div>
      </div>
      <div class="field">
        <label>تم</label>
        <select id="setTheme">
          <option value="dark">تاریک</option>
          <option value="light">روشن</option>
        </select>
      </div>
      <div class="actions">
        <button id="resetSettings" class="btn btn-muted">بازنشانی</button>
        <button id="closeSettings" class="btn btn-muted">بستن</button>
        <button id="saveSettings" class="btn btn-primary">ذخیره</button>
      </div>
    </div>
  </div>  <script>
    // --- State & Storage ---
    const $ = s=>document.querySelector(s);
    const chatWindow = $('#chatWindow');

    let profile = JSON.parse(localStorage.getItem('profile')||'null') || {
      name:'کاربر', email:'', avatar:'https://i.pravatar.cc/100?img=3'
    };
    let theme = JSON.parse(localStorage.getItem('theme')||'null') || {
      mode:'dark', brand:'#58a6ff', user:'#238636'
    };
    let history = JSON.parse(localStorage.getItem('chatHistory')||'[]');

    const botAvatar = 'https://i.ibb.co/ZYW3VTp/bot.png';

    const saveAll = ()=>{
      localStorage.setItem('profile', JSON.stringify(profile));
      localStorage.setItem('theme', JSON.stringify(theme));
      localStorage.setItem('chatHistory', JSON.stringify(history));
    }

    // --- Theme Apply ---
    function applyTheme(){
      const root = document.documentElement;
      root.style.setProperty('--brand', theme.brand);
      root.style.setProperty('--user', theme.user);
      if(theme.mode==='light'){
        root.style.setProperty('--bg', '#f4f6f8');
        root.style.setProperty('--panel', '#ffffff');
        root.style.setProperty('--muted', '#d0d7de');
        root.style.setProperty('--bot', '#eef2f6');
        root.style.setProperty('--text', '#0d1117');
      }else{
        root.style.setProperty('--bg', '#0d1117');
        root.style.setProperty('--panel', '#161b22');
        root.style.setProperty('--muted', '#30363d');
        root.style.setProperty('--bot', '#21262d');
        root.style.setProperty('--text', '#ffffff');
      }
    }

    // --- Render ---
    function renderAll(){
      chatWindow.innerHTML='';
      history.forEach((m,i)=>chatWindow.appendChild(renderMessage(m,i)));
      chatWindow.scrollTop = chatWindow.scrollHeight;
    }

    function renderMessage(msg, idx){
      const wrap = document.createElement('div');
      wrap.className = 'message ' + msg.sender;
      wrap.dataset.index = idx;

      const img = document.createElement('img');
      img.className = 'avatar';
      img.src = msg.sender==='user' ? (profile.avatar||'') : botAvatar;
      img.alt = msg.sender==='user' ? 'avatar' : 'bot';
      img.onerror = ()=>{ img.src = 'data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="36" height="36"><rect width="100%" height="100%" rx="18" fill="%2330363d"/><text x="50%" y="58%" dominant-baseline="middle" text-anchor="middle" font-size="18" fill="white">👤</text></svg>'; };

      const bubble = document.createElement('div');
      bubble.className = 'bubble';
      bubble.textContent = msg.text;

      const time = document.createElement('span');
      time.className = 'time';
      time.textContent = msg.time || new Date().toLocaleTimeString([], {hour:'2-digit',minute:'2-digit'});
      bubble.appendChild(time);

      if(msg.sender==='user'){ wrap.append(bubble, img); } else { wrap.append(img, bubble); }
      return wrap;
    }

    function addMessage(text, sender){
      const time = new Date().toLocaleTimeString([], {hour:'2-digit',minute:'2-digit'});
      const msg = {text, sender, time};
      history.push(msg); saveAll();
      chatWindow.appendChild(renderMessage(msg, history.length-1));
      chatWindow.scrollTop = chatWindow.scrollHeight;
    }

    function showTyping(){
      const wrap = document.createElement('div');
      wrap.className = 'message bot';
      const img = document.createElement('img'); img.className='avatar'; img.src=botAvatar;
      const bubble = document.createElement('div'); bubble.className='bubble typing'; bubble.textContent='در حال تایپ';
      wrap.append(img, bubble); chatWindow.appendChild(wrap); chatWindow.scrollTop = chatWindow.scrollHeight; return wrap;
    }

    async function botReplyEcho(text){
      const n = showTyping();
      await new Promise(r=>setTimeout(r,900));
      n.remove();
      addMessage('پاسخ به: '+text, 'bot');
    }

    // --- Events ---
    $('#sendBtn').onclick = ()=>{
      const val = $('#userInput').value.trim(); if(!val) return;
      addMessage(val,'user'); $('#userInput').value=''; botReplyEcho(val);
    };
    $('#userInput').addEventListener('keydown',e=>{ if(e.key==='Enter' && !e.shiftKey){ e.preventDefault(); $('#sendBtn').click(); }});

    // Menu toggle & outside click
    $('#menuBtn').onclick = ()=> $('#menu').style.display = $('#menu').style.display==='flex'?'none':'flex';
    document.addEventListener('click',e=>{ const m=$('#menu'); if(m.style.display==='flex' && !m.contains(e.target) && e.target!==$('#menuBtn')) m.style.display='none'; });

    // Menu actions
    $('#newChatBtn').onclick = ()=>{ if(confirm('چت جدید؟')){ history=[]; saveAll(); renderAll(); $('#menu').style.display='none'; }};
    $('#downloadTxtBtn').onclick = ()=>{ const name = profile.name||'کاربر'; const txt = history.map(m=>`${m.sender==='user'?name:'🤖'}: ${m.text}`).join('\n'); const blob=new Blob([txt],{type:'text/plain'}); const a=document.createElement('a'); a.href=URL.createObjectURL(blob); a.download='chat.txt'; a.click(); };
    $('#downloadJsonBtn').onclick = ()=>{ const blob=new Blob([JSON.stringify(history,null,2)],{type:'application/json'}); const a=document.createElement('a'); a.href=URL.createObjectURL(blob); a.download='chat.json'; a.click(); };
    $('#deleteLastBtn').onclick = ()=>{ if(history.length){ history.pop(); saveAll(); renderAll(); } };
    $('#searchBtn').onclick = ()=>{ const q=prompt('عبارت جستجو؟'); if(!q) return; const res = history.map((m,i)=>({i,m})).filter(x=>x.m.text.includes(q)); alert(res.length? res.map(x=>`#${x.i+1} • ${x.m.sender==='user'?'👤':'🤖'}: ${x.m.text}`).join('\n\n') : 'چیزی پیدا نشد ❌'); };

    // Settings modal
    const settings = {
      open(){ $('#settingsModal').style.display='flex'; this.load(); },
      close(){ $('#settingsModal').style.display='none'; },
      load(){ $('#setName').value=profile.name||''; $('#setEmail').value=profile.email||''; $('#setAvatar').value=profile.avatar||''; $('#setBrand').value=toColor(theme.brand); $('#setUserColor').value=toColor(theme.user); $('#setTheme').value=theme.mode||'dark'; },
      save(){ profile.name=$('#setName').value.trim()||'کاربر'; profile.email=$('#setEmail').value.trim(); profile.avatar=$('#setAvatar').value.trim()||profile.avatar; theme.brand=$('#setBrand').value; theme.user=$('#setUserColor').value; theme.mode=$('#setTheme').value; saveAll(); applyTheme(); renderAll(); this.close(); },
      reset(){ profile={name:'کاربر', email:'', avatar:'https://i.pravatar.cc/100?img=3'}; theme={mode:'dark', brand:'#58a6ff', user:'#238636'}; saveAll(); applyTheme(); renderAll(); alert('به حالت پیش‌فرض برگشت'); }
    }
    function toColor(v){ try{ return /^#/.test(v)?v:'#'+v; }catch{ return '#58a6ff'; } }

    $('#settingsBtn').onclick = ()=>{ settings.open(); };
    $('#closeSettings').onclick = ()=> settings.close();
    $('#saveSettings').onclick = ()=> settings.save();
    $('#resetSettings').onclick = ()=> settings.reset();

    // Init
    applyTheme();
    renderAll();
  </script></body>
</html>
