<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AutoGov AI 🇮🇳</title>

<style>
body {
  font-family: Arial;
  margin: 0;
  transition: 0.3s;
  background: linear-gradient(135deg,#4facfe,#00f2fe);
}

.dark {
  background: #121212;
  color: white;
}

.container {
  max-width: 420px;
  margin: 20px auto;
  padding: 20px;
  background: white;
  border-radius: 15px;
  box-shadow: 0 10px 25px rgba(0,0,0,0.2);
}

.dark .container {
  background: #1e1e1e;
}

input {
  width: 95%;
  padding: 10px;
  margin: 8px 0;
  border-radius: 6px;
}

button {
  padding: 10px;
  margin: 5px;
  border: none;
  border-radius: 6px;
  background: #007BFF;
  color: white;
  cursor: pointer;
}

.output {
  margin-top: 15px;
  font-size: 14px;
}

.topbar {
  display: flex;
  justify-content: space-between;
}
</style>
</head>

<body>

<div class="container">

<h2>AutoGov AI 🇮🇳</h2>

<!-- LOGIN -->
<div id="loginBox">
  <input id="username" placeholder="Username">
  <input id="password" type="password" placeholder="Password">
  <button onclick="login()">Login</button>
</div>

<!-- APP -->
<div id="app" style="display:none;">

<div class="topbar">
  <button onclick="toggleDark()">🌙</button>
  <button onclick="setLang('en')">EN</button>
  <button onclick="setLang('hi')">HI</button>
  <button onclick="logout()">Logout</button>
</div>

<input id="name" placeholder="Enter Name">
<input id="age" type="number" placeholder="Enter Age">
<input id="state" placeholder="Enter State">

<button onclick="generate()">Check Eligibility</button>
<button onclick="voiceInput()">🎤 Voice</button>
<button onclick="askAI()">🤖 AI</button>
<button onclick="saveProfile()">💾 Save</button>
<button onclick="loadProfile()">📂 Load</button>

<div class="output" id="output"></div>

<div>
<h4>🔐 Privacy</h4>
<p>No data stored on server. Everything stays on device.</p>
</div>

</div>

</div>

<script>

// LANGUAGE SYSTEM
let lang = localStorage.getItem("lang") || "en";

const text = {
  en: {
    eligible: "You are eligible for:",
    noDoc: "No major documents required",
    next: "Next Steps",
    checklist: "Checklist",
    platforms: "Platforms",
    consent: "Do you want to proceed?"
  },
  hi: {
    eligible: "आप इसके लिए पात्र हैं:",
    noDoc: "अभी कोई जरूरी दस्तावेज नहीं",
    next: "अगले कदम",
    checklist: "चेकलिस्ट",
    platforms: "प्लेटफॉर्म",
    consent: "क्या आप आगे बढ़ना चाहते हैं?"
  }
};

function setLang(l){
  lang = l;
  localStorage.setItem("lang", l);
  generate();
}

// LOGIN
function login(){
  let u = username.value;
  let p = password.value;

  if(u && p){
    localStorage.setItem("user", u);
    loginBox.style.display="none";
    app.style.display="block";
  } else alert("Enter details");
}

function logout(){
  localStorage.removeItem("user");
  location.reload();
}

// AUTO LOGIN
if(localStorage.getItem("user")){
  loginBox.style.display="none";
  app.style.display="block";
}

// DARK MODE
if(localStorage.getItem("dark") === "true"){
  document.body.classList.add("dark");
}

function toggleDark(){
  document.body.classList.toggle("dark");
  localStorage.setItem("dark", document.body.classList.contains("dark"));
}

// MAIN LOGIC
function generate(){

  let name = document.getElementById("name").value || "User";
  let age = parseInt(document.getElementById("age").value);
  let state = document.getElementById("state").value || "";

  let t = text[lang];

  let output = `<h3>Hello ${name}</h3><p><b>State:</b> ${state}</p>`;
  let checklist = [];

  if(!age || age<=0){
    output += `<p style="color:red;">Enter valid age</p>`;
  }

  else if(age>=18){

    output += `<p>${t.eligible}</p>
    <ul>
      <li>PAN Card</li>
      <li>Voter ID</li>
      <li>Bank Account</li>
      <li>Passport</li>
    </ul>`;

    output += `<p>🧠 AI Insight: Financial identity + civic participation</p>`;

    checklist = ["PAN Card","Voter ID","Bank Account","Passport"];

    if(state.toLowerCase()==="chhattisgarh"){
      output += `<p>🎯 State schemes available</p>`;
    }

  }

  else if(age>=16){

    output += `<p>Learning Driving License available</p>`;
    checklist=["Learning License"];
    output += `<p>Eligible in ${18-age} years</p>`;
  }

  else{

    output += `<p>${t.noDoc}</p>`;
    output += `<p>Eligible in ${18-age} years</p>`;
  }

  // CHECKLIST
  if(checklist.length){
    output += `<h4>${t.checklist}</h4>`;
    checklist.forEach(item=>{
      let checked = localStorage.getItem(item)=="true"?"checked":"";
      output += `<input type="checkbox" onchange="saveTask('${item}',this.checked)" ${checked}> ${item}<br>`;
    });
  }

  // NEXT STEPS
  output += `<h4>${t.next}</h4>
  <ul>
    <li>Secure documents digitally</li>
    <li>Open bank account</li>
    <li>Stay updated</li>
  </ul>`;

  // PLATFORMS
  output += `<h4>${t.platforms}</h4>
  <ul>
    <li>DigiLocker</li>
    <li>Aadhaar Services</li>
  </ul>`;

  // CONSENT
  output += `<h4>${t.consent}</h4>
  <button onclick="alert('Coming soon')">Yes</button>
  <button onclick="alert('Okay later')">No</button>`;

  document.getElementById("output").innerHTML = output;
}

// SAVE TASK
function saveTask(task,val){
  localStorage.setItem(task,val);
}

// PROFILE
function saveProfile(){
  let data = {
    name:name.value,
    age:age.value,
    state:state.value
  };
  localStorage.setItem("profile",JSON.stringify(data));
  alert("Saved");
}

function loadProfile(){
  let d = JSON.parse(localStorage.getItem("profile"));
  if(d){
    name.value=d.name;
    age.value=d.age;
    state.value=d.state;
  }
}

// VOICE INPUT
function voiceInput(){
  let rec = new(window.SpeechRecognition||window.webkitSpeechRecognition)();
  rec.lang="en-IN";

  rec.onresult=e=>{
    let txt=e.results[0][0].transcript.toLowerCase();

    if(txt.includes("age")){
      age.value = txt.match(/\d+/);
    } else if(txt.includes("name")){
      name.value = txt.replace("name","");
    } else {
      name.value = txt;
    }
  };

  rec.start();
}

// AI
function askAI(){
  let ageVal = age.value || "18";
  let url = "https://www.perplexity.ai/search?q="+encodeURIComponent("documents for "+ageVal+" year old Indian");
  window.open(url);
}

</script>

</body>
</html>
