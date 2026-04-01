<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
    <title>AuditorQuest — Simulador CESPE/CEBRASPE</title>
    <!-- Tailwind + Font Awesome + Chart.js + PDF libs -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.31/jspdf.plugin.autotable.min.js"></script>
    <style>
        /* RESET GLOBAL SEMPRE PERMITINDO ROLAGEM NATURAL */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        html, body {
            height: auto;
            min-height: 100%;
            width: 100%;
            overflow-x: hidden;
            overflow-y: auto;    /* Rolagem natural da página */
            background: linear-gradient(145deg, #f0f4fa 0%, #e9eef5 100%);
            font-family: 'Inter', system-ui, -apple-system, 'Segoe UI', sans-serif;
        }

        /* Layout principal: ocupa pelo menos 100vh, mas expande com conteúdo */
        .main-app {
            display: flex;
            flex-direction: column;
            min-height: 100vh;
            width: 100%;
        }

        /* Layout duas colunas: sem overflow oculto, sem altura fixa travada */
        .gran-layout {
            display: grid;
            grid-template-columns: 380px 1fr;
            gap: 28px;
            padding: 0 20px 20px 20px;
            width: 100%;
        }

        /* Colunas: sem overflow:auto individual, rolagem global da página */
        .left-col, .right-col {
            overflow-y: visible;
            height: auto;
        }

        /* Cards com estilo consistente */
        .question-card {
            background: rgba(255,255,255,0.98);
            border-radius: 2rem;
            box-shadow: 0 8px 30px rgba(0,0,0,0.05);
            padding: 2rem;
            width: 100%;
            transition: all 0.2s ease;
            margin-bottom: 20px;
        }

        .card-gran {
            background: rgba(255,255,255,0.98);
            border-radius: 1.5rem;
            box-shadow: 0 4px 20px rgba(0,0,0,0.03);
            transition: all 0.2s ease;
            margin-bottom: 24px;
        }

        .btn-primary {
            background: linear-gradient(105deg, #0f3b2c 0%, #1e5a3e 100%);
            transition: all 0.2s;
        }

        .btn-primary:hover {
            background: linear-gradient(105deg, #0a2e22, #174a33);
            transform: scale(0.98);
        }

        .btn-certo {
            background: linear-gradient(95deg, #15803d, #16a34a);
        }

        .btn-errado {
            background: linear-gradient(95deg, #b91c1c, #dc2626);
        }

        .progress-bar-animated {
            transition: width 0.4s cubic-bezier(0.2, 0.9, 0.4, 1.1);
        }

        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }

        ::-webkit-scrollbar-track {
            background: #e2e8f0;
            border-radius: 12px;
        }

        ::-webkit-scrollbar-thumb {
            background: #9ca3af;
            border-radius: 12px;
        }

        .question-text {
            font-size: 1.2rem;
            line-height: 1.6;
        }

        .answer-feedback-correct {
            background: #e8f5e9;
            border-left: 6px solid #2e7d32;
        }

        .answer-feedback-wrong {
            background: #ffebee;
            border-left: 6px solid #c62828;
        }

        /* Tela de login (overlay fixo) */
        .login-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.5);
            backdrop-filter: blur(8px);
            z-index: 1000;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .login-card {
            background: white;
            border-radius: 2rem;
            width: 90%;
            max-width: 440px;
            padding: 2rem;
            box-shadow: 0 25px 50px -12px rgba(0,0,0,0.25);
        }

        /* Header responsivo */
        .header-area {
            padding: 1rem 2rem;
            background: transparent;
            flex-shrink: 0;
        }

        /* Responsividade: empilhar colunas em telas pequenas */
        @media (max-width: 900px) {
            .gran-layout {
                grid-template-columns: 1fr;
                gap: 20px;
                padding: 0 16px 20px 16px;
            }
            .left-col, .right-col {
                overflow-y: visible;
            }
            .question-card {
                padding: 1.5rem;
            }
            .header-area {
                padding: 0.75rem 1rem;
            }
        }

        /* Ajustes adicionais para espaçamento e responsividade */
        @media (max-width: 640px) {
            .question-text {
                font-size: 1rem;
            }
            .btn-certo, .btn-errado {
                padding: 0.75rem;
                font-size: 1rem;
            }
        }
    </style>
</head>
<body>

<!-- TELA DE LOGIN -->
<div id="loginScreen" class="login-overlay">
    <div class="login-card">
        <div class="text-center mb-6">
            <div class="bg-gradient-to-br from-emerald-700 to-green-600 rounded-2xl w-16 h-16 flex items-center justify-center mx-auto shadow-md">
                <i class="fas fa-graduation-cap text-white text-3xl"></i>
            </div>
            <h2 class="text-2xl font-bold text-gray-800 mt-4">Auditor<span class="text-emerald-700">Quest</span></h2>
            <p id="greetingMessage" class="text-gray-500 mt-1"></p>
        </div>
        <form id="loginForm" onsubmit="event.preventDefault(); doLogin();">
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-semibold mb-2">E-mail</label>
                <input type="email" id="loginEmail" value="usuario@exemplo.com" class="w-full border border-gray-200 rounded-xl p-3 bg-gray-50 focus:ring-2 focus:ring-emerald-400" />
            </div>
            <div class="mb-6">
                <label class="block text-gray-700 text-sm font-semibold mb-2">Senha</label>
                <input type="password" id="loginPassword" value="senha123" class="w-full border border-gray-200 rounded-xl p-3 bg-gray-50 focus:ring-2 focus:ring-emerald-400" />
            </div>
            <button type="submit" class="w-full btn-primary text-white font-bold py-3 rounded-xl transition flex items-center justify-center gap-2">
                <i class="fas fa-sign-in-alt"></i> Entrar
            </button>
            <p class="text-xs text-center text-gray-400 mt-4">Acesso livre – apenas simulação</p>
        </form>
    </div>
</div>

<!-- CONTEÚDO PRINCIPAL (inicialmente oculto) -->
<div id="mainApp" style="display: none;" class="main-app">
    <!-- HEADER -->
    <div class="header-area flex flex-wrap justify-between items-center gap-4 pb-2 border-b border-gray-200/80">
        <div class="flex items-center gap-3">
            <div class="bg-gradient-to-br from-emerald-700 to-green-600 rounded-2xl w-12 h-12 flex items-center justify-center shadow-md">
                <i class="fas fa-graduation-cap text-white text-2xl"></i>
            </div>
            <div>
                <h1 class="text-3xl font-extrabold tracking-tight text-gray-800">Auditor<span class="text-emerald-700">Quest</span></h1>
                <p class="text-xs text-gray-500 -mt-1">Simulador CESPE / CEBRASPE — Certo ou Errado</p>
            </div>
        </div>
        <div class="flex items-center gap-3 bg-white/80 backdrop-blur-sm px-5 py-2 rounded-full shadow-sm">
            <i class="far fa-clock text-gray-500"></i>
            <span id="currentTime" class="font-mono text-gray-700 font-medium">--:--:--</span>
            <div class="w-px h-5 bg-gray-300 mx-1"></div>
            <i class="fas fa-chart-line text-emerald-600"></i>
            <span id="globalAccuracy" class="text-sm font-semibold text-gray-700">0% acerto</span>
        </div>
    </div>

    <!-- LAYOUT 2 COLUNAS COM ALTURA TOTAL (rolagem global) -->
    <div class="gran-layout">
        <!-- COLUNA ESQUERDA (scroll natural) -->
        <div class="left-col space-y-6">
            <!-- Card importação -->
            <div class="card-gran p-5 border border-gray-100">
                <div class="flex items-center gap-3 mb-3">
                    <div class="bg-emerald-100 p-2 rounded-xl"><i class="fas fa-file-upload text-emerald-700 text-lg"></i></div>
                    <div><h2 class="font-bold text-gray-800">Banco de questões</h2><p class="text-xs text-gray-500">Importe até 20 itens (formato CESPE)</p></div>
                </div>
                <select id="disciplineSelect" class="w-full border border-gray-200 rounded-xl p-2.5 text-sm bg-gray-50 mb-3 focus:ring-2 focus:ring-emerald-400">
                    <!-- Lista completa de disciplinas preenchida via JS -->
                </select>
                <textarea id="rawQuestionsInput" rows="3" class="w-full border border-gray-200 rounded-xl p-3 text-sm font-mono bg-gray-50 focus:ring-2 focus:ring-emerald-400" placeholder="Questão 1: &quot;A Declaração Universal foi adotada em 1948.&quot;

Comentário: &quot;Adotada pela ONU em 10/12/1948.&quot;

Gabarito: Certo"></textarea>
                <div class="flex flex-wrap gap-2 mt-3">
                    <button id="importBtn" class="flex-1 bg-emerald-700 hover:bg-emerald-800 text-white font-semibold py-2 rounded-xl transition flex items-center justify-center gap-1 text-sm"><i class="fas fa-cloud-upload-alt"></i> Importar</button>
                    <button id="loadExampleBtn" class="bg-gray-100 hover:bg-gray-200 text-gray-700 px-3 py-2 rounded-xl transition text-sm"><i class="fas fa-code-branch"></i> Exemplo</button>
                    <button id="importPdfBtn" class="bg-indigo-600 hover:bg-indigo-700 text-white px-3 py-2 rounded-xl transition text-sm"><i class="fas fa-file-pdf"></i> PDF</button>
                    <input type="file" id="pdfUploadInput" accept=".pdf" class="hidden" />
                </div>
                <div id="importFeedback" class="text-xs mt-2 text-center hidden"></div>
                <div class="flex justify-between text-xs text-gray-400 mt-2"><span>Máx 20 questões</span><span id="questionCounter">0/20</span></div>
            </div>

            <!-- Dashboard desempenho -->
            <div class="card-gran p-5 border border-gray-100">
                <div class="flex items-center justify-between flex-wrap gap-2 mb-3">
                    <div class="flex items-center gap-2"><i class="fas fa-chart-pie text-emerald-600 text-xl"></i><h2 class="font-bold text-gray-800">Meu desempenho</h2></div>
                    <button id="generatePdfReportBtn" class="text-indigo-600 text-xs bg-indigo-50 px-3 py-1.5 rounded-full hover:bg-indigo-100"><i class="fas fa-download mr-1"></i> Relatório</button>
                </div>
                <div id="dashboardContainer" class="min-h-[200px]"></div>
                <div id="disciplineChartContainer" class="mt-3 pt-2 border-t border-gray-100">
                    <p class="text-xs font-semibold text-gray-500 mb-1"><i class="fas fa-chart-simple"></i> Acertos por disciplina</p>
                    <canvas id="disciplineBarChart" height="130" style="max-height:130px; width:100%"></canvas>
                </div>
            </div>

            <!-- Cronômetro e ferramentas -->
            <div class="card-gran p-5 border border-gray-100">
                <div class="flex items-center gap-2 mb-2"><i class="fas fa-hourglass-half text-amber-600 text-xl"></i><h2 class="font-bold text-gray-800">Cronômetro</h2></div>
                <div class="text-center"><div id="timerDisplay" class="text-3xl font-mono font-bold bg-gray-100 inline-block px-4 py-1 rounded-2xl">00:00:00</div>
                <div class="flex justify-center gap-2 mt-2"><button id="timerStartBtn" class="bg-green-600 hover:bg-green-700 text-white px-4 py-1.5 rounded-xl text-sm"><i class="fas fa-play"></i> Iniciar</button><button id="timerPauseBtn" class="bg-yellow-500 hover:bg-yellow-600 text-white px-4 py-1.5 rounded-xl text-sm"><i class="fas fa-pause"></i> Pausar</button><button id="timerStopBtn" class="bg-red-500 hover:bg-red-600 text-white px-4 py-1.5 rounded-xl text-sm"><i class="fas fa-stop"></i> Parar</button></div></div>
                <div class="flex flex-col gap-2 mt-4">
                    <button id="downloadWrongAnswersBtn" class="bg-rose-50 text-rose-700 border border-rose-200 font-medium py-2 rounded-xl flex items-center justify-center gap-2 text-sm"><i class="fas fa-book-open"></i> Caderno de erros (PDF)</button>
                    <button id="downloadNotebookLMBtn" class="bg-sky-50 text-sky-700 border border-sky-200 font-medium py-2 rounded-xl flex items-center justify-center gap-2 text-sm"><i class="fas fa-download"></i> Prompt NotebookLM</button>
                </div>
                <div class="flex gap-2 mt-3">
                    <button id="resetOnlyProgressBtn" class="flex-1 bg-white border border-gray-300 text-gray-700 py-1.5 rounded-xl text-sm"><i class="fas fa-undo-alt"></i> Resetar progresso</button>
                    <button id="clearAllDataBtn" class="flex-1 bg-red-50 border border-red-200 text-red-600 py-1.5 rounded-xl text-sm"><i class="fas fa-trash-alt"></i> Limpar tudo</button>
                </div>
            </div>
        </div>

        <!-- COLUNA DIREITA (questão ampliada, sem rolagem interna) -->
        <div class="right-col">
            <div id="questionPanel" class="question-card">
                <div class="flex flex-col items-center justify-center py-16 text-gray-400">
                    <i class="fas fa-brain text-6xl mb-4 text-gray-300"></i>
                    <p class="text-center text-gray-500">Importe suas questões para começar<br />e treine no formato Certo/Errado</p>
                </div>
            </div>
        </div>
    </div>
</div>

<script>
    // ------------------ CONSTANTES E ESTADO GLOBAL ------------------
    const MAX_QUESTIONS = 20;
    const DISCIPLINAS_LIST = [
        "Língua Portuguesa", "Raciocínio Lógico-Matemático", "Direito Constitucional",
        "Direito Administrativo", "Administração Pública", "Administração Financeira e Orçamentária",
        "Contabilidade Geral", "Contabilidade Pública", "Controle Externo", "Auditoria Governamental",
        "Economia", "Finanças Públicas", "Licitações e Contratos", "Lei de Responsabilidade Fiscal",
        "Políticas Públicas", "Gestão de Materiais", "Informática", "Análise de Dados", "Estatística",
        "Inglês", "Legislação Institucional", "História do Rio Grande do Norte", "Geografia do Rio Grande do Norte",
        "Jurisprudência"
    ];
    let questions = [];
    let answers = [];
    let currentIndex = 0;
    let commentVisibleMap = new Map();
    let questionStartTimes = new Map();
    
    // Timer estudo
    let timerInterval = null, timerSeconds = 0, timerRunning = false;
    
    // Charts
    let statsChart = null, disciplineChart = null;
    
    // Elementos DOM
    const dashboardContainer = document.getElementById('dashboardContainer');
    const questionPanel = document.getElementById('questionPanel');
    const rawInput = document.getElementById('rawQuestionsInput');
    const importBtn = document.getElementById('importBtn');
    const loadExampleBtn = document.getElementById('loadExampleBtn');
    const importFeedback = document.getElementById('importFeedback');
    const disciplineSelect = document.getElementById('disciplineSelect');
    const importPdfBtn = document.getElementById('importPdfBtn');
    const pdfUploadInput = document.getElementById('pdfUploadInput');
    const generatePdfReportBtn = document.getElementById('generatePdfReportBtn');
    const questionCounterSpan = document.getElementById('questionCounter');
    const timerDisplaySpan = document.getElementById('timerDisplay');
    const timerStartBtn = document.getElementById('timerStartBtn');
    const timerPauseBtn = document.getElementById('timerPauseBtn');
    const timerStopBtn = document.getElementById('timerStopBtn');
    const downloadWrongAnswersBtn = document.getElementById('downloadWrongAnswersBtn');
    const downloadNotebookLMBtn = document.getElementById('downloadNotebookLMBtn');
    const currentTimeSpan = document.getElementById('currentTime');
    const globalAccuracySpan = document.getElementById('globalAccuracy');
    const resetOnlyProgressBtn = document.getElementById('resetOnlyProgressBtn');
    const clearAllDataBtn = document.getElementById('clearAllDataBtn');

    // Preencher select de disciplinas
    DISCIPLINAS_LIST.forEach(disc => {
        const option = document.createElement('option');
        option.value = disc;
        option.textContent = disc;
        disciplineSelect.appendChild(option);
    });

    // Config pdf.js
    pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.worker.min.js';
    
    // Helpers
    function escapeHtml(str) { if(!str) return ''; return str.replace(/[&<>]/g, m => ({'&':'&amp;','<':'&lt;','>':'&gt;'}[m])); }
    function formatBoldItalic(text) { if(!text) return ''; let escaped = escapeHtml(text); return escaped.replace(/\*\*([^*]+)\*\*/g, '<strong><em>$1</em></strong>'); }
    function showToast(msg, type) {
        importFeedback.classList.remove('hidden');
        importFeedback.innerHTML = `<i class="fas ${type==='success'?'fa-check-circle':type==='error'?'fa-exclamation-circle':'fa-info-circle'} mr-1"></i> ${msg}`;
        importFeedback.className = `text-xs mt-2 text-center p-2 rounded-lg ${type==='success'?'bg-green-50 text-green-700':type==='error'?'bg-red-50 text-red-600':'bg-blue-50 text-blue-600'}`;
        setTimeout(() => { importFeedback.classList.add('hidden'); }, 3200);
    }
    function updateGlobalAccuracy() {
        const total = answers.length;
        const correct = answers.filter(a => a.isCorrect === true).length;
        const percent = total === 0 ? 0 : Math.round((correct/total)*100);
        globalAccuracySpan.innerText = `${percent}% acerto`;
    }
    function updateCounterDisplay() { questionCounterSpan.innerText = `${questions.length}/${MAX_QUESTIONS}`; }
    function saveToLocalStorage() {
        localStorage.setItem('auditor_questions', JSON.stringify(questions));
        localStorage.setItem('auditor_answers', JSON.stringify(answers));
        localStorage.setItem('auditor_currentIndex', currentIndex.toString());
        localStorage.setItem('auditor_commentsVisible', JSON.stringify([...commentVisibleMap.entries()]));
        localStorage.setItem('auditor_timerSeconds', timerSeconds.toString());
        localStorage.setItem('auditor_timerRunning', timerRunning.toString());
        updateGlobalAccuracy();
    }
    function loadFromLocalStorage() {
        const storedQ = localStorage.getItem('auditor_questions');
        const storedA = localStorage.getItem('auditor_answers');
        const storedIdx = localStorage.getItem('auditor_currentIndex');
        const storedComments = localStorage.getItem('auditor_commentsVisible');
        if(storedQ) questions = JSON.parse(storedQ);
        if(storedA) answers = JSON.parse(storedA);
        if(storedIdx) currentIndex = parseInt(storedIdx,10);
        if(storedComments) commentVisibleMap = new Map(JSON.parse(storedComments));
        if(questions.length && currentIndex >= questions.length) currentIndex = questions.length-1;
        if(currentIndex<0) currentIndex=0;
        const storedSec = localStorage.getItem('auditor_timerSeconds');
        const storedRun = localStorage.getItem('auditor_timerRunning');
        if(storedSec) timerSeconds = parseInt(storedSec,10);
        if(storedRun === 'true') { timerRunning=true; startTimerInterval(); }
        updateTimerDisplay();
        questionStartTimes.clear();
        updateCounterDisplay();
        updateGlobalAccuracy();
    }
    function startTimerInterval() { if(timerInterval) clearInterval(timerInterval); timerInterval = setInterval(()=>{ if(timerRunning){ timerSeconds++; updateTimerDisplay(); saveToLocalStorage(); } },1000); }
    function updateTimerDisplay() { const h=Math.floor(timerSeconds/3600), m=Math.floor((timerSeconds%3600)/60), s=timerSeconds%60; timerDisplaySpan.textContent=`${h.toString().padStart(2,'0')}:${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`; }
    function startTimer() { if(!timerRunning){ timerRunning=true; if(!timerInterval) startTimerInterval(); saveToLocalStorage(); showToast("Cronômetro iniciado","success");} }
    function pauseTimer() { if(timerRunning){ timerRunning=false; saveToLocalStorage(); showToast("Pausado","info");} }
    function resetTimer() { timerRunning=false; if(timerInterval){ clearInterval(timerInterval); timerInterval=null; } timerSeconds=0; updateTimerDisplay(); saveToLocalStorage(); showToast("Cronômetro zerado","info");}
    
    // Parsing de questões (idêntico ao original)
    function parseQuestionsFromText(text) {
        const questionsArray = [];
        const blockRegex = /Questão\s+(\d+):\s*([\s\S]*?)(?=Questão\s+\d+:|$)/gi;
        let match;
        while((match = blockRegex.exec(text)) !== null) {
            let number = parseInt(match[1],10);
            let block = match[2].trim();
            if(!block) continue;
            let answer = 'Certo';
            let comment = '';
            const gabRegex = /Gabarito:\s*(Certo|Errado)/i;
            const gabMatch = block.match(gabRegex);
            if(gabMatch) { answer = gabMatch[1]; block = block.replace(gabRegex,''); }
            const comRegex = /Comentário:\s*([\s\S]*?)(?=(?:Gabarito:|$))/i;
            const comMatch = block.match(comRegex);
            if(comMatch) { comment = comMatch[1].trim().replace(/^["']|["']$/g,'') || "Sem comentário."; block = block.replace(comRegex,''); }
            else comment = "Sem comentário.";
            let questionText = block.trim().replace(/^["']|["']$/g,'');
            if(!questionText && block) questionText = block.split('\n')[0].replace(/^["']|["']$/g,'');
            if(questionText.length>0) {
                questionsArray.push({ id: Date.now()+Math.random()*10000+number, number, text: questionText, comment, answer });
            }
        }
        if(questionsArray.length===0 && text.includes('Questão')) {
            const lines = text.split(/\r?\n/);
            let current = null;
            for(let line of lines) {
                line=line.trim();
                if(line.match(/^Questão\s+\d+:/i)) {
                    if(current && current.text) questionsArray.push(current);
                    const numMatch = line.match(/Questão\s+(\d+):/i);
                    const num = numMatch ? parseInt(numMatch[1]) : questionsArray.length+1;
                    let textPart = line.replace(/^Questão\s+\d+:\s*/i,'').replace(/^["']|["']$/g,'');
                    current = { id: Date.now()+Math.random(), number: num, text: textPart, comment: '', answer: 'Certo' };
                } else if(current && line.match(/^Comentário:/i)) {
                    current.comment = line.replace(/^Comentário:\s*/i,'').replace(/^["']|["']$/g,'');
                } else if(current && line.match(/^Gabarito:\s*(Certo|Errado)/i)) {
                    const gMatch = line.match(/Gabarito:\s*(Certo|Errado)/i);
                    if(gMatch) current.answer = gMatch[1];
                } else if(current && line && !line.match(/^Comentário|^Gabarito/i)) {
                    current.text += " " + line;
                }
            }
            if(current && current.text) questionsArray.push(current);
        }
        return questionsArray.map((q,idx)=>({ ...q, id: idx+1, comment: q.comment || "Sem comentário." }));
    }
    function importQuestions(rawText, disciplineOverride=null) {
        if(!rawText.trim()){ showToast("Cole o texto das questões","warning"); return false; }
        let parsed = parseQuestionsFromText(rawText);
        if(parsed.length===0){ showToast("Nenhuma questão válida encontrada","error"); return false; }
        if(parsed.length>MAX_QUESTIONS){ showToast(`Limite de ${MAX_QUESTIONS} questões excedido.`,"error"); return false; }
        const discipline = disciplineOverride !== null ? disciplineOverride : disciplineSelect.value;
        questions = parsed.map(q => ({ ...q, discipline }));
        answers = [];
        currentIndex = 0;
        commentVisibleMap.clear();
        questionStartTimes.clear();
        saveToLocalStorage();
        renderAll();
        rawInput.value = '';
        showToast(`${parsed.length} questões importadas (disciplina: ${discipline})`,"success");
        return true;
    }
    async function extractTextFromPDF(file) {
        const arrayBuffer = await file.arrayBuffer();
        const pdf = await pdfjsLib.getDocument({ data: arrayBuffer }).promise;
        let full="";
        for(let i=1;i<=pdf.numPages;i++){ const page=await pdf.getPage(i); const txt=await page.getTextContent(); full+=txt.items.map(t=>t.str).join(" ")+"\n"; }
        return full;
    }
    async function importFromPdf(file) { showToast("Extraindo PDF...","info"); try { const extracted = await extractTextFromPDF(file); if(!extracted.trim()) throw new Error("texto vazio"); rawInput.value=extracted; importQuestions(extracted); } catch(e){ showToast(e.message,"error"); } }
    
    function recordDisplayTimeForCurrent() { if(!questions.length) return; const q=questions[currentIndex]; if(!answers.some(a=>a.questionId===q.id) && !questionStartTimes.has(q.id)) questionStartTimes.set(q.id, Date.now()); }
    function answerCurrentQuestion(choice) {
        if(!questions.length) return;
        const q = questions[currentIndex];
        if(answers.some(a=>a.questionId===q.id)){ showToast("Você já respondeu esta questão","info"); return; }
        let timeSpent = 0;
        const start = questionStartTimes.get(q.id);
        if(start) timeSpent = Math.round((Date.now()-start)/1000);
        const isCorrect = (choice === q.answer);
        answers.push({ questionId: q.id, isCorrect, userChoice: choice, timestamp: Date.now(), timeSpentSeconds: timeSpent });
        commentVisibleMap.set(q.id, false);
        saveToLocalStorage();
        renderAll();
        if(answers.length === questions.length) showToast("🎉 Todas as questões respondidas!","success");
    }
    function showCommentForCurrent() { if(!questions.length) return; const q=questions[currentIndex]; if(!answers.some(a=>a.questionId===q.id)){ showToast("Responda a questão primeiro","info"); return; } commentVisibleMap.set(q.id,true); saveToLocalStorage(); renderAll(); }
    function nextQuestion() {
        if(!questions.length) return;
        const q = questions[currentIndex];
        if(!answers.some(a=>a.questionId===q.id)){ showToast("Responda a atual antes de avançar","warning"); return; }
        if(currentIndex+1 < questions.length){ currentIndex++; renderAll(); } else { showToast("🏁 Simulado finalizado!","success"); }
    }
    function updateDisciplineForCurrent(newDisc) { if(!questions.length) return; questions[currentIndex].discipline = newDisc; saveToLocalStorage(); renderAll(); showToast(`Disciplina alterada para ${newDisc}`,"success"); }
    
    function computeStats() { const total=answers.length, correct=answers.filter(a=>a.isCorrect===true).length, wrong=total-correct, pct=total===0?0:Math.round((correct/total)*100); return {totalAnswered:total, totalCorrect:correct, totalWrong:wrong, percentage:pct}; }
    
    function renderDashboard() {
        const stats = computeStats();
        dashboardContainer.innerHTML = `
            <div class="grid grid-cols-2 gap-2 mb-3">
                <div class="bg-gray-50 rounded-xl p-2 text-center"><i class="fas fa-check-circle text-green-500 text-lg"></i><div class="text-xl font-bold">${stats.totalCorrect}</div><div class="text-[11px] text-gray-500">Acertos</div></div>
                <div class="bg-gray-50 rounded-xl p-2 text-center"><i class="fas fa-times-circle text-red-500 text-lg"></i><div class="text-xl font-bold">${stats.totalWrong}</div><div class="text-[11px] text-gray-500">Erros</div></div>
                <div class="bg-gray-50 rounded-xl p-2 text-center"><i class="fas fa-list-check text-blue-500 text-lg"></i><div class="text-xl font-bold">${stats.totalAnswered}</div><div class="text-[11px] text-gray-500">Respondidas</div></div>
                <div class="bg-gray-50 rounded-xl p-2 text-center"><i class="fas fa-percent text-purple-500 text-lg"></i><div class="text-xl font-bold">${stats.percentage}%</div><div class="text-[11px] text-gray-500">Aproveitamento</div></div>
            </div>
            <canvas id="statsPieChart" height="120" style="max-height:120px; width:100%"></canvas>
        `;
        if(statsChart) statsChart.destroy();
        const ctx = document.getElementById('statsPieChart')?.getContext('2d');
        if(ctx && (stats.totalCorrect+stats.totalWrong)>0){
            statsChart = new Chart(ctx, { type:'doughnut', data:{ labels:['Acertos','Erros'], datasets:[{ data:[stats.totalCorrect,stats.totalWrong], backgroundColor:['#16a34a','#dc2626'], borderWidth:0 }] }, options:{ responsive:true, maintainAspectRatio:true, plugins:{ legend:{ position:'bottom', labels:{ boxWidth:8, font:{size:9} } } } } });
        } else if(ctx) { statsChart = new Chart(ctx, { type:'doughnut', data:{ labels:['Sem dados'], datasets:[{ data:[1], backgroundColor:['#e2e8f0'] }] }, options:{ plugins:{ legend:{ display:false } } } }); }
        
        const discMap = new Map();
        questions.forEach(q => { if(!discMap.has(q.discipline)) discMap.set(q.discipline, { total:0, correct:0 }); });
        answers.forEach(ans => {
            const q = questions.find(qq=>qq.id===ans.questionId);
            if(q){
                const d = q.discipline;
                const stat = discMap.get(d) || { total:0, correct:0 };
                stat.total++;
                if(ans.isCorrect) stat.correct++;
                discMap.set(d, stat);
            }
        });
        const labels = [], dataCorrect = [], dataTotal = [];
        for(let [disc, stat] of discMap.entries()){
            labels.push(disc.length>14?disc.substring(0,12)+'..':disc);
            dataCorrect.push(stat.correct);
            dataTotal.push(stat.total);
        }
        if(disciplineChart) disciplineChart.destroy();
        const barCtx = document.getElementById('disciplineBarChart')?.getContext('2d');
        if(barCtx && labels.length){
            disciplineChart = new Chart(barCtx, { type:'bar', data:{ labels, datasets:[{ label:'Acertos', data:dataCorrect, backgroundColor:'#2e7d32', borderRadius:4 }, { label:'Respondidas', data:dataTotal, backgroundColor:'#9ca3af', borderRadius:4 }] }, options:{ responsive:true, maintainAspectRatio:true, scales:{ y:{ beginAtZero:true, ticks:{ stepSize:1, font:{size:9} } } }, plugins:{ tooltip:{ callbacks:{ label:(ctx)=>`${ctx.dataset.label}: ${ctx.raw}` } }, legend:{ labels:{ font:{size:9} } } } } });
        } else if(barCtx) { disciplineChart = new Chart(barCtx, { type:'bar', data:{ labels:['Nenhuma'], datasets:[{ data:[0] }] }, options:{ plugins:{ legend:{ display:false } } } }); }
    }
    
    function renderQuestion() {
        if(!questions.length){ questionPanel.innerHTML=`<div class="flex flex-col items-center justify-center py-20 text-gray-400"><i class="fas fa-inbox text-6xl mb-3"></i><p class="text-center">Nenhuma questão carregada</p><p class="text-sm">Importe usando o painel ao lado</p></div>`; return; }
        const q = questions[currentIndex];
        const hasAnswered = answers.some(a=>a.questionId===q.id);
        const userRec = answers.find(a=>a.questionId===q.id);
        const showComment = commentVisibleMap.get(q.id) || false;
        const progress = ((currentIndex + (hasAnswered?1:0))/questions.length)*100;
        let feedbackHtml = '';
        if(hasAnswered){
            const correctAnswer = q.answer;
            const userChoice = userRec.userChoice;
            const resultText = userRec.isCorrect ? '✅ Você acertou!' : `❌ Você errou! A resposta correta é ${correctAnswer}.`;
            feedbackHtml = `<div class="p-4 rounded-xl mb-5 ${userRec.isCorrect ? 'answer-feedback-correct' : 'answer-feedback-wrong'} border-l-4 ${userRec.isCorrect ? 'border-green-600' : 'border-red-600'}"><p class="font-semibold">${resultText}</p><p class="text-sm mt-1">Sua resposta: ${userChoice}</p></div>`;
        }
        let commentSection='';
        if(hasAnswered && !showComment) commentSection = `<button id="showCommentBtn" class="w-full mt-3 bg-gray-100 hover:bg-gray-200 py-3 rounded-xl font-medium flex items-center justify-center gap-2 transition"><i class="far fa-comment-dots"></i> Ver comentário</button>`;
        else if(hasAnswered && showComment) commentSection = `<div class="mt-5 p-5 bg-gray-50 rounded-xl border border-gray-200"><i class="fas fa-quote-left text-gray-400 mr-2"></i><span class="font-semibold">Comentário do professor:</span><p class="text-gray-700 text-sm mt-2 leading-relaxed">${formatBoldItalic(q.comment)}</p></div>`;
        const disciplineOptions = DISCIPLINAS_LIST.map(d => `<option value="${d}" ${q.discipline===d?'selected':''}>${d}</option>`).join('');
        const nextButtonHtml = hasAnswered ? `<div class="mb-5"><button id="nextBtnAction" class="w-full bg-gray-800 hover:bg-gray-900 text-white font-semibold py-3 rounded-xl transition flex items-center justify-center gap-2"><i class="fas fa-arrow-right"></i> ${currentIndex+1>=questions.length ? 'Finalizar simulado' : 'Próxima questão'}</button></div>` : '';
        const html = `
            <div>
                <div class="mb-4"><div class="flex justify-between text-sm text-gray-500 mb-1"><span>Progresso ${currentIndex+ (hasAnswered?1:0)}/${questions.length}</span><span>${Math.round(progress)}%</span></div><div class="h-2 bg-gray-100 rounded-full overflow-hidden"><div class="progress-bar-animated h-full bg-emerald-500 rounded-full" style="width:${progress}%"></div></div></div>
                <div class="flex flex-wrap justify-between items-start gap-3 mb-5 pb-2 border-b border-gray-200">
                    <span class="bg-emerald-100 text-emerald-800 text-xs font-bold px-3 py-1 rounded-full"><i class="far fa-question-circle mr-1"></i> Questão ${q.number}</span>
                    <div class="flex gap-2 items-center"><label class="text-xs text-gray-500">Disciplina:</label><select id="discEditSelect" class="text-xs border rounded-lg p-1 bg-white">${disciplineOptions}</select><button id="saveDiscBtn" class="text-emerald-600 text-xs"><i class="fas fa-save"></i></button></div>
                </div>
                ${nextButtonHtml}
                <div class="bg-gray-50/80 p-6 rounded-2xl mb-6"><p class="question-text text-gray-800 font-medium leading-relaxed">${formatBoldItalic(q.text)}</p></div>
                <div id="answerArea">
                    ${!hasAnswered ? `<div class="flex gap-4 mb-5"><button id="btnCertoQuest" class="flex-1 btn-certo text-white font-bold py-4 rounded-xl text-lg shadow-md transition flex items-center justify-center gap-2"><i class="fas fa-check-circle"></i> Certo</button><button id="btnErradoQuest" class="flex-1 btn-errado text-white font-bold py-4 rounded-xl text-lg shadow-md flex items-center justify-center gap-2"><i class="fas fa-times-circle"></i> Errado</button></div>` : feedbackHtml}
                    ${commentSection}
                </div>
            </div>
        `;
        questionPanel.innerHTML = html;
        if(!hasAnswered){
            document.getElementById('btnCertoQuest')?.addEventListener('click',()=>answerCurrentQuestion('Certo'));
            document.getElementById('btnErradoQuest')?.addEventListener('click',()=>answerCurrentQuestion('Errado'));
            recordDisplayTimeForCurrent();
        } else {
            document.getElementById('showCommentBtn')?.addEventListener('click',()=>showCommentForCurrent());
            document.getElementById('nextBtnAction')?.addEventListener('click',()=>nextQuestion());
        }
        document.getElementById('saveDiscBtn')?.addEventListener('click',()=>{ const newDisc=document.getElementById('discEditSelect').value; updateDisciplineForCurrent(newDisc); });
    }
    
    function resetProgressOnly() { if(questions.length===0) return; answers = []; currentIndex=0; commentVisibleMap.clear(); questionStartTimes.clear(); saveToLocalStorage(); renderAll(); showToast("Progresso reiniciado","success"); }
    function clearAllData() { if(confirm("Apagar TODAS as questões, respostas e cronômetro?")){ localStorage.removeItem('auditor_questions'); localStorage.removeItem('auditor_answers'); localStorage.removeItem('auditor_currentIndex'); localStorage.removeItem('auditor_commentsVisible'); localStorage.removeItem('auditor_timerSeconds'); localStorage.removeItem('auditor_timerRunning'); questions=[]; answers=[]; currentIndex=0; commentVisibleMap.clear(); questionStartTimes.clear(); resetTimer(); renderAll(); showToast("Todos os dados removidos","info"); } }
    
    async function generateWrongAnswersPDF() { if(questions.length===0){ showToast("Nenhuma questão","warning"); return; } const wrongs = answers.filter(a=>!a.isCorrect); if(wrongs.length===0){ showToast("Nenhum erro! 🎉","success"); return; } const { jsPDF } = window.jspdf; const doc = new jsPDF(); doc.setFontSize(18); doc.text("Caderno de Erros - AuditorQuest",14,20); doc.setFontSize(10); doc.text(`Gerado: ${new Date().toLocaleString()}`,14,30); let y=40; for(let wa of wrongs){ const q=questions.find(qq=>qq.id===wa.questionId); if(!q) continue; if(y>260){ doc.addPage(); y=20; } doc.setFont(undefined,'bold'); doc.text(`Questão ${q.number} - ${q.discipline}`,14,y); y+=7; doc.setFont(undefined,'normal'); const txt=doc.splitTextToSize(`Enunciado: ${q.text}`,180); doc.text(txt,14,y); y+=txt.length*5; doc.text(`Sua resposta: ${wa.userChoice} | Gabarito: ${q.answer}`,14,y); y+=6; const com=doc.splitTextToSize(`Comentário: ${q.comment}`,180); doc.text(com,14,y); y+=com.length*5+6; doc.line(14,y-3,200,y-3); } doc.save(`caderno_erros_${Date.now()}.pdf`); showToast("Caderno de erros gerado","success"); }
    async function generatePDFReport() { if(questions.length===0){ showToast("Nenhuma questão","warning"); return; } const stats=computeStats(); const { jsPDF } = window.jspdf; const doc = new jsPDF({orientation:'landscape'}); doc.setFontSize(16); doc.text("Relatório de Desempenho - AuditorQuest",14,18); doc.setFontSize(10); doc.text(`Data: ${new Date().toLocaleString()} | Acertos: ${stats.totalCorrect} | Erros: ${stats.totalWrong} | Aproveitamento: ${stats.percentage}%`,14,28); const totalTime=answers.reduce((s,a)=>s+(a.timeSpentSeconds||0),0); doc.text(`Tempo total em respostas: ${Math.floor(totalTime/60)}min ${totalTime%60}s`,14,36); const tableData = answers.map(a=>{ const q=questions.find(qq=>qq.id===a.questionId); if(!q) return null; return [q.number, q.discipline, q.text.substring(0,70), a.userChoice, q.answer, a.isCorrect?"Acertou":"Errou", a.timeSpentSeconds?`${a.timeSpentSeconds}s`:"—", q.comment.substring(0,50)]; }).filter(v=>v); if(tableData.length) doc.autoTable({ head:[["#","Disciplina","Enunciado","Resp","Gabarito","Result","Tempo","Comentário"]], body:tableData, startY:45, styles:{fontSize:7}, columnStyles:{2:{cellWidth:45},7:{cellWidth:35}} }); doc.save(`relatorio_${Date.now()}.pdf`); showToast("Relatório completo gerado","success"); }
    function generateNotebookLMPrompt() { if(questions.length===0){ showToast("Nenhuma questão","warning"); return; } const stats=computeStats(); let txt=`# AUDITORQUEST - PROMPT NOTEBOOKLM\nTotal respondidas: ${stats.totalAnswered} | Acertos: ${stats.totalCorrect} | Erros: ${stats.totalWrong}\n\n`; questions.forEach(q=>{ const resp=answers.find(a=>a.questionId===q.id); const status=resp? (resp.isCorrect?"Acertou":"Errou") : "Não respondida"; txt+=`### Questão ${q.number} (${q.discipline})\nEnunciado: ${q.text}\nGabarito: ${q.answer}\nSua resposta: ${resp?resp.userChoice:"—"} (${status})\nComentário: ${q.comment}\n\n`; }); const blob=new Blob([txt],{type:"text/plain"}); const a=document.createElement('a'); a.href=URL.createObjectURL(blob); a.download=`prompt_notebooklm_${Date.now()}.txt`; a.click(); URL.revokeObjectURL(a.href); showToast("Prompt NotebookLM baixado","success"); }
    
    function renderAll() { renderDashboard(); renderQuestion(); saveToLocalStorage(); updateCounterDisplay(); }
    
    function initEventListeners(){
        importBtn.onclick=()=>{ const raw=rawInput.value; if(importQuestions(raw)) rawInput.value=''; };
        loadExampleBtn.onclick=()=>{ rawInput.value=`Questão 1: "No movimento abolicionista de Mossoró, a atuação feminina limitou-se ao suporte doméstico e financeiro, não havendo registros de comissões formadas por mulheres para persuadir proprietários de escravizados."\nComentário: "A afirmação está incorreta, pois o movimento contou com a atuação direta de mulheres da elite, como Dona Amélia Dantas de Souza Melo, que constituíram uma Comissão Libertadora para visitar casas e persuadir proprietários a alforriar os cativos."\nGabarito: Errado\n\nQuestão 2: "O sistema de sesmarias previa que o sesmeiro teria a propriedade plena da terra desde que nela se fizesse produzir no prazo máximo de cinco anos."\nComentário: "Prazo de cinco anos sob pena de perda."\nGabarito: Certo`; importQuestions(rawInput.value, "História do Rio Grande do Norte"); };
        importPdfBtn.onclick=()=>pdfUploadInput.click();
        pdfUploadInput.onchange=async(e)=>{ if(e.target.files.length) await importFromPdf(e.target.files[0]); e.target.value=''; };
        generatePdfReportBtn.onclick=generatePDFReport;
        downloadWrongAnswersBtn.onclick=generateWrongAnswersPDF;
        downloadNotebookLMBtn.onclick=generateNotebookLMPrompt;
        timerStartBtn.onclick=startTimer;
        timerPauseBtn.onclick=pauseTimer;
        timerStopBtn.onclick=resetTimer;
        resetOnlyProgressBtn.onclick=resetProgressOnly;
        clearAllDataBtn.onclick=clearAllData;
    }
    
    function updateCurrentTime() { const now=new Date(); currentTimeSpan.textContent=`${now.getHours().toString().padStart(2,'0')}:${now.getMinutes().toString().padStart(2,'0')}:${now.getSeconds().toString().padStart(2,'0')}`; }
    setInterval(updateCurrentTime,1000);
    
    // Tela de login
    function doLogin() {
        document.getElementById('loginScreen').style.display = 'none';
        document.getElementById('mainApp').style.display = 'flex';
        loadFromLocalStorage();
        renderAll();
    }
    
    function setGreeting() {
        const hour = new Date().getHours();
        let greeting = '';
        if (hour < 12) greeting = 'Bom dia! ☀️';
        else if (hour < 18) greeting = 'Boa tarde! 🌤️';
        else greeting = 'Boa noite! 🌙';
        document.getElementById('greetingMessage').innerText = greeting;
    }
    setGreeting();
    
    initEventListeners();
</script>
</body>
</html>
