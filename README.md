# Kallichore
web for kallichore group 
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kallichore - Modular Rank System</title>
    <style>
        :root {
            --accent: #00ffcc;
            --bg: #05080a;
            --panel-bg: #0f172a;
        }

        body {
            font-family: 'Segoe UI', sans-serif;
            background-color: var(--bg);
            color: white;
            margin: 0;
            padding: 20px;
        }

        .dashboard {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 20px;
            max-width: 1300px;
            margin: auto;
        }

        .panel {
            background: var(--panel-bg);
            border: 1px solid rgba(255,255,255,0.1);
            border-radius: 15px;
            padding: 20px;
        }

        .panel-title {
            font-size: 0.8rem;
            text-transform: uppercase;
            letter-spacing: 2px;
            color: var(--accent);
            margin-bottom: 15px;
            display: flex;
            align-items: center;
        }

        input, select {
            width: 100%;
            padding: 10px;
            background: #1e293b;
            border: 1px solid #334155;
            color: white;
            border-radius: 8px;
            margin-bottom: 10px;
            box-sizing: border-box;
        }

        /* Tombol Daftar */
        .btn-register {
            width: 100%;
            padding: 15px;
            background: var(--accent);
            color: #000;
            border: none;
            border-radius: 8px;
            font-weight: bold;
            cursor: pointer;
            text-transform: uppercase;
            margin-top: 10px;
            transition: 0.3s;
        }

        .btn-register:hover { opacity: 0.8; transform: scale(1.02); }

        /* Preview Utama */
        .main-preview {
            grid-column: span 3;
            background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%);
            display: flex;
            align-items: center;
            justify-content: space-around;
            padding: 30px;
            border-radius: 20px;
            border-bottom: 4px solid var(--accent);
        }

        #v-flag { width: 250px; height: 140px; object-fit: cover; border: 3px solid #fff; border-radius: 8px; }

        /* Tabel Ranking */
        .ranking-system {
            grid-column: span 3;
            margin-top: 20px;
            background: var(--panel-bg);
            border-radius: 20px;
            padding: 25px;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }

        th { text-align: left; color: var(--accent); border-bottom: 2px solid #334155; padding: 10px; }
        td { padding: 15px 10px; border-bottom: 1px solid rgba(255,255,255,0.05); }

        .rank-number { font-weight: bold; color: var(--accent); font-size: 1.2rem; }
        .mini-flag { width: 50px; height: 30px; object-fit: cover; border-radius: 3px; vertical-align: middle; margin-right: 10px; }

        @media (max-width: 900px) { .dashboard { grid-template-columns: 1fr; } .main-preview { grid-column: span 1; flex-direction: column; } .ranking-system { grid-column: span 1; } }
    </style>
</head>
<body>

<h1 style="text-align: center; letter-spacing: 5px;">KALLICHORE <span style="color:var(--accent)">OS</span></h1>

<div class="dashboard">
    
    <div class="panel">
        <div class="panel-title">Modul Identitas</div>
        <label>Nama Negara</label>
        <input type="text" id="in-nama" value="Kallichore">
        <label>Nama Ideologi</label>
        <input type="text" id="in-ideo" value="Techno-Democracy">
    </div>

    <div class="panel">
        <div class="panel-title">Modul Visual</div>
        <label>Upload Bendera</label>
        <input type="file" id="up-file" accept="image/*" onchange="previewFile(event)">
        <label>Tema Warna</label>
        <input type="color" id="in-color" value="#00ffcc" oninput="applyTheme()">
    </div>

    <div class="panel">
        <div class="panel-title">Modul Ekonomi & Militer</div>
        <label>Populasi</label>
        <input type="number" id="in-pop" value="1000000">
        <label>Militer</label>
        <input type="number" id="in-mil" value="50000">
        <button class="btn-register" onclick="addToRank()">Daftarkan ke Ranking</button>
    </div>

    <div class="main-preview">
        <img id="v-flag" src="https://via.placeholder.com/250x140/111/fff?text=No+Flag">
        <div style="text-align: center;">
            <h1 id="v-nama" style="margin:0; font-size: 2.5rem;">COUNTRY</h1>
            <div id="v-ideo" style="color: var(--accent); font-weight: bold;">IDEOLOGY</div>
        </div>
        <div style="text-align: center;">
            <div style="font-size: 0.7rem; opacity: 0.6;">POWER RANK</div>
            <div id="v-rank" style="font-size: 80px; font-weight: 900; color: var(--accent);">?</div>
        </div>
    </div>

    <div class="ranking-system">
        <div class="panel-title" style="font-size: 1.2rem;">Sistem Ranking Global</div>
        <p id="empty-msg" style="opacity: 0.5;">Belum ada negara yang terdaftar. Gunakan modul di atas untuk mendaftar.</p>
        
        <table id="rank-table" style="display: none;">
            <thead>
                <tr>
                    <th>Pos</th>
                    <th>Negara</th>
                    <th>Ideologi</th>
                    <th>Militer</th>
                    <th>Skor Kekuatan</th>
                </tr>
            </thead>
            <tbody id="rank-body">
                </tbody>
        </table>
    </div>

</div>

<script>
    let globalRankData = [];
    let currentFlagBase64 = "https://via.placeholder.com/250x140/111/fff?text=No+Flag";

    // Update Tema & Preview Teks Otomatis
    function applyTheme() {
        const color = document.getElementById('in-color').value;
        document.documentElement.style.setProperty('--accent', color);
        
        // Update live preview sederhana
        document.getElementById('v-nama').innerText = document.getElementById('in-nama').value.toUpperCase();
        document.getElementById('v-ideo').innerText = document.getElementById('in-ideo').value.toUpperCase();
    }

    // Handle Image Upload
    function previewFile(event) {
        const reader = new FileReader();
        reader.onload = function() {
            currentFlagBase64 = reader.result;
            document.getElementById('v-flag').src = currentFlagBase64;
        }
        reader.readAsDataURL(event.target.files[0]);
    }

    // Fungsi Utama: Tambah ke Ranking
    function addToRank() {
        const nama = document.getElementById('in-nama').value;
        const ideo = document.getElementById('in-ideo').value;
        const pop = parseFloat(document.getElementById('in-pop').value) || 1;
        const mil = parseFloat(document.getElementById('in-mil').value) || 0;

        // Hitung Skor Kekuatan (Simulasi)
        const powerScore = ((mil / pop) * 1000).toFixed(0);
        
        // Tentukan Label Rank
        let rankLabel = "D";
        if(powerScore > 80) rankLabel = "S";
        else if(powerScore > 50) rankLabel = "A";
        else if(powerScore > 30) rankLabel = "B";

        document.getElementById('v-rank').innerText = rankLabel;

        // Masukkan ke Array Data
        globalRankData.push({
            nama: nama,
            ideo: ideo,
            mil: mil,
            score: parseInt(powerScore),
            flag: currentFlagBase64
        });

        // Urutkan berdasarkan skor tertinggi
        globalRankData.sort((a, b) => b.score - a.score);

        renderTable();
    }

    // Update Tampilan Tabel
    function renderTable() {
        const table = document.getElementById('rank-table');
        const body = document.getElementById('rank-body');
        const emptyMsg = document.getElementById('empty-msg');

        emptyMsg.style.display = "none";
        table.style.display = "table";
        body.innerHTML = "";

        globalRankData.forEach((data, index) => {
            const row = `
                <tr>
                    <td class="rank-number">#${index + 1}</td>
                    <td><img src="${data.flag}" class="mini-flag"> ${data.nama}</td>
                    <td>${data.ideo}</td>
                    <td>${data.mil.toLocaleString()} Personel</td>
                    <td style="font-weight:bold; color:var(--accent)">${data.score} Pts</td>
                </tr>
            `;
            body.innerHTML += row;
        });
        
        applyTheme(); // Refresh warna aksen pada elemen baru
    }

    // Start Up
    setInterval(applyTheme, 500); // Sinkronisasi teks preview setiap 0.5 detik
</script>

</body>
</html>
