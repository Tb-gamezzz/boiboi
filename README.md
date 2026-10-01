# boiboi
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Order & Verification Portal</title>
<style>
body { font-family: Arial, sans-serif; padding: 20px; text-align: center; background-color: #f9f9f9; }
.card { max-width: 400px; margin: auto; padding: 20px; background: white; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); }
input, button, textarea { width: 100%; margin-top: 10px; padding: 10px; box-sizing: border-box; }
button { background-color: #00c4cc; color: white; border: none; font-weight: bold; cursor: pointer; border-radius: 4px; }
.hidden { display: none; }
</style>
</head>
<body>

<div class="card">
<!-- STEP 1: ORDER FORM -->
<div id="order-section">
<h2>Place Your Order</h2>
<input type="text" id="name" placeholder="Your Name" required>
<input type="email" id="email" placeholder="Your Email" required>
<textarea id="details" placeholder="Order Details..."></textarea>
<button onclick="submitOrder()">Submit Order</button>
<p id="order-msg"></p>
</div>

<!-- STEP 2: CODE VERIFICATION -->
<div id="verify-section" class="hidden">
<h2>Enter Verification Code</h2>
<p>Pay in person to receive your one-time unlock code.</p>
<input type="text" id="code" placeholder="Enter 6-Digit Code">
<button onclick="verifyCode()">Unlock Page</button>
<p id="verify-msg" style="color: red;"></p>
</div>

<!-- STEP 3: UNLOCKED CONTENT (PAGE 2) -->
<div id="unlocked-section" class="hidden">
<h2>Welcome to Page 2!</h2>
<p>Your access is permanently saved in this browser.</p>
<!-- Insert your unlocked content, download links, or private media here -->
</div>
</div>

<script>
// Your Apps Script Web App URL
const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbwg5kfjGfAvxu6W0j1-HheFSHOYIFN-rEOR2_6nE4ADhr5P760JMsFgs7Cfot8OXK_4_w/exec";

// Check if the user has already unlocked access previously on this browser
window.onload = function() {
if (localStorage.getItem("is_unlocked") === "true") {
showSection("unlocked-section");
}
};

function showSection(sectionId) {
document.getElementById("order-section").classList.add("hidden");
document.getElementById("verify-section").classList.add("hidden");
document.getElementById("unlocked-section").classList.add("hidden");
document.getElementById(sectionId).classList.remove("hidden");
}

async function submitOrder() {
const name = document.getElementById("name").value;
const email = document.getElementById("email").value;
const details = document.getElementById("details").value;
const msg = document.getElementById("order-msg");

if (!name || !email) {
msg.innerText = "Please fill in all fields.";
return;
}

msg.innerText = "Submitting order...";

try {
await fetch(SCRIPT_URL, {
method: "POST",
mode: "no-cors",
headers: { "Content-Type": "application/json" },
body: JSON.stringify({ action: "createOrder", name, email, orderDetails: details })
});

msg.innerText = "Order submitted! The admin has received your verification code request.";
setTimeout(() => showSection("verify-section"), 1500);
} catch (err) {
msg.innerText = "An error occurred submitting the order. Please try again.";
}
}

async function verifyCode() {
const code = document.getElementById("code").value.trim();
const msg = document.getElementById("verify-msg");

if (!code) {
msg.innerText = "Please enter a code.";
return;
}

msg.innerText = "Verifying...";

try {
const res = await fetch(SCRIPT_URL, {
method: "POST",
headers: { "Content-Type": "text/plain" },
body: JSON.stringify({ action: "verifyCode", code })
});

const result = await res.json();

if (result.valid) {
// Permanently set access flag in user's browser cache
localStorage.setItem("is_unlocked", "true");
showSection("unlocked-section");
} else {
msg.innerText = "Invalid or already used code.";
}
} catch (err) {
msg.innerText = "Verification error. Please check the code and try again.";
}
}
</script>
</body>
</html>

