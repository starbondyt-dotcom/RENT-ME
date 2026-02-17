<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Your Dream House</title>

<style>
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: url('https://images.unsplash.com/photo-1568605114967-8130f3a36994') no-repeat center center/cover;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

/* Overlay for popup */
.overlay {
  position: fixed;
  top:0;
  left:0;
  width:100%;
  height:100%;
  background: rgba(0,0,0,0.85);
  display:flex;
  justify-content:center;
  align-items:center;
  z-index:1000;
}

.popup {
  background: #111;
  padding: 25px;
  border-radius: 15px;
  width: 90%;
  max-width: 350px;
  text-align: center;
  color: white;
}

input {
  width: 90%;
  padding: 10px;
  margin: 8px 0;
  border-radius: 5px;
  border: none;
}

button {
  background: #00c853;
  color: white;
  padding: 12px;
  width: 95%;
  border: none;
  border-radius: 5px;
  font-size: 16px;
  cursor: pointer;
  margin-top: 10px;
}

button:hover {
  background: #009624;
}

.success {
  color: #00ff95;
  margin-top: 15px;
  font-weight: bold;
}

.hidden {
  display: none;
}
</style>
</head>
<body>

<!-- Step 1: Name + Phone -->
<div class="overlay" id="step1">
  <div class="popup">
    <h2>Enter Your Details</h2>
    <input type="text" id="name" placeholder="Enter Your Name">
    <input type="number" id="phone" placeholder="Enter Mobile Number">
    <button onclick="goToStep2()">Continue</button>
  </div>
</div>

<!-- Step 2: Payment + Transaction ID -->
<div class="overlay hidden" id="step2">
  <div class="popup">
    <h2>Pay ₹100 via UPI</h2>
    <!-- 🔥 UPI Deep Link -->
    <!-- Amount is in paise: 10000 = ₹100 -->
    <a href="upi://pay?pa=nileshverma5001@ybl&pn=Your Dream House&am=10000&cu=INR">
      <button>Pay ₹100</button>
    </a>
    <p>Enter Transaction ID after payment</p>
    <input type="text" id="txnId" placeholder="Transaction ID">
    <button onclick="finishPayment()">Confirm Payment</button>
  </div>
</div>

<!-- Step 3: Success -->
<div class="overlay hidden" id="step3">
  <div class="popup">
    <h2>🎉 Registration Successful</h2>
    <div class="success" id="finalMessage"></div>
  </div>
</div>

<!-- Admin Panel Trigger -->
<div class="overlay hidden" id="adminPanel">
  <div class="popup">
    <h2>Admin Panel</h2>
    <div id="adminData" style="text-align:left; max-height:300px; overflow:auto;"></div>
    <button onclick="closeAdmin()">Close</button>
  </div>
</div>

<script>
// Store tickets in localStorage
let tickets = JSON.parse(localStorage.getItem("tickets")) || [];

// Step 1 → Step 2
function goToStep2() {
  let name = document.getElementById("name").value.trim();
  let phone = document.getElementById("phone").value.trim();

  if(name === "" || phone === "") {
    alert("Please fill all details");
    return;
  }

  document.getElementById("step1").classList.add("hidden");
  document.getElementById("step2").classList.remove("hidden");
}

// Step 2 → Step 3
function finishPayment() {
  let name = document.getElementById("name").value.trim();
  let phone = document.getElementById("phone").value.trim();
  let txn = document.getElementById("txnId").value.trim();

  if(txn === "") {
    alert("Please enter Transaction ID");
    return;
  }

  // Generate random ticket 1-20000
  let ticketNumber = Math.floor(Math.random() * 20000) + 1;

  // Save ticket in localStorage
  tickets.push({
    name: name,
    phone: phone,
    txnId: txn,
    ticketNumber: ticketNumber,
    date: new Date().toLocaleString()
  });
  localStorage.setItem("tickets", JSON.stringify(tickets));

  document.getElementById("step2").classList.add("hidden");
  document.getElementById("step3").classList.remove("hidden");

  document.getElementById("finalMessage").innerHTML =
    "Your Ticket Number is <b>" + ticketNumber + "</b><br>" +
    "Transaction ID: <b>" + txn + "</b><br>" +
    "Please take a screenshot.";
}

// Optional: Admin Panel to view all registrations
function openAdmin() {
  document.getElementById("adminPanel").classList.remove("hidden");
  let dataDiv = document.getElementById("adminData");
  dataDiv.innerHTML = "";
  tickets.forEach((t, i) => {
    dataDiv.innerHTML += (i+1) + ". Name: " + t.name + ", Phone: " + t.phone + ", Ticket: " + t.ticketNumber + ", TxnID: " + t.txnId + ", Date: " + t.date + "<br>";
  });
}

function closeAdmin() {
  document.getElementById("adminPanel").classList.add("hidden");
}

// Admin trigger (for example: long press 'A' key)
document.addEventListener("keydown", function(e){
  if(e.key === "A" || e.key === "a") {
    openAdmin();
  }
});
</script>

</body>
</html>
