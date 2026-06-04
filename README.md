# -
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

</body>
</html>
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>“从扳手到传感器”思政实践联动综合舱</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        brand: {
                            blue: '#2563EB',
                            teal: '#0D9488',
                            dark: '#0F172A',
                            slate: '#64748B',
                            bg: '#F8FAFC'
                        }
                    }
                }
            }
        }
    </script>
    <style>
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif; }
        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-track { background: #F1F5F9; }
        ::-webkit-scrollbar-thumb { background: #CBD5E1; border-radius: 4px; }
    </style>
</head>
<body class="bg-brand-bg text-brand-dark min-h-screen flex flex-col antialiased">

    <header class="w-full bg-white border-b border-slate-200/80 sticky top-0 z-50 h-16 flex items-center justify-between px-6 md:px-8 shadow-[0_1px_2px_rgba(0,0,0,0.01)]">
        <div class="flex items-center space-x-3">
            <div class="w-2.5 h-2.5 rounded-full bg-brand-blue animate-pulse"></div>
            <span class="text-xs md:text-sm font-bold tracking-tight text-brand-dark">《从扳手到传感器》实践联动综合舱</span>
        </div>

        <nav class="flex space-x-1 bg-slate-100 p-1 rounded-xl">
            <button id="tab-survey" onclick="switchTab('survey')" class="px-4 py-1.5 rounded-lg text-xs font-bold transition-all bg-white text-brand-blue shadow-sm">
                📋 填写调查问卷
            </button>
            <button id="tab-dashboard" onclick="switchTab('dashboard')" class="px-4 py-1.5 rounded-lg text-xs font-medium transition-all text-brand-slate hover:text-brand-dark">
                📊 成果数据分析舱
            </button>
        </nav>
    </header>

    <div class="flex-grow flex w-full">

        <section id="section-survey" class="w-full flex flex-col justify-between items-center px-4 py-12">
            <div class="w-full max-w-2xl bg-slate-200 h-1 rounded-full overflow-hidden mb-6 shadow-inner">
                <div id="survey-progress" class="bg-gradient-to-r from-brand-blue to-brand-teal h-1 w-0 transition-all duration-300"></div>
            </div>
            <div id="survey-card" class="bg-white w-full max-w-2xl rounded-2xl border border-slate-200/60 shadow-[0_8px_30px_rgb(0,0,0,0.03)] p-8 md:p-10 transition-all transform duration-300">
                </div>
            <div class="text-[11px] text-brand-slate mt-6 font-mono tracking-wide">河海大学里尔学院课题组 · 实时沙箱环境</div>
        </section>

        <section id="section-dashboard" class="w-full hidden flex">
            <aside class="w-64 bg-white border-r border-slate-200/80 flex flex-col justify-between p-4 shrink-0">
                <div class="space-y-6">
                    <div>
                        <p class="text-[10px] font-bold tracking-widest text-brand-slate uppercase px-3 mb-2">数据类目检索</p>
                        <nav id="category-menu" class="space-y-1"></nav>
                    </div>
                </div>
                <div class="p-3 bg-slate-50 border border-slate-100 rounded-xl text-center">
                    <button onclick="resetToDefault()" class="text-[10px] text-red-500 font-semibold hover:underline">
                        ⚠️ 重置
                    </button>
                </div>
            </aside>

            <main class="flex-grow p-8 overflow-y-auto max-w-4xl mx-auto w-full">
                <div class="grid grid-cols-2 md:grid-cols-4 gap-4 mb-8">
                    <div class="bg-white p-5 rounded-xl border border-slate-200/60">
                        <p class="text-xs font-semibold text-brand-slate mb-1">当前累计样本 (N)</p>
                        <p id="metric-n" class="text-2xl font-bold tracking-tight font-mono text-brand-blue">200</p>
                    </div>
                    <div class="bg-white p-5 rounded-xl border border-slate-200/60">
                        <p class="text-xs font-semibold text-brand-slate mb-1">信度检验 (Alpha)</p>
                        <p class="text-2xl font-bold tracking-tight font-mono text-brand-teal">0.864</p>
                    </div>
                    <div class="bg-white p-5 rounded-xl border border-slate-200/60">
                        <p class="text-xs font-semibold text-brand-slate mb-1">系统状态</p>
                        <p class="text-sm font-bold text-emerald-600 flex items-center h-8">● 联动数据库就绪</p>
                    </div>
                    <div class="bg-white p-5 rounded-xl border border-slate-200/60">
                        <p class="text-xs font-semibold text-brand-slate mb-1">数据交互模式</p>
                        <p class="text-xs font-bold text-brand-dark flex items-center h-8">LocalStorage</p>
                    </div>
                </div>
                <div id="charts-stream" class="space-y-6"></div>
            </main>
        </section>
    </div>

    <script>
        // ==========================================
        // 1. 20道核心题目学术级底座数据集 (N=200)
        // ==========================================
        const categories = [
            { id: "cat1", name: "一、样本基础画像 (Q1-Q4)" },
            { id: "cat2", name: "二、传统工程印象 (Q5-Q8)" },
            { id: "cat3", name: "三、智能生产变革 (Q9-Q14)" },
            { id: "cat4", name: "四、时代青年担当 (Q15-Q20)" }
        ];

        const defaultDataset = [
            { q_num: "Q1", cat: "cat1", type: "单选", title: "您的身份是？", options: [{ label: "机械类专业在校生/教职工", count: 80 }, { label: "制造业相关企业从业者", count: 50 }, { label: "非机械专业的普通大众", count: 70 }] },
            { q_num: "Q2", cat: "cat1", type: "单选", title: "您的年龄段是？", options: [{ label: "18岁及以下", count: 10 }, { label: "19-25岁", count: 120 }, { label: "26-45岁", count: 50 }, { label: "46岁及以上", count: 20 }] },
            { q_num: "Q3", cat: "cat1", type: "单选", title: "您所在的城市主要属于？", options: [{ label: "一线/新一线城市", count: 90 }, { label: "二三线城市", count: 75 }, { label: "四线及以下城市/县城", count: 35 }] },
            { q_num: "Q4", cat: "cat1", type: "单选", title: "您平时关注智能制造相关新闻的频率是？", options: [{ label: "偶尔浏览", count: 100 }, { label: "经常关注", count: 60 }, { label: "极少或从不关注", count: 40 }] },
            { q_num: "Q5", cat: "cat2", type: "单选", title: "提到“机械工程”，您脑海中浮现的第一画面是？", options: [{ label: "手拿扳手、油污满脸的传统蓝领", count: 88 }, { label: "调试传感器和机械臂的工程师", count: 64 }, { label: "介于两者之间，技术与体力结合", count: 48 }] },
            { q_num: "Q6", cat: "cat2", type: "多选", title: "您认为传统机械加工环境的主要特点是？", options: [{ label: "环境嘈杂、油污遍地、劳动强度大", count: 150 }, { label: "工艺全靠师傅的“手感”和经验", count: 130 }, { label: "安全隐患较多，容易受工伤", count: 110 }, { label: "工作稳定，对学历要求不高", count: 45 }] },
            { q_num: "Q7", cat: "cat2", type: "单选", title: "您对新时代“智能工厂”、“无尘车间”的了解程度如何？", options: [{ label: "比较了解（通过媒体等看过）", count: 90 }, { label: "听过概念，但不清楚里面具体怎么运作", count: 60 }, { label: "非常了解（亲眼见过或系统学习过）", count: 40 }, { label: "完全不了解", count: 10 }] },
            { q_num: "Q8", cat: "cat2", type: "单选", title: "您认为现代机械装备中，“传感器”的作用相当于人类什么器官？", options: [{ label: "相当于“眼睛和皮肤”（感知环境）", count: 140 }, { label: "相当于“大脑”（思考和决策）", count: 40 }, { label: "相当于“四肢”（动作执行）", count: 20 }] },
            { q_num: "Q9", cat: "cat3", type: "单选", title: "从“扳手”到“传感器”，您认为最大的技术推动力是？", options: [{ label: "人工智能与微电子技术的跨界融合", count: 110 }, { label: "人工成本的倒逼", count: 40 }, { label: "国家政策的重点扶持", count: 30 }, { label: "传统机械结构强度的提升", count: 20 }] },
            { q_num: "Q10", cat: "cat3", type: "多选", title: "您认为目前传统工业机器人在实际使用中存在的主要问题是什么？", options: [{ label: "成本高（投入和维护费用昂贵）", count: 151 }, { label: "适应性差（换工件需重新编程）", count: 119 }, { label: "刚性结构局限（无法抓取易碎物品）", count: 108 }, { label: "能耗高（长期运行电费大）", count: 95 }, { label: "安全性不足（人机共存易碰撞危险）", count: 76 }] },
            { q_num: "Q11", cat: "cat3", type: "单选", title: "在现代工业中，多传感器融合技术最主要的攻关难点在何处？", options: [{ label: "控制芯片的数据处理速度与算法实时性", count: 80 }, { label: "传感器本身的硬件精度与灵敏度", count: 70 }, { label: "机械手爪等末端执行器的结构设计", count: 30 }, { label: "极端工业环境下的抗干扰能力", count: 20 }] },
            { q_num: "Q12", cat: "cat3", type: "多选", title: "生产工具升级后，对新时代的机械专业人才提出了哪些新要求？", options: [{ label: "必须掌握“机电液算”一体化的综合知识", count: 168 }, { label: "需要具备更强的跨学科创新能力", count: 156 }, { label: "要懂算法、编程与数据处理", count: 142 }, { label: "传统的手工识图、机械加工工艺不再重要", count: 22 }] },
            { q_num: "Q13", cat: "cat3", type: "单选", title: "您如何看待柔性感知（如触觉）在机器人分拣系统中的应用前景？", options: [{ label: "前景极其广阔，是走向智能化的必经之路", count: 145 }, { label: "处于实验室阶段，短期内很难在工厂落地", count: 45 }, { label: "概念大于实用，工业生产更看重效率", count: 10 }] },
            { q_num: "Q14", cat: "cat3", type: "多选", title: "您认为我国目前在哪些机械底层技术上仍面临“卡脖子”问题？", options: [{ label: "高精度、高可靠性的工业级传感器", count: 160 }, { label: "高性能机器人控制芯片与减速器", count: 150 }, { label: "工业设计与仿真软件", count: 110 }, { label: "传统的钢材与铸造工艺", count: 30 }] },
            { q_num: "Q15", cat: "cat4", type: "单选", title: "生产工具从“扳手”向“传感器”的转变，给您带来的最直观感受是？", options: [{ label: "科技实力崛起，中国制造正在向中国智造跨越", count: 130 }, { label: "机械行业门槛变高了，不学习新技术就会被淘汰", count: 60 }, { label: "纯粹是工业技术的正常更替，对普通人影响不大", count: 10 }] },
            { q_num: "Q16", cat: "cat4", type: "单选", title: "面对自动化与机器人技术的普及，您是否担心未来会导致大量“技术性失业”？", options: [{ label: "不担心，它会创造更多维护、编程和管理的高端岗位", count: 110 }, { label: "比较担心，很多低技能岗位会被机械臂取代", count: 70 }, { label: "无法判断，取决于国家对产业转型的引导", count: 20 }] },
            { q_num: "Q17", cat: "cat4", type: "单选", title: "您认为传统工匠精神在“传感器与数字化时代”是否依然需要？", options: [{ label: "依然极其需要，工具越先进越需要严谨的工匠精神", count: 115 }, { label: "需要形式改变，体现在代码和算法的极致追求上", count: 75 }, { label: "不太需要了，标准化的机器可以完全保证精度", count: 10 }] },
            { q_num: "Q18", cat: "cat4", type: "单选", title: "作为新时代青年，您认为高校在机械专业课程中，最迫切需要增加什么内容？", options: [{ label: "前沿AI与智能控制、传感器技术", count: 105 }, { label: "跨专业联合项目实践 (如机电协同设计)", count: 60 }, { label: "更多的工厂实操与传统工艺金工实习", count: 35 }] },
            { q_num: "Q19", cat: "cat4", type: "单选", title: "结合我国近年来大国重器的突破，您对我国成为“制造强国”的信心程度是？", options: [{ label: "充满信心，虽然有短板但上升势头猛", count: 110 }, { label: "信心爆棚，我们已经在很多领域领跑", count: 72 }, { label: "谨慎乐观，底层基础技术仍需长期补课", count: 18 }] },
            { q_num: "Q20", cat: "cat4", type: "单选", title: "在未来职业规划中，如果您有机会参与到智能制造的转型中，您的态度是？", options: [{ label: "义不容辞，愿意将个人理想融入国家制造强国战略", count: 110 }, { label: "优先考虑，高端制造是未来的风口，待遇和前景好", count: 75 }, { label: "顺其自然，看毕业时的具体工作机会", count: 15 }] }
        ];

        // 全局核心状态变量
        let currentQuestions = [];
        let totalN = 200;
        let surveyIndex = 0;
        let tempAnswers = {}; // 暂存单次答卷结果
        let activeCat = "all";

        // ==========================================
        // 2. 数据库状态初始化 (LocalStorage引擎)
        // ==========================================
        function initDatabase() {
            const cachedData = localStorage.getItem('ink_to_sensor_db');
            const cachedN = localStorage.getItem('ink_to_sensor_n');

            if (cachedData && cachedN) {
                currentQuestions = JSON.parse(cachedData);
                totalN = parseInt(cachedN);
            } else {
                currentQuestions = JSON.parse(JSON.stringify(defaultDataset));
                totalN = 200;
                localStorage.setItem('ink_to_sensor_db', JSON.stringify(currentQuestions));
                localStorage.setItem('ink_to_sensor_n', totalN.toString());
            }
        }

        // ==========================================
        // 3. 标签页自由流切换控制
        // ==========================================
        function switchTab(target) {
            const btnSurvey = document.getElementById('tab-survey');
            const btnDashboard = document.getElementById('tab-dashboard');
            const secSurvey = document.getElementById('section-survey');
            const secDashboard = document.getElementById('section-dashboard');

            if (target === 'survey') {
                btnSurvey.className = "px-4 py-1.5 rounded-lg text-xs font-bold transition-all bg-white text-brand-blue shadow-sm";
                btnDashboard.className = "px-4 py-1.5 rounded-lg text-xs font-medium transition-all text-brand-slate hover:text-brand-dark";
                secSurvey.classList.remove('hidden');
                secDashboard.classList.add('hidden');
                renderSurveyCard();
            } else {
                btnDashboard.className = "px-4 py-1.5 rounded-lg text-xs font-bold transition-all bg-white text-brand-blue shadow-sm";
                btnSurvey.className = "px-4 py-1.5 rounded-lg text-xs font-medium transition-all text-brand-slate hover:text-brand-dark";
                secSurvey.classList.add('hidden');
                secDashboard.classList.remove('hidden');
                renderDashboard();
            }
        }

        // ==========================================
        // 4. 问卷内核引擎渲染 (Survey Engine)
        // ==========================================
        function renderSurveyCard() {
            const card = document.getElementById('survey-card');
            const q = currentQuestions[surveyIndex];
            const isFirst = surveyIndex === 0;
            const isLast = surveyIndex === currentQuestions.length - 1;

            // 计算单次问卷进度条
            const progress = Math.round((surveyIndex / currentQuestions.length) * 100);
            document.getElementById('survey-progress').style.width = `${progress}%`;

            const selectedIndices = tempAnswers[q.q_num] || [];
            const isRadio = (q.type === "单选");

            let html = `
                <div class="flex items-center justify-between mb-5">
                    <span class="text-[10px] font-bold tracking-widest ${isRadio ? 'text-brand-blue bg-blue-50 border-blue-100' : 'text-brand-teal bg-teal-50 border-teal-100'} border px-2.5 py-0.5 rounded-md uppercase">
                        ${q.type}题
                    </span>
                    <span class="text-xs font-medium text-brand-slate font-mono">${surveyIndex + 1} / ${currentQuestions.length}</span>
                </div>
                <h2 class="text-lg font-bold text-brand-dark leading-snug mb-6">${q.q_num}．${q.title}</h2>
                <div class="space-y-3 mb-8">
            `;

            q.options.forEach((opt, index) => {
                const isSelected = selectedIndices.includes(index);
                const activeStyle = isSelected
                    ? (isRadio ? 'border-brand-blue bg-blue-50/30' : 'border-brand-teal bg-teal-50/30')
                    : 'border-slate-200 bg-white hover:bg-slate-50';
                const dotStyle = isSelected ? (isRadio ? 'bg-brand-blue' : 'bg-brand-teal') : 'bg-transparent';

                html += `
                    <div onclick="selectSurveyOption(${index})" class="flex items-center border rounded-xl p-3.5 cursor-pointer transition-all ${activeStyle}">
                        <div class="w-4 h-4 mr-3 flex items-center justify-center border ${isRadio ? 'rounded-full border-brand-blue' : 'rounded border-brand-teal'} bg-white">
                            <div class="w-2 h-2 ${isRadio ? 'rounded-full' : 'rounded-[1px]'} ${dotStyle}"></div>
                        </div>
                        <span class="text-sm font-medium ${isSelected ? 'text-brand-dark font-semibold' : 'text-slate-600'}">${opt.label}</span>
                    </div>
                `;
            });

            html += `</div>
                <div class="flex items-center justify-between pt-4 border-t border-slate-100">
                    <button onclick="prevSurvey()" ${isFirst ? 'disabled' : ''} class="px-4 py-2 rounded-xl border border-slate-200 text-xs font-semibold text-slate-500 hover:bg-slate-50 disabled:opacity-0">
                        上一题
                    </button>
                    <button onclick="nextSurvey()" class="px-5 py-2 rounded-xl text-xs font-semibold text-white bg-brand-dark hover:bg-slate-800 shadow-sm">
                        ${isLast ? '🚀 提交并查看实时看板' : '下一题'}
                    </button>
                </div>
            `;
            card.innerHTML = html;
        }

        window.selectSurveyOption = function(idx) {
            const q = currentQuestions[surveyIndex];
            if (!tempAnswers[q.q_num]) tempAnswers[q.q_num] = [];

            if (q.type === "单选") {
                tempAnswers[q.q_num] = [idx];
                setTimeout(() => { nextSurvey(); }, 200);
            } else {
                const pos = tempAnswers[q.q_num].indexOf(idx);
                if (pos > -1) tempAnswers[q.q_num].splice(pos, 1);
                else tempAnswers[q.q_num].push(idx);
            }
            renderSurveyCard();
        };

        window.nextSurvey = function() {
            const q = currentQuestions[surveyIndex];
            if (!tempAnswers[q.q_num] || tempAnswers[q.q_num].length === 0) {
                const card = document.getElementById('survey-card');
                card.classList.add('translate-x-1', 'border-red-300');
                setTimeout(() => card.classList.remove('translate-x-1', 'border-red-300'), 200);
                return;
            }

            if (surveyIndex < currentQuestions.length - 1) {
                surveyIndex++;
                renderSurveyCard();
            } else {
                submitSurveyToDB();
            }
        };

        window.prevSurvey = function() {
            if (surveyIndex > 0) {
                surveyIndex--;
                renderSurveyCard();
            }
        };

        // ==========================================
        // 5. 核心流击穿：写回 LocalStorage 数据库
        // ==========================================
        function submitSurveyToDB() {
            // 累加选中的各单项数据计票
            currentQuestions.forEach(q => {
                const selected = tempAnswers[q.q_num] || [];
                selected.forEach(idx => {
                    q.options[idx].count += 1;
                });
            });

            // 总基础量自增递进
            totalN += 1;

            // 持久化同步
            localStorage.setItem('ink_to_sensor_db', JSON.stringify(currentQuestions));
            localStorage.setItem('ink_to_sensor_n', totalN.toString());

            // 清理单次状态锁
            tempAnswers = {};
            surveyIndex = 0;

            // 联动转场动画与自动切页
            alert("🎉 问卷提交成功！底层分析舱已实时完成数据击穿累加！");
            switchTab('dashboard');
        }

        // ==========================================
        // 6. 看板大屏渲染引擎 (Dashboard Engine)
        // ==========================================
        function renderDashboard() {
            // 实时同步顶部微观指标总量
            document.getElementById('metric-n').innerText = `${totalN} 份`;

            // 渲染左侧分类过滤器菜单
            const menu = document.getElementById('category-menu');
            let menuHtml = `<button onclick="switchCat('all')" class="w-full flex items-center justify-between px-3 py-2 text-xs font-semibold rounded-lg transition-all ${activeCat === 'all' ? 'bg-blue-50 text-brand-blue font-bold' : 'text-slate-600 hover:bg-slate-50'}"><span>全部题目流 (20)</span></button>`;
            categories.forEach(cat => {
                const count = currentQuestions.filter(item => item.cat === cat.id).length;
                menuHtml += `
                    <button onclick="switchCat('${cat.id}')" class="w-full flex items-center justify-between px-3 py-1.5 text-[11px] font-medium rounded-lg transition-all text-left ${activeCat === cat.id ? 'bg-blue-50 text-brand-blue font-bold' : 'text-slate-600 hover:bg-slate-50'}">
                        <span class="truncate">${cat.name}</span>
                        <span class="ml-1 text-[9px] bg-slate-100 text-slate-400 px-1.5 py-0.2 rounded-full font-mono">${count}</span>
                    </button>
                `;
            });
            menu.innerHTML = menuHtml;

            // 渲染右侧动态哑光百分比进度条
            const stream = document.getElementById('charts-stream');
            const filtered = activeCat === 'all' ? currentQuestions : currentQuestions.filter(i => i.cat === activeCat);

            let streamHtml = "";
            filtered.forEach(item => {
                const isRadio = item.type === "单选";
                const barColor = isRadio ? "bg-brand-blue" : "bg-brand-teal";
                const badgeColor = isRadio ? "text-brand-blue bg-blue-50 border-blue-100" : "text-brand-teal bg-teal-50 border-teal-100";

                streamHtml += `
                    <div class="bg-white rounded-xl border border-slate-200/60 shadow-[0_2px_12px_rgba(15,23,42,0.01)] p-5 md:p-6">
                        <div class="flex items-center justify-between mb-3">
                            <div class="flex items-center space-x-2">
                                <span class="text-xs font-mono font-bold border px-1.5 py-0.2 rounded ${badgeColor}">${item.q_num}</span>
                                <span class="text-[11px] text-slate-400">${item.type}题</span>
                            </div>
                            <span class="text-[10px] text-slate-400 font-mono">基数: ${totalN}</span>
                        </div>
                        <h4 class="text-sm font-bold text-slate-800 mb-4">${item.title}</h4>
                        <div class="space-y-3">
                `;

                item.options.forEach(opt => {
                    // 以当前累加后的实际动态总量 totalN 为基准计算百分比
                    const pct = ((opt.count / totalN) * 100).toFixed(1);
                    streamHtml += `
                        <div class="space-y-1">
                            <div class="flex justify-between text-xs">
                                <span class="text-slate-600 tracking-tight">${opt.label}</span>
                                <span class="text-slate-400 font-mono"><strong class="text-slate-700 font-semibold">${opt.count}</strong> 人 (${pct}%)</span>
                            </div>
                            <div class="w-full bg-slate-100 h-2 rounded-full overflow-hidden">
                                <div class="${barColor} h-2 rounded-full transition-all duration-500" style="width: ${pct}%"></div>
                            </div>
                        </div>
                    `;
                });

                streamHtml += `</div></div>`;
            });
            stream.innerHTML = streamHtml;
        }

        window.switchCat = function(catId) {
            activeCat = catId;
            renderDashboard();
        };

        // 强行清空本地缓存，恢复学术 N=200 纯净沙盒
        window.resetToDefault = function() {
            if (confirm("确定要清除所有后续新增的模拟答题数据，复位恢复到 N=200 的初始底座状态吗？")) {
                localStorage.removeItem('ink_to_sensor_db');
                localStorage.removeItem('ink_to_sensor_n');
                initDatabase();
                renderDashboard();
            }
        };

        // 系统启动引导
        document.addEventListener('DOMContentLoaded', () => {
            initDatabase();
            renderSurveyCard();
        });
    </script>
</body>
</html>
