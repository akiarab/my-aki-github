# my-aki-github
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=2" />
  <title>Create new site for Akram</title>
  <style>
    :root{--bg:#0f1722;--card:#0b1220;--accent:#60a5fa;--muted:#9ca4af;color-scheme🌲}
    *{box-sizing:border-box}
    body{font-family:system-ui,-apple-system,Segoe UI,Roboto,"Helvetica Neue",Arial;background:linear-gradient(180deg,#071124 0%,#071a2a 100%);color:#e6eef6;margin:0;padding:22px;display:flex;align-items:center;justify-content:center;min-height:100vh}
    .card{background:linear-gradient(180deg,rgba(255,255,255,0.02),rgba(255,255,255,0.01));border-radius:12px;box-shadow:0 7px 30px rgba(2,6,23,0.6);width:100%;max-width:760px;padding:20px}
    header{display:flex;align-items:center;justify-content:space-between;margin-bottom:22px}
    h1{font-size:20px;margin:0}
    .date{color:var(--muted);font-size:23px}
    .quote{margin:12px 0;padding:12px;border-radius:20px;background:rgba(255,253,255,0.02);font-size:15px}
    textarea{width:100%;min-height:90px;padding:12px;border-radius:8px;border:2px solid rgba(255,255,255,0.04);background:transparent;color:inherit;font-size:14px;resize:vertical}
    .controls{display:flex;gap:8px;margin-top:8px}
    button{padding:8px 12px;border-radius:4px;border:0;background:var(--accent);color:#042034;cursor:pointer;font-weight:600}
    button.ghost{background:transparent;border:1px solid rgba(255,255,255,0.08);color:var(--muted)}
    ul.entries{list-style:none;padding:0;margin-top:16px;display:flex;flex-direction:column;gap:10px;max-height:360px;overflow:auto}
    li.entry{padding:20px;border-radius:20px;background:rgba(255,255,255,0.025);display:flex;flex-direction:column}
    .time{font-size:12px;color:var(--muted);margin-bottom:7px}
    .empty{color:var(--muted);text-align:center;padding:10px}
    footer{margin-top:15px;color:var(--muted);font-size:13px;display:flex;justify-content:space-between;align-items:center}
    a.link{color:var(--accent);text-decoration:none}
    @media (max-width:520px){.card{padding:14px}h1{font-size:18px}}
  </style>
</head>
<body>
  <main class="card">
    <header>
      <div>
        <h1>Daily Motivation & Log</h1>
        <div class="date" id="today">Loading date...</div>
      </div>
      <div>
        <button id="clearAll" class="ghost" title="Clear">Clear</button>
      </div>
    </header>

    <section class="quote" id="quote">"Be consistent — little steps every day."</section>

    <label for="entry">Write a short note (today's highlight, intention or a quote):</label>
    <textarea id="entry" placeholder="Type something small you did or plan to do today..."></textarea>

    <div class="controls">
      <button id="add">Add Entry</button>
      <button id="saveReadme" class="ghost">Copy README Snippet</button>
      <button id="download" class="ghost">Download as HTML</button>
    </div>

    <ul class="entries" id="entries">
      <!-- entries appear here -->
    </ul>

    <div class="empty" id="empty">No entries yet — add one to keep your GitHub active!</div>

    <footer>
      <div>Pro-tip: edit this file in GitHub once a day to get a contribution square.</div>
      <div><a class="link" href="#" id="version">v1.0</a></div>
    </footer>
  </main>

  <script>
    // Simple single-file daily activity page
    const todayEl = document.getElementById('today')
    const qEl = document.getElementById('quote')
    const entryEl = document.getElementById('entry')
    const addBtn = document.getElementById('add')
    const entriesEl = document.getElementById('entries')
    const emptyEl = document.getElementById('empty')
    const clearBtn = document.getElementById('clearAll')
    const saveReadmeBtn = document.getElementById('saveReadme')
    const downloadBtn = document.getElementById('download')

    const quotes = [
      "Believe in yourself and all that you are.",
      "Every day is a new beginning.",
      "Dream big, work hard, stay humble.",
      "Do something today that your future self will thank you for.",
      "Small progress is still progress."
    ]

    function formatDate(d){
      const opts = {year:'numeric',month:'short',day:'numeric'}
      return d.toLocaleDateString(undefined,opts)
    }

    function nowTime(){
      return new Date().toLocaleTimeString(undefined,{hour:'2-digit',minute:'2-digit'})
    }

    // initialize
    const today = new Date()
    todayEl.textContent = formatDate(today)
    qEl.textContent = quotes[Math.floor(Math.random()*quotes.length)]

    // storage key per date so you can keep daily logs separate
    const storageKey = 'daily-activity-' + today.toISOString().slice(0,10)

    function load(){
      const raw = localStorage.getItem(storageKey)
      const items = raw ? JSON.parse(raw) : []
      entriesEl.innerHTML = ''
      if(items.length === 0){
        emptyEl.style.display = 'block'
      } else {
        emptyEl.style.display = 'none'
        items.forEach(it => entriesEl.appendChild(renderEntry(it)))
      }
    }

    function renderEntry(item){
      const li = document.createElement('li')
      li.className = 'entry'
      const time = document.createElement('div')
      time.className = 'time'
      time.textContent = item.time
      const text = document.createElement('div')
      text.textContent = item.text
      li.appendChild(time)
      li.appendChild(text)
      return li
    }

    function saveItems(items){
      localStorage.setItem(storageKey, JSON.stringify(items))
    }

    addBtn.addEventListener('click', ()=>{
      const text = entryEl.value.trim()
      if(!text) return
      const raw = localStorage.getItem(storageKey)
      const items = raw ? JSON.parse(raw) : []
      const item = {time: nowTime(), text}
      items.unshift(item)
      saveItems(items)
      entryEl.value = ''
      load()
    })

    clearBtn.addEventListener('click', ()=>{
      if(confirm('Clear all entries for today?')){
        localStorage.removeItem(storageKey)
        load()
      }
    })

    // Copy a README-friendly markdown snippet to clipboard
    saveReadmeBtn.addEventListener('click', async ()=>{
      const raw = localStorage.getItem(storageKey)
      const items = raw ? JSON.parse(raw) : []
      let md = `### ${formatDate(today)}\n`
      if(items.length===0){ md += '- No entry yet.' } else {
        items.forEach(it=> md += `- ${it.time}: ${it.text}\n`)
      }
      try{
        await navigator.clipboard.writeText(md)
        alert('README snippet copied to clipboard. Paste into README.md and commit on GitHub.')
      }catch(e){
        alert('Copy failed — you can manually copy the snippet.')
        console.log(md)
      }
    })

    // Download current HTML (useful if you edit locally)
    downloadBtn.addEventListener('click', ()=>{
      const blob = new Blob([document.documentElement.outerHTML],{type:'text/html'})
      const url = URL.createObjectURL(blob)
      const a = document.createElement('a')
      a.href = url; a.download = 'daily-activity.html'
      document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url)
    })

    load()
  </script>
</body>
</html>
