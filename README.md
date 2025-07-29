<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>CRB Status Checker</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f2f2f2;
      margin: 0;
      padding: 0;
      display: flex;
      height: 100vh;
      justify-content: center;
      align-items: center;
    }

    .container {
      background: white;
      padding: 2em;
      border-radius: 8px;
      width: 90%;
      max-width: 400px;
      box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }

    h2 {
      margin-bottom: 1em;
    }

    input[type="text"],
    input[type="email"],
    input[type="tel"],
    textarea {
      width: 100%;
      padding: 10px;
      margin: 10px 0;
      border: 1px solid #ccc;
      border-radius: 5px;
    }

    button {
      width: 100%;
      background: #28a745;
      color: white;
      padding: 10px;
      margin-top: 10px;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }

    button:hover {
      background: #218838;
    }

    .till-box {
      background: #f0f0f0;
      padding: 10px;
      border-radius: 5px;
      margin: 10px 0;
    }

    #result {
      margin-top: 20px;
      font-size: 18px;
    }
  </style>
</head>
<body>
  <div class="container">
    <!-- PAGE 1: USER INFO -->
    <div id="page1">
      <h2>Check Your CRB Status</h2>
      <form id="userForm">
        <input type="text" id="name" placeholder="Full Name" required />
        <input type="email" id="email" placeholder="Email Address" required />
        <input type="tel" id="phone" placeholder="Phone Number" required />
        <input type="text" id="reason" placeholder="Reason for checking status" required />
        <button type="submit">Continue</button>
      </form>
    </div>

    <!-- PAGE 2: PAYMENT -->
    <div id="page2" style="display: none;">
      <h2>Payment Required</h2>
      <p>Please pay <strong>KES 200</strong> to the Till Number below:</p>

      <div class="till-box">
        <p><strong>Till Name:</strong> Platinum Solutions</p>
        <p>
          <strong>Till Number:</strong>
          <span id="tillNumber">8566678</span>
        </p>
        <button type="button" id="copyTill">Copy Till Number</button>
      </div>

      <p style="margin-top: 20px;">
        After payment, paste the full M-Pesa confirmation message below:
      </p>
      <textarea
        id="mpesaMessage"
        rows="4"
        placeholder="Paste M-Pesa confirmation message here..."
        required
      ></textarea>

      <button id="payBtn" disabled>Continue</button>
    </div>

    <!-- PAGE 3: CRB RESULT -->
    <div id="page3" style="display: none;">
      <h2>CRB Status Result</h2>
      <form id="statusForm">
        <label for="nationalId">Enter National ID:</label>
        <input type="text" id="nationalId" required />
        <button type="submit">Check Status</button>
      </form>
      <div id="result"></div>
    </div>
  </div>

  <script>
    // Function to get CRB status based on ID range
    function getCRBStatus(idNumber) {
      const id = parseInt(idNumber, 10);

      if (id >= 101000000 && id <= 22999999) {
        return "Cleared";
      } else if (id >= 23000000 && id <=39999999) {
        return "Blacklisted";
      } else if (id >= 400000000 && id <= 499999999) {
        return "Pending";
      } else {
        return "ID Not Found";
      }
    }

    // Page 1 → Page 2
    document.getElementById("userForm").addEventListener("submit", function (e) {
      e.preventDefault();

      const name = document.getElementById("name").value.trim();
      const email = document.getElementById("email").value.trim();
      const phone = document.getElementById("phone").value.trim();
      const reason = document.getElementById("reason").value.trim();

      if (name && email && phone && reason) {
        document.getElementById("page1").style.display = "none";
        document.getElementById("page2").style.display = "block";
      }
    });

    // Copy Till Number
    document.getElementById("copyTill").addEventListener("click", function () {
      const tillNumber = document.getElementById("tillNumber").innerText;
      navigator.clipboard
        .writeText(tillNumber)
        .then(() => {
          alert("Till Number copied to clipboard!");
        })
        .catch(() => {
          alert("Failed to copy. Please copy manually.");
        });
    });

    // Enable Continue if M-Pesa message pasted
    const mpesaInput = document.getElementById("mpesaMessage");
    const payBtn = document.getElementById("payBtn");

    mpesaInput.addEventListener("input", function () {
      if (mpesaInput.value.trim().length > 10) {
        payBtn.disabled = false;
      } else {
        payBtn.disabled = true;
      }
    });

    // Page 2 → Page 3
    payBtn.addEventListener("click", function () {
      const message = mpesaInput.value.trim();

      if (message.length < 10) {
        alert("Please paste a valid M-Pesa confirmation message.");
        return;
      }

      alert("Payment verified. Thank you!");

      document.getElementById("page2").style.display = "none";
      document.getElementById("page3").style.display = "block";
    });

    // CRB Status Checker with National ID validation (21... to 53...)
    document.getElementById("statusForm").addEventListener("submit", function (e) {
      e.preventDefault();

      const nationalId = document.getElementById("nationalId").value.trim();
      const resultDiv = document.getElementById("result");

      // Validate ID numeric
      if (!/^\d+$/.test(nationalId)) {
        alert("National ID must be numeric.");
        return;
      }
      if (nationalId.length < 8) {
        alert("National ID must be at least 8 digits.");
        return;
      }

      // Extract first two digits as number
      const prefix = parseInt(nationalId.substring(0, 2), 10);

      if (prefix < 21 || prefix > 53) {
        alert("National ID must start with a number between 1,2,3,4,5,6,7,8 and 9.");
        return;
      }

      // Use getCRBStatus function
      const status = getCRBStatus(nationalId);
      resultDiv.innerHTML = `Status: <strong>${status}</strong>`;
    });
  </script>
</body>
</html>
