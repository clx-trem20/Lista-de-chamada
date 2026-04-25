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
            background: var(--card-bg);
            border-radius: 15px;
            overflow: hidden;
        }

        table { width: 100%; border-collapse: collapse; }
        th { background: rgba(0,0,0,0.2); text-align: left; padding: 15px; color: var(--text-dim); }
        td { padding: 15px; border-bottom: 1px solid rgba(255, 255, 255, 0.05); }

        .badge { padding: 6px 12px; border-radius: 20px; cursor: pointer; border: none; font-weight: 600; }
        .badge-present { background: rgba(34, 197, 94, 0.2); color: var(--accent-green); }
        .badge-absent { background: rgba(239, 68, 68, 0.2); color: var(--accent-red); }

        .btn-delete { color: var(--accent-red); background: none; border: 1px solid rgba(239, 68, 68, 0.3); padding: 5px 10px; border-radius: 6px; cursor: pointer; }

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

        .sheet-content { max-width: 800px; margin: 0 auto; background: white; color: black; padding: 40px; border-radius: 5px; }
        .sheet-table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        .sheet-table th, .sheet-table td { border: 1px solid #ddd; padding: 10px; color: black; }
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
                <button class="btn-action" onclick="showSection('users')">🔑 Acessos</button>
                <button class="btn-action" style="color:var(--accent-red)" onclick="location.reload()">Sair</button>
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
                <button class="btn-action" onclick="showSection('attendance')">Voltar</button><br><br>
                <div class="input-group">
                    <input type="text" id="new-user-login" placeholder="Login">
                    <input type="password" id="new-user-pass" placeholder="Senha">
                    <button class="btn-save" onclick="addNewUser()">Criar Acesso</button>
                </div>
                <tbody id="users-list-body"></tbody>
            </div>
        </div>
    </main>

    <div id="sheet-overlay">
        <div class="sheet-content">
            <button onclick="toggleSheet(false)">Fechar</button>
            <button onclick="window.print()">PDF</button>
            <table class="sheet-table">
                <thead><tr><th>Nome</th><th>Status</th></tr></thead>
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

        // --- AUTH E INIT ---
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

            // Alunos (Público no contexto do App)
            const studentsCol = collection(db, 'artifacts', appId, 'public', 'data', 'students');
            onSnapshot(studentsCol, (snap) => {
                studentsList = snap.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                renderStudents();
            }, (err) => console.error("Erro alunos:", err));

            // Usuários/Acessos
            const usersCol = collection(db, 'artifacts', appId, 'public', 'data', 'users');
            onSnapshot(usersCol, (snap) => {
                usersList = snap.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                if (usersList.length === 0) {
                    // Criar usuário padrão se banco estiver vazio
                    addDoc(usersCol, { login: "CLX", pass: "02072007" });
                }
            });
        }

        // --- FUNÇÕES GLOBAIS (Expostas para o HTML) ---
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
            if (confirm("Apagar aluno?")) {
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
            // Ordenar por data de criação para manter ordem de inserção
            const sorted = [...studentsList].sort((a, b) => a.createdAt - b.createdAt);
            sorted.forEach(s => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td>${s.name}</td>
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

        function renderSheet() {
            const tbody = document.getElementById('sheet-table-body');
            tbody.innerHTML = "";
            const sorted = [...studentsList].sort((a, b) => a.name.localeCompare(b.name));
            sorted.forEach(s => {
                tbody.innerHTML += `<tr><td>${s.name}</td><td>${s.present ? 'PRESENÇA' : 'FALTA'}</td></tr>`;
            });
        }

        init();
    </script>
</body>
</html>
