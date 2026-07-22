<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DYNAMIC KINETIC FACILITY // WALLET MATRIX</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        :root {
            --bg-color: #0b0f19;
            --card-bg: #111827;
            --border-color: #1f2937;
            --accent-green: #10b981;
            --accent-dim: #059669;
            --accent-red: #ef4444;
            --text-main: #f3f4f6;
            --text-muted: #9ca3af;
            --lock-amber: #f59e0b;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Courier New', Courier, monospace;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-main);
            padding: 24px;
            min-height: 100vh;
        }

        header {
            border-bottom: 2px solid var(--accent-green);
            padding-bottom: 16px;
            margin-bottom: 24px;
            display: flex;
            justify-content: space-between;
            align-items: flex-end;
        }

        h1 {
            font-size: 1.4rem;
            color: var(--accent-green);
            letter-spacing: 2px;
        }

        .sys-status {
            font-size: 0.85rem;
            color: var(--text-muted);
            text-align: right;
        }

        .grid-container {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
            gap: 18px;
        }

        .wallet-card {
            background-color: var(--card-bg);
            border: 1px solid var(--border-color);
            border-radius: 6px;
            padding: 16px;
            position: relative;
            transition: transform 0.2s, border-color 0.2s;
        }

        .wallet-card:hover {
            border-color: var(--accent-green);
            transform: translateY(-2px);
        }

        .wallet-card.locked {
            border-color: var(--lock-amber);
            background-color: #1a160c;
        }

        .wallet-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 1px solid var(--border-color);
            padding-bottom: 8px;
            margin-bottom: 12px;
        }

        .node-id {
            font-weight: bold;
            font-size: 0.95rem;
            color: var(--accent-green);
        }

        .node-id.locked-text {
            color: var(--lock-amber);
        }

        .badge {
            font-size: 0.7rem;
            padding: 2px 6px;
            border-radius: 4px;
            background: #064e3b;
            color: var(--accent-green);
            text-transform: uppercase;
        }

        .badge.locked-badge {
            background: #78350f;
            color: var(--lock-amber);
        }

        .metrics {
            display: flex;
            flex-direction: column;
            gap: 8px;
            margin-bottom: 12px;
        }

        .metric-row {
            display: flex;
            justify-content: space-between;
            font-size: 0.85rem;
        }

        .metric-label {
            color: var(--text-muted);
        }

        .metric-val {
            font-weight: bold;
        }

        .chart-box {
            height: 80px;
            margin-bottom: 12px;
        }

        .address-box {
            background-color: #030712;
            padding: 6px 8px;
            border-radius: 4px;
            font-size: 0.75rem;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border: 1px solid #111827;
        }

        .addr-text {
            color: var(--text-muted);
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
            max-width: 220px;
        }

        .copy-btn {
            background: transparent;
            border: none;
            color: var(--accent-green);
            cursor: pointer;
            font-size: 0.75rem;
        }

        .copy-btn:hover {
            text-decoration: underline;
        }

        .locked-overlay {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 140px;
            color: var(--lock-amber);
            text-align: center;
            gap: 8px;
        }

        .lock-icon {
            font-size: 2rem;
        }

        footer {
            margin-top: 32px;
            padding-top: 16px;
            border-top: 1px solid var(--border-color);
            font-size: 0.75rem;
            color: var(--text-muted);
            text-align: center;
        }
    </style>
</head>
<body>

    <header>
        <div>
            <h1>DYNAMIC KINETICS FACILITY</h1>
            <div style="font-size: 0.8rem; color: var(--text-muted); margin-top: 4px;">OPERATIONAL WALLET MATRIX // R&D SECTOR</div>
        </div>
        <div class="sys-status">
            [SYS STATUS]: ONLINE<br>
            [ACTIVE NODES]: 11/12 ACCESSIBLE
        </div>
    </header>

    <div class="grid-container" id="wallet-grid"></div>

    <footer>
        DYNAMIC KINETICS FACILITY (DKF-SYS) // UNCLEARED ACCESS PROHIBITED
    </footer>

    <script>
        // Node 12 is Andy's private/untouchable wallet
        const walletNodes = [
            { id: "NODE-01", name: "Alpha Research Reserve", balance: "142.85 SOL", value: "$21,427.50", address: "3kF9xA1...9b2Z", isLocked: false, history: [120, 125, 130, 128, 142.85] },
            { id: "NODE-02", name: "Propulsion Engineering", balance: "89.10 SOL", value: "$13,365.00", address: "7mP2qC8...4v1L", isLocked: false, history: [95, 92, 88, 90, 89.10] },
            { id: "NODE-03", name: "Hardware Prototyping", balance: "310.40 SOL", value: "$46,560.00", address: "9xT4vN1...1m8K", isLocked: false, history: [280, 290, 300, 305, 310.40] },
            { id: "NODE-04", name: "Cybernetics Field Lab", balance: "54.20 SOL", value: "$8,130.00", address: "2bL8rY9...6p3W", isLocked: false, history: [50, 52, 51, 53, 54.20] },
            { id: "NODE-05", name: "Materials Fabrication", balance: "198.75 SOL", value: "$29,812.50", address: "5vZ1mQ4...8t7X", isLocked: false, history: [180, 185, 190, 195, 198.75] },
            { id: "NODE-06", name: "Autonomous Systems Pool", balance: "412.00 SOL", value: "$61,800.00", address: "8kN3pL2...5x9Y", isLocked: false, history: [390, 400, 405, 410, 412.00] },
            { id: "NODE-07", name: "Ballistics Vault", balance: "76.50 SOL", value: "$11,475.00", address: "1qW4eR7...3y2U", isLocked: false, history: [80, 78, 77, 75, 76.50] },
            { id: "NODE-08", name: "Sub-Layer Energy Network", balance: "520.10 SOL", value: "$78,015.00", address: "4uI9oP1...7a6S", isLocked: false, history: [480, 495, 500, 510, 520.10] },
            { id: "NODE-09", name: "Liquidity & Exchange Operations", balance: "230.90 SOL", value: "$34,635.00", address: "6dF3gH8...2j1K", isLocked: false, history: [210, 220, 225, 228, 230.90] },
            { id: "NODE-10", name: "Secure Protocol Reserve", balance: "115.30 SOL", value: "$17,295.00", address: "9zC5vB0...4m3N", isLocked: false, history: [100, 105, 110, 112, 115.30] },
            { id: "NODE-11", name: "Emergency Hardware Contingency", balance: "67.00 SOL", value: "$10,050.00", address: "3xS7dF2...8g9H", isLocked: false, history: [65, 66, 66, 67, 67.00] },
            { id: "NODE-12", name: "Andy's Vault // Untouchable Node", balance: "LOCKED", value: "ENCRYPTED", address: "UNASSIGNED / PRIVATE", isLocked: true, history: [] }
        ];

        const gridEl = document.getElementById("wallet-grid");

        walletNodes.forEach((node, index) => {
            const card = document.createElement("div");
            card.className = `wallet-card ${node.isLocked ? 'locked' : ''}`;

            if (node.isLocked) {
                card.innerHTML = `
                    <div class="wallet-header">
                        <span class="node-id locked-text">${node.id}</span>
                        <span class="badge locked-badge">Restricted</span>
                    </div>
                    <div class="locked-overlay">
                        <div class="lock-icon">🔒</div>
                        <div style="font-size: 0.85rem; font-weight: bold;">ANDY'S VAULT NODE</div>
                        <div style="font-size: 0.7rem; color: var(--text-muted);">Access Class: Level-5 Untouchable</div>
                    </div>
                    <div class="address-box">
                        <span class="addr-text">SECURE ENCLAVE // READ-ONLY</span>
                        <span style="color: var(--lock-amber); font-size: 0.7rem;">[LOCKED]</span>
                    </div>
                `;
            } else {
                card.innerHTML = `
                    <div class="wallet-header">
                        <span class="node-id">${node.id}</span>
                        <span class="badge">Active</span>
                    </div>
                    <div style="font-size: 0.8rem; color: var(--text-muted); margin-bottom: 8px;">${node.name}</div>
                    <div class="metrics">
                        <div class="metric-row">
                            <span class="metric-label">Holdings:</span>
                            <span class="metric-val" style="color: var(--accent-green);">${node.balance}</span>
                        </div>
                        <div class="metric-row">
                            <span class="metric-label">Estimated Value:</span>
                            <span class="metric-val">${node.value}</span>
                        </div>
                    </div>
                    <div class="chart-box">
                        <canvas id="chart-${index}"></canvas>
                    </div>
                    <div class="address-box">
                        <span class="addr-text">${node.address}</span>
                        <button class="copy-btn" onclick="copyAddr('${node.address}')">Copy</button>
                    </div>
                `;
            }

            gridEl.appendChild(card);

            // Render mini sparkline charts for active nodes
            if (!node.isLocked) {
                const ctx = document.getElementById(`chart-${index}`).getContext('2d');
                new Chart(ctx, {
                    type: 'line',
                    data: {
                        labels: ['T-4', 'T-3', 'T-2', 'T-1', 'Now'],
                        datasets: [{
                            data: node.history,
                            borderColor: '#10b981',
                            borderWidth: 1.5,
                            pointRadius: 0,
                            fill: false,
                            tension: 0.3
                        }]
                    },
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        plugins: { legend: { display: false } },
                        scales: {
                            x: { display: false },
                            y: { display: false }
                        }
                    }
                });
            }
        });

        function copyAddr(addr) {
            navigator.clipboard.writeText(addr);
            alert("Contract/Wallet Address copied: " + addr);
        }
    </script>
</body>
</html>
