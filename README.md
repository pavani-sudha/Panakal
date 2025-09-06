<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Culture & Historical Tourism</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    body { font-family: Arial, sans-serif; margin: 0; background: #f7fafc; }
    header { background: #2b6cb0; color: white; padding: 1rem; text-align: center; }
    nav { display: flex; justify-content: center; gap: 1rem; background: #e2e8f0; padding: 0.5rem; }
    nav button { padding: 0.5rem 1rem; border: none; border-radius: 6px; cursor: pointer; }
    section { display: none; padding: 1rem; }
    .active { display: block; }
    .card { background: white; padding: 1rem; border-radius: 8px; margin-bottom: 1rem; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
    input, select { width: 100%; padding: 0.5rem; margin: 0.5rem 0; border-radius: 6px; border: 1px solid #ccc; }
    button.primary { background: #2b6cb0; color: white; }
    .group { border: 1px solid #ccc; border-radius: 6px; padding: 0.5rem; margin-bottom: 0.5rem; }
  </style>
</head>
<body>
  <header>
    <h1>Culture & Historical Tourism</h1>
    <p>Discover heritage, connect with travelers, and plan trips</p>
  </header>

  <nav>
    <button onclick="showSection('register')">Register</button>
    <button onclick="showSection('login')">Login</button>
    <button onclick="showSection('attractions')">Attractions</button>
    <button onclick="showSection('itinerary')">Itinerary</button>
    <button onclick="showSection('budget')">Budget Matching</button>
    <button onclick="showSection('sos')">SOS Help</button>
  </nav>

  <!-- Registration -->
  <section id="register" class="active">
    <div class="card">
      <h2>Register</h2>
      <input id="regName" placeholder="Your Name">
      <input id="regBudget" placeholder="Budget (₹)" type="number">
      <select id="regVibe">
        <option value="Family">Family</option>
        <option value="Partners">Partners</option>
        <option value="Friends">Friends</option>
        <option value="Introvert">Introvert</option>
      </select>
      <input id="regPassword" type="password" placeholder="Password">
      <button class="primary" onclick="registerUser()">Register</button>
    </div>
  </section>

  <!-- Login -->
  <section id="login">
    <div class="card">
      <h2>Login</h2>
      <input id="loginName" placeholder="Name">
      <input id="loginPassword" type="password" placeholder="Password">
      <button class="primary" onclick="loginUser()">Login</button>
    </div>
  </section>

  <!-- Attractions -->
  <section id="attractions">
    <h2>Attractions</h2>
    <div class="card">
      <p><b>Taj Mahal</b> — Agra, India</p>
      <button onclick="addToItinerary('Taj Mahal')">Add to Itinerary</button>
    </div>
    <div class="card">
      <p><b>Charminar</b> — Hyderabad, India</p>
      <button onclick="addToItinerary('Charminar')">Add to Itinerary</button>
    </div>
    <div class="card">
      <p><b>Qutub Minar</b> — Delhi, India</p>
      <button onclick="addToItinerary('Qutub Minar')">Add to Itinerary</button>
    </div>
  </section>

  <!-- Itinerary -->
  <section id="itinerary">
    <h2>Your Itinerary</h2>
    <div id="itineraryList"></div>
  </section>

  <!-- Budget Matching -->
  <section id="budget">
    <h2>Find Travel Groups</h2>
    <div id="groupList"></div>
  </section>

  <!-- SOS -->
  <section id="sos">
    <div class="card">
      <h2>SOS Help</h2>
      <button class="primary" onclick="sendSOS()">Send SOS Alert 🚨</button>
      <p id="sosStatus"></p>
    </div>
  </section>

  <script>
    let currentUser = null;

    function showSection(id) {
      document.querySelectorAll("section").forEach(s => s.classList.remove("active"));
      document.getElementById(id).classList.add("active");
      if (id === "itinerary") loadItinerary();
      if (id === "budget") loadGroups();
    }

    function registerUser() {
      const name = document.getElementById("regName").value;
      const budget = parseInt(document.getElementById("regBudget").value);
      const vibe = document.getElementById("regVibe").value;
      const password = document.getElementById("regPassword").value;

      if (!name || !budget || !password) return alert("Fill all fields!");
      let users = JSON.parse(localStorage.getItem("users") || "[]");
      if (users.find(u => u.name === name)) return alert("User exists!");

      users.push({ name, budget, vibe, password, itinerary: [] });
      localStorage.setItem("users", JSON.stringify(users));
      alert("Registered successfully!");
    }

    function loginUser() {
      const name = document.getElementById("loginName").value;
      const password = document.getElementById("loginPassword").value;
      let users = JSON.parse(localStorage.getItem("users") || "[]");
      let user = users.find(u => u.name === name && u.password === password);
      if (!user) return alert("Invalid credentials!");
      currentUser = user;
      alert("Welcome, " + user.name);
      showSection("attractions");
    }

    function addToItinerary(place) {
      if (!currentUser) return alert("Login first!");
      let users = JSON.parse(localStorage.getItem("users"));
      let user = users.find(u => u.name === currentUser.name);
      user.itinerary.push(place);
      localStorage.setItem("users", JSON.stringify(users));
      currentUser = user;
      alert(place + " added!");
    }

    function loadItinerary() {
      if (!currentUser) return alert("Login first!");
      let list = document.getElementById("itineraryList");
      list.innerHTML = "";
      currentUser.itinerary.forEach(item => {
        let div = document.createElement("div");
        div.className = "card";
        div.innerText = item;
        list.appendChild(div);
      });
    }

    function loadGroups() {
      let users = JSON.parse(localStorage.getItem("users") || "[]");
      if (!currentUser) return alert("Login first!");
      let groupList = document.getElementById("groupList");
      groupList.innerHTML = "";

      let matches = users.filter(u =>
        u.name !== currentUser.name &&
        u.vibe === currentUser.vibe &&
        Math.abs(u.budget - currentUser.budget) <= currentUser.budget * 0.2
      );

      if (matches.length === 0) {
        groupList.innerHTML = "<p>No matching travelers found.</p>";
        return;
      }

      matches.forEach(m => {
        let div = document.createElement("div");
        div.className = "group";
        div.innerHTML = `<b>${m.name}</b> (${m.vibe}, ₹${m.budget}) 
                         <button onclick="joinGroup('${m.name}')">Join Group</button>`;
        groupList.appendChild(div);
      });
    }

    function joinGroup(name) {
      alert("You joined " + name + "'s group!");
    }

    function sendSOS() {
      document.getElementById("sosStatus").innerText = "🚨 SOS Alert sent to nearby authorities!";
    }
  </script>
</body>
</html>
