const express = require('express')
const app = express()
const PORT = process.env.PORT || 3000

app.use(express.json())

let messages = []
let users = [
  { id: "u1", name: "Rahul Sharma", location: "Mumbai", skillsOffered: ["Photoshop"], skillsWanted: ["Video Editing"], availability: "WK‑E & Evenings", public: true, banned: false },
  { id: "u2", name: "Priya Patel", location: "Delhi", skillsOffered: ["Graphic Design"], skillsWanted: ["SEO"], availability: "Evenings", public: true, banned: false },
  { id: "u3", name: "Arjun Verma", location: "Chennai", skillsOffered: ["Cooking"], skillsWanted: ["Guitar"], availability: "Weekends", public: true, banned: false },
  { id: "u4", name: "Ananya Iyer", location: "Bangalore", skillsOffered: ["Python"], skillsWanted: ["Spanish"], availability: "Weekdays", public: true, banned: false },
  { id: "u5", name: "Kunal Mehta", location: "Hyderabad", skillsOffered: ["Excel"], skillsWanted: ["Public Speaking"], availability: "Evenings", public: true, banned: false },
  { id: "u6", name: "Sneha Rao", location: "Kolkata", skillsOffered: ["SEO"], skillsWanted: ["Graphic Design"], availability: "Weekends", public: true, banned: false },
  { id: "u7", name: "Vikram Singh", location: "Pune", skillsOffered: ["Guitar"], skillsWanted: ["Cooking"], availability: "Evenings", public: true, banned: false }
]

let swapRequests = [
  { id:"r1", from:"Priya Patel", to:"Rahul Sharma", offer:"Graphic Design", want:"Photoshop", status:"pending" },
  { id:"r2", from:"Arjun Verma", to:"Ananya Iyer", offer:"Cooking", want:"Python", status:"accepted" },
  { id:"r3", from:"Kunal Mehta", to:"Rahul Sharma", offer:"Excel", want:"Video Editing", status:"rejected" },
  { id:"r4", from:"Ananya Iyer", to:"Priya Patel", offer:"Python", want:"SEO", status:"pending" },
  { id:"r5", from:"Sneha Rao", to:"Kunal Mehta", offer:"SEO", want:"Excel", status:"pending" },
  { id:"r6", from:"Vikram Singh", to:"Sneha Rao", offer:"Guitar", want:"SEO", status:"accepted" }
]

let feedbacks = []

// serve frontend
app.get('/', (req,res) => res.send(`
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8"/>
    <meta name="viewport" content="width=device-width,initial-scale=1.0"/>
    <title>Skill Swap</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #e0f7fa, #80deea);
            color: #333;
            margin: 0;
            padding: 20px;
        }
        h1 {
            color: #00796b;
            text-align: center;
            text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.1);
        }
        .card {
            background-color: #ffffff;
            border-radius: 12px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
            padding: 1.5rem;
            margin: 1rem 0;
            transition: transform 0.3s;
        }
        .card:hover {
            transform: scale(1.02);
        }
        input[type="text"] {
            padding: 0.5rem;
            border: 1px solid #ccc;
            border-radius: 4px;
            width: calc(100% - 120px);
            margin-right: 10px;
        }
        button {
            background-color: #00796b;
            color: white;
            border: none;
            border-radius: 4px;
            padding: 0.5rem 1rem;
            cursor: pointer;
            transition: background-color 0.3s;
            font-weight: bold;
        }
        button:hover {
            background-color: #004d40;
        }
        .admin-log {
            height: 100px;
            overflow: auto;
            border: 1px solid #ccc;
            background-color: #f9f9f9;
            padding: 10px;
            border-radius: 4px;
        }
        ul {
            list-style-type: none;
            padding: 0;
        }
        li {
            margin: 0.5rem 0;
            padding: 0.5rem;
            background-color: #e0f2f1;
            border-radius: 4px;
            position: relative;
        }
        li button {
            position: absolute;
            right: 10px;
            top: 50%;
            transform: translateY(-50%);
            background-color: #ff6f61;
        }
        li button:hover {
            background-color: #e65c50;
        }
        .highlight {
            background-color: #d1e7dd;
        }
        .error {
            color: #dc3545;
        }
        .success {
            color: #198754;
        }
        .emoji {
            font-size: 1.2em;
        }
    </style>
</head>
<body>
    <h1>Skill Swap Platform 🌟</h1>
    <div class="card">
        <h2>Browse & Search 🔍</h2>
        <input id="searchBox" placeholder="Search skills"/>
        <button onclick="loadUsers()">Search</button>
        <ul id="userList"></ul>
    </div>
    <div class="card">
        <h2>My Profile 👤</h2>
        <div id="profileInfo"></div>
    </div>
    <div class="card">
        <h2>Swap Requests 📩</h2>
        <ul id="swapList"></ul>
    </div>
    <div class="card">
        <h2>Admin Panel 🛠️</h2>
        <button onclick="banUser  ('u6')">Ban Sneha (u6)</button>
        <button onclick="rejectSpam()">Reject Spammy Skills</button>
        <button onclick="sendMessage()">Broadcast Message</button>
        <button onclick="downloadReport()">Download Report</button>
        <div class="admin-log" id="adminLog"></div>
    </div>

    <script>
        async function loadUsers(){
            const term = document.getElementById('searchBox').value.toLowerCase()
            let data = await fetch('/users').then(r=>r.json())
            data = data.filter(u => u.skillsOffered.join('|').toLowerCase().includes(term) || u.skillsWanted.join('|').toLowerCase().includes(term))
            let ul = document.getElementById('userList'); ul.innerHTML=''
            data.forEach(u=>{
                ul.innerHTML += \`<li>\${u.name} (Offered: \${u.skillsOffered}) - <button onclick="req('\${u.name}')">Send Swap</button></li>\`
            })
        }
        async function req(to){
            await fetch('/swap',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({from:'AdminTest',to,offer:'TestSkill',want:'TestSkill'})})
            loadSwaps()
        }

        async function loadSwaps(){
            let s = await fetch('/swaps').then(r=>r.json()), ul=document.getElementById('swapList'); ul.innerHTML=''
            s.forEach(x=>{
                ul.innerHTML+=\`<li class="\${x.status}">\${x.id}: \${x.from}->\${x.to} (\${x.offer} for \${x.want}) [\${x.status}] 
                <button onclick="update('\${x.id}','accepted')">Accept</button>
                <button onclick="update('\${x.id}','rejected')">Reject</button>
                <button onclick="remove('\${x.id}')">Delete</button></li>\`
            })
            log('Swap counts: ' + JSON.stringify(Object.fromEntries(Object.entries({pending:s.filter(x=>x.status==='pending').length, accepted:s.filter(x=>x.status==='accepted').length, rejected:s.filter(x=>x.status==='rejected').length}))))
        }

        async function update(id,status){
            await fetch('/swap/update',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({id,status})})
            loadSwaps()
        }
        async function remove(id){ await fetch('/swap/delete',{method:'POST', headers:{'Content-Type':'application/json'},body:JSON.stringify({id})}); loadSwaps() }
        async function banUser  (u){ await fetch('/admin/ban',{method:'POST', headers:{'Content-Type':'application/json'}, body:JSON.stringify({id:u})}); loadUsers(); log('Banned user '+u) }

        async function rejectSpam(){
            const resp = await fetch('/admin/reject-spam',{method:'POST'}).then(r=>r.json())
            log('Rejected spam from users: ' + resp.rejected.join(', '))
            loadUsers()
        }

        async function sendMessage(){
            const msg = prompt('Type message to broadcast:')
            if(!msg) return
            await fetch('/admin/broadcast',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({msg})})
            log('Broadcast sent: ' + msg)
        }

        async function downloadReport(){
            const blob = await fetch('/admin/report').then(r=>r.blob())
            const url = URL.createObjectURL(blob)
            const a = document.createElement('a'); a.href=url; a.download='report.txt'; a.click()
            log('Report downloaded')
        }

        function log(txt){ const el=document.getElementById('adminLog'); el.innerHTML += txt + '<br>' }

        window.onload = ()=>{ loadUsers(); loadSwaps(); document.getElementById('profileInfo').innerText='(Profile editing coming soon)' }
    </script>
</body>
</html>
`))

// APIs
app.get('/users', (req,res)=>{
    res.json(users.filter(u=>u.public && !u.banned))
})
app.post('/swap', (req,res)=>{
    swapRequests.push({ id:'r'+Date.now(), ...req.body, status:'pending' })
    res.json({success:true})
})
app.get('/swaps', (req,res)=> res.json(swapRequests))
app.post('/swap/update',(req,res)=>{
    const s = swapRequests.find(x=>x.id===req.body.id)
    if(s) s.status=req.body.status
    res.json({success:true})})
app.post('/swap/delete',(req,res)=>{
    swapRequests = swapRequests.filter(x=>x.id!==req.body.id)
    res.json({success:true})})
app.post('/admin/ban',(req,res)=>{
    const u = users.find(x=>x.id===req.body.id)
    if(u) u.banned=true
    res.json({success:true})})
app.post('/admin/reject-spam',(req,res)=>{
    // Remove users whose skills contain spammy phrases
    const spamPhrases=["hack","free money"]
    const bad = []
    users = users.filter(u=>{
        const all = [...u.skillsOffered, ...u.skillsWanted].join(' ').toLowerCase()
        if(spamPhrases.some(w=>all.includes(w))){
            bad.push(u.name)
            return false
        }
        return true
    })
    res.json({rejected:bad})
})
app.post('/admin/broadcast',(req,res)=>{
    messages.push({ msg:req.body.msg, date: new Date() })
    res.json({success:true})
})
app.get('/admin/report',(req,res)=>{
    const rpt = [
        '--- USERS ---',
        ...users.map(u=>u.name + ' | ' + u.skillsOffered.join(',') + ' | banned:' + u.banned),
        '--- SWAPS ---',
        ...swapRequests.map(s=>`${s.id}: ${s.from}->${s.to} (${s.status})`),
        '--- MESSAGES ---',
        ...messages.map(m=>m.date+': '+m.msg)
    ].join('\n')
    res.setHeader('Content-Disposition','attachment; filename=report.txt')
    res.send(rpt)
})

app.listen(PORT, ()=>console.log('App ready on port', PORT))
