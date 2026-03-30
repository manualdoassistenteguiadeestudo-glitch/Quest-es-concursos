<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover, user-scalable=yes">
    <title>Questões CESPE/CEBRASPE | Treino Certo ou Errado</title>
    <!-- Tailwind CSS + Font Awesome + Chart.js -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        'sans': ['Inter', 'system-ui', 'Segoe UI', 'sans-serif']
                    },
                    animation: {
                        'fade-in': 'fadeIn 0.3s ease-out',
                        'slide-up': 'slideUp 0.2s ease-out',
                    },
                    keyframes: {
                        fadeIn: { '0%': { opacity: '0' }, '100%': { opacity: '1' } },
                        slideUp: { '0%': { transform: 'translateY(8px)', opacity: '0' }, '100%': { transform: 'translateY(0)', opacity: '1' } }
                    }
                }
            }
        }
    </script>
    <style>
        body {
            background: linear-gradient(135deg, #f8fafc 0%, #f1f5f9 100%);
            font-family: 'Inter', system-ui, -apple-system, sans-serif;
        }
        .card-modern {
            transition: all 0.2s ease;
        }
        .card-modern:hover {
            transform: translateY(-2px);
            box-shadow: 0 20px 25px -12px rgba(0, 0, 0, 0.08);
        }
        .btn-certo {
            background: linear-gradient(135deg, #22c55e, #16a34a);
        }
        .btn-errado {
            background: linear-gradient(135deg, #ef4444, #dc2626);
        }
        .progress-bar {
            transition: width 0.4s cubic-bezier(0.2, 0.9, 0.4, 1.1);
        }
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #e2e8f0;
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb {
            background: #94a3b8;
            border-radius: 10px;
        }
    </style>
</head>
<body>

    <div class="max-w-7xl mx-auto px-4 py-6 md:py-8">
        <!-- Header com logo dupla: ícone original + imagem ilustrativa -->
        <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 mb-8 pb-4 border-b border-gray-200">
            <div class="flex items-center gap-4">
                <!-- Ícone principal (já existente) -->
                <div class="bg-gradient-to-br from-emerald-500 to-green-600 w-10 h-10 rounded-xl flex items-center justify-center shadow-md">
                    <i class="fas fa-check-double text-white text-xl"></i>
                </div>
                <!-- Nova imagem representando "fazer questões" (ícone estilizado) -->
                <div class="hidden sm:flex items-center gap-2 bg-gray-100 px-3 py-2 rounded-full">
                    <i class="fas fa-file-alt text-green-600 text-lg"></i>
                    <i class="fas fa-arrow-right text-gray-400 text-sm"></i>
                    <i class="fas fa-pen-fancy text-blue-500 text-lg"></i>
                    <span class="text-xs font-medium text-gray-600">Modo simulado</span>
                </div>
                <div>
                    <h1 class="text-2xl md:text-3xl font-bold text-gray-800 tracking-tight">Questões <span class="text-green-600">CESPE/CEBRASPE</span></h1>
                    <p class="text-gray-500 text-sm">Treine com questões estilo Certo ou Errado</p>
                </div>
            </div>
            <div class="flex gap-2">
                <button id="resetOnlyProgressBtn" class="bg-white border border-gray-300 text-gray-700 px-4 py-2 rounded-xl text-sm font-medium hover:bg-gray-50 transition shadow-sm flex items-center gap-2"><i class="fas fa-undo-alt"></i> Resetar progresso</button>
                <button id="clearAllDataBtn" class="bg-red-50 border border-red-200 text-red-600 px-4 py-2 rounded-xl text-sm font-medium hover:bg-red-100 transition shadow-sm flex items-center gap-2"><i class="fas fa-trash-alt"></i> Limpar tudo</button>
            </div>
        </div>

        <!-- Layout grid: coluna esquerda (Importador + Dashboard) e coluna direita (Questão ativa) -->
        <div class="grid lg:grid-cols-12 gap-6">
            <!-- Sidebar esquerda -->
            <div class="lg:col-span-5 space-y-6">
                <!-- Card Importação -->
                <div class="bg-white rounded-2xl shadow-sm border border-gray-100 p-5 card-modern">
                    <div class="flex items-center gap-3 mb-4">
                        <div class="bg-green-100 p-2 rounded-xl"><i class="fas fa-file-import text-green-600 text-lg"></i></div>
                        <div>
                            <h2 class="font-bold text-gray-800">Importar questões</h2>
                            <p class="text-xs text-gray-500">Formato CESPE/CEBRASPE: Questão X: enunciado + Comentário: + Gabarito: Certo/Errado</p>
                        </div>
                    </div>
                    <textarea id="rawQuestionsInput" rows="6" class="w-full border border-gray-200 rounded-xl p-3 text-sm font-mono bg-gray-50 focus:ring-2 focus:ring-green-400 focus:outline-none transition" placeholder='Questão 1: "A Declaração Universal dos Direitos Humanos foi adotada em 1948."

Comentário: "Adotada pela ONU em 10 de dezembro de 1948."

Gabarito: Certo

Questão 2: "O Brasil foi o primeiro país a abolir a escravidão nas Américas."

Comentário: "Na verdade foi o último, com a Lei Áurea em 1888."

Gabarito: Errado'></textarea>
                    <div class="flex gap-3 mt-4">
                        <button id="importBtn" class="flex-1 bg-green-600 hover:bg-green-700 text-white font-semibold py-2.5 rounded-xl transition shadow-md flex items-center justify-center gap-2"><i class="fas fa-cloud-upload-alt"></i> Importar questões</button>
                        <button id="loadExampleBtn" class="bg-gray-100 hover:bg-gray-200 text-gray-700 font-medium py-2.5 px-5 rounded-xl transition flex items-center gap-2"><i class="fas fa-code-branch"></i> Exemplo</button>
                    </div>
                    <p id="importFeedback" class="text-xs text-center mt-3 text-gray-400 hidden"></p>
                </div>

                <!-- Dashboard (estatísticas + gráfico) -->
                <div class="bg-white rounded-2xl shadow-sm border border-gray-100 p-5 card-modern">
                    <div class="flex items-center gap-2 mb-4">
                        <i class="fas fa-chart-line text-blue-500 text-lg"></i>
                        <h2 class="font-bold text-gray-800">Meu desempenho</h2>
                    </div>
                    <div id="dashboardContainer">
                        <div class="text-center py-8 text-gray-400"><i class="fas fa-chart-simple text-3xl mb-2 block"></i> Nenhuma questão respondida ainda</div>
                    </div>
                </div>
            </div>

            <!-- Coluna direita: Questão ativa -->
            <div class="lg:col-span-7">
                <div class="bg-white rounded-2xl shadow-sm border border-gray-100 p-5 md:p-6 card-modern min-h-[500px] transition-all" id="questionPanel">
                    <div class="flex flex-col items-center justify-center py-12 text-gray-400">
                        <i class="fas fa-brain text-5xl mb-3 text-gray-300"></i>
                        <p class="text-center">Importe ou selecione questões<br>para começar a treinar</p>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // ---------- Estrutura de dados ----------
        let questions = [];            // { id, number, text, comment, answer }
        let answers = [];              // { questionId, isCorrect, userChoice, timestamp }
        let currentIndex = 0;
        let commentVisibleMap = new Map();   // key = questionId, boolean
        
        // DOM references
        const dashboardContainer = document.getElementById('dashboardContainer');
        const questionPanel = document.getElementById('questionPanel');
        const rawInput = document.getElementById('rawQuestionsInput');
        const importBtn = document.getElementById('importBtn');
        const loadExampleBtn = document.getElementById('loadExampleBtn');
        const resetProgressBtn = document.getElementById('resetOnlyProgressBtn');
        const clearAllBtn = document.getElementById('clearAllDataBtn');
        const importFeedback = document.getElementById('importFeedback');
        
        // ---------- localStorage ----------
        function saveToLocalStorage() {
            localStorage.setItem('cebraspe_questions', JSON.stringify(questions));
            localStorage.setItem('cebraspe_answers', JSON.stringify(answers));
            localStorage.setItem('cebraspe_currentIndex', currentIndex.toString());
            localStorage.setItem('cebraspe_commentsVisible', JSON.stringify([...commentVisibleMap.entries()]));
        }
        
        function loadFromLocalStorage() {
            const storedQuestions = localStorage.getItem('cebraspe_questions');
            const storedAnswers = localStorage.getItem('cebraspe_answers');
            const storedIndex = localStorage.getItem('cebraspe_currentIndex');
            const storedComments = localStorage.getItem('cebraspe_commentsVisible');
            
            if (storedQuestions) questions = JSON.parse(storedQuestions);
            if (storedAnswers) answers = JSON.parse(storedAnswers);
            if (storedIndex) currentIndex = parseInt(storedIndex, 10);
            if (storedComments) {
                const arr = JSON.parse(storedComments);
                commentVisibleMap = new Map(arr);
            } else commentVisibleMap.clear();
            
            if (questions.length > 0 && currentIndex >= questions.length) currentIndex = questions.length - 1;
            if (currentIndex < 0) currentIndex = 0;
        }
        
        function clearAllData() {
            localStorage.removeItem('cebraspe_questions');
            localStorage.removeItem('cebraspe_answers');
            localStorage.removeItem('cebraspe_currentIndex');
            localStorage.removeItem('cebraspe_commentsVisible');
            questions = [];
            answers = [];
            currentIndex = 0;
            commentVisibleMap.clear();
            renderAll();
        }
        
        function resetProgressOnly() {
            if (questions.length === 0) return;
            answers = [];
            currentIndex = 0;
            commentVisibleMap.clear();
            saveToLocalStorage();
            renderAll();
        }
        
        // ---------- PARSER CORRIGIDO: separa enunciado, comentário e gabarito ----------
        function parseQuestionsFromText(text) {
            const questionsArray = [];
            // Expressão para capturar blocos individuais: "Questão X:" até o próximo "Questão" ou fim do texto
            const blockRegex = /Questão\s+(\d+):\s*([\s\S]*?)(?=Questão\s+\d+:|$)/gi;
            let match;
            
            while ((match = blockRegex.exec(text)) !== null) {
                const number = parseInt(match[1], 10);
                let blockContent = match[2].trim();
                if (!blockContent) continue;
                
                // 1) Extrair GABARITO primeiro (Certo ou Errado)
                let answer = 'Certo';
                const gabaritoRegex = /Gabarito:\s*(Certo|Errado)/i;
                const gabMatch = blockContent.match(gabaritoRegex);
                if (gabMatch) {
                    answer = gabMatch[1];
                    // Remove a linha do gabarito do conteúdo para não poluir comentário/enunciado
                    blockContent = blockContent.replace(gabaritoRegex, '');
                }
                
                // 2) Extrair COMENTÁRIO: tudo que vem após "Comentário:" até antes de "Gabarito" ou fim do bloco
                let comment = '';
                const commentRegex = /Comentário:\s*([\s\S]*?)(?=(?:Gabarito:|$))/i;
                const commentMatch = blockContent.match(commentRegex);
                if (commentMatch) {
                    let rawComment = commentMatch[1].trim();
                    // Remover possíveis aspas extras e quebras malucas
                    rawComment = rawComment.replace(/^["']|["']$/g, '').replace(/\n{3,}/g, '\n\n').trim();
                    comment = rawComment || "Sem comentário detalhado para esta questão.";
                    // Remove o trecho do comentário do bloco para isolar o enunciado
                    blockContent = blockContent.replace(commentRegex, '');
                } else {
                    comment = "Sem comentário detalhado para esta questão.";
                }
                
                // 3) O que restou é o ENUNCIADO (pode ter quebras de linha, aspas, etc.)
                let questionText = blockContent.trim();
                // Remover aspas duplas ou simples no início e fim do enunciado, se existirem
                questionText = questionText.replace(/^["']|["']$/g, '').trim();
                // Se o enunciado ficou vazio, tentar pegar a primeira linha significativa
                if (questionText === "" && blockContent !== "") {
                    const lines = blockContent.split(/\r?\n/);
                    questionText = lines[0].replace(/^["']|["']$/g, '').trim();
                }
                
                // Validação final
                if (questionText.length > 0) {
                    questionsArray.push({
                        id: Date.now() + Math.random() * 10000 + number,
                        number: number,
                        text: questionText,
                        comment: comment,
                        answer: answer
                    });
                }
            }
            
            // Fallback para casos de formatação menos rigorosa (ex: sem quebra de linha após "Comentário:")
            if (questionsArray.length === 0 && text.includes('Questão')) {
                // Tentativa mais simples: dividir por linhas e reconstruir
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
                        const commentContent = line.replace(/^Comentário:\s*/i, '').replace(/^["']|["']$/g, '');
                        current.comment = commentContent;
                    } else if (current && line.match(/^Gabarito:\s*(Certo|Errado)/i)) {
                        const gabMatch = line.match(/Gabarito:\s*(Certo|Errado)/i);
                        if (gabMatch) current.answer = gabMatch[1];
                    } else if (current && line !== "" && !line.match(/^Comentário|^Gabarito/i)) {
                        current.text += " " + line;
                    }
                }
                if (current && current.text) questionsArray.push(current);
            }
            
            // Garantir que comentário nunca fique vazio e que enunciado não contenha "Comentário:"
            return questionsArray.map((q, idx) => ({
                ...q,
                id: idx+1,
                comment: q.comment && q.comment !== "" ? q.comment : "Sem comentário disponível."
            }));
        }
        
        // ---------- Estatísticas ----------
        function computeStats() {
            const totalAnswered = answers.length;
            const totalCorrect = answers.filter(a => a.isCorrect === true).length;
            const totalWrong = totalAnswered - totalCorrect;
            const percentage = totalAnswered === 0 ? 0 : Math.round((totalCorrect / totalAnswered) * 100);
            return { totalAnswered, totalCorrect, totalWrong, percentage };
        }
        
        // ---------- Manipulação do fluxo ----------
        function importQuestions(rawText) {
            if (!rawText.trim()) {
                showToast("Cole o texto das questões antes de importar.", "warning");
                return false;
            }
            const parsed = parseQuestionsFromText(rawText);
            if (parsed.length === 0) {
                showToast("Nenhuma questão válida encontrada. Use o formato: Questão X: enunciado + Comentário: + Gabarito: Certo/Errado", "error");
                return false;
            }
            questions = parsed;
            answers = [];
            currentIndex = 0;
            commentVisibleMap.clear();
            saveToLocalStorage();
            renderAll();
            showToast(`${parsed.length} questões importadas com sucesso!`, "success");
            return true;
        }
        
        function answerCurrentQuestion(choice) {
            if (questions.length === 0) return;
            const currentQ = questions[currentIndex];
            if (!currentQ) return;
            const alreadyAnswered = answers.some(a => a.questionId === currentQ.id);
            if (alreadyAnswered) {
                showToast("Você já respondeu esta questão. Vá para a próxima.", "info");
                return;
            }
            const isCorrect = (choice === currentQ.answer);
            answers.push({
                questionId: currentQ.id,
                isCorrect: isCorrect,
                userChoice: choice,
                timestamp: Date.now()
            });
            commentVisibleMap.set(currentQ.id, false);
            saveToLocalStorage();
            renderAll();
        }
        
        function showCommentForCurrent() {
            if (questions.length === 0) return;
            const q = questions[currentIndex];
            if (!q) return;
            const hasAnswered = answers.some(a => a.questionId === q.id);
            if (!hasAnswered) {
                showToast("Responda a questão primeiro para ver o comentário.", "info");
                return;
            }
            commentVisibleMap.set(q.id, true);
            saveToLocalStorage();
            renderAll();
        }
        
        function nextQuestion() {
            if (questions.length === 0) return;
            const currentQ = questions[currentIndex];
            const hasAnswered = currentQ ? answers.some(a => a.questionId === currentQ.id) : false;
            if (!hasAnswered) {
                showToast("Responda a questão atual antes de avançar.", "warning");
                return;
            }
            if (currentIndex + 1 < questions.length) {
                currentIndex++;
                renderAll();
            } else {
                showToast("🏆 Você concluiu todas as questões! Parabéns!", "success");
            }
        }
        
        // ---------- Renderização do Dashboard com gráfico ----------
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
                <div class="border-t border-gray-100 pt-3 mt-1">
                    <canvas id="statsPieChart" width="200" height="200" style="max-height: 200px; margin: 0 auto;"></canvas>
                </div>
                ${questions.length > 0 ? `<div class="text-center text-xs text-gray-400 mt-2">Total de questões: ${questions.length}</div>` : ''}
            `;
            dashboardContainer.innerHTML = htmlStats;
            if (statsChart) statsChart.destroy();
            const ctx = document.getElementById('statsPieChart')?.getContext('2d');
            if (ctx && (stats.totalCorrect + stats.totalWrong) > 0) {
                statsChart = new Chart(ctx, {
                    type: 'doughnut',
                    data: {
                        labels: ['Acertos', 'Erros'],
                        datasets: [{
                            data: [stats.totalCorrect, stats.totalWrong],
                            backgroundColor: ['#22c55e', '#ef4444'],
                            borderWidth: 0,
                            hoverOffset: 8
                        }]
                    },
                    options: { responsive: true, maintainAspectRatio: true, plugins: { legend: { position: 'bottom', labels: { font: { size: 11 } } } } }
                });
            } else if (ctx) {
                statsChart = new Chart(ctx, { type: 'doughnut', data: { labels: ['Nenhum dado'], datasets: [{ data: [1], backgroundColor: ['#e2e8f0'] }] }, options: { plugins: { legend: { display: false } } } });
            }
        }
        
        // ---------- Renderização da questão ativa (com separação clara) ----------
        function renderQuestion() {
            if (!questions.length) {
                questionPanel.innerHTML = `
                    <div class="flex flex-col items-center justify-center py-12 text-gray-400">
                        <i class="fas fa-inbox text-5xl mb-3"></i>
                        <p class="text-center font-medium">Nenhuma questão carregada</p>
                        <p class="text-sm text-center mt-1">Importe questões ao lado para iniciar os treinos.</p>
                    </div>
                `;
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
            if (hasAnswered) {
                const correctText = isAnswerCorrect ? '✅ Você acertou!' : `❌ Você errou! A resposta correta é ${currentQ.answer}.`;
                feedbackHtml = `<div class="p-4 rounded-xl mb-4 ${isAnswerCorrect ? 'bg-green-50 border border-green-200 text-green-700' : 'bg-red-50 border border-red-200 text-red-700'} animate-fade-in"><p class="font-medium text-center">${correctText}</p></div>`;
            }
            
            let commentSection = '';
            if (hasAnswered && !isCommentVisible) {
                commentSection = `<button id="showCommentBtn" class="w-full py-3 bg-gray-100 hover:bg-gray-200 rounded-xl text-gray-700 font-medium transition flex items-center justify-center gap-2 mt-2"><i class="far fa-comment-dots"></i> Ver comentário</button>`;
            } else if (hasAnswered && isCommentVisible) {
                commentSection = `<div class="mt-4 p-4 bg-gray-50 rounded-xl border border-gray-200 animate-fade-in"><i class="fas fa-quote-left text-gray-400 mr-2"></i><span class="font-semibold text-gray-800">Comentário:</span><p class="text-gray-600 text-sm mt-1 leading-relaxed">${escapeHtml(currentQ.comment)}</p></div>`;
            }
            
            const nextDisabled = !hasAnswered;
            const nextButtonText = (currentIndex + 1 >= total) ? '🏁 Finalizar simulado' : 'Próxima questão →';
            
            const html = `
                <div class="animate-slide-up">
                    <div class="mb-5">
                        <div class="flex justify-between text-sm text-gray-600 mb-1"><span>Progresso</span><span>${currentIndex + (hasAnswered ? 1 : 0)}/${total}</span></div>
                        <div class="h-2 bg-gray-100 rounded-full overflow-hidden"><div class="progress-bar h-full bg-green-500 rounded-full" style="width: ${progressPercent}%"></div></div>
                    </div>
                    <div class="flex justify-between items-start mb-4 pb-3 border-b border-gray-100">
                        <span class="px-3 py-1 bg-green-100 text-green-700 text-xs font-semibold rounded-full"><i class="far fa-file-alt mr-1"></i> Questão ${currentQ.number}</span>
                        <button id="resetOnlyFromQuestionBtn" class="text-gray-400 hover:text-gray-600 transition" title="Resetar progresso"><i class="fas fa-sync-alt"></i></button>
                    </div>
                    <!-- ENUNCIADO PURIFICADO: sem comentários ou gabaritos -->
                    <div class="mb-6 bg-gray-50 p-4 rounded-xl"><p class="text-gray-800 text-base md:text-lg leading-relaxed">${escapeHtml(currentQ.text)}</p></div>
                    <div id="answerArea">
                        ${!hasAnswered ? `
                            <div class="flex gap-4 mb-6">
                                <button id="btnCerto" class="flex-1 btn-certo text-white font-bold py-4 rounded-xl text-lg flex items-center justify-center gap-2 shadow-md transition"><i class="fas fa-check-circle"></i> Certo</button>
                                <button id="btnErrado" class="flex-1 btn-errado text-white font-bold py-4 rounded-xl text-lg flex items-center justify-center gap-2 shadow-md transition"><i class="fas fa-times-circle"></i> Errado</button>
                            </div>
                        ` : feedbackHtml}
                        ${commentSection}
                        ${hasAnswered ? `<div class="mt-5"><button id="nextQuestionBtn" class="w-full bg-gray-900 hover:bg-gray-800 text-white font-semibold py-3 rounded-xl transition flex items-center justify-center gap-2 ${nextDisabled ? 'opacity-50 cursor-not-allowed' : ''}" ${nextDisabled ? 'disabled' : ''}><i class="fas fa-arrow-right"></i> ${nextButtonText}</button></div>` : ''}
                    </div>
                    ${hasAnswered && currentIndex+1 >= total ? `<div class="mt-3 p-3 bg-green-50 rounded-xl text-center text-green-700 text-sm"><i class="fas fa-trophy"></i> Parabéns! Você finalizou todas as questões.</div>` : ''}
                </div>
            `;
            questionPanel.innerHTML = html;
            
            // Eventos dinâmicos
            if (!hasAnswered) {
                document.getElementById('btnCerto')?.addEventListener('click', () => answerCurrentQuestion('Certo'));
                document.getElementById('btnErrado')?.addEventListener('click', () => answerCurrentQuestion('Errado'));
            } else {
                const showCommentBtn = document.getElementById('showCommentBtn');
                if (showCommentBtn) showCommentBtn.addEventListener('click', () => showCommentForCurrent());
                const nextBtn = document.getElementById('nextQuestionBtn');
                if (nextBtn && !nextDisabled) nextBtn.addEventListener('click', () => nextQuestion());
            }
            const resetQuestionBtn = document.getElementById('resetOnlyFromQuestionBtn');
            if (resetQuestionBtn) resetQuestionBtn.addEventListener('click', () => resetProgressOnly());
        }
        
        function escapeHtml(str) {
            if (!str) return '';
            return str.replace(/[&<>]/g, function(m) {
                if (m === '&') return '&amp;';
                if (m === '<') return '&lt;';
                if (m === '>') return '&gt;';
                return m;
            });
        }
        
        function showToast(msg, type = 'info') {
            importFeedback.classList.remove('hidden');
            importFeedback.innerHTML = `<i class="fas ${type === 'success' ? 'fa-check-circle' : type === 'error' ? 'fa-exclamation-circle' : 'fa-info-circle'} mr-1"></i> ${msg}`;
            importFeedback.classList.add('text-green-600', 'bg-green-50', 'p-2', 'rounded-lg');
            if (type === 'error') importFeedback.classList.add('text-red-600', 'bg-red-50');
            else if (type === 'warning') importFeedback.classList.add('text-yellow-600', 'bg-yellow-50');
            else importFeedback.classList.add('text-blue-600', 'bg-blue-50');
            setTimeout(() => { importFeedback.classList.add('hidden'); importFeedback.classList.remove('text-green-600','text-red-600','bg-green-50','bg-red-50','bg-yellow-50','bg-blue-50'); }, 2500);
        }
        
        function renderAll() {
            renderDashboard();
            renderQuestion();
            saveToLocalStorage();
        }
        
        // ---------- Event listeners ----------
        importBtn.addEventListener('click', () => {
            const raw = rawInput.value;
            if (importQuestions(raw)) rawInput.value = '';
        });
        loadExampleBtn.addEventListener('click', () => {
            const example = `Questão 1: "Embora João de Barros tenha sido o donatário original da Capitania do Rio Grande por meio da Carta de Doação de 1535, a historiografia registra que ele jamais tomou posse física de suas terras, administrando-as por intermédio de um procurador."
Comentário: "João de Barros enfrentou dificuldades financeiras e resistência indígena, nunca assumindo pessoalmente a capitania. O controle era exercido pelo procurador Antônio Pinheiro."
Gabarito: Certo

Questão 2: "No contexto da ocupação holandesa na Capitania do Rio Grande no século XVII, o massacre de Uruaçu ocorreu cronologicamente antes do massacre de Cunhaú."
Comentário: "O massacre de Cunhaú ocorreu em 16 de julho de 1645, enquanto o de Uruaçu aconteceu cerca de três meses depois, em outubro do mesmo ano."
Gabarito: Errado

Questão 3: "O sistema de sesmarias implementado para estimular a colonização da capitania previa que o sesmeiro teria a propriedade plena da terra desde que nela se fizesse produzir no prazo máximo de cinco anos."
Comentário: "A legislação sesmarial estabelecia o prazo de cinco anos para a produção da terra, sob pena de o colono ser multado ou perder a sua doação territorial."
Gabarito: Certo`;
            rawInput.value = example;
            importQuestions(example);
        });
        resetProgressBtn.addEventListener('click', () => { if(questions.length) resetProgressOnly(); else showToast("Nenhuma questão importada.", "warning"); });
        clearAllBtn.addEventListener('click', () => { if(confirm("Tem certeza? Isso apagará todas as questões e respostas.")) clearAllData(); });
        
        // Inicialização
        loadFromLocalStorage();
        renderAll();
    </script>
</body>
</html>
