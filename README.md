<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Account PM Data Entry</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #2563eb;
            --primary-hover: #1d4ed8;
            --bg: #f8fafc;
            --card-bg: #ffffff;
            --text-main: #1e293b;
            --text-muted: #64748b;
        }

        body {
            font-family: 'Inter', sans-serif;
            background-color: var(--bg);
            color: var(--text-main);
            margin: 0;
            padding: 20px;
            line-height: 1.5;
        }

        .container {
            max-width: 1000px;
            margin: 20px auto;
        }

        /* Header Section */
        header {
            text-align: center;
            margin-bottom: 30px;
        }

        header h1 {
            font-weight: 800;
            color: var(--primary);
            margin: 0;
            letter-spacing: -0.025em;
        }

        /* Card Styling */
        .card {
            background: var(--card-bg);
            padding: 30px;
            margin-bottom: 24px;
            border-radius: 12px;
            box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
            border: 1px solid #e2e8f0;
        }

        h2 {
            font-size: 1.25rem;
            font-weight: 700;
            margin-top: 0;
            margin-bottom: 20px;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        /* Form Layout */
        .form-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
            margin-bottom: 20px;
        }

        input, select {
            width: 100%;
            padding: 12px;
            border: 1px solid #cbd5e1;
            border-radius: 8px;
            font-size: 14px;
            box-sizing: border-box;
            transition: all 0.2s;
            outline: none;
        }

        input:focus, select:focus {
            border-color: var(--primary);
            box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.1);
        }

        button {
            background: var(--primary);
            color: white;
            padding: 12px 24px;
            border: none;
            border-radius: 8px;
            font-weight: 600;
            cursor: pointer;
            transition: background 0.2s;
            width: 100%;
            font-size: 16px;
        }

        button:hover {
            background: var(--primary-hover);
        }

        /* Table Styling */
        .table-container {
            overflow-x: auto;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            text-align: left;
        }

        th {
            background: #f1f5f9;
            color: var(--text-muted);
            font-weight: 600;
            text-transform: uppercase;
            font-size: 12px;
            letter-spacing: 0.05em;
            padding: 12px;
        }

        td {
            padding: 14px 12px;
            border-bottom: 1px solid #f1f5f9;
            font-size: 14px;
        }

        tr:hover {
            background-color: #f8fafc;
        }

        /* Summary Stats */
        .stats-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 15px;
            margin-top: 25px;
            padding-top: 20px;
            border-top: 2px solid #f1f5f9;
        }

        .stat-item {
            text-align: center;
        }

        .stat-label {
            font-size: 12px;
            color: var(--text-muted);
            display: block;
            margin-bottom: 5px;
        }

        .stat-value {
            font-size: 1.1rem;
            font-weight: 700;
            color: var(--text-main);
        }

        .grand-total {
            color: var(--primary);
        }

        /* Responsive Fix */
        @media (max-width: 600px) {
            .stats-grid {
                grid-template-columns: 1fr;
                gap: 10px;
            }
        }
    </style>
</head>

<body>

<div class="container">
    <header>
        <h1>Print & Medical Data Entry</h1>
    </header>

    <div class="card">
        <h2>Data Entry Form</h2>
        <form id="entryForm">
            <div class="form-grid">
                <input type="number" id="adno" placeholder="Admission No" required>
                <input type="text" id="name" placeholder="Student Name" required>
                <input type="text" id="class" placeholder="Class" required>
                <select id="category">
                    <option value="MEDICAL">MEDICAL</option>
                    <option value="PRINT">PRINT</option>
                </select>
                <input type="number" id="amount" placeholder="Amount" required>
            </div>
            <button type="submit">Submit Record</button>
        </form>
    </div>

    <div class="card">
        <h2>Records History</h2>
        <div class="table-container">
            <table>
                <thead>
                    <tr>
                        <th>ADNO</th>
                        <th>Name</th>
                        <th>Class</th>
                        <th>Category</th>
                        <th>Amount</th>
                    </tr>
                </thead>
                <tbody id="tableBody"></tbody>
            </table>
        </div>

        <div class="stats-grid">
            <div class="stat-item">
                <span class="stat-label">MEDICAL TOTAL</span>
                <span class="stat-value">₹<span id="medTotal">0</span></span>
            </div>
            <div class="stat-item">
                <span class="stat-label">PRINT TOTAL</span>
                <span class="stat-value">₹<span id="printTotal">0</span></span>
            </div>
            <div class="stat-item">
                <span class="stat-label">GRAND TOTAL</span>
                <span class="stat-value grand-total">₹<span id="grandTotal">0</span></span>
            </div>
        </div>
    </div>
</div>

<script>
    const form = document.getElementById("entryForm");
    const tableBody = document.getElementById("tableBody");

    let db = [];

    function updateTotals() {
        let med = 0, pr = 0;
        db.forEach(d => {
            if (d.category === "MEDICAL") med += Number(d.amount);
            else pr += Number(d.amount);
        });
        document.getElementById("medTotal").innerText = med.toLocaleString('en-IN', {minimumFractionDigits: 2});
        document.getElementById("printTotal").innerText = pr.toLocaleString('en-IN', {minimumFractionDigits: 2});
        document.getElementById("grandTotal").innerText = (med + pr).toLocaleString('en-IN', {minimumFractionDigits: 2});
    }

    function renderTable() {
        tableBody.innerHTML = "";
        db.forEach(d => {
            tableBody.innerHTML += `
            <tr>
                <td><strong>#${d.adno}</strong></td>
                <td>${d.name}</td>
                <td>${d.class}</td>
                <td><span style="font-size: 11px; padding: 2px 8px; border-radius: 12px; background: #e2e8f0;">${d.category}</span></td>
                <td>₹${Number(d.amount).toLocaleString('en-IN', {minimumFractionDigits: 2})}</td>
            </tr>
            `;
        });
    }

    form.addEventListener("submit", function(e) {
        e.preventDefault();
        const adno = document.getElementById("adno").value.trim();
        if (!adno) {
            alert("ADNO is required!");
            return;
        }

        const data = {
            adno: adno,
            name: document.getElementById("name").value.toUpperCase(),
            class: document.getElementById("class").value,
            category: document.getElementById("category").value,
            amount: document.getElementById("amount").value
        };

        // UI feedback - disable button while sending
        const btn = form.querySelector("button");
        const originalText = btn.innerText;
        btn.innerText = "Sending...";
        btn.disabled = true;

        fetch("https://script.google.com/macros/s/AKfycbzcI4hcFa8p5WHV7O1j2yTcbRTjGejAvFLBOwh0TKUPENNoUQ1yCw1_fVAPp-0WEjg/exec", {
            method: "POST",
            body: JSON.stringify(data)
        })
        .then(res => res.text())
        .then(response => {
            db.push(data);
            renderTable();
            updateTotals();
            form.reset();
        })
        .catch(error => {
            console.error("Error:", error);
            alert("Failed to send data!");
        })
        .finally(() => {
            btn.innerText = originalText;
            btn.disabled = false;
        });
    });
</script>

</body>
</html>
