# Quiz-magic
A quiz game that you can set question by yourselfAnd it contains dictation multiple choiceAnd long question type question
<!doctype html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>測驗魔法 Quiz Magic</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { font-family: system-ui, sans-serif; background: linear-gradient(135deg, #667eea, #764ba2); min-height: 100vh; margin: 0; color: #333; }
        .container { max-width: 900px; margin: 20px auto; background: white; border-radius: 24px; overflow: hidden; box-shadow: 0 20px 40px rgba(0,0,0,0.2); }
        header { background: linear-gradient(135deg, #f093fb, #f5576c); color: white; padding: 40px; text-align: center; position: relative; }
        .lang-switch { position: absolute; left: 20px; top: 20px; background: rgba(255,255,255,0.3); border-radius: 30px; overflow: hidden; }
        .lang-btn { padding: 10px 20px; cursor: pointer; font-weight: bold; }
        .lang-btn.active { background: rgba(255,255,255,0.4); }
        .add-btn { position: absolute; right: 20px; top: 20px; background: rgba(255,255,255,0.3); padding: 10px 20px; border-radius: 25px; cursor: pointer; }
        .section { padding: 40px; }
        button { background: linear-gradient(135deg, #f093fb, #f5576c); color: white; border: none; padding: 15px 30px; margin: 10px 5px; border-radius: 50px; font-size: 18px; cursor: pointer; }
        button.secondary { background: linear-gradient(135deg, #a8edea, #fed6e3); color: #333; }
        select, textarea, input { width: 100%; padding: 15px; margin: 10px 0; border-radius: 15px; border: 2px solid #e2e8f0; }
        #progress { text-align: center; font-size: 18px; color: #667eea; margin-bottom: 30px; }
        .question { font-size: 26px; text-align: center; margin: 30px 0; padding: 20px; background: #fef5e7; border-radius: 20px; border-left: 6px solid #f5576c; }
        .result { text-align: center; padding: 50px; background: #ffecd2; }
        .score { font-size: 60px; font-weight: bold; background: linear-gradient(135deg, #667eea, #764ba2); -webkit-background-clip: text; color: transparent; }
        .score-note { font-size: 20px; color: #666; margin: 15px 0; }
        .toast { position: fixed; top: 20px; right: 20px; background: #333; color: white; padding: 15px 25px; border-radius: 10px; display: none; }
        .answer-pair { display: flex; gap: 20px; margin: 20px 0; padding: 20px; background: #f9f9f9; border-radius: 12px; }
        .answer-left, .answer-right { flex: 1; }
        .answer-left { border-right: 3px dashed #ccc; padding-right: 20px; }
        .answer-right { padding-left: 20px; }
        .answer-title { font-weight: bold; margin-bottom: 10px; color: #555; }
        pre { background: #f0f0f0; padding: 12px; border-radius: 8px; white-space: pre-wrap; margin: 0; }
        .correct-pair { border-left: 5px solid #28a745; }
        .wrong-pair { border-left: 5px solid #dc3545; }
        .neutral-pair { border-left: 5px solid #aaa; }
        .feedback { font-weight: bold; text-align: center; margin: 15px 0; font-size: 20px; }
        .correct-feedback { color: #28a745; }
        .wrong-feedback { color: #dc3545; }
        .neutral-feedback { color: #666; }
    </style>
</head>
<body>
<div class="container">
    <header>
        <div class="lang-switch">
            <span class="lang-btn active" id="lang-zh">中文 🇭🇰</span>
            <span class="lang-btn" id="lang-en">English 🇬🇧</span>
        </div>
        <div class="add-btn" id="add-btn">➕ 添加題目</div>
        <h1 id="main-title">測驗魔法 ✨</h1>
        <p id="subtitle">測試你的知識！挑戰自我！</p>
    </header>

    <div id="setup" class="section">
        <label id="mode-label">答題模式：</label>
        <select id="mode">
            <option value="dictation">默書模式</option>
            <option value="multiple" selected>選擇題模式</option>
            <option value="long">長答題模式</option>
        </select>
        <div style="text-align:center;margin-top:30px;">
            <button id="start-btn">🚀 開始測驗</button>
            <button class="secondary" id="manage-btn">📝 管理題目</button>
        </div>
    </div>

    <div id="quiz" class="section" style="display:none;">
        <div id="progress"></div>
        <div class="question" id="question"></div>
        <div id="feedback" class="feedback" style="display:none;"></div>
        <div id="content"></div>
        <div style="text-align:center;margin-top:30px;">
            <button id="next-btn" style="display:none;">下一題</button>
        </div>
    </div>

    <div id="result" class="result" style="display:none;">
        <div class="score" id="score"></div>
        <p id="score-note" class="score-note"></p>
        <p id="msg" style="font-size:28px;"></p>
        <div style="text-align:center;">
            <button id="view-answers-btn" class="secondary">查看答案對照</button>
            <button id="again-btn">再做一次</button>
        </div>
    </div>

    <div id="answers-section" class="section" style="display:none;">
        <h2 style="text-align:center;margin-bottom:30px;" id="answers-title">答案對照</h2>
        <div id="answers-list"></div>
        <div style="text-align:center;margin-top:40px;">
            <button class="secondary" id="back-result-btn">返回結果</button>
        </div>
    </div>

    <div id="manage-section" class="section" style="display:none;">
        <h2 style="text-align:center;" id="manage-title">我的題目</h2>
        <div id="list"></div>
        <div style="text-align:center;margin-top:30px;">
            <button class="secondary" id="back-manage-btn">返回</button>
        </div>
    </div>
</div>

<div id="toast" class="toast"></div>

<!-- 添加題目彈窗 -->
<div id="modal" style="display:none;position:fixed;inset:0;background:rgba(0,0,0,0.7);display:flex;justify-content:center;align-items:center;">
    <div style="background:white;padding:40px;border-radius:24px;max-width:600px;width:90%;">
        <h2 id="modal-title">添加新題目</h2>
        <form id="add-form">
            <label id="type-label">題目類型：</label>
            <select id="type">
                <option value="multiple">選擇題</option>
                <option value="dictation">默書</option>
                <option value="long">長答題</option>
            </select>
            <label id="q-label">題目：</label>
            <textarea id="q-text" required></textarea>
            <div id="mc-options">
                <label id="opt-a-label">選項 A：</label><input id="a" type="text">
                <label id="opt-b-label">選項 B：</label><input id="b" type="text">
                <label id="opt-c-label">選項 C：</label><input id="c" type="text">
                <label id="opt-d-label">選項 D：</label><input id="d" type="text">
                <label id="correct-label">正確答案：</label>
                <select id="correct">
                    <option value="0">A</option>
                    <option value="1">B</option>
                    <option value="2">C</option>
                    <option value="3">D</option>
                </select>
            </div>
            <div id="short-answer" style="display:none;">
                <label id="short-label">標準答案：</label>
                <textarea id="short-ans"></textarea>
            </div>
            <div id="long-answer" style="display:none;">
                <label id="long-label">參考答案：</label>
                <textarea id="long-ans"></textarea>
            </div>
            <div style="text-align:center;margin-top:20px;">
                <button type="submit" id="save-btn">保存</button>
                <button type="button" class="secondary" id="cancel-btn">取消</button>
            </div>
        </form>
    </div>
</div>

<script>
    const questions = JSON.parse(localStorage.getItem('quizQuestions') || '[]');
    let current = [], idx = 0, autoScore = 0, autoTotal = 0;
    let userAnswers = [];

    const texts = {
        zh: {
            mainTitle: "測驗魔法 ✨",
            subtitle: "測試你的知識！挑戰自我！",
            modeLabel: "答題模式：",
            startBtn: "🚀 開始測驗",
            manageBtn: "📝 管理題目",
            nextBtn: "下一題",
            viewAnswersBtn: "查看答案對照",
            againBtn: "再做一次",
            answersTitle: "答案對照",
            backResultBtn: "返回結果",
            manageTitle: "我的題目",
            backManageBtn: "返回",
            modalTitle: "添加新題目",
            typeLabel: "題目類型：",
            qLabel: "題目：",
            optALabel: "選項 A：",
            optBLabel: "選項 B：",
            optCLabel: "選項 C：",
            optDLabel: "選項 D：",
            correctLabel: "正確答案：",
            shortLabel: "標準答案：",
            longLabel: "參考答案：",
            saveBtn: "保存",
            cancelBtn: "取消",
            scoreNoteAuto: "(自動評分，不含長答題)",
            scoreNoteLongOnly: "本測驗僅含長答題，請自行判斷答案",
            fullMsg: "🎉 自動評分題全對！太棒了！",
            partMsg: (s, t) => `自動評分正確 ${s} / ${t} 題，繼續加油！`,
            longOnlyMsg: "測驗完成！長答題請查看答案對照自行判斷",
            noQuestions: "還沒有題目！請先添加",
            noModeQuestions: "此模式無題目",
            progressPrefix: "第",
            progressSuffix: "題",
            yourAnswer: "你的答案",
            standardAnswer: "標準答案",
            referenceAnswer: "參考答案",
            feedbackCorrect: "正確！",
            feedbackWrong: "錯誤！",
            feedbackLong: "已提交（長答題請自行判斷）",
            submitDictation: "檢查答案",
            submitLong: "提交答案"
        },
        en: {
            mainTitle: "Quiz Magic ✨",
            subtitle: "Test Your Knowledge! Challenge Yourself!",
            modeLabel: "Quiz Mode:",
            startBtn: "🚀 Start Quiz",
            manageBtn: "📝 Manage Questions",
            nextBtn: "Next Question",
            viewAnswersBtn: "View Answer Key",
            againBtn: "Try Again",
            answersTitle: "Answer Key",
            backResultBtn: "Back to Result",
            manageTitle: "My Questions",
            backManageBtn: "Back",
            modalTitle: "Add New Question",
            typeLabel: "Question Type:",
            qLabel: "Question:",
            optALabel: "Option A:",
            optBLabel: "Option B:",
            optCLabel: "Option C:",
            optDLabel: "Option D:",
            correctLabel: "Correct Answer:",
            shortLabel: "Standard Answer:",
            longLabel: "Reference Answer:",
            saveBtn: "Save",
            cancelBtn: "Cancel",
            scoreNoteAuto: "(Auto-scored, excluding long answers)",
            scoreNoteLongOnly: "This quiz contains only long answers. Please check yourself.",
            fullMsg: "🎉 All auto-scored questions correct! Great job!",
            partMsg: (s, t) => `Auto-scored: ${s} / ${t} correct, keep going!`,
            longOnlyMsg: "Quiz completed! Long answers require self-checking.",
            noQuestions: "No questions yet! Please add some first.",
            noModeQuestions: "No questions in this mode.",
            progressPrefix: "Question",
            progressSuffix: "",
            yourAnswer: "Your Answer",
            standardAnswer: "Standard Answer",
            referenceAnswer: "Reference Answer",
            feedbackCorrect: "Correct!",
            feedbackWrong: "Wrong!",
            feedbackLong: "Submitted (Long answer: self-check required)",
            submitDictation: "Check Answer",
            submitLong: "Submit Answer"
        }
    };

    let lang = 'zh';
    let T = texts.zh;

    function updateAllTexts() {
        T = texts[lang];
        document.getElementById('main-title').textContent = T.mainTitle;
        document.getElementById('subtitle').textContent = T.subtitle;
        document.getElementById('mode-label').textContent = T.modeLabel;
        document.getElementById('start-btn').textContent = T.startBtn;
        document.getElementById('manage-btn').textContent = T.manageBtn;
        document.getElementById('next-btn').textContent = T.nextBtn;
        document.getElementById('view-answers-btn').textContent = T.viewAnswersBtn;
        document.getElementById('again-btn').textContent = T.againBtn;
        document.getElementById('answers-title').textContent = T.answersTitle;
        document.getElementById('back-result-btn').textContent = T.backResultBtn;
        document.getElementById('manage-title').textContent = T.manageTitle;
        document.getElementById('back-manage-btn').textContent = T.backManageBtn;
        document.getElementById('modal-title').textContent = T.modalTitle;
        document.getElementById('type-label').textContent = T.typeLabel;
        document.getElementById('q-label').textContent = T.qLabel;
        document.getElementById('opt-a-label').textContent = T.optALabel;
        document.getElementById('opt-b-label').textContent = T.optBLabel;
        document.getElementById('opt-c-label').textContent = T.optCLabel;
        document.getElementById('opt-d-label').textContent = T.optDLabel;
        document.getElementById('correct-label').textContent = T.correctLabel;
        document.getElementById('short-label').textContent = T.shortLabel;
        document.getElementById('long-label').textContent = T.longLabel;
        document.getElementById('save-btn').textContent = T.saveBtn;
        document.getElementById('cancel-btn').textContent = T.cancelBtn;
        // 模式選項文字
        document.getElementById('mode').options[0].text = lang === 'zh' ? '默書模式' : 'Dictation Mode';
        document.getElementById('mode').options[1].text = lang === 'zh' ? '選擇題模式' : 'Multiple Choice Mode';
        document.getElementById('mode').options[2].text = lang === 'zh' ? '長答題模式' : 'Long Answer Mode';
    }

    document.getElementById('lang-zh').onclick = () => {
        lang = 'zh';
        document.querySelectorAll('.lang-btn').forEach(b => b.classList.remove('active'));
        document.getElementById('lang-zh').classList.add('active');
        updateAllTexts();
    };

    document.getElementById('lang-en').onclick = () => {
        lang = 'en';
        document.querySelectorAll('.lang-btn').forEach(b => b.classList.remove('active'));
        document.getElementById('lang-en').classList.add('active');
        updateAllTexts();
    };

    function toast(msg) {
        const t = document.getElementById('toast');
        t.textContent = msg;
        t.style.display = 'block';
        setTimeout(() => t.style.display = 'none', 3000);
    }

    // 彈窗
    document.getElementById('add-btn').onclick = () => document.getElementById('modal').style.display = 'flex';
    document.getElementById('cancel-btn').onclick = () => document.getElementById('modal').style.display = 'none';

    document.getElementById('type').onchange = () => {
        const t = document.getElementById('type').value;
        document.getElementById('mc-options').style.display = t === 'multiple' ? 'block' : 'none';
        document.getElementById('short-answer').style.display = t === 'dictation' ? 'block' : 'none';
        document.getElementById('long-answer').style.display = t === 'long' ? 'block' : 'none';
    };

    document.getElementById('add-form').onsubmit = e => {
        e.preventDefault();
        const type = document.getElementById('type').value;
        const q = { type, question: document.getElementById('q-text').value.trim() };
        if (type === 'multiple') {
            q.options = [document.getElementById('a').value.trim(), document.getElementById('b').value.trim(), document.getElementById('c').value.trim(), document.getElementById('d').value.trim()];
            q.correct = parseInt(document.getElementById('correct').value);
        } else {
            q.answer = type === 'dictation' ? document.getElementById('short-ans').value.trim() : document.getElementById('long-ans').value.trim();
        }
        questions.push(q);
        localStorage.setItem('quizQuestions', JSON.stringify(questions));
        toast(lang === 'zh' ? '題目已保存！' : 'Question saved!');
        document.getElementById('modal').style.display = 'none';
        e.target.reset();
        loadList();
    };

    function loadList() {
        const list = document.getElementById('list');
        list.innerHTML = questions.length === 0 ? '<p style="text-align:center;color:#888;">' + (lang === 'zh' ? '還沒有題目，快去創作吧！' : 'No questions yet! Create some!') + '</p>' : '';
        questions.forEach((q, i) => {
            const div = document.createElement('div');
            div.style = 'background:#f9f9f9;padding:15px;margin:10px 0;border-radius:12px;position:relative;';
            div.innerHTML = `<strong>${q.question}</strong><br>
                ${q.type === 'multiple' ? 'A:'+q.options[0]+' | 正確:'+'ABCD'[q.correct] : '答案:'+q.answer.substring(0,40)+(q.answer.length>40?'...':'')}
                <button style="position:absolute;top:10px;right:10px;background:#ff6b6b;color:white;border:none;padding:5px 10px;border-radius:8px;" onclick="delQuestion(\( {i})"> \){lang === 'zh' ? '刪除' : 'Delete'}</button>`;
            list.appendChild(div);
        });
    }

    window.delQuestion = i => {
        questions.splice(i, 1);
        localStorage.setItem('quizQuestions', JSON.stringify(questions));
        toast(lang === 'zh' ? '題目已刪除' : 'Question deleted');
        loadList();
    };

    document.getElementById('manage-btn').onclick = () => {
        document.getElementById('setup').style.display = 'none';
        document.getElementById('manage-section').style.display = 'block';
        loadList();
    };

    document.getElementById('back-manage-btn').onclick = () => {
        document.getElementById('manage-section').style.display = 'none';
        document.getElementById('setup').style.display = 'block';
    };

    // 開始測驗
    document.getElementById('start-btn').onclick = () => {
        if (questions.length === 0) return toast(T.noQuestions);
        const mode = document.getElementById('mode').value;
        current = questions.filter(q => q.type === mode).sort(() => Math.random() - 0.5);
        if (current.length === 0) return toast(T.noModeQuestions);
        current = current.slice(0, Math.min(10, current.length));
        idx = autoScore = 0;
        autoTotal = current.filter(q => q.type !== 'long').length;
        userAnswers = [];
        document.getElementById('setup').style.display = 'none';
        document.getElementById('quiz').style.display = 'block';
        showQuestion();
    };

    function showQuestion() {
        const q = current[idx];
        document.getElementById('progress').textContent = T.progressPrefix + ' ' + (idx + 1) + ' / ' + current.length + ' ' + T.progressSuffix;
        document.getElementById('question').textContent = q.question;
        const c = document.getElementById('content');
        c.innerHTML = '';
        document.getElementById('feedback').style.display = 'none';
        document.getElementById('next-btn').style.display = 'none';

        if (q.type === 'multiple') {
            q.options.forEach((opt, i) => {
                const label = document.createElement('label');
                label.innerHTML = `<input type="radio" name="ans" value="${i}"> ${opt}`;
                c.appendChild(label);
            });
            c.onchange = () => {
                const selected = parseInt(document.querySelector('input[name="ans"]:checked').value);
                userAnswers.push(selected);
                const correct = selected === q.correct;
                if (correct) autoScore++;
                const feedback = document.getElementById('feedback');
                feedback.textContent = correct ? T.feedbackCorrect : T.feedbackWrong;
                feedback.className = 'feedback ' + (correct ? 'correct-feedback' : 'wrong-feedback');
                feedback.style.display = 'block';
                document.getElementById('next-btn').style.display = 'block';
            };
        } else if (q.type === 'dictation') {
            const ta = document.createElement('textarea');
            ta.placeholder = lang === 'zh' ? '輸入答案...' : 'Type your answer...';
            ta.style.height = '100px';
            c.appendChild(ta);
            const submitBtn = document.createElement('button');
            submitBtn.textContent = T.submitDictation;
            submitBtn.onclick = () => {
                const ans = ta.value.trim();
                userAnswers.push(ans);
                const correct = ans.toLowerCase() === q.answer.toLowerCase();
                if (correct) autoScore++;
                ta.disabled = true;
                submitBtn.disabled = true;
                const feedback = document.getElementById('feedback');
                feedback.textContent = correct ? T.feedbackCorrect : T.feedbackWrong;
                feedback.className = 'feedback ' + (correct ? 'correct-feedback' : 'wrong-feedback');
                feedback.style.display = 'block';
                document.getElementById('next-btn').style.display = 'block';
            };
            c.appendChild(submitBtn);
        } else { // long
            const ta = document.createElement('textarea');
            ta.placeholder = lang === 'zh' ? '輸入詳細答案...' : 'Write your detailed answer...';
            ta.style.height = '150px';
            c.appendChild(ta);
            const submitBtn = document.createElement('button');
            submitBtn.textContent = T.submitLong;
            submitBtn.onclick = () => {
                userAnswers.push(ta.value.trim());
                ta.disabled = true;
                submitBtn.disabled = true;
                const feedback = document.getElementById('feedback');
                feedback.textContent = T.feedbackLong;
                feedback.className = 'feedback neutral-feedback';
                feedback.style.display = 'block';
                document.getElementById('next-btn').style.display = 'block';
            };
            c.appendChild(submitBtn);
        }
    }

    document.getElementById('next-btn').onclick = () => {
        idx++;
        if (idx < current.length) showQuestion();
        else endQuiz();
    };

    function endQuiz() {
        document.getElementById('quiz').style.display = 'none';
        document.getElementById('result').style.display = 'block';
        if (autoTotal > 0) {
            document.getElementById('score').textContent = autoScore + ' / ' + autoTotal;
            document.getElementById('score-note').textContent = T.scoreNoteAuto;
            document.getElementById('msg').textContent = autoScore === autoTotal ? T.fullMsg : T.partMsg(autoScore, autoTotal);
        } else {
            document.getElementById('score').textContent = '—';
            document.getElementById('score-note').textContent = T.scoreNoteLongOnly;
            document.getElementById('msg').textContent = T.longOnlyMsg;
        }
    }

    // 答案對照 - 正常字串拼接
    document.getElementById('view-answers-btn').onclick = () => {
        document.getElementById('result').style.display = 'none';
        document.getElementById('answers-section').style.display = 'block';
        const list = document.getElementById('answers-list');
        list.innerHTML = '';
        current.forEach((q, i) => {
            const pair = document.createElement('div');
            pair.className = 'answer-pair';
            let user = userAnswers[i] || (lang === 'zh' ? '(未回答)' : '(No answer)');
            let userText = user;
            let standardText = '';

            if (q.type === 'multiple') {
                const correctLetter = 'ABCD'[q.correct];
                userText = 'ABCD'[userAnswers[i]] || (lang === 'zh' ? '(未選)' : '(Not selected)');
                standardText = correctLetter + ' (' + q.options[q.correct] + ')';
            } else {
                userText = user;
                standardText = q.answer;
            }

            const questionNumber = (lang === 'zh' ? '第 ' : 'Question ') + (i + 1) + (lang === 'zh' ? ' 題：' : ': ');
            pair.innerHTML = '<strong style="display:block;margin-bottom:10px;">' + questionNumber + q.question + '</strong>' +
                '<div class="answer-left">' +
                    '<div class="answer-title">' + T.yourAnswer + '</div>' +
                    '<pre>' + userText + '</pre>' +
                '</div>' +
                '<div class="answer-right">' +
                    '<div class="answer-title">' + (q.type === 'long' ? T.referenceAnswer : T.standardAnswer) + '</div>' +
                    '<pre>' + standardText + '</pre>' +
                '</div>';

            if (q.type === 'long') {
                pair.classList.add('neutral-pair');
            } else {
                const correct = (q.type === 'multiple') ? userAnswers[i] == q.correct : user.toLowerCase() === q.answer.toLowerCase();
                if (correct) pair.classList.add('correct-pair');
                else if (user !== (lang === 'zh' ? '(未回答)' : '(No answer)')) pair.classList.add('wrong-pair');
            }

            list.appendChild(pair);
        });
    };

    document.getElementById('back-result-btn').onclick = () => {
        document.getElementById('answers-section').style.display = 'none';
        document.getElementById('result').style.display = 'block';
    };

    document.getElementById('again-btn').onclick = () => {
        document.getElementById('result').style.display = 'none';
        document.getElementById('setup').style.display = 'block';
    };

    loadList();
    updateAllTexts();
</script>
</body>
</html>
