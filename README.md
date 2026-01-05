<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SMK PASUNDAN MALANGBONG | Sistem Absensi Digital</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@300;400;500;700&family=Orbitron:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        :root {
            --primary: #0066cc;
            --secondary: #00ccff;
            --accent: #00ff99;
            --dark: #0a1929;
            --darker: #051021;
            --light: #e6f7ff;
            --terminal: #00ff00;
            --server: #ff6600;
            --network: #9966ff;
            --danger: #ff3333;
            --warning: #ffcc00;
            --success: #00cc66;
            --gray: #6c757d;
        }

        body {
            background-color: var(--darker);
            background-image: 
                radial-gradient(circle at 10% 20%, rgba(0, 102, 204, 0.1) 0%, transparent 20%),
                radial-gradient(circle at 90% 80%, rgba(0, 204, 255, 0.1) 0%, transparent 20%),
                radial-gradient(circle at 50% 50%, rgba(0, 255, 153, 0.05) 0%, transparent 30%);
            color: var(--light);
            font-family: 'JetBrains Mono', monospace;
            min-height: 100vh;
            overflow-x: hidden;
        }

        .scanline {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(to bottom, transparent 50%, rgba(0, 255, 153, 0.03) 50%);
            background-size: 100% 4px;
            z-index: 9999;
            pointer-events: none;
            opacity: 0.3;
            animation: scan 8s linear infinite;
        }

        @keyframes scan {
            0% { transform: translateY(-100%); }
            100% { transform: translateY(100%); }
        }

        .container {
            max-width: 1600px;
            margin: 0 auto;
            padding: 20px;
            position: relative;
            z-index: 1;
        }

        /* Header dengan tema jaringan */
        header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 20px 0;
            margin-bottom: 30px;
            border-bottom: 2px solid var(--primary);
            position: relative;
        }

        .logo-area {
            display: flex;
            align-items: center;
            gap: 15px;
        }

        .server-icon {
            width: 60px;
            height: 60px;
            background: linear-gradient(45deg, var(--dark), var(--primary));
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            position: relative;
            overflow: hidden;
        }

        .server-icon:before {
            content: '';
            position: absolute;
            width: 100%;
            height: 100%;
            background: linear-gradient(45deg, transparent, rgba(0, 255, 153, 0.3), transparent);
            animation: serverGlow 3s linear infinite;
        }

        @keyframes serverGlow {
            0% { transform: translateX(-100%); }
            100% { transform: translateX(100%); }
        }

        .server-icon i {
            font-size: 28px;
            color: var(--accent);
            z-index: 2;
        }

        .logo-text h1 {
            font-family: 'Orbitron', sans-serif;
            font-size: 2.2rem;
            background: linear-gradient(to right, var(--primary), var(--secondary));
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            letter-spacing: 1px;
            margin-bottom: 5px;
        }

        .logo-text p {
            color: var(--accent);
            font-size: 0.9rem;
            letter-spacing: 2px;
        }

        .network-status {
            display: flex;
            align-items: center;
            gap: 10px;
            background-color: rgba(0, 0, 0, 0.6);
            padding: 10px 20px;
            border-radius: 5px;
            border: 1px solid var(--primary);
        }

        .status-indicator {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            background-color: var(--success);
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.5; }
        }

        /* Terminal style dashboard */
        .dashboard {
            display: grid;
            grid-template-columns: 1fr 400px;
            gap: 25px;
            margin-bottom: 30px;
        }

        @media (max-width: 1200px) {
            .dashboard {
                grid-template-columns: 1fr;
            }
        }

        .terminal-card {
            background-color: rgba(10, 25, 41, 0.85);
            border-radius: 10px;
            border: 1px solid var(--primary);
            box-shadow: 0 0 20px rgba(0, 102, 204, 0.2);
            overflow: hidden;
        }

        .terminal-header {
            background-color: rgba(5, 16, 33, 0.9);
            padding: 15px 20px;
            border-bottom: 1px solid var(--primary);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .terminal-title {
            display: flex;
            align-items: center;
            gap: 10px;
            color: var(--accent);
            font-family: 'Orbitron', sans-serif;
            font-size: 1.2rem;
        }

        .terminal-controls {
            display: flex;
            gap: 8px;
        }

        .control-btn {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            border: none;
        }

        .close { background-color: var(--danger); }
        .minimize { background-color: var(--warning); }
        .maximize { background-color: var(--success); }

        .terminal-body {
            padding: 0;
            max-height: 600px;
            overflow-y: auto;
        }

        /* Tabel dengan tema coding */
        .table-container {
            overflow-x: auto;
            padding: 15px;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 0.9rem;
        }

        thead {
            background-color: rgba(0, 102, 204, 0.2);
            position: sticky;
            top: 0;
        }

        th {
            padding: 12px 10px;
            text-align: left;
            color: var(--secondary);
            font-weight: 500;
            border-bottom: 2px solid var(--primary);
            font-family: 'Orbitron', sans-serif;
            letter-spacing: 1px;
            white-space: nowrap;
        }

        td {
            padding: 12px 10px;
            border-bottom: 1px solid rgba(0, 102, 204, 0.2);
        }

        tr:hover {
            background-color: rgba(0, 102, 204, 0.1);
        }

        /* Status buttons dengan tema teknologi */
        .status-buttons {
            display: flex;
            flex-wrap: wrap;
            gap: 5px;
        }

        .status-btn {
            padding: 6px 10px;
            border: none;
            border-radius: 4px;
            font-family: 'JetBrains Mono', monospace;
            font-size: 0.8rem;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            gap: 4px;
            min-width: 70px;
            justify-content: center;
        }

        .status-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 10px rgba(0, 0, 0, 0.3);
        }

        .status-btn.active {
            box-shadow: 0 0 8px currentColor;
        }

        .hadir {
            background-color: rgba(0, 204, 102, 0.15);
            color: var(--accent);
            border: 1px solid var(--accent);
        }

        .izin {
            background-color: rgba(255, 204, 0, 0.15);
            color: var(--warning);
            border: 1px solid var(--warning);
        }

        .sakit {
            background-color: rgba(0, 204, 255, 0.15);
            color: var(--secondary);
            border: 1px solid var(--secondary);
        }

        .alpa {
            background-color: rgba(255, 51, 51, 0.15);
            color: var(--danger);
            border: 1px solid var(--danger);
        }

        /* Sidebar dengan tema sistem */
        .system-sidebar {
            display: flex;
            flex-direction: column;
            gap: 25px;
        }

        .system-card {
            background-color: rgba(10, 25, 41, 0.85);
            border-radius: 10px;
            border: 1px solid var(--primary);
            box-shadow: 0 0 20px rgba(0, 102, 204, 0.2);
            overflow: hidden;
        }

        .system-header {
            background-color: rgba(5, 16, 33, 0.9);
            padding: 15px 20px;
            border-bottom: 1px solid var(--primary);
            color: var(--secondary);
            font-family: 'Orbitron', sans-serif;
            font-size: 1.1rem;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .system-body {
            padding: 20px;
        }

        /* Statistik */
        .stats-container {
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .stat-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px 0;
            border-bottom: 1px solid rgba(0, 102, 204, 0.2);
        }

        .stat-label {
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .stat-icon {
            width: 32px;
            height: 32px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            background-color: rgba(0, 102, 204, 0.2);
        }

        .stat-name {
            color: var(--light);
            font-size: 0.9rem;
        }

        .stat-value {
            font-family: 'Orbitron', sans-serif;
            font-size: 1.2rem;
            color: var(--accent);
        }

        /* Progress bar */
        .progress-container {
            margin-top: 10px;
        }

        .progress-label {
            display: flex;
            justify-content: space-between;
            margin-bottom: 5px;
        }

        .progress-bar {
            height: 10px;
            background-color: rgba(0, 102, 204, 0.2);
            border-radius: 5px;
            overflow: hidden;
        }

        .progress-fill {
            height: 100%;
            background: linear-gradient(to right, var(--primary), var(--accent));
            border-radius: 5px;
            transition: width 0.5s ease;
        }

        /* Input tanggal dengan tema terminal */
        .date-selector {
            display: flex;
            align-items: center;
            gap: 15px;
            background-color: rgba(5, 16, 33, 0.7);
            padding: 15px;
            border-radius: 5px;
            border: 1px solid var(--primary);
            margin-bottom: 20px;
            flex-wrap: wrap;
        }

        .date-selector label {
            color: var(--secondary);
            font-family: 'Orbitron', sans-serif;
            letter-spacing: 1px;
            font-size: 0.9rem;
        }

        .date-selector input {
            background-color: rgba(0, 0, 0, 0.5);
            border: 1px solid var(--primary);
            border-radius: 5px;
            color: var(--accent);
            padding: 8px 12px;
            font-family: 'JetBrains Mono', monospace;
            flex-grow: 1;
            min-width: 150px;
        }

        /* Action buttons */
        .action-buttons {
            display: flex;
            justify-content: flex-end;
            gap: 15px;
            margin-top: 30px;
            flex-wrap: wrap;
        }

        .action-btn {
            padding: 12px 20px;
            border: none;
            border-radius: 5px;
            font-family: 'Orbitron', sans-serif;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            gap: 10px;
            letter-spacing: 1px;
            font-size: 0.9rem;
        }

        .action-btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.4);
        }

        .btn-save {
            background: linear-gradient(to right, var(--primary), var(--secondary));
            color: white;
        }

        .btn-reset {
            background-color: rgba(255, 51, 51, 0.2);
            color: var(--danger);
            border: 1px solid var(--danger);
        }

        .btn-export {
            background-color: rgba(0, 255, 153, 0.2);
            color: var(--accent);
            border: 1px solid var(--accent);
        }

        .btn-print {
            background-color: rgba(153, 102, 255, 0.2);
            color: var(--network);
            border: 1px solid var(--network);
        }

        /* Modal untuk cetak laporan */
        .print-modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.9);
            z-index: 10001;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }

        .print-modal.active {
            display: flex;
        }

        .print-content {
            background-color: var(--darker);
            border: 2px solid var(--primary);
            border-radius: 10px;
            width: 90%;
            max-width: 900px;
            max-height: 90vh;
            overflow-y: auto;
            padding: 0;
            box-shadow: 0 0 40px rgba(0, 102, 204, 0.5);
        }

        .print-header {
            background-color: rgba(5, 16, 33, 0.95);
            padding: 20px;
            border-bottom: 2px solid var(--primary);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .print-header h2 {
            color: var(--accent);
            font-family: 'Orbitron', sans-serif;
            font-size: 1.5rem;
        }

        .print-close {
            background: none;
            border: none;
            color: var(--danger);
            font-size: 1.5rem;
            cursor: pointer;
        }

        .print-body {
            padding: 25px;
        }

        .print-options {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }

        .print-option {
            background-color: rgba(5, 16, 33, 0.7);
            border-radius: 8px;
            padding: 20px;
            border: 1px solid var(--primary);
            transition: all 0.3s;
            cursor: pointer;
        }

        .print-option:hover {
            border-color: var(--accent);
            transform: translateY(-5px);
        }

        .print-option.active {
            border-color: var(--accent);
            box-shadow: 0 0 15px rgba(0, 255, 153, 0.3);
        }

        .print-icon {
            font-size: 2.5rem;
            color: var(--accent);
            margin-bottom: 15px;
        }

        .print-option h3 {
            color: var(--secondary);
            margin-bottom: 10px;
            font-family: 'Orbitron', sans-serif;
        }

        .print-option p {
            color: var(--gray);
            font-size: 0.9rem;
            line-height: 1.5;
        }

        .print-range {
            background-color: rgba(5, 16, 33, 0.7);
            border-radius: 8px;
            padding: 20px;
            margin-bottom: 30px;
            border: 1px solid var(--primary);
        }

        .print-range h3 {
            color: var(--secondary);
            margin-bottom: 15px;
            font-family: 'Orbitron', sans-serif;
        }

        .date-range {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
        }

        .date-input {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }

        .date-input label {
            color: var(--light);
            font-size: 0.9rem;
        }

        .date-input input {
            background-color: rgba(0, 0, 0, 0.5);
            border: 1px solid var(--primary);
            border-radius: 5px;
            color: var(--accent);
            padding: 10px;
            font-family: 'JetBrains Mono', monospace;
        }

        .print-actions {
            display: flex;
            justify-content: flex-end;
            gap: 15px;
        }

        .print-action-btn {
            padding: 12px 25px;
            border: none;
            border-radius: 5px;
            font-family: 'Orbitron', sans-serif;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .btn-cancel {
            background-color: rgba(255, 51, 51, 0.2);
            color: var(--danger);
            border: 1px solid var(--danger);
        }

        .btn-generate {
            background: linear-gradient(to right, var(--primary), var(--secondary));
            color: white;
        }

        /* Styling untuk laporan cetak */
        @media print {
            body * {
                visibility: hidden;
            }
            
            .print-report, .print-report * {
                visibility: visible;
            }
            
            .print-report {
                position: absolute;
                left: 0;
                top: 0;
                width: 100%;
                padding: 20px;
                font-family: Arial, sans-serif;
                color: #000;
                background-color: white;
            }
            
            .no-print {
                display: none !important;
            }
        }

        .print-report {
            display: none;
            padding: 30px;
            background-color: white;
            color: #000;
            font-family: Arial, sans-serif;
        }

        .report-header {
            text-align: center;
            margin-bottom: 30px;
            border-bottom: 3px solid #0066cc;
            padding-bottom: 20px;
        }

        .report-header h1 {
            color: #0066cc;
            margin-bottom: 10px;
            font-size: 28px;
        }

        .report-header h2 {
            color: #333;
            margin-bottom: 15px;
            font-size: 20px;
        }

        .report-meta {
            display: flex;
            justify-content: space-between;
            margin-bottom: 25px;
            padding: 15px;
            background-color: #f5f5f5;
            border-radius: 5px;
            font-size: 14px;
        }

        .report-summary {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
            padding: 20px;
            background-color: #f9f9f9;
            border-radius: 8px;
        }

        .summary-item {
            text-align: center;
            padding: 15px;
            border-radius: 5px;
        }

        .summary-hadir { background-color: #e6f7e6; border-left: 5px solid #00cc66; }
        .summary-izin { background-color: #fff7e6; border-left: 5px solid #ffcc00; }
        .summary-sakit { background-color: #e6f7ff; border-left: 5px solid #00ccff; }
        .summary-alpa { background-color: #ffe6e6; border-left: 5px solid #ff3333; }

        .report-table {
            width: 100%;
            border-collapse: collapse;
            margin-bottom: 30px;
            font-size: 12px;
        }

        .report-table th {
            background-color: #0066cc;
            color: white;
            padding: 10px;
            text-align: left;
            border: 1px solid #ddd;
        }

        .report-table td {
            padding: 8px;
            border: 1px solid #ddd;
        }

        .report-table tr:nth-child(even) {
            background-color: #f9f9f9;
        }

        .report-footer {
            margin-top: 40px;
            padding-top: 20px;
            border-top: 2px solid #ccc;
            display: flex;
            justify-content: space-between;
        }

        .signature-area {
            text-align: center;
            width: 200px;
        }

        .signature-line {
            border-top: 1px solid #000;
            margin-top: 60px;
            padding-top: 5px;
        }

        /* Notification dengan tema terminal */
        .terminal-notification {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background-color: rgba(10, 25, 41, 0.95);
            border: 1px solid var(--accent);
            border-radius: 5px;
            padding: 15px 20px;
            display: flex;
            align-items: center;
            gap: 15px;
            transform: translateX(150%);
            transition: transform 0.5s;
            z-index: 10000;
            max-width: 350px;
            box-shadow: 0 0 20px rgba(0, 255, 153, 0.3);
        }

        .terminal-notification.show {
            transform: translateX(0);
        }

        .notification-icon {
            color: var(--accent);
            font-size: 1.5rem;
        }

        .notification-content h4 {
            color: var(--secondary);
            margin-bottom: 5px;
            font-family: 'Orbitron', sans-serif;
        }

        .notification-content p {
            color: var(--light);
            font-size: 0.9rem;
        }

        /* Footer dengan tema jaringan */
        footer {
            text-align: center;
            padding: 25px 0;
            margin-top: 40px;
            border-top: 1px solid var(--primary);
            color: var(--gray);
            font-size: 0.9rem;
            position: relative;
        }

        .footer-links {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin-top: 15px;
            flex-wrap: wrap;
        }

        .footer-link {
            color: var(--secondary);
            text-decoration: none;
            transition: color 0.3s;
            font-size: 0.85rem;
        }

        .footer-link:hover {
            color: var(--accent);
        }

        /* Responsif */
        @media (max-width: 768px) {
            header {
                flex-direction: column;
                align-items: flex-start;
                gap: 15px;
            }
            
            .network-status {
                align-self: flex-start;
            }
            
            .action-buttons {
                flex-direction: column;
            }
            
            .action-btn {
                justify-content: center;
            }
            
            .status-buttons {
                flex-direction: column;
            }
            
            .status-btn {
                width: 100%;
            }
            
            .print-options {
                grid-template-columns: 1fr;
            }
            
            .date-range {
                grid-template-columns: 1fr;
            }
            
            .terminal-body {
                max-height: 500px;
            }
        }
    </style>
</head>
<body>
    <div class="scanline"></div>
    
    <div class="container">
        <header>
            <div class="logo-area">
                <div class="server-icon">
                    <i class="fas fa-server"></i>
                </div>
                <div class="logo-text">
                    <h1>SMK PASUNDAN MALANGBONG</h1>
                    <p>SISTEM ABSENSI DIGITAL | KELAS XI TKJ 1 (28 SISWA)</p>
                </div>
            </div>
            
            <div class="network-status">
                <div class="status-indicator"></div>
                <div class="status-text">
                    <span>SERVER: ONLINE</span>
                    <span style="margin-left: 10px; color: var(--accent);">28 SISWA TERKONEKSI</span>
                </div>
            </div>
        </header>
        
        <div class="date-selector">
            <label for="date"><i class="fas fa-calendar-alt"></i> TANGGAL ABSENSI:</label>
            <input type="date" id="date" value="2026-01-01">
            <div style="color: var(--accent); font-family: 'Orbitron', sans-serif;">
                <i class="fas fa-clock"></i> <span id="live-clock">00:00:00</span>
            </div>
        </div>
        
        <div class="dashboard">
            <main>
                <div class="terminal-card">
                    <div class="terminal-header">
                        <div class="terminal-title">
                            <i class="fas fa-terminal"></i>
                            <span>DATA_KEHADIRAN_28_SISWA.exe</span>
                        </div>
                        <div class="terminal-controls">
                            <button class="control-btn minimize"></button>
                            <button class="control-btn maximize"></button>
                            <button class="control-btn close"></button>
                        </div>
                    </div>
                    
                    <div class="terminal-body">
                        <div class="table-container">
                            <table>
                                <thead>
                                    <tr>
                                        <th>NO</th>
                                        <th>NIS</th>
                                        <th>NAMA SISWA</th>
                                        <th>KELAS</th>
                                        <th>STATUS</th>
                                    </tr>
                                </thead>
                                <tbody id="student-table">
                                    <!-- Data 28 siswa akan diisi oleh JavaScript -->
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
                
                <div class="code-notes">
                    <div class="code-header">
                        <i class="fas fa-code"></i>
                        <span>CATATAN_GURU.txt</span>
                    </div>
                    <textarea id="teacher-notes" placeholder="// Tambahkan catatan untuk absensi hari ini...&#10;// Contoh: Beberapa siswa mengikuti lomba IT Network Systems Administration"></textarea>
                </div>
            </main>
            
            <aside class="system-sidebar">
                <div class="system-card">
                    <div class="system-header">
                        <i class="fas fa-chart-network"></i>
                        <span>STATISTIK KEHADIRAN</span>
                    </div>
                    <div class="system-body">
                        <div class="stats-container">
                            <div class="stat-item">
                                <div class="stat-label">
                                    <div class="stat-icon">
                                        <i class="fas fa-check-circle" style="color: var(--accent);"></i>
                                    </div>
                                    <span class="stat-name">HADIR</span>
                                </div>
                                <div class="stat-value" id="present-count">0</div>
                            </div>
                            
                            <div class="stat-item">
                                <div class="stat-label">
                                    <div class="stat-icon">
                                        <i class="fas fa-envelope" style="color: var(--warning);"></i>
                                    </div>
                                    <span class="stat-name">IZIN</span>
                                </div>
                                <div class="stat-value" id="permission-count">0</div>
                            </div>
                            
                            <div class="stat-item">
                                <div class="stat-label">
                                    <div class="stat-icon">
                                        <i class="fas fa-thermometer-half" style="color: var(--secondary);"></i>
                                    </div>
                                    <span class="stat-name">SAKIT</span>
                                </div>
                                <div class="stat-value" id="sick-count">0</div>
                            </div>
                            
                            <div class="stat-item">
                                <div class="stat-label">
                                    <div class="stat-icon">
                                        <i class="fas fa-times-circle" style="color: var(--danger);"></i>
                                    </div>
                                    <span class="stat-name">ALPA</span>
                                </div>
                                <div class="stat-value" id="absent-count">0</div>
                            </div>
                        </div>
                        
                        <div class="progress-container">
                            <div class="progress-label">
                                <span>Persentase Kehadiran</span>
                                <span id="attendance-percentage">0%</span>
                            </div>
                            <div class="progress-bar">
                                <div class="progress-fill" id="progress-fill" style="width: 0%"></div>
                            </div>
                        </div>
                        
                        <div style="margin-top: 20px; padding: 15px; background-color: rgba(5, 16, 33, 0.7); border-radius: 5px;">
                            <div style="display: flex; justify-content: space-between; margin-bottom: 8px;">
                                <span style="color: var(--gray);">Total Siswa</span>
                                <span style="color: var(--accent); font-family: 'Orbitron', sans-serif;" id="total-students">0</span>
                            </div>
                            <div style="display: flex; justify-content: space-between;">
                                <span style="color: var(--gray);">Kelas</span>
                                <span style="color: var(--secondary);">XI TKJ 1</span>
                            </div>
                        </div>
                    </div>
                </div>
                
                <div class="system-card">
                    <div class="system-header">
                        <i class="fas fa-network-wired"></i>
                        <span>SISTEM LOG</span>
                    </div>
                    <div class="system-body">
                        <div style="background-color: rgba(0, 0, 0, 0.7); border-radius: 5px; padding: 15px; font-size: 0.85rem; min-height: 200px; max-height: 200px; overflow-y: auto; font-family: 'JetBrains Mono', monospace;">
                            <div style="color: var(--accent); margin-bottom: 10px;">> Sistem Absensi TKJ v3.0</div>
                            <div style="color: var(--terminal); margin-bottom: 5px;">[10:15:23] Initializing attendance module...</div>
                            <div style="color: var(--terminal); margin-bottom: 5px;">[10:15:25] Connected to database server</div>
                            <div style="color: var(--terminal); margin-bottom: 5px;">[10:15:26] Loading 28 student data...</div>
                            <div style="color: var(--accent); margin-bottom: 5px;">[10:15:27] Ready to record attendance</div>
                            <div style="color: var(--terminal); margin-bottom: 5px;" id="log-entry">[<span id="log-time">10:15:30</span>] Waiting for input...</div>
                        </div>
                    </div>
                </div>
            </aside>
        </div>
        
        <div class="action-buttons">
            <button class="action-btn btn-reset" id="reset-btn">
                <i class="fas fa-redo-alt"></i> RESET DATA
            </button>
            <button class="action-btn btn-export" id="export-btn">
                <i class="fas fa-file-export"></i> EXPORT DATA
            </button>
            <button class="action-btn btn-print" id="print-btn">
                <i class="fas fa-print"></i> CETAK LAPORAN
            </button>
            <button class="action-btn btn-save" id="save-btn">
                <i class="fas fa-save"></i> SIMPAN KE DATABASE
            </button>
        </div>
        
        <footer>
            <p>ABSENSI SMK PASMAL | Teknik Komputer dan Jaringan | Kelas XI TKJ 1</p>
            <p>SMK PASUNDAN MALANGBONG | 28 Siswa Aktif</p>
            <div class="footer-links">
                <a href="#" class="footer-link"><i class="fas fa-network-wired"></i> Network Status</a>
                <a href="#" class="footer-link"><i class="fas fa-database"></i> Database Admin</a>
                <a href="#" class="footer-link"><i class="fas fa-user-shield"></i> Administrator</a>
                <a href="#" class="footer-link"><i class="fas fa-chart-line"></i> Statistics</a>
            </div>
        </footer>
    </div>
    
    <!-- Modal untuk cetak laporan -->
    <div class="print-modal" id="printModal">
        <div class="print-content">
            <div class="print-header">
                <h2><i class="fas fa-print"></i> CETAK LAPORAN KEHADIRAN</h2>
                <button class="print-close" id="closePrintModal">&times;</button>
            </div>
            <div class="print-body">
                <div class="print-options">
                    <div class="print-option active" data-type="daily">
                        <div class="print-icon">
                            <i class="fas fa-calendar-day"></i>
                        </div>
                        <h3>Laporan Harian</h3>
                        <p>Cetak laporan kehadiran untuk satu hari tertentu. Menampilkan daftar lengkap 28 siswa dengan status kehadiran.</p>
                    </div>
                    <div class="print-option" data-type="weekly">
                        <div class="print-icon">
                            <i class="fas fa-calendar-week"></i>
                        </div>
                        <h3>Laporan Mingguan</h3>
                        <p>Cetak laporan kehadiran untuk satu minggu. Menampilkan rekapitulasi kehadiran 28 siswa selama seminggu.</p>
                    </div>
                    <div class="print-option" data-type="monthly">
                        <div class="print-icon">
                            <i class="fas fa-calendar-alt"></i>
                        </div>
                        <h3>Laporan Bulanan</h3>
                        <p>Cetak laporan kehadiran untuk satu bulan. Menampilkan statistik dan analisis kehadiran bulanan 28 siswa.</p>
                    </div>
                </div>
                
                <div class="print-range">
                    <h3><i class="fas fa-calendar-check"></i> Pilih Periode Laporan</h3>
                    <div class="date-range">
                        <div class="date-input">
                            <label for="startDate">Tanggal Mulai</label>
                            <input type="date" id="startDate" value="2023-10-23">
                        </div>
                        <div class="date-input">
                            <label for="endDate">Tanggal Selesai</label>
                            <input type="date" id="endDate" value="2023-10-27">
                        </div>
                    </div>
                </div>
                
                <div class="print-actions">
                    <button class="print-action-btn btn-cancel" id="cancelPrint">
                        <i class="fas fa-times"></i> BATAL
                    </button>
                    <button class="print-action-btn btn-generate" id="generateReport">
                        <i class="fas fa-file-pdf"></i> GENERATE LAPORAN
                    </button>
                </div>
            </div>
        </div>
    </div>
    
    <!-- Area untuk laporan yang akan dicetak -->
    <div class="print-report" id="printReport">
        <!-- Laporan akan di-generate oleh JavaScript -->
    </div>
    
    <div class="terminal-notification" id="notification">
        <div class="notification-icon">
            <i class="fas fa-check-circle"></i>
        </div>
        <div class="notification-content">
            <h4>DATA TERSIMPAN</h4>
            <p id="notification-message">Absensi berhasil disimpan ke database.</p>
        </div>
    </div>

    <script>
        // Data 28 siswa untuk kelas TKJ
        const students = [
            { id: 1, nis: "30979740508", name: "Alpiansyah", class: "XI TKJ 1", status: "hadir" },
            { id: 2, nis: "0086981390", name: "Cahya Lesmana", class: "XI TKJ 1", status: "hadir" },
            { id: 3, nis: "3094396399", name: "Desi Listiani", class: "XI TKJ 1", status: "hadir" },
            { id: 4, nis: "3083573240", name: "Dini Aprianti", class: "XI TKJ 1", status: "hadir" },
            { id: 5, nis: "0093681550", name: "Dini Nur Eni", class: "XI TKJ 1", status: "hadir" },
            { id: 6, nis: "0084158547", name: "Fuji Irma Lestari", class: "XI TKJ 1", status: "hadir" },
            { id: 7, nis: "3087820464", name: "Idni Nurpatimah", class: "XI TKJ 1", status: "hadir" },
            { id: 8, nis: "3078484110", name: "Imam Maulana", class: "XI TKJ 1", status: "hadir" },
            { id: 9, nis: "0084666652", name: "Intan", class: "XI TKJ 1", status: "hadir" },
            { id: 10, nis: "0094190450", name: "Intan Nuraeni", class: "XI TKJ 1", status: "hadir" },
            { id: 11, nis: "0000000011", name: "Irpan", class: "XI TKJ 1", status: "hadir" },
            { id: 12, nis: "3085343047", name: "Lukman Nur Hakim", class: "XI TKJ 1", status: "hadir" },
            { id: 13, nis: "3092919877", name: "Lutfi Maulana Amrulloh", class: "XI TKJ 1", status: "hadir" },
            { id: 14, nis: "0088237246", name: "Nur Sandri Yani", class: "XI TKJ 1", status: "hadir" },
            { id: 15, nis: "0083852313", name: "Pricylla Carisa Putri Pratama", class: "XI TKJ 1", status: "hadir" },
            { id: 16, nis: "0084988732", name: "Raisa Rosdiananda", class: "XI TKJ 1", status: "hadir" },
            { id: 17, nis: "08835539164", name: "Renita Dwiyanti", class: "XI TKJ 1", status: "hadir" },
            { id: 18, nis: "3081137187", name: "Resti Ratnasari", class: "XI TKJ 1", status: "hadir" },
            { id: 19, nis: "3084410877", name: "Rehan Saepul Malik", class: "XI TKJ 1", status: "hadir" },
            { id: 20, nis: "3091520881", name: "Rizki Muhammad Taupik", class: "XI TKJ 1", status: "hadir" },
            { id: 21, nis: "3074669405", name: "Romi", class: "XI TKJ 1", status: "hadir" },
            { id: 22, nis: "0087965311", name: "Sahrul Anwar", class: "XI TKJ 1", status: "hadir" },
            { id: 23, nis: "3088088677", name: "Saripudin", class: "XI TKJ 1", status: "hadir" },
            { id: 24, nis: "3083496049", name: "Seni Nadianti", class: "XI TKJ 1", status: "hadir" },
            { id: 25, nis: "0077302858", name: "Solehudin", class: "XI TKJ 1", status: "hadir" },
            { id: 26, nis: "3089644545", name: "Solehudin Asmar", class: "XI TKJ 1", status: "hadir" },
            { id: 27, nis: "0089873120", name: "Susi Susilawati", class: "XI TKJ 1", status: "hadir" },
            { id: 28, nis: "0089760372", name: "Windri Asmawijaya", class: "XI TKJ 1", status: "hadir" }
        ];

        // Data kehadiran historis untuk laporan mingguan/bulanan
        const attendanceHistory = {
            "2023-10-23": JSON.parse(JSON.stringify(students.map(s => ({...s, status: Math.random() > 0.1 ? "hadir" : ["izin", "sakit", "alpa"][Math.floor(Math.random() * 3)]})))),
            "2023-10-24": JSON.parse(JSON.stringify(students.map(s => ({...s, status: Math.random() > 0.15 ? "hadir" : ["izin", "sakit", "alpa"][Math.floor(Math.random() * 3)]})))),
            "2023-10-25": JSON.parse(JSON.stringify(students.map(s => ({...s, status: Math.random() > 0.2 ? "hadir" : ["izin", "sakit", "alpa"][Math.floor(Math.random() * 3)]})))),
            "2023-10-26": JSON.parse(JSON.stringify(students)),
            "2023-10-27": JSON.parse(JSON.stringify(students.map(s => ({...s, status: Math.random() > 0.1 ? "hadir" : ["izin", "sakit", "alpa"][Math.floor(Math.random() * 3)]})))),
            "2023-10-30": JSON.parse(JSON.stringify(students.map(s => ({...s, status: Math.random() > 0.12 ? "hadir" : ["izin", "sakit", "alpa"][Math.floor(Math.random() * 3)]})))),
            "2023-10-31": JSON.parse(JSON.stringify(students.map(s => ({...s, status: Math.random() > 0.18 ? "hadir" : ["izin", "sakit", "alpa"][Math.floor(Math.random() * 3)]})))),
        };

        // Inisialisasi
        let attendanceData = JSON.parse(JSON.stringify(students));
        let currentDate = new Date().toISOString().split('T')[0];
        let selectedReportType = 'daily';
        
        // Set tanggal default
        document.getElementById('date').value = currentDate;
        
        // Fungsi untuk mengupdate jam live
        function updateLiveClock() {
            const now = new Date();
            const timeString = now.toLocaleTimeString('id-ID', { hour12: false });
            document.getElementById('live-clock').textContent = timeString;
            
            // Update log time
            const logTime = now.toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit', second: '2-digit' });
            document.getElementById('log-time').textContent = logTime;
        }
        
        // Update clock setiap detik
        setInterval(updateLiveClock, 1000);
        updateLiveClock(); // Panggil pertama kali
        
        // Render tabel siswa (28 siswa)
        function renderStudentTable() {
            const tableBody = document.getElementById('student-table');
            tableBody.innerHTML = '';
            
            attendanceData.forEach((student, index) => {
                const row = document.createElement('tr');
                
                row.innerHTML = `
                    <td>${index + 1}</td>
                    <td>${student.nis}</td>
                    <td>${student.name}</td>
                    <td>${student.class}</td>
                    <td>
                        <div class="status-buttons">
                            <button class="status-btn hadir ${student.status === 'hadir' ? 'active' : ''}" 
                                    data-id="${student.id}" data-status="hadir">
                                <i class="fas fa-check-circle"></i> Hadir
                            </button>
                            <button class="status-btn izin ${student.status === 'izin' ? 'active' : ''}" 
                                    data-id="${student.id}" data-status="izin">
                                <i class="fas fa-envelope"></i> Izin
                            </button>
                            <button class="status-btn sakit ${student.status === 'sakit' ? 'active' : ''}" 
                                    data-id="${student.id}" data-status="sakit">
                                <i class="fas fa-thermometer-half"></i> Sakit
                            </button>
                            <button class="status-btn alpa ${student.status === 'alpa' ? 'active' : ''}" 
                                    data-id="${student.id}" data-status="alpa">
                                <i class="fas fa-times-circle"></i> Alpa
                            </button>
                        </div>
                    </td>
                `;
                
                tableBody.appendChild(row);
            });
            
            // Tambahkan event listener untuk tombol status
            document.querySelectorAll('.status-btn').forEach(button => {
                button.addEventListener('click', function() {
                    const studentId = parseInt(this.getAttribute('data-id'));
                    const status = this.getAttribute('data-status');
                    
                    // Update status siswa
                    updateStudentStatus(studentId, status);
                    
                    // Update tampilan tombol aktif
                    const allButtons = this.parentElement.querySelectorAll('.status-btn');
                    allButtons.forEach(btn => btn.classList.remove('active'));
                    this.classList.add('active');
                    
                    // Update statistik
                    updateStatistics();
                    
                    // Tambahkan log
                    addLog(`Status siswa ID ${studentId} diubah: ${status.toUpperCase()}`);
                });
            });
            
            // Update statistik setelah render
            updateStatistics();
        }
        
        // Update status siswa
        function updateStudentStatus(studentId, status) {
            const studentIndex = attendanceData.findIndex(s => s.id === studentId);
            if (studentIndex !== -1) {
                attendanceData[studentIndex].status = status;
            }
        }
        
        // Update statistik kehadiran
        function updateStatistics() {
            const presentCount = attendanceData.filter(s => s.status === 'hadir').length;
            const permissionCount = attendanceData.filter(s => s.status === 'izin').length;
            const sickCount = attendanceData.filter(s => s.status === 'sakit').length;
            const absentCount = attendanceData.filter(s => s.status === 'alpa').length;
            const totalStudents = attendanceData.length;
            const attendancePercentage = totalStudents > 0 ? Math.round((presentCount / totalStudents) * 100) : 0;
            
            // Update elemen statistik
            document.getElementById('present-count').textContent = presentCount;
            document.getElementById('permission-count').textContent = permissionCount;
            document.getElementById('sick-count').textContent = sickCount;
            document.getElementById('absent-count').textContent = absentCount;
            document.getElementById('total-students').textContent = totalStudents;
            document.getElementById('attendance-percentage').textContent = `${attendancePercentage}%`;
            document.getElementById('progress-fill').style.width = `${attendancePercentage}%`;
            
            // Update warna progress bar berdasarkan persentase
            const progressFill = document.getElementById('progress-fill');
            if (attendancePercentage >= 90) {
                progressFill.style.background = 'linear-gradient(to right, var(--accent), #00cc66)';
            } else if (attendancePercentage >= 70) {
                progressFill.style.background = 'linear-gradient(to right, var(--warning), #ffcc00)';
            } else {
                progressFill.style.background = 'linear-gradient(to right, var(--danger), #ff3333)';
            }
        }
        
        // Tambahkan log ke sistem
        function addLog(message) {
            const now = new Date();
            const timeString = now.toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit', second: '2-digit' });
            const logEntry = document.getElementById('log-entry');
            
            // Buat log baru di atas log sebelumnya
            const newLog = document.createElement('div');
            newLog.style.color = 'var(--terminal)';
            newLog.style.marginBottom = '5px';
            newLog.innerHTML = `[${timeString}] ${message}`;
            
            logEntry.parentElement.insertBefore(newLog, logEntry);
            
            // Batasi jumlah log yang ditampilkan
            const logContainer = logEntry.parentElement;
            if (logContainer.children.length > 10) {
                logContainer.removeChild(logContainer.children[1]);
            }
        }
        
        // Tampilkan notifikasi
        function showNotification(title, message) {
            const notification = document.getElementById('notification');
            const notificationTitle = notification.querySelector('h4');
            const notificationMessage = document.getElementById('notification-message');
            
            notificationTitle.textContent = title;
            notificationMessage.textContent = message;
            
            notification.classList.add('show');
            
            setTimeout(() => {
                notification.classList.remove('show');
            }, 4000);
        }
        
        // Simpan data absensi
        document.getElementById('save-btn').addEventListener('click', function() {
            const selectedDate = document.getElementById('date').value;
            const teacherNotes = document.getElementById('teacher-notes').value;
            
            // Simulasi penyimpanan data
            const attendanceRecord = {
                date: selectedDate,
                students: attendanceData,
                notes: teacherNotes,
                summary: {
                    present: attendanceData.filter(s => s.status === 'hadir').length,
                    permission: attendanceData.filter(s => s.status === 'izin').length,
                    sick: attendanceData.filter(s => s.status === 'sakit').length,
                    absent: attendanceData.filter(s => s.status === 'alpa').length,
                    total: attendanceData.length
                }
            };
            
            // Simpan ke localStorage (simulasi database)
            localStorage.setItem(`attendance_${selectedDate}`, JSON.stringify(attendanceRecord));
            
            // Tampilkan notifikasi
            showNotification('DATA TERSIMPAN', `Absensi tanggal ${selectedDate} berhasil disimpan ke database.`);
            
            // Tambahkan log
            addLog(`Data absensi disimpan untuk tanggal: ${selectedDate}`);
        });
        
        // Reset data
        document.getElementById('reset-btn').addEventListener('click', function() {
            if (confirm("Reset semua status kehadiran 28 siswa ke 'HADIR'?")) {
                attendanceData.forEach(student => {
                    student.status = 'hadir';
                });
                
                renderStudentTable();
                showNotification('DATA DIREFRESH', 'Semua status kehadiran 28 siswa telah direset ke HADIR.');
                addLog('Semua data kehadiran 28 siswa direset ke status default.');
            }
        });
        
        // Export data
        document.getElementById('export-btn').addEventListener('click', function() {
            const selectedDate = document.getElementById('date').value;
            const teacherNotes = document.getElementById('teacher-notes').value;
            
            // Format data untuk ekspor
            const exportData = {
                date: selectedDate,
                class: "XI TKJ 1",
                total_students: 28,
                students: attendanceData,
                notes: teacherNotes
            };
            
            // Buat blob dan download
            const dataStr = JSON.stringify(exportData, null, 2);
            const dataBlob = new Blob([dataStr], { type: 'application/json' });
            const url = URL.createObjectURL(dataBlob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `absensi_tkj_28siswa_${selectedDate}.json`;
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
            
            showNotification('DATA DIEXPORT', `File absensi 28 siswa berhasil diunduh.`);
            addLog(`Data absensi 28 siswa diexport ke file JSON.`);
        });
        
        // Event listener untuk perubahan tanggal
        document.getElementById('date').addEventListener('change', function() {
            const selectedDate = this.value;
            
            // Coba muat data yang tersimpan untuk tanggal tersebut
            const savedData = localStorage.getItem(`attendance_${selectedDate}`);
            
            if (savedData) {
                const attendanceRecord = JSON.parse(savedData);
                attendanceData = attendanceRecord.students;
                document.getElementById('teacher-notes').value = attendanceRecord.notes || '';
                
                showNotification('DATA DIMUAT', `Data absensi tanggal ${selectedDate} berhasil dimuat.`);
                addLog(`Data absensi tanggal ${selectedDate} dimuat dari penyimpanan.`);
            } else {
                // Reset ke data default jika tidak ada data tersimpan
                attendanceData = JSON.parse(JSON.stringify(students));
                document.getElementById('teacher-notes').value = '';
                
                showNotification('DATA BARU', `Membuat data absensi baru untuk tanggal ${selectedDate}.`);
                addLog(`Data absensi baru dibuat untuk tanggal: ${selectedDate}`);
            }
            
            renderStudentTable();
        });
        
        // FITUR CETAK LAPORAN
        // Buka modal cetak laporan
        document.getElementById('print-btn').addEventListener('click', function() {
            document.getElementById('printModal').classList.add('active');
            addLog('Membuka menu cetak laporan.');
        });
        
        // Tutup modal cetak laporan
        document.getElementById('closePrintModal').addEventListener('click', function() {
            document.getElementById('printModal').classList.remove('active');
        });
        
        document.getElementById('cancelPrint').addEventListener('click', function() {
            document.getElementById('printModal').classList.remove('active');
        });
        
        // Pilih jenis laporan
        document.querySelectorAll('.print-option').forEach(option => {
            option.addEventListener('click', function() {
                // Hapus active dari semua option
                document.querySelectorAll('.print-option').forEach(opt => {
                    opt.classList.remove('active');
                });
                
                // Tambahkan active ke option yang diklik
                this.classList.add('active');
                selectedReportType = this.getAttribute('data-type');
                addLog(`Jenis laporan dipilih: ${selectedReportType}`);
            });
        });
        
        // Generate dan cetak laporan
        document.getElementById('generateReport').addEventListener('click', function() {
            const startDate = document.getElementById('startDate').value;
            const endDate = document.getElementById('endDate').value;
            
            if (!startDate || !endDate) {
                showNotification('PERINGATAN', 'Harap pilih tanggal mulai dan selesai.');
                return;
            }
            
            if (new Date(startDate) > new Date(endDate)) {
                showNotification('PERINGATAN', 'Tanggal mulai tidak boleh lebih besar dari tanggal selesai.');
                return;
            }
            
            // Generate laporan
            generatePrintReport(startDate, endDate, selectedReportType);
            
            // Tutup modal
            document.getElementById('printModal').classList.remove('active');
            
            // Tunggu sebentar lalu cetak
            setTimeout(() => {
                window.print();
            }, 500);
            
            showNotification('LAPORAN DIHASILKAN', `Laporan ${selectedReportType} berhasil digenerate.`);
            addLog(`Laporan ${selectedReportType} digenerate untuk periode ${startDate} sampai ${endDate}.`);
        });
        
        // Fungsi untuk generate laporan cetak
        function generatePrintReport(startDate, endDate, type) {
            const reportElement = document.getElementById('printReport');
            
            // Format tanggal Indonesia
            const formatDate = (dateString) => {
                const date = new Date(dateString);
                return date.toLocaleDateString('id-ID', { 
                    weekday: 'long', 
                    year: 'numeric', 
                    month: 'long', 
                    day: 'numeric' 
                });
            };
            
            // Hitung statistik berdasarkan tipe laporan
            let reportTitle, reportData, summaryStats;
            
            if (type === 'daily') {
                reportTitle = `LAPORAN KEHADIRAN HARIAN - ${formatDate(startDate)}`;
                reportData = attendanceData;
                
                // Statistik harian
                const present = reportData.filter(s => s.status === 'hadir').length;
                const permission = reportData.filter(s => s.status === 'izin').length;
                const sick = reportData.filter(s => s.status === 'sakit').length;
                const absent = reportData.filter(s => s.status === 'alpa').length;
                const total = reportData.length;
                const percentage = total > 0 ? Math.round((present / total) * 100) : 0;
                
                summaryStats = {
                    present, permission, sick, absent, total, percentage
                };
            } else if (type === 'weekly' || type === 'monthly') {
                // Hitung statistik untuk periode
                let datesInRange = [];
                let currentDate = new Date(startDate);
                const endDateObj = new Date(endDate);
                
                // Generate semua tanggal dalam range
                while (currentDate <= endDateObj) {
                    datesInRange.push(currentDate.toISOString().split('T')[0]);
                    currentDate.setDate(currentDate.getDate() + 1);
                }
                
                // Kumpulkan data untuk semua tanggal dalam range
                let allAttendance = [];
                let dailyStats = [];
                
                datesInRange.forEach(date => {
                    let dayData = attendanceHistory[date] || JSON.parse(JSON.stringify(students.map(s => ({...s, status: 'alpa'}))));
                    
                    const dayPresent = dayData.filter(s => s.status === 'hadir').length;
                    const dayPermission = dayData.filter(s => s.status === 'izin').length;
                    const daySick = dayData.filter(s => s.status === 'sakit').length;
                    const dayAbsent = dayData.filter(s => s.status === 'alpa').length;
                    const dayTotal = dayData.length;
                    
                    dailyStats.push({
                        date,
                        present: dayPresent,
                        permission: dayPermission,
                        sick: daySick,
                        absent: dayAbsent,
                        total: dayTotal,
                        percentage: dayTotal > 0 ? Math.round((dayPresent / dayTotal) * 100) : 0
                    });
                    
                    allAttendance.push(...dayData);
                });
                
                // Hitung rata-rata dan total
                const totalPresent = dailyStats.reduce((sum, day) => sum + day.present, 0);
                const totalPermission = dailyStats.reduce((sum, day) => sum + day.permission, 0);
                const totalSick = dailyStats.reduce((sum, day) => sum + day.sick, 0);
                const totalAbsent = dailyStats.reduce((sum, day) => sum + day.absent, 0);
                const totalDays = datesInRange.length;
                const avgPercentage = totalDays > 0 ? Math.round(dailyStats.reduce((sum, day) => sum + day.percentage, 0) / totalDays) : 0;
                
                reportTitle = type === 'weekly' 
                    ? `LAPORAN KEHADIRAN MINGGUAN (${formatDate(startDate)} - ${formatDate(endDate)})`
                    : `LAPORAN KEHADIRAN BULANAN (${formatDate(startDate)} - ${formatDate(endDate)})`;
                
                reportData = allAttendance;
                summaryStats = {
                    present: totalPresent,
                    permission: totalPermission,
                    sick: totalSick,
                    absent: totalAbsent,
                    total: totalDays * students.length,
                    percentage: avgPercentage,
                    dailyStats: dailyStats
                };
            }
            
            // Generate HTML untuk laporan
            let reportHTML = `
                <div class="report-header">
                    <h1>${reportTitle}</h1>
                    <h2>KELAS XI TEKNIK KOMPUTER DAN JARINGAN 1 (28 SISWA)</h2>
                    <p>SMK NEGERI 1 TEKNOLOGI INFORMASI</p>
                    <p>Periode: ${formatDate(startDate)} ${type !== 'daily' ? `s/d ${formatDate(endDate)}` : ''}</p>
                    <p>Total Siswa: 28 Orang</p>
                </div>
                
                <div class="report-meta">
                    <div>
                        <p><strong>Tanggal Cetak:</strong> ${new Date().toLocaleDateString('id-ID', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' })}</p>
                        <p><strong>Waktu Cetak:</strong> ${new Date().toLocaleTimeString('id-ID')}</p>
                    </div>
                    <div>
                        <p><strong>Total Siswa:</strong> 28</p>
                        <p><strong>Jenis Laporan:</strong> ${type === 'daily' ? 'Harian' : type === 'weekly' ? 'Mingguan' : 'Bulanan'}</p>
                    </div>
                </div>
                
                <div class="report-summary">
                    <div class="summary-item summary-hadir">
                        <h3>HADIR</h3>
                        <p style="font-size: 24px; font-weight: bold; color: #006600;">${summaryStats.present}</p>
                        <p>Siswa (${summaryStats.total > 0 ? Math.round((summaryStats.present/summaryStats.total)*100) : 0}%)</p>
                    </div>
                    <div class="summary-item summary-izin">
                        <h3>IZIN</h3>
                        <p style="font-size: 24px; font-weight: bold; color: #cc9900;">${summaryStats.permission}</p>
                        <p>Siswa (${summaryStats.total > 0 ? Math.round((summaryStats.permission/summaryStats.total)*100) : 0}%)</p>
                    </div>
                    <div class="summary-item summary-sakit">
                        <h3>SAKIT</h3>
                        <p style="font-size: 24px; font-weight: bold; color: #0066cc;">${summaryStats.sick}</p>
                        <p>Siswa (${summaryStats.total > 0 ? Math.round((summaryStats.sick/summaryStats.total)*100) : 0}%)</p>
                    </div>
                    <div class="summary-item summary-alpa">
                        <h3>ALPA</h3>
                        <p style="font-size: 24px; font-weight: bold; color: #cc0000;">${summaryStats.absent}</p>
                        <p>Siswa (${summaryStats.total > 0 ? Math.round((summaryStats.absent/summaryStats.total)*100) : 0}%)</p>
                    </div>
                </div>
                
                <div style="text-align: center; margin: 20px 0;">
                    <h3 style="color: #0066cc;">PERSENTASE KEHADIRAN: ${summaryStats.percentage}%</h3>
                    <div style="background-color: #f0f0f0; height: 20px; border-radius: 10px; margin: 10px auto; max-width: 400px;">
                        <div style="background-color: ${summaryStats.percentage >= 90 ? '#00cc66' : summaryStats.percentage >= 70 ? '#ffcc00' : '#ff3333'}; 
                                    height: 100%; width: ${summaryStats.percentage}%; border-radius: 10px;"></div>
                    </div>
                </div>
            `;
            
            // Tambahkan tabel statistik harian untuk laporan mingguan/bulanan
            if (type !== 'daily' && summaryStats.dailyStats) {
                reportHTML += `
                    <h3 style="color: #0066cc; margin: 30px 0 15px 0;">STATISTIK HARIAN (${summaryStats.dailyStats.length} Hari)</h3>
                    <table class="report-table">
                        <thead>
                            <tr>
                                <th>Tanggal</th>
                                <th>Hadir</th>
                                <th>Izin</th>
                                <th>Sakit</th>
                                <th>Alpa</th>
                                <th>Total</th>
                                <th>Presentase</th>
                            </tr>
                        </thead>
                        <tbody>
                `;
                
                summaryStats.dailyStats.forEach(day => {
                    const date = new Date(day.date);
                    const formattedDate = date.toLocaleDateString('id-ID', { day: 'numeric', month: 'short', year: 'numeric' });
                    reportHTML += `
                        <tr>
                            <td>${formattedDate}</td>
                            <td>${day.present}</td>
                            <td>${day.permission}</td>
                            <td>${day.sick}</td>
                            <td>${day.absent}</td>
                            <td>${day.total}</td>
                            <td>${day.percentage}%</td>
                        </tr>
                    `;
                });
                
                // Tambahkan baris total
                reportHTML += `
                        <tr style="font-weight: bold; background-color: #f0f0f0;">
                            <td>TOTAL / RATA-RATA</td>
                            <td>${summaryStats.present}</td>
                            <td>${summaryStats.permission}</td>
                            <td>${summaryStats.sick}</td>
                            <td>${summaryStats.absent}</td>
                            <td>${summaryStats.total}</td>
                            <td>${summaryStats.percentage}%</td>
                        </tr>
                    </tbody>
                </table>
                `;
            }
            
            // Tambahkan tabel detail 28 siswa
            reportHTML += `
                <h3 style="color: #0066cc; margin: 30px 0 15px 0;">DATA DETAIL 28 SISWA</h3>
                <table class="report-table">
                    <thead>
                        <tr>
                            <th>No</th>
                            <th>NIS</th>
                            <th>Nama Siswa</th>
                            <th>Kelas</th>
                            <th>Status</th>
                        </tr>
                    </thead>
                    <tbody>
            `;
            
            // Untuk laporan harian, gunakan data saat ini
            // Untuk laporan periodik, hitung status paling sering
            if (type === 'daily') {
                attendanceData.forEach((student, index) => {
                    const statusText = student.status === 'hadir' ? 'Hadir' : 
                                     student.status === 'izin' ? 'Izin' : 
                                     student.status === 'sakit' ? 'Sakit' : 'Alpa';
                    
                    const statusColor = student.status === 'hadir' ? '#006600' : 
                                      student.status === 'izin' ? '#cc9900' : 
                                      student.status === 'sakit' ? '#0066cc' : '#cc0000';
                    
                    reportHTML += `
                        <tr>
                            <td>${index + 1}</td>
                            <td>${student.nis}</td>
                            <td>${student.name}</td>
                            <td>${student.class}</td>
                            <td style="color: ${statusColor}; font-weight: bold;">${statusText}</td>
                        </tr>
                    `;
                });
            } else {
                // Untuk laporan periodik, hitung status dominan per siswa
                students.forEach((student, index) => {
                    // Hitung frekuensi status untuk siswa ini
                    const statusCount = { hadir: 0, izin: 0, sakit: 0, alpa: 0 };
                    
                    // Cek data historis untuk setiap tanggal
                    Object.keys(attendanceHistory).forEach(date => {
                        if (date >= startDate && date <= endDate) {
                            const dayData = attendanceHistory[date];
                            const studentData = dayData.find(s => s.id === student.id);
                            if (studentData) {
                                statusCount[studentData.status]++;
                            }
                        }
                    });
                    
                    // Tentukan status dominan
                    let dominantStatus = 'hadir';
                    let maxCount = 0;
                    
                    Object.keys(statusCount).forEach(status => {
                        if (statusCount[status] > maxCount) {
                            maxCount = statusCount[status];
                            dominantStatus = status;
                        }
                    });
                    
                    const statusText = dominantStatus === 'hadir' ? 'Hadir' : 
                                     dominantStatus === 'izin' ? 'Izin' : 
                                     dominantStatus === 'sakit' ? 'Sakit' : 'Alpa';
                    
                    const statusColor = dominantStatus === 'hadir' ? '#006600' : 
                                      dominantStatus === 'izin' ? '#cc9900' : 
                                      dominantStatus === 'sakit' ? '#0066cc' : '#cc0000';
                    
                    // Hitung persentase kehadiran per siswa
                    const totalDays = Object.values(statusCount).reduce((a, b) => a + b, 0);
                    const presentPercentage = totalDays > 0 ? Math.round((statusCount.hadir / totalDays) * 100) : 0;
                    
                    reportHTML += `
                        <tr>
                            <td>${index + 1}</td>
                            <td>${student.nis}</td>
                            <td>${student.name}</td>
                            <td>${student.class}</td>
                            <td style="color: ${statusColor}; font-weight: bold;">${statusText} (${presentPercentage}%)</td>
                        </tr>
                    `;
                });
            }
            
            reportHTML += `
                    </tbody>
                </table>
                
                <div class="report-footer">
                    <div class="signature-area">
                        <p>Mengetahui,</p>
                        <p>Kepala Sekolah</p>
                        <div class="signature-line"></div>
                        <p><strong>Lutfi Lintang Karahinan, S.Pd.</strong></p>
                        <p>NIP. </p>
                    </div>
                    <div class="signature-area">
                        <p>Wali Kelas XI TKJ 3</p>
                        <div class="signature-line"></div>
                        <p><strong>Teti Syamsiah, S.Pd.</strong></p>
                        <p>NIP. </p>
                    </div>
                    <div class="signature-area">
                        <p>Yang Mencetak,</p>
                        <div class="signature-line"></div>
                        <p><strong>Administrator Sistem</strong></p>
                        <p>${new Date().toLocaleDateString('id-ID')}</p>
                    </div>
                </div>
                
                <div style="text-align: center; margin-top: 30px; font-size: 12px; color: #666;">
                    <p>Dicetak dari Sistem Absensi TKJ v3.0 | Kelas XI TKJ 1 | ${window.location.href}</p>
                    <p>Halaman ini dicetak secara otomatis oleh sistem.</p>
                </div>
            `;
            
            reportElement.innerHTML = reportHTML;
            reportElement.style.display = 'block';
        }
        
        // Inisialisasi aplikasi
        renderStudentTable();
        addLog('Sistem absensi TKJ v3.0 dengan 28 siswa siap digunakan.');
    </script>
</body>
</html>
