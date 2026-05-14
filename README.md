<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GABOJAGO V3 | 실시간 가치 부스팅</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;700;900&display=swap');
        body { font-family: 'Noto Sans KR', sans-serif; background-color: #000; color: #fff; margin: 0; -webkit-font-smoothing: antialiased; overflow-x: hidden; }
        .hero-font { font-family: 'Noto Sans KR', sans-serif; font-weight: 900; letter-spacing: -0.05em; line-height: 1.2; }
        .gradient-text { background: linear-gradient(90deg, #facc15, #fbbf24, #f59e0b); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .glass-card { background: rgba(255, 255, 255, 0.05); backdrop-filter: blur(20px); border: 1px solid rgba(250, 204, 21, 0.2); transition: all 0.3s ease; }
        .boost-btn { transition: all 0.2s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
        .boost-btn:active { transform: scale(0.9); }
        #particle-canvas { position: fixed; top: 0; left: 0; z-index: -1; pointer-events: none; }
        @keyframes flash { 0% { background: rgba(250, 204, 21, 0.5); } 100% { background: transparent; } }
        .flash-effect { animation: flash 0.5s ease-out; }
    </style>
</head>
<body onload="initAll()">
    <canvas id="particle-canvas"></canvas>

    <nav class="p-6 flex justify-between items-center border-b border-white/10 sticky top-0 z-50 bg-black/80 backdrop-blur-xl">
        <div class="flex items-center gap-3">
            <h1 class="text-2xl font-black tracking-tighter text-yellow-400">GABOJAGO</h1>
            <span class="text-lg font-bold text-white/90">가보자고 V3</span>
        </div>
        <div class="flex gap-2">
            <button onclick="resetValue()" class="text-[10px] text-gray-500 hover:text-red-500 underline">데이터 초기화</button>
            <button onclick="copyToClipboard()" class="bg-yellow-400 text-black px-4 py-1.5 rounded-full text-xs font-bold">가치 인증</button>
        </div>
    </nav>

    <main class="px-4 py-8 max-w-7xl mx-auto w-full relative">
        <div id="welcome-msg" class="space-y-10 text-center py-20">
            <h2 class="hero-font text-5xl md:text-8xl mb-8">
                나의 가치는 매초<br/><span class="gradient-text">기록되며 증식한다.</span>
            </h2>
            <button onclick="activateDisplay()" class="px-12 py-5 bg-yellow-400 text-black rounded-2xl font-black text-xl md:text-2xl shadow-[0_20px_50px_rgba(250,204,21,0.3)]">3단계 엔진 가동 ▲</button>
        </div>

        <div id="user-profile" class="hidden grid grid-cols-1 lg:grid-cols-4 gap-6">
            <div class="lg:col-span-1 space-y-4">
                <div class="glass-card p-6 rounded-[2rem]">
                    <h3 class="text-yellow-400 font-bold mb-4 flex items-center gap-2">🚀 가치 부스팅 미션</h3>
                    <div class="grid grid-cols-1 gap-3">
                        <button onclick="boostValue(50000000, '운동 완료')" class="boost-btn w-full py-4 bg-white/5 border border-white/10 rounded-xl text-sm font-bold hover:bg-yellow-400 hover:text-black">🏋️ 체력 단련 (+5천만)</button>
                        <button onclick="boostValue(100000000, '코딩/공부 완료')" class="boost-btn w-full py-4 bg-white/5 border border-white/10 rounded-xl text-sm font-bold hover:bg-yellow-400 hover:text-black">💻 지식 습득 (+1억)</button>
                        <button onclick="boostValue(30000000, '독서 완료')" class="boost-btn w-full py-4 bg-white/5 border border-white/10 rounded-xl text-sm font-bold hover:bg-yellow-400 hover:text-black">📚 영감 충전 (+3천만)</button>
                    </div>
                </div>
                <div class="glass-card p-5 rounded-2xl bg-yellow-400/5">
                    <p class="text-[11px] text-yellow-500 font-bold mb-1">AI 리얼타임 분석</p>
                    <p id="ai-status" class="text-xs text-gray-400 leading-relaxed">미션을 수행하여 가치를 증명하세요.</p>
                </div>
            </div>

            <div class="lg:col-span-2">
                <div id="main-counter-card" class="glass-card p-8 rounded-[2.5rem] flex flex-col justify-between min-h-[500px]">
                    <div>
                        <p class="text-gray-400 text-xs uppercase tracking-widest font-bold mb-2">누적 자산 가치</p>
                        <h3 class="text-5xl md:text-7xl font-black tracking-tighter mb-4">₩ <span id="value-counter">0</span></h3>
                    </div>
                    <div class="flex-grow"><canvas id="valueChart"></canvas></div>
                </div>
            </div>

            <div class="lg:col-span-1">
                <div class="glass-card p-6 rounded-[2rem] h-full">
                    <h3 class="font-bold text-yellow-400 mb-6 text-center italic">가치 DNA 레이더</h3>
                    <canvas id="radarChart"></canvas>
                </div>
            </div>
        </div>
    </main>

    <script>
        // 데이터 불러오기 (Local Storage)
        let myValue = parseInt(localStorage.getItem('gabojago_value')) || 1096784219;
        let myChart, radarChart, particles = [];
        const canvas = document.getElementById('particle-canvas');
        const ctx = canvas.getContext('2d');

        function initAll() {
            window.addEventListener('resize', () => { canvas.width = window.innerWidth; canvas.height = window.innerHeight; });
            initParticles();
        }

        function initParticles() {
            canvas.width = window.innerWidth; canvas.height = window.innerHeight;
            particles = Array.from({length: 60}, () => ({
                x: Math.random() * canvas.width, y: Math.random() * canvas.height,
                size: Math.random() * 2, speed: Math.random() * 0.5 + 0.1
            }));
            animate();
        }

        function animate() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = 'rgba(250, 204, 21, 0.3)';
            particles.forEach(p => {
                p.y -= p.speed; if(p.y < 0) p.y = canvas.height;
                ctx.beginPath(); ctx.arc(p.x, p.y, p.size, 0, Math.PI*2); ctx.fill();
            });
            requestAnimationFrame(animate);
        }

        function activateDisplay() {
            document.getElementById('welcome-msg').classList.add('hidden');
            document.getElementById('user-profile').classList.remove('hidden');
            document.getElementById('user-profile').classList.add('grid');
            document.getElementById('value-counter').innerText = myValue.toLocaleString();
            initCharts();
            
            // 자동 증식 엔진 (2초마다 실행)
            setInterval(() => {
                let gain = Math.floor(Math.random() * 50000) + 10000;
                updateValue(gain);
            }, 2000);
        }

        function updateValue(amount) {
            myValue += amount;
            document.getElementById('value-counter').innerText = myValue.toLocaleString();
            localStorage.setItem('gabojago_value', myValue); // 데이터 저장
            
            if(myChart) {
                myChart.data.datasets[0].data.shift();
                myChart.data.datasets[0].data.push(100 + (amount/1000));
                myChart.update('none');
            }
        }

        function boostValue(amount, type) {
            updateValue(amount);
            document.getElementById('ai-status').innerText = `[${type}] 수행 완료! 가치가 폭발적으로 상승했습니다.`;
            
            // 시각적 피드백
            const card = document.getElementById('main-counter-card');
            card.classList.add('flash-effect');
            setTimeout(() => card.classList.remove('flash-effect'), 500);
        }

        function resetValue() {
            if(confirm("모든 가치 데이터를 초기화하시겠습니까?")) {
                localStorage.removeItem('gabojago_value');
                location.reload();
            }
        }

        function initCharts() {
            myChart = new Chart(document.getElementById('valueChart'), {
                type: 'line',
                data: { labels: ['', '', '', '', '', ''], datasets: [{ data: [80, 85, 90, 85, 95, 100], borderColor: '#facc15', borderWidth: 4, tension: 0.4, fill: true, backgroundColor: 'rgba(250, 204, 21, 0.05)', pointRadius: 0 }] },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } }, scales: { x: { display: false }, y: { display: false } } }
            });

            radarChart = new Chart(document.getElementById('radarChart'), {
                type: 'radar',
                data: {
                    labels: ['창의력', '열정', '실행력', '회복탄력성', '비전'],
                    datasets: [{ data: [95, 92, 88, 80, 98], backgroundColor: 'rgba(250, 204, 21, 0.2)', borderColor: '#facc15', borderWidth: 2 }]
                },
                options: { scales: { r: { grid: { color: '#333' }, pointLabels: { color: '#aaa', font: { size: 11, weight: 'bold' } }, ticks: { display: false } } }, plugins: { legend: { display: false } } }
            });
        }

        function copyToClipboard() {
            const text = `나의 현재 가치: ₩${myValue.toLocaleString()} | 가보자고 V3 엔진`;
            navigator.clipboard.writeText(text).then(() => alert('가치 인증 완료!'));
        }
    </script>
</body>
</html>
