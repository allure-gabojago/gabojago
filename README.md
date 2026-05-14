[index.html](https://github.com/user-attachments/files/27754354/index.html)
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>가보자고 | GABOJAGO VALUE ENGINE</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Black+Han+Sans&family=Noto+Sans+KR:wght@400;700&display=swap');
        body { font-family: 'Noto Sans KR', sans-serif; transition: all 0.5s ease; overflow-x: hidden; margin: 0; background-color: #000; color: #fff; }
        .hero-font { font-family: 'Black Han Sans', sans-serif; }
        .gradient-text { background: linear-gradient(90deg, #facc15, #fbbf24, #f59e0b); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .glass-card { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(20px); border: 1px solid rgba(250, 204, 21, 0.15); box-shadow: 0 10px 40px rgba(0,0,0,0.5); transition: transform 0.3s ease; }
        .glass-card:hover { transform: translateY(-5px); border-color: rgba(250, 204, 21, 0.4); }
        #particle-canvas { position: fixed; top: 0; left: 0; z-index: -1; pointer-events: none; }
    </style>
</head>
<body class="dark" onload="initAll()">
    <canvas id="particle-canvas"></canvas>

    <nav class="p-6 flex justify-between items-center border-b border-white/10 sticky top-0 z-50 bg-black/60 backdrop-blur-xl">
        <div class="flex items-baseline gap-2">
            <h1 class="text-3xl font-bold tracking-tighter text-yellow-400 hero-font">GABOJAGO</h1>
            <span class="text-xl font-bold text-white/80 hero-font">가보자고</span>
        </div>
        <div class="flex items-center gap-3">
            <button onclick="copyToClipboard()" class="bg-yellow-400 text-black px-4 py-1.5 rounded-full text-xs font-bold hover:scale-105 transition-transform">가치 인증하기</button>
            <button onclick="toggleTheme()" class="w-10 h-10 rounded-full border border-white/20 flex items-center justify-center hover:bg-white/10">🌓</button>
        </div>
    </nav>

    <main class="px-4 py-8 max-w-7xl mx-auto w-full relative">
        <div id="welcome-msg" class="space-y-8 text-center py-20">
            <div class="inline-block px-4 py-1 rounded-full border border-yellow-400/30 text-yellow-400 text-xs mb-4 uppercase tracking-widest">System Stable: Ready to Expand</div>
            <h2 class="hero-font text-6xl md:text-9xl mb-6 leading-tight">
                나의 가치는<br/>매초 <span class="gradient-text">증식한다.</span>
            </h2>
            <p class="text-white/60 text-lg md:text-xl max-w-2xl mx-auto mb-10">돈이 사람을 고르는 시대는 끝났다. 이제 당신의 가치를 직접 증명할 시간.</p>
            <button onclick="activateDisplay()" class="px-14 py-6 bg-yellow-400 text-black font-extrabold rounded-2xl hover:bg-yellow-300 shadow-[0_20px_50px_rgba(250,204,21,0.3)] text-2xl transition-all active:scale-95 hero-font">엔진 가동 시작 ▲</button>
        </div>

        <div id="user-profile" class="hidden grid grid-cols-1 lg:grid-cols-4 gap-6">
            <div class="lg:col-span-1 space-y-6">
                <div class="glass-card p-6 rounded-[2rem] text-center relative overflow-hidden">
                    <div class="absolute top-0 left-0 w-full h-1 bg-gradient-to-r from-transparent via-yellow-400 to-transparent"></div>
                    <div class="w-20 h-20 mx-auto rounded-full border-2 border-yellow-400 flex items-center justify-center text-2xl font-bold text-yellow-400 mb-4">A</div>
                    <h2 class="hero-font text-2xl text-yellow-400 italic">ALLURE</h2>
                    <p class="text-[10px] text-gray-500 mb-6 uppercase tracking-[0.2em]">글로벌 클래스 인적 자산</p>
                    <div class="space-y-2 text-left" id="ranking-list"></div>
                    <p class="text-[9px] text-gray-600 mt-4">* 리더의 이름을 클릭하여 철학을 확인하세요</p>
                </div>
            </div>

            <div class="lg:col-span-2 space-y-6">
                <div class="glass-card p-8 rounded-[2.5rem] flex flex-col justify-between min-h-[500px]">
                    <div>
                        <div class="flex justify-between items-start mb-2">
                            <p class="text-gray-400 text-xs uppercase tracking-widest">실시간 자산 누적 현황</p>
                            <span class="text-[10px] bg-green-500/20 text-green-500 px-2 py-1 rounded-md animate-pulse font-bold">● LIVE INCREASING</span>
                        </div>
                        <h3 class="text-5xl md:text-7xl font-bold tracking-tighter mb-4">₩ <span id="value-counter">0</span></h3>
                    </div>
                    <div class="flex-grow"><canvas id="valueChart"></canvas></div>
                </div>
            </div>

            <div class="lg:col-span-1 space-y-6">
                <div class="glass-card p-6 rounded-[2rem] h-full flex flex-col">
                    <h3 class="hero-font text-xl text-yellow-400 mb-6 text-center italic">가치 DNA 분석</h3>
                    <div class="flex-grow"><canvas id="radarChart"></canvas></div>
                    <div class="mt-8 p-5 bg-white/5 rounded-2xl border border-white/10">
                        <p class="text-[10px] text-yellow-500 font-bold mb-2 tracking-tighter">AI 인사이트</p>
                        <p id="dynamic-insight" class="text-xs leading-relaxed text-gray-400">"귀하의 실행력은 시장 평균의 14배를 상회하며, 이는 곧 폭발적인 자산 가치로 직결됩니다."</p>
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
            const names = [
                {n: "일론 머스크", q: "끈기가 아주 중요합니다. 포기하도록 강요받지 않는 한, 결코 포기하지 마세요."},
                {n: "비탈릭 부테린", q: "중앙집중화된 모든 것은 결국 혁신의 대상이 됩니다."},
                {n: "젠슨 황", q: "고통과 고난은 당신을 더 강한 리더로 만듭니다."},
                {n: "샘 알트만", q: "미래는 기다리는 것이 아니라 우리가 직접 코딩하는 것입니다."}
            ];
            document.getElementById('ranking-list').innerHTML = names.map((p, i) => `
                <div onclick="alert('${p.q}')" class="group cursor-pointer flex justify-between p-3 rounded-xl hover:bg-yellow-400/10 border border-transparent hover:border-yellow-400/20 transition-all">
                    <span class="text-[11px] font-bold text-yellow-500 font-mono">#${i+1}</span>
                    <span class="text-[11px] font-bold group-hover:text-yellow-400">${p.n}</span>
                    <span class="text-[9px] text-gray-500 uppercase">View</span>
                </div>
            `).join('');
        }

        function initCharts() {
            myChart = new Chart(document.getElementById('valueChart'), {
                type: 'line',
                data: { labels: ['', '', '', '', '', ''], datasets: [{ data: [60, 80, 75, 90, 85, 100], borderColor: '#facc15', borderWidth: 4, tension: 0.4, fill: true, backgroundColor: 'rgba(250, 204, 21, 0.05)', pointRadius: 0 }] },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } }, scales: { x: { display: false }, y: { display: false } } }
            });

            radarChart = new Chart(document.getElementById('radarChart'), {
                type: 'radar',
                data: {
                    labels: ['창의력', '열정', '실행력', '회복탄력성', '비전'],
                    datasets: [{ data: [95, 92, 88, 80, 98], backgroundColor: 'rgba(250, 204, 21, 0.2)', borderColor: '#facc15', borderWidth: 2 }]
                },
                options: { scales: { r: { grid: { color: '#333' }, pointLabels: { color: '#888', font: { size: 11, weight: 'bold' } }, ticks: { display: false } } }, plugins: { legend: { display: false } } }
            });
        }

        function updateChart(val) {
            myChart.data.datasets[0].data.shift();
            myChart.data.datasets[0].data.push(100 + (val/10000));
            myChart.update();
        }

        function copyToClipboard() {
            const text = `현재 ALLURE님의 실시간 가치: ₩${myValue.toLocaleString()} | 가보자고(GABOJAGO)에서 확인하세요!`;
            navigator.clipboard.writeText(text).then(() => alert('가치 인증 문구가 복사되었습니다! SNS에 공유해보세요.'));
        }

        function toggleTheme() {
            const isDark = document.body.classList.contains('dark');
            document.body.style.backgroundColor = isDark ? '#f8f9fa' : '#000';
            document.body.style.color = isDark ? '#1a1a1a' : '#fff';
            document.body.classList.toggle('dark');
        }
    </script>
</body>
</html>
