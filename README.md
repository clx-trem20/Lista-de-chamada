<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Chamada Profissional - CLX</title>
    <style>
        :root {
            --bg-color: #0f172a;
            --card-bg: #1e293b;
            --accent-blue: #38bdf8;
            --accent-green: #22c55e;
            --accent-red: #ef4444;
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
            max-width: 900px;
            padding: 20px;
        }

        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 30px;
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

        .btn-save {
            background: var(--accent-green);
            color: white;
            border: none;
            padding: 0 20px;
            border-radius: 8px;
            cursor: pointer;
            font-weight: 600;
        }

        .list-container {
            background: #e2e8f0; /* Fundo cinza claro para destacar o texto preto */
            border-radius: 15px;
            overflow: hidden;
        }

        table { width: 100%; border-collapse: collapse; }
        th { background: #cbd5e1; text-align: left; padding: 15px; color: #1e293b; font-weight: bold; }
        
        /* AJUSTE DE COR DOS NOMES PARA PRETO */
        td { 
            padding: 15px; 
            border-bottom: 1px solid rgba(0, 0, 0, 0.1);
            color: #000000 !important; /* FORÇA COR PRETA */
            font-size: 1rem;
            font-weight: 500;
        }

        .badge { padding: 6px 12px; border-radius: 20px; cursor: pointer; border: none; font-weight: 600; }
        .badge-present { background: #22c55e; color: white; }
        .badge-absent { background: #ef4444; color: white; }

        .btn-delete { color: #b91c1c; background: #fee2e2; border: 1px solid #f87171; padding: 5px 10px; border-radius: 6px; cursor: pointer; }

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
            .btn-action, button { display: none; }
        }
    </style>
</head>
<body>

    <div id="loading-overlay">Carregando Banco de Dados...</div>

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
            <h1>Chamada CLX</h1>
            <div class="nav-buttons">
                <button class="btn-action" onclick="toggleSheet(true)">📑 Planilha</button>
                <button class="btn-action" onclick="downloadBackup()">📥 Baixar Lista</button>
                <button class="btn-action" onclick="document.getElementById('import-file').click()">📤 Carregar Lista</button>
                <button class="btn-action" onclick="showSection('users')">🔑 Acessos</button>
                <button class="btn-action" style="color:var(--accent-red)" onclick="location.reload()">Sair</button>
                <input type="file" id="import-file" style="display:none;" accept=".json" onchange="importBackup(event)">
            </div>
        </header>

        <div id="section-attendance">
            <div class="section-container">
                <h3>Adição em Massa</h3><br>
                <div class="input-group" style="flex-direction: column;">
                    <textarea id="bulk-names" placeholder="Nomes por linha..."></textarea>
                    <button class="btn-save" style="margin-top:10px; padding:12px" onclick="addBulkStudents()">Salvar Lista</button>
                </div>
                <div class="input-group">
                    <input type="text" id="new-student-name" placeholder="Nome único...">
                    <button class="btn-save" onclick="addNewStudent()">Adicionar</button>
                </div>
            </div>

            <div class="list-container">
                <table>
                    <thead>
                        <tr>
                            <th>Nome do Aluno</th>
                            <th style="text-align:center">Presença</th>
                            <th style="text-align:right">Ação</th>
                        </tr>
                    </thead>
                    <tbody id="student-table-body"></tbody>
                </table>
            </div>
        </div>

        <div id="section-users" style="display:none">
            <div class="section-container">
                <div style="display:flex; justify-content: space-between; align-items:center">
                    <h3>Gerenciar Acessos</h3>
                    <button class="btn-action" onclick="showSection('attendance')">Voltar</button>
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

    <div id="sheet-overlay">
        <div class="sheet-content">
            <div style="display:flex; justify-content: space-between; align-items:center; margin-bottom: 20px;">
                <h2 style="color:black">Relatório de Chamada</h2>
                <div>
                    <button class="btn-action" style="background:#000; color:#fff" onclick="toggleSheet(false)">Fechar</button>
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
        import { getFirestore, doc, setDoc, getDoc, collection, onSnapshot, updateDoc, deleteDoc, addDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

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
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'chamada-clx-v1';

        let currentUser = null;
        let studentsList = [];
        let usersList = [];

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
                alert("Erro ao conectar com o servidor.");
            }
        }

        function setupListeners() {
            if (!currentUser) return;

            const studentsCol = collection(db, 'artifacts', appId, 'public', 'data', 'students');
            onSnapshot(studentsCol, (snap) => {
                studentsList = snap.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                renderStudents();
            });

            const usersCol = collection(db, 'artifacts', appId, 'public', 'data', 'users');
            onSnapshot(usersCol, (snap) => {
                usersList = snap.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                if (usersList.length === 0) {
                    addDoc(usersCol, { login: "CLX", pass: "02072007" });
                }
                renderUsers();
            });
        }

        window.downloadBackup = () => {
            if (studentsList.length === 0) return alert("Não há alunos para exportar.");
            const exportData = studentsList.map(s => ({ name: s.name, present: s.present }));
            const blob = new Blob([JSON.stringify(exportData, null, 2)], { type: "application/json" });
            const url = URL.createObjectURL(blob);
            const link = document.createElement("a");
            link.href = url;
            link.download = `backup_chamada_clx_${new Date().toLocaleDateString().replace(/\//g, '-')}.json`;
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
                } catch (err) { alert("Erro ao ler arquivo."); }
            };
            reader.readAsText(file);
            event.target.value = "";
        };

        window.validateAccess = () => {
            const userIn = document.getElementById('user-input').value;
            const passIn = document.getElementById('pass-input').value;
            const found = usersList.find(u => u.login === userIn && u.pass === passIn);
            if (found) {
                document.getElementById('lock-screen').classList.add('hidden');
                document.getElementById('app-content').style.display = 'block';
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

        window.removeStudent = async (id) => {
            if (confirm("Apagar permanentemente?")) {
                const docRef = doc(db, 'artifacts', appId, 'public', 'data', 'students', id);
                await deleteDoc(docRef);
            }
        };

        window.addNewUser = async () => {
            const login = document.getElementById('new-user-login').value.trim();
            const pass = document.getElementById('new-user-pass').value.trim();
            if (!login || !pass) return;
            const col = collection(db, 'artifacts', appId, 'public', 'data', 'users');
            await addDoc(col, { login, pass });
            alert("Acesso criado!");
        };

        window.removeUser = async (id) => {
            if(usersList.length <= 1) return alert("Não é possível remover o único acesso.");
            if(confirm("Remover este acesso?")) {
                await deleteDoc(doc(db, 'artifacts', appId, 'public', 'data', 'users', id));
            }
        };

        window.showSection = (id) => {
            document.getElementById('section-attendance').style.display = id === 'attendance' ? 'block' : 'none';
            document.getElementById('section-users').style.display = id === 'users' ? 'block' : 'none';
        };

        window.toggleSheet = (show) => {
            document.getElementById('sheet-overlay').style.display = show ? 'block' : 'none';
            if (show) renderSheet();
        };

        function renderStudents() {
            const tbody = document.getElementById('student-table-body');
            tbody.innerHTML = "";
            const sorted = [...studentsList].sort((a, b) => a.createdAt - b.createdAt);
            sorted.forEach(s => {
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
        }

        function renderUsers() {
            const tbody = document.getElementById('users-list-body');
            if(!tbody) return;
            tbody.innerHTML = "";
            usersList.forEach(u => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td style="color: white">${u.login}</td>
                    <td style="text-align:right">
                        <button class="btn-delete" onclick="removeUser('${u.id}')">Remover</button>
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        function renderSheet() {
            const tbody = document.getElementById('sheet-table-body');
            tbody.innerHTML = "";
            const sorted = [...studentsList].sort((a, b) => a.name.localeCompare(b.name));
            sorted.forEach(s => {
                tbody.innerHTML += `<tr><td style="color: black !important;">${s.name}</td><td style="color: ${s.present ? 'green' : 'red'}; font-weight: bold">${s.present ? 'PRESENÇA' : 'FALTA'}</td></tr>`;
            });
        }

        init();
    </script>
</body>
</html>
