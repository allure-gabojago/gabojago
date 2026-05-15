<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>가보자고 | 프리미엄 투자 대시보드</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@100;300;400;500;700;900&family=Montserrat:wght@700;900&display=swap');
        
        :root { --primary-gold: #facc15; --bg-black: #050505; }
        
        body { 
            font-family: 'Noto Sans KR', sans-serif; 
            background-color: var(--bg-black); 
            color: #fff; 
            margin: 0; 
            -webkit-font-smoothing: antialiased;
        }

        .trial-bar { 
            background: var(--primary-gold); 
            padding: 12px 0; 
            text-align: center; 
            font-size: 14px; 
            font-weight: 900; 
            color: #000; 
            box-shadow: 0 4px 20px rgba(250, 204, 21, 0.4); 
            z-index: 100; 
            position: relative;
        }

        .brand-kor { font-size: 2.5rem; font-weight: 900; color: #fff; letter-spacing: -0.05em; line-height: 0.9; }
        .brand-eng { 
            font-family: 'Montserrat', sans-serif; 
            font-size: 0.8rem; 
            color: var(--primary-gold); 
            letter-spacing: 0.4em; 
            font-weight: 900; 
            margin-top: 4px; 
            display: flex; 
            justify-content: space-between; 
        }
        
        .glass-card { 
            background: rgba(255, 255, 255, 0.03); 
            backdrop-filter: blur(20px); 
            border: 1px solid rgba(250, 204, 21, 0.1); 
            transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            position: relative;
            overflow: hidden;
            display: flex;
            flex-direction: column;
            justify-content: center; /* 세로 중앙 정렬 */
            align-items: center;     /* 가로 중앙 정렬 */
            text-align: center;
            min-height: 180px;       /* 박스 최소 높이 고정 */
        }

        .glass-card:hover {
            transform: translateY(-10px);
            border-color: var(--primary-gold);
            background: rgba(250, 204, 21, 0.05);
            box-shadow: 0 20px 40px rgba(0,0,0,0.5);
        }
        
        /* VIP 수익률 선명하게 수정 */
        .vip-rate { 
            color: var(--primary-gold); 
            text-shadow: 0 0 15px rgba(250, 204, 21, 0.5); /* 은은한 광채 효과 */
            font-size: 2.25rem; /* 크기 최적화 */
        }

        /* VIP 전용 데이터 뱃지 키우기 */
        .premium-lock { 
            position: absolute; 
            inset: 0; 
            background: rgba(0,0,0,0.7); 
            display: flex; 
            align-items: center; 
            justify-content: center; 
            z-index: 10; 
            border-radius: 2.5rem;
        }

        .vip-badge {
            font-size: 14px; /* 글자 크기 키움 */
            font-weight: 900;
            color: #000;
            background: var(--primary-gold);
            padding: 8px 20px;
            border-radius: 999px;
            box-shadow: 0 0 20px rgba(250, 204, 21, 0.4);
        }

        @keyframes pulse-gold {
            0% { box-shadow: 0 0 0 0 rgba(250, 204, 21, 0.4); }
            70% { box-shadow: 0 0 0 12px rgba(250, 204, 21, 0); }
            100% { box-shadow: 0 0 0 0 rgba(250, 204, 21, 0); }
        }

        .btn-premium-action {
            animation: pulse-gold 2s infinite;
            background: rgba(0,0,0,0.8);
            border: 1px solid var(--primary-gold);
            color: var(--primary-gold);
            font-weight: 900;
            padding: 12px 28px;
            border-radius: 999px;
            font-size: 13px;
            transition: 0.3s;
        }

        .btn-gold { background: var(--primary-gold); color: #000; font-weight: 900; border-radius: 12px; transition: 0.3s; }
        .btn-gold:hover { transform: scale(1.05); box-shadow: 0 0 20px rgba(250, 204, 21, 0.4); }
        
        #particle-canvas { position: fixed; top: 0; left: 0; z-index: -1; pointer-events: none; }
        .num-font { font-family: 'Montserrat', sans-serif; }
    </style>
</head>
<body onload="initAll()">
    <canvas id="particle-canvas"></canvas>

    <div class="trial-bar">
        📢 현재 베이직 모드 이용 중 | 프리미엄 전환 시 모든 프로젝트의 상세 분석 리포트가 즉시 공개됩니다.
    </div>

    <nav class="p-8 flex justify-between items-center border-b border-white/5 sticky top-0 z-50 bg-black/80 backdrop-blur-md">
        <div class="brand-container flex flex-col">
            <span class="brand-kor">가보자고</span>
            <div class="brand-eng uppercase"><span>G</span><span>A</span><span>B</span><span>O</span><span>J</span><span>A</span><span>G</span><span>O</span></div>
        </div>
        <div class="flex items-center gap-8">
            <div class="text-right border-r border-white/10 pr-8">
                <p class="text-[13px] text-gray-400 font-black mb-1">나의 보유 자산</p>
                <p class="text-2xl font-black text-white italic num-font"><span id="my-wallet" class="text-yellow-400 text-3xl">0</span> 원</p>
            </div>
            <button onclick="openPayment()" class="btn-gold px-8 py-3 text-sm">VIP 멤버십 가입</button>
        </div>
    </nav>

    <main class="px-8 py-16 max-w-[1400px] mx-auto">
        <!-- 상단 요약 카드 (정렬 및 비율 수정) -->
        <section class="mb-20 grid grid-cols-1 md:grid-cols-3 gap-8">
            <!-- 1. 총 포트폴리오 가치 -->
            <div class="glass-card p-8 rounded-[2.5rem]">
                <p class="text-gray-500 text-[11px] font-black mb-3 uppercase tracking-widest">총 포트폴리오 가치</p>
                <h3 class="text-2xl lg:text-3xl font-black text-white italic num-font break-all px-2">
                    48,450,000,000 <span class="text-xs font-normal not-italic text-gray-600 ml-1">KRW</span>
                </h3>
            </div>
            
            <!-- 2. VIP 전용 예상 수익률 -->
            <div class="glass-card p-8 rounded-[2.5rem] relative">
                <p class="text-gray-500 text-[11px] font-black mb-3 uppercase tracking-widest">VIP 전용 예상 수익률</p>
                <h3 class="vip-rate font-black italic num-font">+24.8%</h3>
                <div class="premium-lock">
                    <span class="vip-badge">VIP 전용 데이터</span>
                </div>
            </div>
            
            <!-- 3. 운용 중인 투자 엔진 -->
            <div class="glass-card p-8 rounded-[2.5rem]">
                <p class="text-gray-500 text-[11px] font-black mb-3 uppercase tracking-widest">운용 중인 투자 엔진</p>
                <h3 class="text-3xl font-black text-white italic">
                    12 <span class="text-sm font-normal not-italic text-gray-600 ml-1">개 분야</span>
                </h3>
            </div>
        </section>

        <!-- 프로젝트 리스트 -->
        <div id="project-container" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6"></div>
    </main>

    <script>
        const projects = [
            { name: "사이버 보안 엔진", sub: "Security", fund: 4200000000, tag: "필수 자산", isPremium: false },
            { name: "인공지능 퀀트", sub: "AI Quant", fund: 7800000000, tag: "고수익군", isPremium: false },
            { name: "반도체 클러스터", sub: "Semi-Conductor", fund: 8900000000, tag: "프리미엄", isPremium: true },
            { name: "디지털 헬스케어", sub: "Bio Tech", fund: 5200000000, tag: "프리미엄", isPremium: true },
            { name: "우주 물류 시스템", sub: "Space X", fund: 3500000000, tag: "프리미엄", isPremium: true },
            { name: "차세대 2차 전지", sub: "Energy", fund: 3100000000, tag: "필수 자산", isPremium: false }
        ];

        function initAll() { 
            renderProjects(); 
            initParticles(); 
            animateValue("my-wallet", 0, 500000000, 2000);
        }

        function animateValue(id, start, end, duration) {
            const obj = document.getElementById(id);
            let startTimestamp = null;
            const step = (timestamp) => {
                if (!startTimestamp) startTimestamp = timestamp;
                const progress = Math.min((timestamp - startTimestamp) / duration, 1);
                obj.innerText = Math.floor(progress * (end - start) + start).toLocaleString();
                if (progress < 1) window.requestAnimationFrame(step);
            };
            window.requestAnimationFrame(step);
        }

        function renderProjects() {
            const container = document.getElementById('project-container');
            container.innerHTML = projects.map((p, i) => `
                <div class="glass-card p-8 rounded-[2.5rem] !justify-between !items-stretch text-left">
                    ${p.isPremium ? `
                    <div class="premium-lock">
                        <button onclick="openPayment()" class="btn-premium-action">분석 리포트 보기</button>
                    </div>` : ''}
                    <div class="${p.isPremium ? 'opacity-20 blur-sm' : ''} h-full flex flex-col justify-between">
                        <div>
                            <span class="px-2 py-1 bg-yellow-400/10 text-yellow-400 text-[9px] font-black rounded uppercase">${p.tag}</span>
                            <h4 class="text-xl font-black mt-4 mb-2 leading-tight">${p.name}</h4>
                            <p class="text-white/20 font-light text-[10px] italic uppercase tracking-widest mb-6">${p.sub}</p>
                        </div>
                        <div class="flex justify-between items-end mt-auto">
                            <p class="text-sm font-black text-white num-font">${p.fund.toLocaleString()} 원</p>
                            <button class="px-5 py-2 bg-white/5 border border-white/10 rounded-xl text-[10px] font-bold hover:bg-yellow-400 hover:text-black transition-all uppercase">거래</button>
                        </div>
                    </div>
                </div>`).join('');
        }

        function openPayment() { alert('VIP 멤버십 가입 페이지로 연결됩니다.'); }

        function initParticles() {
            const canvas = document.getElementById('particle-canvas'); 
            const ctx = canvas.getContext('2d');
            canvas.width = window.innerWidth; canvas.height = window.innerHeight;
            let p = Array.from({length: 40}, () => ({ 
                x: Math.random() * canvas.width, 
                y: Math.random() * canvas.height, 
                s: Math.random() * 1.5, 
                v: Math.random() * 0.2 
            }));
            function draw() { 
                ctx.clearRect(0,0,canvas.width, canvas.height); 
                ctx.fillStyle = 'rgba(250,204,21,0.15)'; 
                p.forEach(i => { 
                    i.y -= i.v; if(i.y < 0) i.y = canvas.height; 
                    ctx.beginPath(); ctx.arc(i.x, i.y, i.s, 0, Math.PI*2); ctx.fill(); 
                }); 
                requestAnimationFrame(draw); 
            }
            draw();
        }
    </script>
</body>
</html>
