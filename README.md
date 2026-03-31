<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover, user-scalable=yes">
    <title>Questões CESPE/CEBRASPE | Treino Certo ou Errado + Disciplinas</title>
    <!-- Tailwind CSS + Font Awesome + Chart.js -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
    <!-- Bibliotecas para PDF -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.31/jspdf.plugin.autotable.min.js"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: { 'sans': ['Inter', 'system-ui', 'Segoe UI', 'sans-serif'] },
                    animation: { 'fade-in': 'fadeIn 0.3s ease-out', 'slide-up': 'slideUp 0.2s ease-out' },
                    keyframes: { fadeIn: { '0%': { opacity: '0' }, '100%': { opacity: '1' } }, slideUp: { '0%': { transform: 'translateY(8px)', opacity: '0' }, '100%': { transform: 'translateY(0)', opacity: '1' } } }
                }
            }
        }
    </script>
    <style>
        body { background: linear-gradient(135deg, #f8fafc 0%, #f1f5f9 100%); font-family: 'Inter', system-ui, sans-serif; }
        .card-modern { transition: all 0.2s ease; }
        .card-modern:hover { transform: translateY(-2px); box-shadow: 0 20px 25px -12px rgba(0, 0, 0, 0.08); }
        .btn-certo { background: linear-gradient(135deg, #22c55e, #16a34a); }
        .btn-errado { background: linear-gradient(135deg, #ef4444, #dc2626); }
        .progress-bar { transition: width 0.4s cubic-bezier(0.2, 0.9, 0.4, 1.1); }
        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-track { background: #e2e8f0; border-radius: 10px; }
        ::-webkit-scrollbar-thumb { background: #94a3b8; border-radius: 10px; }
        .max-w-wide { max-width: 1600px; margin: 0 auto; }
        .break-word { word-break: break-word; }
    </style>
</head>
<body>

<div class="max-w-wide px-4 py-6 md:py-8 w-full">
    <!-- Header -->
    <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 mb-8 pb-4 border-b border-gray-200">
        <div class="flex items-center gap-4 flex-wrap">
            <div class="bg-gradient-to-br from-emerald-500 to-green-600 w-10 h-10 rounded-xl flex items-center justify-center shadow-md">
                <i class="fas fa-check-double text-white text-xl"></i>
            </div>
            <div class="hidden sm:flex items-center gap-2 bg-gray-100 px-3 py-2 rounded-full">
                <i class="fas fa-file-alt text-green-600 text-lg"></i>
                <i class="fas fa-arrow-right text-gray-400 text-sm"></i>
                <i class="fas fa-pen-fancy text-blue-500 text-lg"></i>
                <span class="text-xs font-medium text-gray-600">Modo simulado + disciplinas</span>
            </div>
            <div>
                <h1 class="text-2xl md:text-3xl font-bold text-gray-800 tracking-tight">Questões <span class="text-green-600">CESPE/CEBRASPE</span></h1>
                <p class="text-gray-500 text-sm">Treine com estilo Certo ou Errado | até 20 questões</p>
            </div>
        </div>
        <div class="flex gap-2">
            <button id="resetOnlyProgressBtn" class="bg-white border border-gray-300 text-gray-700 px-4 py-2 rounded-xl text-sm font-medium hover:bg-gray-50 transition shadow-sm flex items-center gap-2"><i class="fas fa-undo-alt"></i> Resetar progresso</button>
            <button id="clearAllDataBtn" class="bg-red-50 border border-red-200 text-red-600 px-4 py-2 rounded-xl text-sm font-medium hover:bg-red-100 transition shadow-sm flex items-center gap-2"><i class="fas fa-trash-alt"></i> Limpar tudo</button>
        </div>
    </div>

    <div class="grid lg:grid-cols-12 gap-6">
        <!-- Sidebar esquerda (mais larga) -->
        <div class="lg:col-span-5 space-y-6">
            <!-- Card Importação com disciplina e PDF -->
            <div class="bg-white rounded-2xl shadow-sm border border-gray-100 p-5 card-modern">
                <div class="flex items-center gap-3 mb-4">
                    <div class="bg-green-100 p-2 rounded-xl"><i class="fas fa-file-import text-green-600 text-lg"></i></div>
                    <div>
                        <h2 class="font-bold text-gray-800">Importar questões</h2>
                        <p class="text-xs text-gray-500">Formato: Questão X: enunciado + Comentário: + Gabarito: Certo/Errado</p>
                    </div>
                </div>
                <!-- Seletor de disciplina para importação -->
                <div class="mb-3">
                    <label class="block text-sm font-medium text-gray-700 mb-1">Disciplina padrão para importação</label>
                    <select id="disciplineSelect" class="w-full border border-gray-200 rounded-xl p-2.5 text-sm bg-gray-50 focus:ring-2 focus:ring-green-400">
                        <option value="Língua Portuguesa">Língua Portuguesa</option>
                        <option value="Raciocínio Lógico-Matemático">Raciocínio Lógico-Matemático</option>
                        <option value="Língua Inglesa">Língua Inglesa</option>
                        <option value="Noções de Direito Administrativo">Noções de Direito Administrativo</option>
                        <option value="Direito Constitucional">Direito Constitucional</option>
                        <option value="Jurisprudência Aplicada">Jurisprudência Aplicada</option>
                        <option value="Administração Pública">Administração Pública</option>
                        <option value="Políticas Públicas">Políticas Públicas</option>
                        <option value="Gestão de Materiais">Gestão de Materiais</option>
                        <option value="Administração Financeira e Orçamentária (AFO)">Administração Financeira e Orçamentária (AFO)</option>
                        <option value="Lei de Responsabilidade Fiscal (LRF)">Lei de Responsabilidade Fiscal (LRF)</option>
                        <option value="Controle Externo">Controle Externo</option>
                        <option value="Auditoria Governamental">Auditoria Governamental</option>
                        <option value="Licitações e Contratos Administrativos">Licitações e Contratos Administrativos</option>
                        <option value="Legislação Institucional">Legislação Institucional</option>
                        <option value="Informática e Competências Digitais">Informática e Competências Digitais</option>
                        <option value="História do Rio Grande do Norte">História do Rio Grande do Norte</option>
                        <option value="Geografia do Rio Grande do Norte">Geografia do Rio Grande do Norte</option>
                    </select>
                </div>
                <textarea id="rawQuestionsInput" rows="6" class="w-full border border-gray-200 rounded-xl p-3 text-sm font-mono bg-gray-50 focus:ring-2 focus:ring-green-400 focus:outline-none transition" placeholder='Questão 1: "A Declaração Universal dos Direitos Humanos foi adotada em 1948."

Comentário: "Adotada pela ONU em 10 de dezembro de 1948."

Gabarito: Certo'></textarea>
                <div class="flex flex-wrap gap-3 mt-4">
                    <button id="importBtn" class="flex-1 bg-green-600 hover:bg-green-700 text-white font-semibold py-2.5 rounded-xl transition shadow-md flex items-center justify-center gap-2"><i class="fas fa-cloud-upload-alt"></i> Importar texto</button>
                    <button id="loadExampleBtn" class="bg-gray-100 hover:bg-gray-200 text-gray-700 font-medium py-2.5 px-5 rounded-xl transition flex items-center gap-2"><i class="fas fa-code-branch"></i> Exemplo</button>
                    <button id="importPdfBtn" class="bg-blue-600 hover:bg-blue-700 text-white font-semibold py-2.5 px-5 rounded-xl transition shadow-md flex items-center gap-2"><i class="fas fa-file-pdf"></i> Extrair PDF</button>
                    <input type="file" id="pdfUploadInput" accept=".pdf" class="hidden" />
                </div>
                <p id="importFeedback" class="text-xs text-center mt-3 text-gray-400 hidden"></p>
                <div class="text-xs text-gray-400 mt-2 flex justify-between"><span>Máx. 20 questões</span><span id="questionCounter">0/20 carregadas</span></div>
            </div>

            <!-- Dashboard -->
            <div class="bg-white rounded-2xl shadow-sm border border-gray-100 p-5 card-modern">
                <div class="flex items-center justify-between flex-wrap gap-2 mb-4">
                    <div class="flex items-center gap-2"><i class="fas fa-chart-line text-blue-500 text-lg"></i><h2 class="font-bold text-gray-800">Meu desempenho</h2></div>
                    <button id="generatePdfReportBtn" class="bg-indigo-50 hover:bg-indigo-100 text-indigo-700 text-sm px-3 py-1.5 rounded-lg transition flex items-center gap-1"><i class="fas fa-file-pdf"></i> Relatório PDF</button>
                </div>
                <div id="dashboardContainer"><div class="text-center py-8 text-gray-400"><i class="fas fa-chart-simple text-3xl mb-2 block"></i> Nenhuma questão respondida ainda</div></div>
            </div>

            <!-- NOVO: Cronômetro de Estudo -->
            <div class="bg-white rounded-2xl shadow-sm border border-gray-100 p-5 card-modern">
                <div class="flex items-center gap-2 mb-3">
                    <i class="fas fa-hourglass-half text-amber-600 text-lg"></i>
                    <h2 class="font-bold text-gray-800">Cronômetro de Estudo</h2>
                </div>
                <div class="text-center">
                    <div id="timerDisplay" class="text-4xl font-mono font-bold text-gray-700 bg-gray-50 inline-block px-6 py-3 rounded-xl mb-3">00:00:00</div>
                    <div class="flex justify-center gap-3 mt-2">
                        <button id="timerStartBtn" class="bg-green-600 hover:bg-green-700 text-white px-5 py-2 rounded-xl text-sm font-medium flex items-center gap-2"><i class="fas fa-play"></i> Iniciar</button>
                        <button id="timerPauseBtn" class="bg-yellow-500 hover:bg-yellow-600 text-white px-5 py-2 rounded-xl text-sm font-medium flex items-center gap-2"><i class="fas fa-pause"></i> Pausar</button>
                        <button id="timerStopBtn" class="bg-red-500 hover:bg-red-600 text-white px-5 py-2 rounded-xl text-sm font-medium flex items-center gap-2"><i class="fas fa-stop"></i> Parar</button>
                    </div>
                    <p class="text-xs text-gray-400 mt-3">Registre o tempo total de estudo. Use Iniciar, Pausar e Parar (zerar).</p>
                </div>
            </div>

            <!-- NOVO: Ferramentas adicionais (Caderno de Erros + NotebookLM) -->
            <div class="bg-white rounded-2xl shadow-sm border border-gray-100 p-5 card-modern">
                <div class="flex items-center gap-2 mb-4">
                    <i class="fas fa-tools text-purple-600"></i>
                    <h2 class="font-bold text-gray-800">Ferramentas de Revisão</h2>
                </div>
                <div class="flex flex-col sm:flex-row gap-3">
                    <button id="downloadWrongAnswersBtn" class="flex-1 bg-rose-50 hover:bg-rose-100 text-rose-700 font-medium py-2.5 rounded-xl transition flex items-center justify-center gap-2 border border-rose-200"><i class="fas fa-book-open"></i> Caderno de Erros (PDF)</button>
                    <button id="downloadNotebookLMBtn" class="flex-1 bg-sky-50 hover:bg-sky-100 text-sky-700 font-medium py-2.5 rounded-xl transition flex items-center justify-center gap-2 border border-sky-200"><i class="fas fa-download"></i> Prompt NotebookLM</button>
                </div>
                <p class="text-xs text-gray-400 mt-3 text-center">Caderno de Erros: apenas questões erradas + comentários.<br>Prompt: exporta todas as questões para estudo no Google NotebookLM.</p>
            </div>
        </div>

        <!-- Coluna direita: Questão ativa (largura expandida) -->
        <div class="lg:col-span-7">
            <div class="bg-white rounded-2xl shadow-sm border border-gray-100 p-5 md:p-6 card-modern min-h-[550px] transition-all" id="questionPanel">
                <div class="flex flex-col items-center justify-center py-12 text-gray-400">
                    <i class="fas fa-brain text-5xl mb-3 text-gray-300"></i>
                    <p class="text-center">Importe ou selecione questões<br>para começar a treinar</p>
                </div>
            </div>
        </div>
    </div>
</div>

<script>
    // ---------- CONSTANTES ----------
    const MAX_QUESTIONS = 20;
    const DISCIPLINAS = [
        "Língua Portuguesa", "Raciocínio Lógico-Matemático", "Língua Inglesa", "Noções de Direito Administrativo",
        "Direito Constitucional", "Jurisprudência Aplicada", "Administração Pública", "Políticas Públicas",
        "Gestão de Materiais", "Administração Financeira e Orçamentária (AFO)", "Lei de Responsabilidade Fiscal (LRF)",
        "Controle Externo", "Auditoria Governamental", "Licitações e Contratos Administrativos",
        "Legislação Institucional", "Informática e Competências Digitais", "História do Rio Grande do Norte",
        "Geografia do Rio Grande do Norte"
    ];
    
    let questions = [];            // { id, number, text, comment, answer, discipline }
    let answers = [];              // { questionId, isCorrect, userChoice, timestamp }
    let currentIndex = 0;
    let commentVisibleMap = new Map();   // questionId -> bool
    
    // Variáveis do cronômetro
    let timerInterval = null;
    let timerSeconds = 0;
    let timerRunning = false;
    
    // DOM
    const dashboardContainer = document.getElementById('dashboardContainer');
    const questionPanel = document.getElementById('questionPanel');
    const rawInput = document.getElementById('rawQuestionsInput');
    const importBtn = document.getElementById('importBtn');
    const loadExampleBtn = document.getElementById('loadExampleBtn');
    const resetProgressBtn = document.getElementById('resetOnlyProgressBtn');
    const clearAllBtn = document.getElementById('clearAllDataBtn');
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
    
    // pdf.js worker
    pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.worker.min.js';
    
    // ---------- localStorage ----------
    function saveToLocalStorage() {
        localStorage.setItem('cebraspe_questions_v2', JSON.stringify(questions));
        localStorage.setItem('cebraspe_answers_v2', JSON.stringify(answers));
        localStorage.setItem('cebraspe_currentIndex_v2', currentIndex.toString());
        localStorage.setItem('cebraspe_commentsVisible_v2', JSON.stringify([...commentVisibleMap.entries()]));
        // Salvar estado do cronômetro (opcional)
        localStorage.setItem('cebraspe_timerSeconds', timerSeconds.toString());
        localStorage.setItem('cebraspe_timerRunning', timerRunning.toString());
    }
    
    function loadFromLocalStorage() {
        const storedQuestions = localStorage.getItem('cebraspe_questions_v2');
        const storedAnswers = localStorage.getItem('cebraspe_answers_v2');
        const storedIndex = localStorage.getItem('cebraspe_currentIndex_v2');
        const storedComments = localStorage.getItem('cebraspe_commentsVisible_v2');
        if (storedQuestions) questions = JSON.parse(storedQuestions);
        if (storedAnswers) answers = JSON.parse(storedAnswers);
        if (storedIndex) currentIndex = parseInt(storedIndex, 10);
        if (storedComments) commentVisibleMap = new Map(JSON.parse(storedComments));
        else commentVisibleMap.clear();
        if (questions.length && currentIndex >= questions.length) currentIndex = questions.length - 1;
        if (currentIndex < 0) currentIndex = 0;
        updateCounterDisplay();
        
        // Carregar estado do cronômetro
        const storedSeconds = localStorage.getItem('cebraspe_timerSeconds');
        const storedRunning = localStorage.getItem('cebraspe_timerRunning');
        if (storedSeconds) timerSeconds = parseInt(storedSeconds, 10);
        if (storedRunning === 'true') {
            // Se estava rodando, recriar intervalo com cuidado
            timerRunning = true;
            startTimerInterval();
        } else {
            timerRunning = false;
        }
        updateTimerDisplay();
    }
    
    function clearAllData() {
        localStorage.removeItem('cebraspe_questions_v2');
        localStorage.removeItem('cebraspe_answers_v2');
        localStorage.removeItem('cebraspe_currentIndex_v2');
        localStorage.removeItem('cebraspe_commentsVisible_v2');
        localStorage.removeItem('cebraspe_timerSeconds');
        localStorage.removeItem('cebraspe_timerRunning');
        questions = [];
        answers = [];
        currentIndex = 0;
        commentVisibleMap.clear();
        // Resetar cronômetro também
        resetTimer();
        renderAll();
        showToast("Todos os dados foram apagados.", "info");
    }
    
    function resetProgressOnly() {
        if (questions.length === 0) return;
        answers = [];
        currentIndex = 0;
        commentVisibleMap.clear();
        saveToLocalStorage();
        renderAll();
        showToast("Progresso resetado. Todas as respostas foram removidas.", "success");
    }
    
    // ---------- PARSER (enunciado, comentário, gabarito) ----------
    function parseQuestionsFromText(text) {
        const questionsArray = [];
        const blockRegex = /Questão\s+(\d+):\s*([\s\S]*?)(?=Questão\s+\d+:|$)/gi;
        let match;
        while ((match = blockRegex.exec(text)) !== null) {
            const number = parseInt(match[1], 10);
            let blockContent = match[2].trim();
            if (!blockContent) continue;
            let answer = 'Certo';
            const gabaritoRegex = /Gabarito:\s*(Certo|Errado)/i;
            const gabMatch = blockContent.match(gabaritoRegex);
            if (gabMatch) { answer = gabMatch[1]; blockContent = blockContent.replace(gabaritoRegex, ''); }
            let comment = '';
            const commentRegex = /Comentário:\s*([\s\S]*?)(?=(?:Gabarito:|$))/i;
            const commentMatch = blockContent.match(commentRegex);
            if (commentMatch) {
                let rawComment = commentMatch[1].trim().replace(/^["']|["']$/g, '').replace(/\n{3,}/g, '\n\n');
                comment = rawComment || "Sem comentário detalhado.";
                blockContent = blockContent.replace(commentRegex, '');
            } else { comment = "Sem comentário detalhado."; }
            let questionText = blockContent.trim().replace(/^["']|["']$/g, '').trim();
            if (questionText === "" && blockContent !== "") {
                const lines = blockContent.split(/\r?\n/);
                questionText = lines[0].replace(/^["']|["']$/g, '').trim();
            }
            if (questionText.length > 0) {
                questionsArray.push({ id: Date.now() + Math.random() * 10000 + number, number, text: questionText, comment, answer });
            }
        }
        // fallback simples
        if (questionsArray.length === 0 && text.includes('Questão')) {
            const lines = text.split(/\r?\n/);
            let current = null;
            for (let line of lines) {
                line = line.trim();
                if (line.match(/^Questão\s+\d+:/i)) {
                    if (current && current.text) questionsArray.push(current);
                    const numMatch = line.match(/Questão\s+(\d+):/i);
                    const num = numMatch ? parseInt(numMatch[1]) : questionsArray.length+1;
                    let textPart = line.replace(/^Questão\s+\d+:\s*/i, '').replace(/^["']|["']$/g, '');
                    current = { id: Date.now()+Math.random(), number: num, text: textPart, comment: '', answer: 'Certo' };
                } else if (current && line.match(/^Comentário:/i)) {
                    current.comment = line.replace(/^Comentário:\s*/i, '').replace(/^["']|["']$/g, '');
                } else if (current && line.match(/^Gabarito:\s*(Certo|Errado)/i)) {
                    const gabMatch = line.match(/Gabarito:\s*(Certo|Errado)/i);
                    if (gabMatch) current.answer = gabMatch[1];
                } else if (current && line !== "" && !line.match(/^Comentário|^Gabarito/i)) {
                    current.text += " " + line;
                }
            }
            if (current && current.text) questionsArray.push(current);
        }
        return questionsArray.map((q, idx) => ({ ...q, id: idx+1, comment: q.comment || "Sem comentário disponível." }));
    }
    
    // Importar com limite e disciplina
    function importQuestions(rawText, disciplineOverride = null) {
        if (!rawText.trim()) { showToast("Cole o texto das questões.", "warning"); return false; }
        let parsed = parseQuestionsFromText(rawText);
        if (parsed.length === 0) { showToast("Nenhuma questão válida encontrada. Use o formato padrão.", "error"); return false; }
        if (parsed.length > MAX_QUESTIONS) { showToast(`Limite de ${MAX_QUESTIONS} questões excedido. Foram encontradas ${parsed.length}.`, "error"); return false; }
        const discipline = disciplineOverride !== null ? disciplineOverride : disciplineSelect.value;
        parsed = parsed.map(q => ({ ...q, discipline: discipline }));
        questions = parsed;
        answers = [];
        currentIndex = 0;
        commentVisibleMap.clear();
        saveToLocalStorage();
        renderAll();
        showToast(`${parsed.length} questões importadas com disciplina: ${discipline}`, "success");
        return true;
    }
    
    // Extrair PDF
    async function extractTextFromPDF(file) {
        try {
            const arrayBuffer = await file.arrayBuffer();
            const pdf = await pdfjsLib.getDocument({ data: arrayBuffer }).promise;
            let fullText = "";
            for (let i = 1; i <= pdf.numPages; i++) {
                const page = await pdf.getPage(i);
                const textContent = await page.getTextContent();
                const pageText = textContent.items.map(item => item.str).join(" ");
                fullText += pageText + "\n";
            }
            return fullText;
        } catch (err) { throw new Error("Erro ao extrair PDF: " + err.message); }
    }
    
    async function importFromPdf(file) {
        showToast("Extraindo texto do PDF... aguarde.", "info");
        try {
            const extracted = await extractTextFromPDF(file);
            if (!extracted.trim()) throw new Error("Nenhum texto encontrado no PDF.");
            rawInput.value = extracted;
            importQuestions(extracted);
        } catch (err) { showToast(err.message, "error"); }
    }
    
    // Ações de resposta
    function answerCurrentQuestion(choice) {
        if (questions.length === 0) return;
        const currentQ = questions[currentIndex];
        if (answers.some(a => a.questionId === currentQ.id)) { showToast("Você já respondeu esta questão.", "info"); return; }
        const isCorrect = (choice === currentQ.answer);
        answers.push({ questionId: currentQ.id, isCorrect, userChoice: choice, timestamp: Date.now() });
        commentVisibleMap.set(currentQ.id, false);
        saveToLocalStorage();
        renderAll();
        // Se acabaram todas as questões, notificar (opcional)
        if (answers.length === questions.length) {
            showToast("🎉 Parabéns! Você respondeu todas as questões! Use o cronômetro para ver seu tempo total.", "success");
        }
    }
    
    function showCommentForCurrent() {
        if (!questions.length) return;
        const q = questions[currentIndex];
        if (!answers.some(a => a.questionId === q.id)) { showToast("Responda a questão primeiro.", "info"); return; }
        commentVisibleMap.set(q.id, true);
        saveToLocalStorage();
        renderAll();
    }
    
    function nextQuestion() {
        if (!questions.length) return;
        const currentQ = questions[currentIndex];
        if (!answers.some(a => a.questionId === currentQ.id)) { showToast("Responda a questão atual antes de avançar.", "warning"); return; }
        if (currentIndex + 1 < questions.length) { currentIndex++; renderAll(); }
        else { showToast("🏆 Você concluiu todas as questões! Parabéns!", "success"); }
    }
    
    function updateDisciplineForCurrent(newDiscipline) {
        if (!questions.length) return;
        const q = questions[currentIndex];
        q.discipline = newDiscipline;
        saveToLocalStorage();
        renderAll();
        showToast(`Disciplina alterada para ${newDiscipline}`, "success");
    }
    
    // Estatísticas
    function computeStats() {
        const totalAnswered = answers.length;
        const totalCorrect = answers.filter(a => a.isCorrect === true).length;
        const totalWrong = totalAnswered - totalCorrect;
        const percentage = totalAnswered === 0 ? 0 : Math.round((totalCorrect / totalAnswered) * 100);
        return { totalAnswered, totalCorrect, totalWrong, percentage };
    }
    
    // Gráfico
    let statsChart = null;
    function renderDashboard() {
        const stats = computeStats();
        const totalQuestions = questions.length;
        const htmlStats = `
            <div class="grid grid-cols-2 gap-3 mb-5">
                <div class="bg-gray-50 rounded-xl p-3 text-center"><i class="fas fa-check-circle text-green-500 mb-1 block"></i><span class="text-2xl font-bold text-gray-800">${stats.totalCorrect}</span><p class="text-xs text-gray-500">Acertos</p></div>
                <div class="bg-gray-50 rounded-xl p-3 text-center"><i class="fas fa-times-circle text-red-500 mb-1 block"></i><span class="text-2xl font-bold text-gray-800">${stats.totalWrong}</span><p class="text-xs text-gray-500">Erros</p></div>
                <div class="bg-gray-50 rounded-xl p-3 text-center"><i class="fas fa-list-check text-blue-500 mb-1 block"></i><span class="text-2xl font-bold text-gray-800">${stats.totalAnswered}</span><p class="text-xs text-gray-500">Respondidas</p></div>
                <div class="bg-gray-50 rounded-xl p-3 text-center"><i class="fas fa-percent text-purple-500 mb-1 block"></i><span class="text-2xl font-bold text-gray-800">${stats.percentage}%</span><p class="text-xs text-gray-500">Aproveitamento</p></div>
            </div>
            <div class="border-t border-gray-100 pt-3 mt-1"><canvas id="statsPieChart" width="200" height="200" style="max-height: 200px; margin: 0 auto;"></canvas></div>
            ${totalQuestions > 0 ? `<div class="text-center text-xs text-gray-400 mt-2">Total: ${totalQuestions} questões | Máx: ${MAX_QUESTIONS}</div>` : ''}
        `;
        dashboardContainer.innerHTML = htmlStats;
        if (statsChart) statsChart.destroy();
        const ctx = document.getElementById('statsPieChart')?.getContext('2d');
        if (ctx && (stats.totalCorrect + stats.totalWrong) > 0) {
            statsChart = new Chart(ctx, { type: 'doughnut', data: { labels: ['Acertos', 'Erros'], datasets: [{ data: [stats.totalCorrect, stats.totalWrong], backgroundColor: ['#22c55e', '#ef4444'], borderWidth: 0, hoverOffset: 8 }] }, options: { responsive: true, maintainAspectRatio: true, plugins: { legend: { position: 'bottom', font: { size: 11 } } } } });
        } else if (ctx) { statsChart = new Chart(ctx, { type: 'doughnut', data: { labels: ['Nenhum dado'], datasets: [{ data: [1], backgroundColor: ['#e2e8f0'] }] }, options: { plugins: { legend: { display: false } } } }); }
    }
    
    function updateCounterDisplay() { questionCounterSpan.innerText = `${questions.length}/${MAX_QUESTIONS}`; }
    
    // Renderização da questão (com edição de disciplina)
    function renderQuestion() {
        if (!questions.length) {
            questionPanel.innerHTML = `<div class="flex flex-col items-center justify-center py-12 text-gray-400"><i class="fas fa-inbox text-5xl mb-3"></i><p class="text-center font-medium">Nenhuma questão carregada</p><p class="text-sm text-center mt-1">Importe questões ao lado para iniciar os treinos.</p></div>`;
            return;
        }
        const currentQ = questions[currentIndex];
        const total = questions.length;
        const hasAnswered = answers.some(a => a.questionId === currentQ.id);
        const userAnswerRecord = answers.find(a => a.questionId === currentQ.id);
        const isAnswerCorrect = userAnswerRecord ? userAnswerRecord.isCorrect : false;
        const isCommentVisible = commentVisibleMap.get(currentQ.id) || false;
        const progressPercent = ((currentIndex + (hasAnswered ? 1 : 0)) / total) * 100;
        
        let feedbackHtml = '';
        if (hasAnswered) { feedbackHtml = `<div class="p-4 rounded-xl mb-4 ${isAnswerCorrect ? 'bg-green-50 border border-green-200 text-green-700' : 'bg-red-50 border border-red-200 text-red-700'}"><p class="font-medium text-center">${isAnswerCorrect ? '✅ Você acertou!' : `❌ Você errou! A resposta correta é ${currentQ.answer}.`}</p></div>`; }
        
        let commentSection = '';
        if (hasAnswered && !isCommentVisible) commentSection = `<button id="showCommentBtn" class="w-full py-3 bg-gray-100 hover:bg-gray-200 rounded-xl text-gray-700 font-medium transition flex items-center justify-center gap-2 mt-2"><i class="far fa-comment-dots"></i> Ver comentário</button>`;
        else if (hasAnswered && isCommentVisible) commentSection = `<div class="mt-4 p-4 bg-gray-50 rounded-xl border border-gray-200"><i class="fas fa-quote-left text-gray-400 mr-2"></i><span class="font-semibold text-gray-800">Comentário:</span><p class="text-gray-600 text-sm mt-1 leading-relaxed">${escapeHtml(currentQ.comment)}</p></div>`;
        
        const nextDisabled = !hasAnswered;
        const nextButtonText = (currentIndex + 1 >= total) ? '🏁 Finalizar simulado' : 'Próxima questão →';
        
        const disciplineOptions = DISCIPLINAS.map(d => `<option value="${d}" ${currentQ.discipline === d ? 'selected' : ''}>${d}</option>`).join('');
        
        const html = `
            <div class="animate-slide-up">
                <div class="mb-5"><div class="flex justify-between text-sm text-gray-600 mb-1"><span>Progresso</span><span>${currentIndex + (hasAnswered ? 1 : 0)}/${total}</span></div><div class="h-2 bg-gray-100 rounded-full overflow-hidden"><div class="progress-bar h-full bg-green-500 rounded-full" style="width: ${progressPercent}%"></div></div></div>
                <div class="flex justify-between items-start mb-4 pb-3 border-b border-gray-100 flex-wrap gap-2">
                    <span class="px-3 py-1 bg-green-100 text-green-700 text-xs font-semibold rounded-full"><i class="far fa-file-alt mr-1"></i> Questão ${currentQ.number}</span>
                    <div class="flex items-center gap-2"><label class="text-xs text-gray-500">Disciplina:</label><select id="disciplineEditSelect" class="text-xs border rounded-lg p-1 bg-white">${disciplineOptions}</select><button id="saveDisciplineBtn" class="text-blue-500 hover:text-blue-700 text-xs"><i class="fas fa-save"></i></button><button id="resetOnlyFromQuestionBtn" class="text-gray-400 hover:text-gray-600" title="Resetar progresso"><i class="fas fa-sync-alt"></i></button></div>
                </div>
                <div class="mb-6 bg-gray-50 p-4 rounded-xl"><p class="text-gray-800 text-base md:text-lg leading-relaxed break-word">${escapeHtml(currentQ.text)}</p></div>
                <div id="answerArea">
                    ${!hasAnswered ? `<div class="flex gap-4 mb-6"><button id="btnCerto" class="flex-1 btn-certo text-white font-bold py-4 rounded-xl text-lg flex items-center justify-center gap-2 shadow-md"><i class="fas fa-check-circle"></i> Certo</button><button id="btnErrado" class="flex-1 btn-errado text-white font-bold py-4 rounded-xl text-lg flex items-center justify-center gap-2 shadow-md"><i class="fas fa-times-circle"></i> Errado</button></div>` : feedbackHtml}
                    ${commentSection}
                    ${hasAnswered ? `<div class="mt-5"><button id="nextQuestionBtn" class="w-full bg-gray-900 hover:bg-gray-800 text-white font-semibold py-3 rounded-xl transition flex items-center justify-center gap-2 ${nextDisabled ? 'opacity-50 cursor-not-allowed' : ''}" ${nextDisabled ? 'disabled' : ''}><i class="fas fa-arrow-right"></i> ${nextButtonText}</button></div>` : ''}
                </div>
                ${hasAnswered && currentIndex+1 >= total ? `<div class="mt-3 p-3 bg-green-50 rounded-xl text-center text-green-700 text-sm"><i class="fas fa-trophy"></i> Parabéns! Você finalizou todas as questões.</div>` : ''}
            </div>
        `;
        questionPanel.innerHTML = html;
        if (!hasAnswered) {
            document.getElementById('btnCerto')?.addEventListener('click', () => answerCurrentQuestion('Certo'));
            document.getElementById('btnErrado')?.addEventListener('click', () => answerCurrentQuestion('Errado'));
        } else {
            document.getElementById('showCommentBtn')?.addEventListener('click', () => showCommentForCurrent());
            const nextBtn = document.getElementById('nextQuestionBtn');
            if (nextBtn && !nextDisabled) nextBtn.addEventListener('click', () => nextQuestion());
        }
        document.getElementById('resetOnlyFromQuestionBtn')?.addEventListener('click', () => resetProgressOnly());
        const saveDiscBtn = document.getElementById('saveDisciplineBtn');
        const discSelect = document.getElementById('disciplineEditSelect');
        if (saveDiscBtn && discSelect) saveDiscBtn.addEventListener('click', () => updateDisciplineForCurrent(discSelect.value));
    }
    
    // ---------- Funções do Cronômetro ----------
    function updateTimerDisplay() {
        const hours = Math.floor(timerSeconds / 3600);
        const minutes = Math.floor((timerSeconds % 3600) / 60);
        const secs = timerSeconds % 60;
        timerDisplaySpan.textContent = `${hours.toString().padStart(2,'0')}:${minutes.toString().padStart(2,'0')}:${secs.toString().padStart(2,'0')}`;
    }
    
    function startTimerInterval() {
        if (timerInterval) clearInterval(timerInterval);
        timerInterval = setInterval(() => {
            if (timerRunning) {
                timerSeconds++;
                updateTimerDisplay();
                saveToLocalStorage(); // salva estado do timer também
            }
        }, 1000);
    }
    
    function startTimer() {
        if (timerRunning) return;
        timerRunning = true;
        if (!timerInterval) startTimerInterval();
        saveToLocalStorage();
        showToast("Cronômetro iniciado!", "success");
    }
    
    function pauseTimer() {
        if (!timerRunning) return;
        timerRunning = false;
        saveToLocalStorage();
        showToast("Cronômetro pausado.", "info");
    }
    
    function resetTimer() {
        timerRunning = false;
        if (timerInterval) {
            clearInterval(timerInterval);
            timerInterval = null;
        }
        timerSeconds = 0;
        updateTimerDisplay();
        saveToLocalStorage();
        showToast("Cronômetro zerado.", "info");
    }
    
    // ---------- Caderno de Erros (PDF apenas com erros) ----------
    async function generateWrongAnswersPDF() {
        if (questions.length === 0) { showToast("Nenhuma questão importada.", "warning"); return; }
        const wrongAnswers = answers.filter(a => a.isCorrect === false);
        if (wrongAnswers.length === 0) { showToast("Você não errou nenhuma questão até o momento! 🎉", "success"); return; }
        
        const wrongQuestions = wrongAnswers.map(wa => {
            const q = questions.find(qq => qq.id === wa.questionId);
            return { ...q, userChoice: wa.userChoice };
        });
        
        const doc = new jspdf.jsPDF({ orientation: 'landscape', unit: 'mm', format: 'a4' });
        doc.setFont("helvetica");
        doc.setFontSize(18);
        doc.text("Caderno de Erros - Questões CESPE/CEBRASPE", 14, 20);
        doc.setFontSize(11);
        doc.text(`Data: ${new Date().toLocaleString()}`, 14, 30);
        doc.text(`Total de questões erradas: ${wrongAnswers.length}`, 14, 38);
        
        const tableData = [];
        for (let q of wrongQuestions) {
            tableData.push([
                q.number,
                q.discipline,
                q.text.length > 80 ? q.text.substring(0,77)+"..." : q.text,
                q.userChoice,
                q.answer,
                q.comment.length > 100 ? q.comment.substring(0,97)+"..." : q.comment
            ]);
        }
        doc.autoTable({
            head: [["#", "Disciplina", "Enunciado (resumo)", "Sua resposta", "Gabarito", "Comentário"]],
            body: tableData,
            startY: 45,
            styles: { fontSize: 8, cellPadding: 2, overflow: 'linebreak' },
            columnStyles: { 2: { cellWidth: 50 }, 5: { cellWidth: 45 } },
            margin: { left: 10, right: 10 }
        });
        doc.save(`caderno_erros_${new Date().toISOString().slice(0,19)}.pdf`);
        showToast("Caderno de Erros gerado com sucesso!", "success");
    }
    
    // ---------- Prompt para Google NotebookLM ----------
    function generateNotebookLMPrompt() {
        if (questions.length === 0) { showToast("Nenhuma questão para exportar.", "warning"); return; }
        const stats = computeStats();
        let promptText = `# PROMPT PARA GOOGLE NOTEBOOKLM - ESTUDO CESPE/CEBRASPE\n\n`;
        promptText += `## Resumo do desempenho:\n`;
        promptText += `- Total de questões respondidas: ${stats.totalAnswered}\n`;
        promptText += `- Acertos: ${stats.totalCorrect}\n`;
        promptText += `- Erros: ${stats.totalWrong}\n`;
        promptText += `- Aproveitamento: ${stats.percentage}%\n\n`;
        promptText += `## Lista de todas as questões (com enunciado, gabarito e comentário):\n\n`;
        
        for (let i = 0; i < questions.length; i++) {
            const q = questions[i];
            const userResp = answers.find(a => a.questionId === q.id);
            const respStatus = userResp ? (userResp.isCorrect ? "Acertou" : "Errou") : "Não respondida";
            const userChoice = userResp ? userResp.userChoice : "—";
            promptText += `### Questão ${q.number} - ${q.discipline}\n`;
            promptText += `**Enunciado:** ${q.text}\n`;
            promptText += `**Gabarito:** ${q.answer}\n`;
            promptText += `**Sua resposta:** ${userChoice} (${respStatus})\n`;
            promptText += `**Comentário:** ${q.comment}\n\n`;
        }
        promptText += `\n## Instrução para o NotebookLM:\n`;
        promptText += `Analise estas questões no estilo CESPE/CEBRASPE (certo/errado). Para cada uma, explique o motivo do gabarito e aponte os principais conceitos cobrados. Dê dicas de estudo focadas nas bancas.`;
        
        const blob = new Blob([promptText], { type: "text/plain" });
        const link = document.createElement("a");
        const url = URL.createObjectURL(blob);
        link.href = url;
        link.download = `prompt_notebooklm_${new Date().toISOString().slice(0,19)}.txt`;
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
        URL.revokeObjectURL(url);
        showToast("Prompt para NotebookLM baixado!", "success");
    }
    
    // Gerar PDF com relatório completo (já existente)
    async function generatePDFReport() {
        if (questions.length === 0) { showToast("Nenhuma questão para gerar relatório.", "warning"); return; }
        const stats = computeStats();
        const doc = new jspdf.jsPDF({ orientation: 'landscape', unit: 'mm', format: 'a4' });
        doc.setFont("helvetica");
        doc.setFontSize(18);
        doc.text("Relatório de Desempenho - Questões CESPE/CEBRASPE", 14, 20);
        doc.setFontSize(11);
        doc.text(`Data: ${new Date().toLocaleString()}`, 14, 30);
        doc.text(`Total de questões respondidas: ${stats.totalAnswered}`, 14, 38);
        doc.text(`Acertos: ${stats.totalCorrect} | Erros: ${stats.totalWrong} | Aproveitamento: ${stats.percentage}%`, 14, 46);
        
        const tableData = [];
        const answeredMap = new Map(answers.map(a => [a.questionId, a]));
        for (let q of questions) {
            const resp = answeredMap.get(q.id);
            if (!resp) continue;
            const resultado = resp.isCorrect ? "Acertou" : "Errou";
            const userChoice = resp.userChoice;
            const gabarito = q.answer;
            let enunciadoResumido = q.text.length > 90 ? q.text.substring(0, 87) + "..." : q.text;
            tableData.push([q.number, q.discipline, enunciadoResumido, userChoice, gabarito, resultado, q.comment.substring(0, 80)]);
        }
        if (tableData.length === 0) { doc.text("Nenhuma questão respondida até o momento.", 14, 60); }
        else {
            doc.autoTable({
                head: [["#", "Disciplina", "Enunciado", "Sua resp.", "Gabarito", "Resultado", "Comentário (resumo)"]],
                body: tableData,
                startY: 55,
                styles: { fontSize: 8, cellPadding: 2, overflow: 'linebreak' },
                columnStyles: { 2: { cellWidth: 45 }, 6: { cellWidth: 40 } },
                margin: { left: 10, right: 10 }
            });
        }
        doc.save(`relatorio_cebraspe_${new Date().toISOString().slice(0,19)}.pdf`);
        showToast("Relatório PDF gerado com sucesso!", "success");
    }
    
    function escapeHtml(str) { if (!str) return ''; return str.replace(/[&<>]/g, m => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;' }[m])); }
    function showToast(msg, type) {
        importFeedback.classList.remove('hidden');
        importFeedback.innerHTML = `<i class="fas ${type === 'success' ? 'fa-check-circle' : type === 'error' ? 'fa-exclamation-circle' : 'fa-info-circle'} mr-1"></i> ${msg}`;
        importFeedback.classList.add('p-2', 'rounded-lg', type === 'success' ? 'text-green-600 bg-green-50' : type === 'error' ? 'text-red-600 bg-red-50' : 'text-blue-600 bg-blue-50');
        setTimeout(() => { importFeedback.classList.add('hidden'); importFeedback.classList.remove('text-green-600','text-red-600','bg-green-50','bg-red-50','bg-blue-50'); }, 3000);
    }
    
    function renderAll() { renderDashboard(); renderQuestion(); saveToLocalStorage(); updateCounterDisplay(); }
    
    // Eventos
    importBtn.addEventListener('click', () => { const raw = rawInput.value; if (importQuestions(raw)) rawInput.value = ''; });
    loadExampleBtn.addEventListener('click', () => {
        const example = `Questão 1: "Embora João de Barros tenha sido o donatário original da Capitania do Rio Grande por meio da Carta de Doação de 1535, a historiografia registra que ele jamais tomou posse física de suas terras, administrando-as por intermédio de um procurador."
Comentário: "João de Barros enfrentou dificuldades financeiras e resistência indígena, nunca assumindo pessoalmente a capitania."
Gabarito: Certo

Questão 2: "No contexto da ocupação holandesa na Capitania do Rio Grande no século XVII, o massacre de Uruaçu ocorreu cronologicamente antes do massacre de Cunhaú."
Comentário: "O massacre de Cunhaú ocorreu em 16 de julho de 1645, enquanto o de Uruaçu aconteceu cerca de três meses depois."
Gabarito: Errado

Questão 3: "O sistema de sesmarias implementado para estimular a colonização da capitania previa que o sesmeiro teria a propriedade plena da terra desde que nela se fizesse produzir no prazo máximo de cinco anos."
Comentário: "A legislação sesmarial estabelecia o prazo de cinco anos para a produção da terra, sob pena de perda."
Gabarito: Certo`;
        rawInput.value = example;
        importQuestions(example, "História do Rio Grande do Norte");
    });
    resetProgressBtn.addEventListener('click', () => { if(questions.length) resetProgressOnly(); else showToast("Nenhuma questão importada.", "warning"); });
    clearAllBtn.addEventListener('click', () => { if(confirm("Tem certeza? Isso apagará todas as questões, respostas e o cronômetro.")) clearAllData(); });
    importPdfBtn.addEventListener('click', () => pdfUploadInput.click());
    pdfUploadInput.addEventListener('change', async (e) => { if(e.target.files.length) { await importFromPdf(e.target.files[0]); e.target.value = ''; } });
    generatePdfReportBtn.addEventListener('click', generatePDFReport);
    downloadWrongAnswersBtn.addEventListener('click', generateWrongAnswersPDF);
    downloadNotebookLMBtn.addEventListener('click', generateNotebookLMPrompt);
    
    timerStartBtn.addEventListener('click', startTimer);
    timerPauseBtn.addEventListener('click', pauseTimer);
    timerStopBtn.addEventListener('click', resetTimer);
    
    loadFromLocalStorage();
    renderAll();
</script>
</body>
</html>
