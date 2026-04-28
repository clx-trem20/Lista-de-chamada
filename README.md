<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Chamada Profissional - CLX</title>
    <!-- Biblioteca para exportar Excel -->
    <script src="https://cdn.sheetjs.com/xlsx-0.20.1/package/dist/xlsx.full.min.js"></script>
    <style>
        :root {
            --bg-color: #0f172a;
            --card-bg: #1e293b;
            --accent-blue: #38bdf8;
            --accent-green: #22c55e;
            --accent-red: #ef4444;
            --accent-purple: #a855f7;
            --text-main: #f8fafc;
            --text-dim: #94a3b8;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-main);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            overflow-x: hidden;
        }

        /* --- LOADING OVERLAY --- */
        #loading-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: var(--bg-color);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 3000;
        }

        /* --- TELA DE BLOQUEIO --- */
        #lock-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(135deg, #0f172a 0%, #1e1b4b 100%);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }

        #lock-screen.hidden {
            display: none;
        }

        .login-card {
            background: rgba(30, 41, 59, 0.7);
            backdrop-filter: blur(10px);
            padding: 40px;
            border-radius: 20px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
            width: 90%;
            max-width: 400px;
            text-align: center;
        }

        .input-field {
            width: 100%;
            padding: 14px;
            margin-bottom: 16px;
            background: rgba(15, 23, 42, 0.6);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 12px;
            color: white;
            font-size: 1rem;
            outline: none;
        }

        .btn-login {
            width: 100%;
            padding: 14px;
            background: var(--accent-blue);
            color: #0f172a;
            border: none;
            border-radius: 12px;
            font-weight: 700;
            font-size: 1rem;
            cursor: pointer;
        }

        /* --- SISTEMA --- */
        #app-content {
            display: none;
            width: 100%;
            max-width: 1100px;
            padding: 20px;
        }

        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 30px;
            flex-wrap: wrap;
            gap: 20px;
        }

        .nav-buttons {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
        }

        .btn-action {
            background: var(--card-bg);
            border: 1px solid rgba(255,255,255,0.1);
            color: var(--text-main);
            padding: 10px 18px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 0.85rem;
            transition: all 0.2s;
        }

        .btn-action:hover {
            background: #334155;
        }

        .section-container {
            background: var(--card-bg);
            padding: 25px;
            border-radius: 15px;
            margin-bottom: 20px;
        }

        .input-group {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
        }

        .input-group input, .input-group textarea {
            flex: 1;
            padding: 12px;
            background: var(--bg-color);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 8px;
            color: white;
            outline: none;
        }

        /* BARRA DE PESQUISA */
        .search-container {
            margin-bottom: 15px;
            position: relative;
        }

        .search-input {
            width: 100%;
            padding: 12px 40px 12px 15px;
            background: #cbd5e1;
            border: none;
            border-radius: 10px;
            color: #0f172a;
            font-weight: 600;
            outline: none;
        }

        .btn-save {
            background: var(--accent-green);
            color: white;
            border: none;
            padding: 12px 20px;
            border-radius: 8px;
            cursor: pointer;
            font-weight: 600;
        }

        .list-container {
            background: #e2e8f0; 
            border-radius: 15px;
            overflow: hidden;
        }

        table { width: 100%; border-collapse: collapse; }
        th { background: #cbd5e1; text-align: left; padding: 15px; color: #1e293b; font-weight: bold; }
        
        td { 
            padding: 15px; 
            border-bottom: 1px solid rgba(0, 0, 0, 0.1);
            color: #000000 !important; 
            font-size: 1rem;
            font-weight: 500;
        }

        .badge { padding: 6px 12px; border-radius: 20px; cursor: pointer; border: none; font-weight: 600; }
        .badge-present { background: #22c55e; color: white; }
        .badge-absent { background: #ef4444; color: white; }

        .btn-delete { color: #b91c1c; background: #fee2e2; border: 1px solid #f87171; padding: 5px 10px; border-radius: 6px; cursor: pointer; }

        /* --- CONTADOR --- */
        .counter-badge {
            background: var(--accent-blue);
            color: #0f172a;
            padding: 4px 12px;
            border-radius: 8px;
            font-weight: 800;
            font-size: 0.9rem;
            margin-left: 10px;
        }

        /* --- HISTÓRICO CARDS --- */
        .history-card {
            background: #334155;
            border-radius: 12px;
            padding: 15px;
            margin-bottom: 10px;
            cursor: pointer;
            border-left: 5px solid var(--accent-purple);
            display: flex;
            justify-content: space-between;
            align-items: center;
            transition: transform 0.1s;
        }

        .history-card:hover {
            transform: scale(1.01);
            background: #475569;
        }

        /* --- PLANILHA OVERLAY --- */
        #sheet-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: var(--bg-color);
            z-index: 2000;
            display: none;
            padding: 40px;
            overflow-y: auto;
        }

        .sheet-content { 
            max-width: 800px; 
            margin: 0 auto; 
            background: white; 
            color: black; 
            padding: 40px; 
            border-radius: 5px; 
        }
        
        .sheet-table { width: 100%; border-collapse: collapse; margin-top: 20px; border: 1px solid #ddd; }
        
        .sheet-table th, .sheet-table td { 
            border: 1px solid #ddd; 
            padding: 10px; 
            color: #000 !important; 
            text-align: left;
        }
        
        .sheet-table th {
            background: #f1f5f9;
            font-weight: bold;
        }

        @media print {
            #sheet-overlay { position: static; background: white; padding: 0; }
            .btn-action, button, .search-container, .nav-buttons { display: none; }
        }
    </style>
</head>
<body>

    <div id="loading-overlay">A carregar o Banco de Dados...</div>

    <div id="lock-screen">
        <div class="login-card">
            <h2>Chamada CLX</h2><br>
            <input type="text" id="user-input" class="input-field" placeholder="Usuário">
            <input type="password" id="pass-input" class="input-field" placeholder="Senha">
            <button class="btn-login" onclick="validateAccess()">DESBLOQUEAR</button>
            <div id="login-error" style="color:red; display:none; margin-top:10px;">Acesso Negado</div>
        </div>
    </div>

    <main id="app-content">
        <header class="header">
            <h1 id="welcome-title">Chamada CLX</h1>
            <div class="nav-buttons">
                <button class="btn-action" onclick="showSection('attendance')">📋 Chamada</button>
                <button class="btn-action" onclick="showSection('history')">📅 Histórico</button>
                <button class="btn-action" onclick="toggleSheet(true)">📑 Planilha Atual</button>
                <button class="btn-action" onclick="downloadBackup()">📥 Baixar JSON</button>
                <button class="btn-action" onclick="document.getElementById('import-file').click()">📤 Carregar JSON</button>
                <input type="file" id="import-file" style="display:none;" accept=".json" onchange="importBackup(event)">
                
                <!-- Botão de acesso controlado por JS -->
                <button id="nav-btn-users" class="btn-action" style="display:none" onclick="showSection('users')">🔑 Acessos (Admin)</button>
                
                <button class="btn-action" style="color:var(--accent-red)" onclick="location.reload()">Sair</button>
            </div>
        </header>

        <!-- SEÇÃO CHAMADA -->
        <div id="section-attendance">
            <div class="section-container">
                <div style="display:flex; justify-content: space-between; align-items: flex-start; margin-bottom: 20px; flex-wrap: wrap; gap: 10px;">
                    <h3>Gerir Alunos</h3>
                    <div style="display:flex; gap:10px">
                        <button class="btn-save" style="background: var(--accent-red);" onclick="absentAllStudents()">🚫 DAR FALTA GERAL</button>
                        <button class="btn-save" style="background: var(--accent-purple);" onclick="saveDailyCall()">💾 SALVAR CHAMADA DO DIA</button>
                    </div>
                </div>
                <div class="input-group" style="flex-direction: column;">
                    <textarea id="bulk-names" placeholder="Nomes por linha..."></textarea>
                    <button class="btn-save" onclick="addBulkStudents()">Salvar Lista de Alunos</button>
                </div>
                <div class="input-group">
                    <input type="text" id="new-student-name" placeholder="Nome único...">
                    <button class="btn-save" onclick="addNewStudent()">Adicionar Aluno</button>
                </div>
            </div>

            <div class="search-container">
                <input type="text" id="search-input" class="search-input" placeholder="🔍 Pesquisar nome do aluno..." oninput="renderStudents()">
            </div>

            <div style="margin-bottom: 10px; display: flex; align-items: center;">
                <h3 style="font-size: 1.1rem;">Lista de Alunos</h3>
                <span id="student-counter" class="counter-badge">0 Alunos</span>
            </div>

            <div class="list-container">
                <table>
                    <thead>
                        <tr>
                            <th>Nome do Aluno (A-Z)</th>
                            <th style="text-align:center">Presença</th>
                            <th style="text-align:right">Ação</th>
                        </tr>
                    </thead>
                    <tbody id="student-table-body"></tbody>
                </table>
            </div>
        </div>

        <!-- SEÇÃO HISTÓRICO -->
        <div id="section-history" style="display:none">
            <div class="section-container">
                <div style="display:flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
                    <h3>Histórico de Chamadas</h3>
                    <button class="btn-save" style="background: #1e40af;" onclick="exportFullHistoryToExcel()">📥 BAIXAR HISTÓRICO COMPLETO (EXCEL)</button>
                </div>
                <div id="history-list">
                    <!-- Cards de histórico aparecerão aqui -->
                </div>
            </div>
        </div>

        <!-- SEÇÃO ACESSOS (SÓ ADMIN CLX) -->
        <div id="section-users" style="display:none">
            <div class="section-container">
                <div style="display:flex; justify-content: space-between; align-items:center">
                    <h3>Gerir Acessos (Restrito ao Admin CLX)</h3>
                </div><br>
                <div class="input-group">
                    <input type="text" id="new-user-login" placeholder="Login">
                    <input type="password" id="new-user-pass" placeholder="Senha">
                    <button class="btn-save" onclick="addNewUser()">Criar Acesso</button>
                </div>
                <div class="list-container" style="background: #1e293b;">
                    <table>
                        <thead>
                            <tr>
                                <th style="color: white; background: rgba(0,0,0,0.3)">Usuário</th>
                                <th style="text-align:right; color: white; background: rgba(0,0,0,0.3)">Ação</th>
                            </tr>
                        </thead>
                        <tbody id="users-list-body"></tbody>
                    </table>
                </div>
            </div>
        </div>
    </main>

    <!-- OVERLAY PLANILHA / VISUALIZADOR -->
    <div id="sheet-overlay">
        <div class="sheet-content">
            <div style="display:flex; justify-content: space-between; align-items:center; margin-bottom: 20px;">
                <h2 id="sheet-title" style="color:black">Relatório de Chamada</h2>
                <div>
                    <button class="btn-action" style="background:#000; color:#fff" onclick="toggleSheet(false)">Fechar</button>
                    <button class="btn-action" style="background:#1e40af; color:#fff" onclick="exportToExcel()">📥 Baixar Excel</button>
                    <button class="btn-action" style="background:#10b981; color:#fff" onclick="window.print()">Imprimir PDF</button>
                </div>
            </div>
            <table class="sheet-table">
                <thead>
                    <tr>
                        <th>Nome do Aluno</th>
                        <th>Situação</th>
                    </tr>
                </thead>
                <tbody id="sheet-table-body"></tbody>
            </table>
        </div>
    </div>

    <!-- Firebase SDKs -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, setDoc, getDoc, collection, onSnapshot, updateDoc, deleteDoc, addDoc, query, getDocs, writeBatch } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        const firebaseConfig = {
            apiKey: "AIzaSyBiBWPAEjNjzWFrws6HnZ-_Kziiwq1d0Zs",
            authDomain: "lista-de-chamada-44bb7.firebaseapp.com",
            projectId: "lista-de-chamada-44bb7",
            storageBucket: "lista-de-chamada-44bb7.firebasestorage.app",
            messagingSenderId: "835709176234",
            appId: "1:835709176234:web:4bf4b225cd49f6420c43d3"
        };

        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);
        
        // REVERSÃO DO APP ID: Usando o ID original 'chamada-clx-v1' para recuperar os dados
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'chamada-clx-v1';

        let currentUser = null;
        let loggedUserName = ""; 
        let studentsList = [];
        let usersList = [];
        let historyList = [];
        let currentViewingData = null; 

        async function init() {
            try {
                await signInAnonymously(auth);
                onAuthStateChanged(auth, (user) => {
                    if (user) {
                        currentUser = user;
                        document.getElementById('loading-overlay').style.display = 'none';
                        setupListeners();
                    }
                });
            } catch (err) {
                console.error(err);
                alert("Erro ao ligar ao servidor.");
            }
        }

        function setupListeners() {
            if (!currentUser) return;

            const studentsCol = collection(db, 'artifacts', appId, 'public', 'data', 'students');
            onSnapshot(studentsCol, (snap) => {
                studentsList = snap.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                renderStudents();
            }, (err) => console.error("Erro Students:", err));

            const usersCol = collection(db, 'artifacts', appId, 'public', 'data', 'users');
            onSnapshot(usersCol, (snap) => {
                usersList = snap.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                if (usersList.length === 0) {
                    addDoc(usersCol, { login: "CLX", pass: "02072007" });
                }
                renderUsers();
            }, (err) => console.error("Erro Users:", err));

            const historyCol = collection(db, 'artifacts', appId, 'public', 'data', 'history');
            onSnapshot(historyCol, (snap) => {
                historyList = snap.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                renderHistory();
            }, (err) => console.error("Erro History:", err));
        }

        window.validateAccess = () => {
            const userIn = document.getElementById('user-input').value;
            const passIn = document.getElementById('pass-input').value;
            const found = usersList.find(u => u.login === userIn && u.pass === passIn);
            
            if (found) {
                loggedUserName = found.login;
                document.getElementById('lock-screen').classList.add('hidden');
                document.getElementById('app-content').style.display = 'block';
                document.getElementById('welcome-title').innerText = `CLX - Olá, ${loggedUserName}`;
                
                if (loggedUserName === "CLX") {
                    document.getElementById('nav-btn-users').style.display = 'inline-block';
                } else {
                    document.getElementById('nav-btn-users').style.display = 'none';
                }
            } else {
                document.getElementById('login-error').style.display = 'block';
            }
        };

        window.addNewStudent = async () => {
            const name = document.getElementById('new-student-name').value.trim();
            if (!name) return;
            const col = collection(db, 'artifacts', appId, 'public', 'data', 'students');
            await addDoc(col, { name, present: true, createdAt: Date.now() });
            document.getElementById('new-student-name').value = "";
        };

        window.addBulkStudents = async () => {
            const text = document.getElementById('bulk-names').value.trim();
            if (!text) return;
            const names = text.split('\n');
            const col = collection(db, 'artifacts', appId, 'public', 'data', 'students');
            for (let n of names) {
                if (n.trim()) await addDoc(col, { name: n.trim(), present: true, createdAt: Date.now() });
            }
            document.getElementById('bulk-names').value = "";
        };

        window.togglePresence = async (id, current) => {
            const docRef = doc(db, 'artifacts', appId, 'public', 'data', 'students', id);
            await updateDoc(docRef, { present: !current });
        };

        window.absentAllStudents = async () => {
            if (studentsList.length === 0) return;
            if (!confirm("Isso marcará FALTA para todos os alunos da lista. Continuar?")) return;
            
            const batch = writeBatch(db);
            studentsList.forEach(student => {
                const docRef = doc(db, 'artifacts', appId, 'public', 'data', 'students', student.id);
                batch.update(docRef, { present: false });
            });
            
            await batch.commit();
        };

        window.removeStudent = async (id) => {
            if (confirm("Apagar permanentemente este aluno?")) {
                const docRef = doc(db, 'artifacts', appId, 'public', 'data', 'students', id);
                await deleteDoc(docRef);
            }
        };

        window.saveDailyCall = async () => {
            if (studentsList.length === 0) return alert("Não há alunos para salvar.");
            if (!confirm("Deseja salvar a chamada atual no histórico?")) return;
            const now = new Date();
            const dateStr = now.toLocaleDateString('pt-BR');
            const timeStr = now.toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' });
            const historyEntry = {
                date: dateStr,
                time: timeStr,
                timestamp: Date.now(),
                data: studentsList.map(s => ({ name: s.name, present: s.present }))
            };
            const col = collection(db, 'artifacts', appId, 'public', 'data', 'history');
            await addDoc(col, historyEntry);
            alert(`Chamada de ${dateStr} salva com sucesso no Histórico!`);
        };

        window.downloadBackup = () => {
            if (studentsList.length === 0) return alert("Não há alunos para exportar.");
            const exportData = studentsList.map(s => ({ name: s.name, present: s.present }));
            const blob = new Blob([JSON.stringify(exportData, null, 2)], { type: "application/json" });
            const url = URL.createObjectURL(blob);
            const link = document.createElement("a");
            link.href = url;
            link.download = `backup_alunos_clx_${new Date().toLocaleDateString().replace(/\//g, '-')}.json`;
            link.click();
        };

        window.importBackup = async (event) => {
            const file = event.target.files[0];
            if (!file) return;
            const reader = new FileReader();
            reader.onload = async (e) => {
                try {
                    const data = JSON.parse(e.target.result);
                    if (!Array.isArray(data)) throw new Error();
                    if (confirm(`Deseja importar ${data.length} alunos?`)) {
                        const col = collection(db, 'artifacts', appId, 'public', 'data', 'students');
                        for (let item of data) {
                            if (item.name) await addDoc(col, { name: item.name, present: item.present ?? true, createdAt: Date.now() });
                        }
                    }
                } catch (err) { alert("Erro ao ler ficheiro."); }
            };
            reader.readAsText(file);
            event.target.value = "";
        };

        window.exportFullHistoryToExcel = () => {
            if (historyList.length === 0) return alert("Não há histórico para exportar.");
            const workbook = XLSX.utils.book_new();
            // Ordena do mais antigo para o mais novo para as abas seguirem ordem cronológica
            const sortedHistory = [...historyList].sort((a, b) => a.timestamp - b.timestamp);
            
            // Controle de nomes duplicados de abas (o Excel não permite abas com o mesmo nome)
            const usedSheetNames = {};

            sortedHistory.forEach(entry => {
                const formattedData = entry.data
                    .sort((a, b) => a.name.localeCompare(b.name))
                    .map(s => ({
                        "Nome do Aluno": s.name,
                        "Situação": s.present ? "PRESENÇA" : "FALTA"
                    }));
                
                const worksheet = XLSX.utils.json_to_sheet(formattedData);
                
                // Define o nome da aba como a data (limpando caracteres inválidos se houver)
                let baseName = entry.date.replace(/\//g, '-');
                let sheetName = baseName;
                
                // Se houver mais de uma chamada no mesmo dia, adiciona o horário ou um contador
                if (usedSheetNames[sheetName]) {
                    sheetName = `${baseName} (${entry.time.replace(/:/g, 'h')})`;
                }
                
                // Limite de 31 caracteres para nome de aba no Excel
                if (sheetName.length > 31) sheetName = sheetName.substring(0, 31);
                
                usedSheetNames[sheetName] = true;
                XLSX.utils.book_append_sheet(workbook, worksheet, sheetName);
            });
            
            XLSX.writeFile(workbook, `Historico_Completo_Chamada_CLX.xlsx`);
        };

        window.exportToExcel = () => {
            const dataToExport = currentViewingData || studentsList;
            if (dataToExport.length === 0) return alert("Não há dados para exportar.");
            const list = Array.isArray(dataToExport) ? dataToExport : dataToExport.data;
            const formatted = list
                .sort((a, b) => a.name.localeCompare(b.name))
                .map(s => ({
                    "Nome do Aluno": s.name,
                    "Situação": s.present ? "PRESENÇA" : "FALTA"
                }));
            const worksheet = XLSX.utils.json_to_sheet(formatted);
            const workbook = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(workbook, worksheet, "Chamada");
            const dateLabel = currentViewingData ? currentViewingData.date.replace(/\//g, '-') : 'Atual';
            XLSX.writeFile(workbook, `Chamada_CLX_${dateLabel}.xlsx`);
        };

        window.renderStudents = () => {
            const tbody = document.getElementById('student-table-body');
            const counterSpan = document.getElementById('student-counter');
            const searchTerm = document.getElementById('search-input').value.toLowerCase();
            
            tbody.innerHTML = "";
            const sorted = [...studentsList].sort((a, b) => a.name.localeCompare(b.name));
            const filtered = sorted.filter(s => s.name.toLowerCase().includes(searchTerm));
            
            counterSpan.innerText = `${filtered.length} ${filtered.length === 1 ? 'Aluno' : 'Alunos'}`;

            filtered.forEach(s => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td style="color: #000000 !important;">${s.name}</td>
                    <td style="text-align:center">
                        <button class="badge ${s.present ? 'badge-present' : 'badge-absent'}" onclick="togglePresence('${s.id}', ${s.present})">
                            ${s.present ? 'PRESENÇA' : 'FALTA'}
                        </button>
                    </td>
                    <td style="text-align:right">
                        <button class="btn-delete" onclick="removeStudent('${s.id}')">Apagar</button>
                    </td>
                `;
                tbody.appendChild(tr);
            });
        };

        window.renderHistory = () => {
            const container = document.getElementById('history-list');
            container.innerHTML = "";
            const sortedHistory = [...historyList].sort((a, b) => b.timestamp - a.timestamp);
            if (sortedHistory.length === 0) {
                container.innerHTML = "<p style='color:var(--text-dim)'>Nenhum registo encontrado.</p>";
                return;
            }
            sortedHistory.forEach(entry => {
                const div = document.createElement('div');
                div.className = 'history-card';
                div.onclick = () => openHistoryEntry(entry);
                div.innerHTML = `
                    <div>
                        <strong style="color:white; font-size: 1.1rem">${entry.date}</strong><br>
                        <span style="color:var(--text-dim); font-size: 0.8rem">Salvo às ${entry.time}</span>
                    </div>
                    <div style="text-align:right">
                        <span style="color:var(--accent-blue)">${entry.data.length} Alunos</span><br>
                        <button class="btn-delete" style="margin-top:5px; padding: 2px 8px; font-size: 0.7rem" onclick="event.stopPropagation(); deleteHistory('${entry.id}')">Excluir</button>
                    </div>
                `;
                container.appendChild(div);
            });
        };

        window.openHistoryEntry = (entry) => {
            currentViewingData = entry;
            document.getElementById('sheet-title').innerText = `Relatório de ${entry.date} (${entry.time})`;
            toggleSheet(true);
        };

        window.deleteHistory = async (id) => {
            if (confirm("Deseja apagar este registo de histórico?")) {
                await deleteDoc(doc(db, 'artifacts', appId, 'public', 'data', 'history', id));
            }
        };

        window.renderSheet = () => {
            const tbody = document.getElementById('sheet-table-body');
            tbody.innerHTML = "";
            const dataToRender = currentViewingData ? currentViewingData.data : studentsList;
            if (!currentViewingData) document.getElementById('sheet-title').innerText = "Relatório de Chamada (Atual)";
            const sorted = [...dataToRender].sort((a, b) => a.name.localeCompare(b.name));
            sorted.forEach(s => {
                tbody.innerHTML += `
                    <tr>
                        <td style="color: black !important;">${s.name}</td>
                        <td style="color: ${s.present ? 'green' : 'red'}; font-weight: bold">${s.present ? 'PRESENÇA' : 'FALTA'}</td>
                    </tr>`;
            });
        };

        window.toggleSheet = (show) => {
            document.getElementById('sheet-overlay').style.display = show ? 'block' : 'none';
            if (show) renderSheet();
            else currentViewingData = null;
        };

        window.showSection = (id) => {
            if (id === 'users' && loggedUserName !== "CLX") {
                alert("Acesso negado. Apenas o administrador CLX pode gerir logins.");
                return;
            }
            document.getElementById('section-attendance').style.display = id === 'attendance' ? 'block' : 'none';
            document.getElementById('section-history').style.display = id === 'history' ? 'block' : 'none';
            document.getElementById('section-users').style.display = id === 'users' ? 'block' : 'none';
        };

        function renderUsers() {
            const tbody = document.getElementById('users-list-body');
            if(!tbody) return;
            tbody.innerHTML = "";
            usersList.forEach(u => {
                const tr = document.createElement('tr');
                const isMainAdmin = u.login === "CLX";
                tr.innerHTML = `
                    <td style="color: white">${u.login} ${isMainAdmin ? '(Admin Principal)' : ''}</td>
                    <td style="text-align:right">
                        ${!isMainAdmin ? `<button class="btn-delete" onclick="removeUser('${u.id}')">Remover</button>` : ''}
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        window.addNewUser = async () => {
            if (loggedUserName !== "CLX") return;
            const l = document.getElementById('new-user-login').value.trim();
            const p = document.getElementById('new-user-pass').value.trim();
            if(l && p) {
                await addDoc(collection(db, 'artifacts', appId, 'public', 'data', 'users'), { login: l, pass: p });
                alert("Novo acesso criado com sucesso!");
            }
            document.getElementById('new-user-login').value = ""; 
            document.getElementById('new-user-pass').value = "";
        };

        window.removeUser = async (id) => { 
            if (loggedUserName !== "CLX") return;
            if(usersList.length > 1 && confirm("Remover este usuário?")) {
                await deleteDoc(doc(db, 'artifacts', appId, 'public', 'data', 'users', id));
            }
        };

        init();
    </script>
</body>
</html>
