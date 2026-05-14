<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>가보자고 | GABOJAGO</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Black+Han+Sans&family=Noto+Sans+KR:wght@400;700&display=swap');
        
        /* 폰트 안정화 및 글자 선명도 개선 */
        body { 
            font-family: 'Noto Sans KR', sans-serif; 
            transition: all 0.5s ease; 
            overflow-x: hidden; 
            margin: 0; 
            background-color: #000; 
            color: #fff;
            -webkit-font-smoothing: antialiased; /* 글자 깨짐 방지 */
            -moz-osx-font-smoothing: grayscale;
        }

        .hero-font { 
            font-family: 'Black Han Sans', sans-serif; 
            letter-spacing: -0.02em; /* 글자 뭉개짐 방지를 위한 자간 조절 */
        }

        .gradient-text { 
            background: linear-gradient(90deg, #facc15, #fbbf24, #f59e0b); 
            -webkit-background-clip: text; 
            -webkit-text-fill-color: transparent; 
        }

        /* 카드 가독성 개선 */
        .glass-card { 
            background: rgba(255, 255, 255, 0.05); 
            backdrop-filter: blur(20px); 
            border: 1px solid rgba(250, 204, 21, 0.2); 
            box-shadow: 0 10px 40px rgba(0,0,0,0.5); 
        }

        #particle-canvas { position: fixed; top: 0; left: 0; z-index: -1; pointer-events: none; }
    </style>
</head>
<body class="dark" onload="initAll()">
    <canvas id="particle-canvas"></canvas>

    <nav class="p-6 flex justify-between items-center border-b border-white/10 sticky top-0 z-50 bg-black/60 backdrop-blur-xl">
        <div class="flex items-center gap-3">
            <h1 class="text-2xl md:text-3xl font-bold tracking-tighter text-yellow-400 hero-font">GABOJAGO</h1>
            <span class="text-lg md:text-xl font-bold text-white/90 hero-font">가보자고</span>
        </div>
        <div class="flex items-center gap-3">
            <button onclick="copyToClipboard()" class="bg-yellow-400 text-black px-4 py-1.5 rounded-full text-xs font-bold">가치 인증</button>
            <button onclick="toggleTheme()" class="w-10 h-10 rounded-full border border-white/20 flex items-center justify-center">🌓</button>
        </div>
    </nav>

    <main class="px-4 py-8 max-w-7xl mx-auto w-full relative">
        <div id="welcome-msg" class="space-y-10 text-center py-20">
            <div class="inline-block px-4 py-1 rounded-full border border-yellow-400/30 text-yellow-400 text-xs mb-4 uppercase tracking-widest">System Online</div>
            <h2 class="hero-font text-5xl md:text-8xl mb-6 leading-[1.2] md:leading-[1.1]">
                나의 가치는<br/>매초 <span class="gradient-text">증식한다.</span>
            </h2>
            <p class="text-white/70 text-base md:text-lg max-w-2xl mx-auto mb-10 leading-relaxed">
                돈이 사람을 고르는 시대는 끝났다.<br/>
                이제 당신의 가치를 직접 증명할 시간.
            </p>
            <button onclick="activateDisplay()" class="px-10 py-5 bg-yellow-400 text-black font-extrabold rounded-2xl hover:bg-yellow-300 shadow-[0_20px_50px_rgba(250,204,21,0.3)] text-xl md:text-2xl transition-all active:scale-95 hero-font">
                엔진 가동 시작 ▲
            </button>
        </div>

        <div id="user-profile" class="hidden grid grid-cols-1 lg:grid-cols-4 gap-6">
            <div class="lg:col-span-1 space-y-6">
                <div class="glass-card p-6 rounded-[2rem] text-center">
                    <div class="w-16 h-16 mx-auto rounded-full border-2 border-yellow-400 flex items-center justify-center text-xl font-bold text-yellow-400 mb-4">A</div>
                    <h2 class="hero-font text-xl text-yellow-400 italic">ALLURE</h2>
                    <p class="text-[10px] text-gray-500 mb-6 uppercase tracking-widest">Global Asset</p>
                    <div class="space-y-2 text-left" id="ranking-list"></div>
                </div>
            </div>

            <div class="lg:col-span-2 space-y-6">
                <div class="glass-card p-8 rounded-[2.5rem] flex flex-col justify-between min-h-[450px]">
                    <div>
                        <div class="flex justify-between items-start mb-2">
                            <p class="text-gray-400 text-xs uppercase tracking-widest font-bold">실시간 자산 현황</p>
                            <span class="text-[10px] bg-green-500/20 text-green-500 px-2 py-1 rounded-md animate-pulse">● LIVE</span>
                        </div>
                        <h3 class="text-4xl md:text-6xl font-bold tracking-tighter mb-4">₩ <span id="value-counter">0</span></h3>
                    </div>
                    <div class="flex-grow"><canvas id="valueChart"></canvas></div>
                </div>
            </div>

            <div class="lg:col-span-1 space-y-6">
                <div class="glass-card p-6 rounded-[2rem] h-full flex flex-col">
                    <h3 class="hero-font text-lg text-yellow-400 mb-6 text-center">가치 DNA 분석</h3>
                    <div class="flex-grow"><canvas id="radarChart"></canvas></div>
                    <div class="mt-6 p-4 bg-white/5 rounded-2xl border border-white/10">
                        <p id="dynamic-insight" class="text-xs leading-relaxed text-gray-400">나의 실행력은 시장 평균을 상회하며, 폭발적인 자산 가치로 직결됩니다.</p>
                    </div>
                </div>
            </div>
        </div>
    </main>

    <script>
        let myValue = 1096784219;
        let myChart, radarChart, particles = [];
        const canvas = document.getElementById('particle-canvas');
        const ctx = canvas.getContext('2d');

        function initAll() {
            window.addEventListener('resize', () => { canvas.width = window.innerWidth; canvas.height = window.innerHeight; });
            initParticles();
        }

        function initParticles() {
            canvas.width = window.innerWidth; canvas.height = window.innerHeight;
            particles = Array.from({length: 80}, () => ({
                x: Math.random() * canvas.width, y: Math.random() * canvas.height,
                size: Math.random() * 1.5, speed: Math.random() * 0.4 + 0.1
            }));
            animate();
        }

        function animate() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = 'rgba(250, 204, 21, 0.4)';
            particles.forEach(p => {
                p.y -= p.speed; if(p.y < 0) p.y = canvas.height;
                ctx.beginPath(); ctx.arc(p.x, p.y, p.size, 0, Math.PI*2); ctx.fill();
            });
            requestAnimationFrame(animate);
        }

        function activateDisplay() {
            document.getElementById('welcome-msg').style.display = 'none';
            document.getElementById('user-profile').style.display = 'grid';
            initCharts();
            generateRanking();
            setInterval(() => {
                let increase = Math.floor(Math.random() * 50000) + 10000;
                myValue += increase;
                document.getElementById('value-counter').innerText = myValue.toLocaleString();
                updateChart(increase);
            }, 2000);
        }

        function generateRanking() {
            const names = [{n: "일론 머스크", q: "끈기가 아주 중요합니다."}, {n: "비탈릭 부테린", q: "혁신은 계속됩니다."}, {n: "젠슨 황", q: "더 강한 리더가 되세요."}, {n: "샘 알트만", q: "미래를 코딩하세요."}];
            document.getElementById('ranking-list').innerHTML = names.map((p, i) => `
                <div onclick="alert('${p.q}')" class="flex justify-between p-3 rounded-xl border border-transparent hover:bg-yellow-400/10 transition-all cursor-pointer">
                    <span class="text-[11px] font-bold text-yellow-500">#${i+1}</span>
                    <span class="text-[11px] font-bold text-white/80">${p.n}</span>
                    <span class="text-[9px] text-gray-500">View</span>
                </div>
            `).join('');
        }

        function initCharts() {
            myChart = new Chart(document.getElementById('valueChart'), {
                type: 'line',
                data: { labels: ['', '', '', '', '', ''], datasets: [{ data: [60, 80, 75, 90, 85, 100], borderColor: '#facc15', borderWidth: 3, tension: 0.4, fill: true, backgroundColor: 'rgba(250, 204, 21, 0.05)', pointRadius: 0 }] },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } }, scales: { x: { display: false }, y: { display: false } } }
            });

            radarChart = new Chart(document.getElementById('radarChart'), {
                type: 'radar',
                data: {
                    labels: ['창의력', '열정', '실행력', '회복탄력성', '비전'],
                    datasets: [{ data: [95, 92, 88, 80, 98], backgroundColor: 'rgba(250, 204, 21, 0.2)', borderColor: '#facc15', borderWidth: 2 }]
                },
                options: { scales: { r: { grid: { color: '#333' }, pointLabels: { color: '#aaa', font: { size: 10, weight: 'bold' } }, ticks: { display: false } } }, plugins: { legend: { display: false } } }
            });
        }

        function updateChart(val) {
            myChart.data.datasets[0].data.shift();
            myChart.data.datasets[0].data.push(100 + (val/10000));
            myChart.update();
        }

        function copyToClipboard() {
            const text = `나의 가치: ₩${myValue.toLocaleString()} | GABOJAGO`;
            navigator.clipboard.writeText(text).then(() => alert('복사 완료!'));
        }

        function toggleTheme() {
            const isDark = document.body.classList.contains('dark');
            document.body.style.backgroundColor = isDark ? '#fff' : '#000';
            document.body.style.color = isDark ? '#000' : '#fff';
            document.body.classList.toggle('dark');
        }
    </script>
</body>
</html>
