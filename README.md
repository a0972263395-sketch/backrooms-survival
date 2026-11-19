<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>LEVEL-0 交易終端 | 後室物資價值評估與玩家討論系統</title>
    <!-- 引入 Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- 引入 Google Fonts: Share Tech Mono, Noto Sans TC & Inter -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700;800&family=Noto+Sans+TC:wght@400;500;700;900&family=Share+Tech+Mono&display=swap" rel="stylesheet">
    <!-- 引入 Font Awesome 圖示 -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        body {
            font-family: 'Noto Sans TC', 'Inter', sans-serif;
            background-color: #16140b;
            color: #e6dfb8;
            background-image: 
                radial-gradient(circle at 50% 0%, rgba(235, 224, 152, 0.12) 0%, rgba(18, 16, 8, 0.92) 85%),
                repeating-linear-gradient(
                    90deg,
                    rgba(184, 168, 94, 0.04) 0px,
                    rgba(184, 168, 94, 0.04) 16px,
                    rgba(138, 124, 62, 0.07) 16px,
                    rgba(138, 124, 62, 0.07) 32px
                );
            background-attachment: fixed;
        }
        .font-mono-tech {
            font-family: 'Share Tech Mono', 'Noto Sans TC', monospace;
        }
        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: #14120a; border-radius: 4px; }
        ::-webkit-scrollbar-thumb { background: #574e2a; border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: #7d703d; }
        .backrooms-panel {
            background: rgba(26, 23, 13, 0.88);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(212, 196, 118, 0.25);
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.65), inset 0 1px 0 rgba(255, 248, 196, 0.08);
        }
        .scanlines::before {
            content: " ";
            display: block;
            position: fixed;
            top: 0; left: 0; bottom: 0; right: 0;
            background: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.18) 50%);
            background-size: 100% 4px;
            z-index: 40;
            pointer-events: none;
        }
        @keyframes fluorescent-flicker {
            0%, 19%, 21%, 23%, 25%, 54%, 56%, 100% {
                opacity: 1;
                text-shadow: 0 0 10px rgba(245, 233, 154, 0.6);
            }
            20%, 24%, 55% { opacity: 0.65; text-shadow: none; }
        }
        .flicker-text { animation: fluorescent-flicker 7s infinite; }
        .glow-win { box-shadow: 0 0 35px rgba(74, 222, 128, 0.25), inset 0 0 15px rgba(74, 222, 128, 0.12); }
        .glow-loss { box-shadow: 0 0 35px rgba(248, 113, 113, 0.25), inset 0 0 15px rgba(248, 113, 113, 0.12); }
        .glow-even { box-shadow: 0 0 35px rgba(234, 179, 8, 0.25), inset 0 0 15px rgba(234, 179, 8, 0.12); }
        input[type=number]::-webkit-inner-spin-button, 
        input[type=number]::-webkit-outer-spin-button { -webkit-appearance: none; margin: 0; }
        input[type=number] { -moz-appearance: textfield; }

        /* 手機／全螢幕聊天放大模式 */
        body.chat-fullscreen-active header,
        body.chat-fullscreen-active footer {
            display: none !important;
        }
        body.chat-fullscreen-active main {
            max-width: 100% !important;
            padding: 0 !important;
            margin: 0 !important;
        }
        #chat-panel-box.is-fullscreen {
            position: fixed;
            inset: 0;
            z-index: 45;
            height: 100dvh;
            min-height: 100dvh;
            border-radius: 0 !important;
            border: none !important;
        }
        #chat-panel-box.is-fullscreen #chat-header-info {
            display: none;
        }
        #chat-panel-box.is-fullscreen #chat-top-bar {
            padding: 0.65rem 0.9rem;
            justify-content: space-between;
        }

        /* 管理員彩虹流光特效 */
        @keyframes rainbow-flow {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }
        .admin-rainbow-text {
            background: linear-gradient(90deg, #ff595e, #ffca3a, #8ac926, #1982c4, #c77dff, #ff595e);
            background-size: 300% 300%;
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            animation: rainbow-flow 3.5s linear infinite;
        }
        .admin-rainbow-bubble {
            position: relative;
            background: linear-gradient(135deg, rgba(46, 26, 54, 0.92), rgba(22, 34, 48, 0.94), rgba(43, 35, 16, 0.92));
            border: 2px solid transparent;
            background-clip: padding-box;
            box-shadow: 0 0 18px rgba(199, 125, 255, 0.35), 0 0 8px rgba(255, 202, 58, 0.25);
        }
        .admin-rainbow-bubble::before {
            content: "";
            position: absolute;
            inset: -2px;
            border-radius: inherit;
            padding: 2px;
            background: linear-gradient(90deg, #ff595e, #ffca3a, #8ac926, #1982c4, #c77dff, #ff595e);
            background-size: 300% 300%;
            animation: rainbow-flow 3.5s linear infinite;
            -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
            -webkit-mask-composite: xor;
            mask-composite: exclude;
            pointer-events: none;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between selection:bg-amber-500 selection:text-stone-950 scanlines">

    <!-- 頂端新訊息即時廣播橫幅 -->
    <div id="top-msg-banner" onclick="handleTopBannerClick()" class="fixed top-3 left-1/2 -translate-x-1/2 z-[60] w-[92%] max-w-md cursor-pointer transition-all duration-300 -translate-y-28 opacity-0 pointer-events-none">
        <div class="bg-[#1c180c]/95 backdrop-blur-xl border border-amber-400/80 rounded-2xl px-4 py-3 shadow-[0_10px_35px_rgba(0,0,0,0.85)] flex items-center gap-3">
            <img id="top-banner-avatar" src="" alt="頭像" class="w-9 h-9 rounded-full object-cover border border-amber-400 shrink-0 bg-[#2c2714]">
            <div class="min-w-0 flex-grow">
                <div class="flex items-center justify-between gap-2">
                    <span id="top-banner-nick" class="text-xs font-black text-amber-300 truncate">新訊息</span>
                    <span class="text-[10px] font-mono-tech text-emerald-400 shrink-0">點擊查看 <i class="fa-solid fa-arrow-right"></i></span>
                </div>
                <p id="top-banner-text" class="text-xs text-[#faf2c3] truncate mt-0.5">...</p>
            </div>
        </div>
    </div>

    <!-- 頂部後室螢光燈管導航列 -->
    <header class="bg-[#1b180d]/95 backdrop-blur-xl border-b border-[#5e542c]/60 sticky top-0 z-30 shadow-2xl">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4 flex flex-col sm:flex-row items-center justify-between gap-4">
            <div class="flex items-center gap-3.5">
                <div class="w-11 h-11 rounded-xl bg-[#2c2714] border border-[#a89852]/60 flex items-center justify-center shadow-[0_0_15px_rgba(235,220,130,0.2)]">
                    <i class="fa-solid fa-radiation text-[#f2e394] text-xl flicker-text"></i>
                </div>
                <div>
                    <div class="flex items-center gap-2">
                        <span class="text-[10px] font-mono-tech px-2 py-0.5 rounded bg-[#3a3319] text-[#f2e394] border border-[#7d7038]">M.E.G. 終端 // LEVEL 0</span>
                        <span class="text-[10px] font-mono-tech text-[#9c9163] hidden sm:inline">HUM-60HZ</span>
                    </div>
                    <h1 class="text-xl sm:text-2xl font-black tracking-wide text-[#f5eab0] mt-0.5 flicker-text">
                        後室物資交易評估系統
                    </h1>
                </div>
            </div>

            <!-- 功能分頁導航 & 估價基準切換器 -->
            <div class="flex flex-wrap items-center gap-2.5 w-full sm:w-auto justify-between sm:justify-end">
                <div class="bg-[#121008] p-1 rounded-xl border border-[#524926] flex items-center text-xs sm:text-sm font-bold shadow-inner">
                    <button type="button" onclick="switchTab('calculator')" id="tab-calculator" class="px-3.5 py-1.5 rounded-lg transition-all bg-[#8c7b38] text-[#141208] shadow-md font-black flex items-center gap-1.5">
                        <i class="fa-solid fa-scale-balanced"></i>
                        <span>交易評估</span>
                    </button>
                    <button type="button" onclick="switchTab('chat')" id="tab-chat" class="px-3.5 py-1.5 rounded-lg transition-all text-[#9c9163] hover:text-[#f5eab0] flex items-center gap-1.5 relative">
                        <i class="fa-solid fa-comments text-amber-400"></i>
                        <span>玩家討論</span>
                        <span id="unread-chat-badge" class="hidden ml-0.5 px-1.5 py-0.2 rounded-full bg-emerald-500 text-stone-950 text-[10px] font-mono-tech font-black">NEW</span>
                    </button>
                </div>

                <div id="valuation-controls" class="flex items-center gap-2">
                    <div class="bg-[#121008] p-1 rounded-xl border border-[#524926] flex items-center text-xs font-bold shadow-inner">
                        <button type="button" onclick="setValuationMode('min')" id="mode-min" class="px-2.5 py-1.5 rounded-lg transition-all text-[#9c9163] hover:text-[#f5eab0]">低標</button>
                        <button type="button" onclick="setValuationMode('avg')" id="mode-avg" class="px-2.5 py-1.5 rounded-lg transition-all bg-[#8c7b38] text-[#141208] shadow-md font-black">市價</button>
                        <button type="button" onclick="setValuationMode('max')" id="mode-max" class="px-2.5 py-1.5 rounded-lg transition-all text-[#9c9163] hover:text-[#f5eab0]">高標</button>
                    </div>

                    <button type="button" onclick="resetAll()" title="清除終端" class="px-3 py-2 rounded-xl bg-red-950/60 hover:bg-red-900/70 text-red-300 border border-red-700/50 text-xs font-bold transition-all flex items-center gap-1.5 active:scale-95">
                        <i class="fa-solid fa-rotate-right"></i>
                        <span class="hidden md:inline">清除</span>
                    </button>
                </div>
            </div>
        </div>
    </header>

    <!-- 主要內容區 -->
    <main class="max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-6 flex-grow">

        <!-- ==================== 分頁 1：交易評估系統 ==================== -->
        <div id="view-calculator" class="space-y-6">
            <section class="backrooms-panel rounded-2xl p-5 sm:p-6 transition-all duration-300" id="verdict-panel">
                <div class="grid grid-cols-1 md:grid-cols-12 gap-4 items-stretch">
                    <div class="md:col-span-4 bg-[#121008]/90 rounded-xl p-4 sm:p-5 border border-[#5c522b] shadow-inner flex flex-col justify-between">
                        <div class="flex items-center justify-between text-xs sm:text-sm mb-2">
                            <span class="font-bold flex items-center gap-2 text-[#f2e394]">
                                <span class="w-2.5 h-2.5 rounded-sm bg-[#f2e394] shadow-[0_0_8px_#f2e394]"></span>
                                我方付出總價值
                            </span>
                            <span id="my-item-count" class="text-xs font-mono-tech bg-[#2b2613] border border-[#574d28] px-2.5 py-0.5 rounded text-[#f2e394]">0 件物資</span>
                        </div>
                        <div class="text-3xl sm:text-4xl font-mono-tech font-bold text-[#faf2c3] tracking-tight my-1.5" id="my-total-display">0</div>
                        <div class="text-xs text-[#9c9163] flex justify-between pt-2 border-t border-[#363018]" id="my-range-display">
                            <span>區間: 0 ~ 0</span>
                            <span id="my-points-sub" class="text-[#f2e394] font-bold">含積分: 0</span>
                        </div>
                    </div>

                    <div class="md:col-span-4 flex flex-col items-center justify-center text-center py-4 px-5 rounded-xl bg-[#0f0d06]/95 border-2 border-[#4d4423] shadow-2xl relative overflow-hidden transition-all duration-300" id="verdict-box">
                        <div id="verdict-badge" class="px-3.5 py-1 rounded text-xs font-black tracking-wider mb-2 bg-[#2b2613] text-[#c7ba81] border border-[#574d28]">
                            [ 系統待機：請輸入交易物資 ]
                        </div>
                        <div id="verdict-percent" class="text-4xl sm:text-5xl font-mono-tech font-bold tracking-tight text-[#d6c67c] my-1">0.0%</div>
                        <div id="verdict-diff" class="text-xs sm:text-sm font-bold text-[#b8ab72] mt-1.5 bg-[#1c180b] px-3.5 py-1 rounded border border-[#453d1f]">淨差額: 0</div>
                    </div>

                    <div class="md:col-span-4 bg-[#121008]/90 rounded-xl p-4 sm:p-5 border border-[#5c522b] shadow-inner flex flex-col justify-between">
                        <div class="flex items-center justify-between text-xs sm:text-sm mb-2">
                            <span class="font-bold flex items-center gap-2 text-[#6ee7b7]">
                                <span class="w-2.5 h-2.5 rounded-sm bg-[#34d399] shadow-[0_0_8px_#34d399]"></span>
                                對方拿出總價值
                            </span>
                            <span id="their-item-count" class="text-xs font-mono-tech bg-[#13261e] border border-[#2a5945] px-2.5 py-0.5 rounded text-[#6ee7b7]">0 件物資</span>
                        </div>
                        <div class="text-3xl sm:text-4xl font-mono-tech font-bold text-[#faf2c3] tracking-tight my-1.5" id="their-total-display">0</div>
                        <div class="text-xs text-[#9c9163] flex justify-between pt-2 border-t border-[#363018]" id="their-range-display">
                            <span>區間: 0 ~ 0</span>
                            <span id="their-points-sub" class="text-[#6ee7b7] font-bold">含積分: 0</span>
                        </div>
                    </div>
                </div>
            </section>

            <!-- 橫向並排交易配置區 -->
            <div class="overflow-x-auto pb-2">
                <div class="grid grid-cols-2 gap-4 sm:gap-6 min-w-[680px]">
                    <!-- 左欄：我方交易欄位 -->
                    <div class="backrooms-panel rounded-2xl border border-[#8c7b38]/60 flex flex-col overflow-hidden">
                        <div class="bg-gradient-to-r from-[#2e2812] via-[#211d0d] to-[#1a170b] px-5 py-4 border-b border-[#594e25] flex items-center justify-between">
                            <div class="flex items-center gap-3">
                                <div class="w-9 h-9 rounded-lg bg-[#3d3518] text-[#f2e394] flex items-center justify-center border border-[#857537]">
                                    <i class="fa-solid fa-hand-holding-dollar text-base"></i>
                                </div>
                                <div>
                                    <h2 class="text-base sm:text-lg font-black text-[#faf2c3]">我方付出 (YOUR OFFER)</h2>
                                    <p class="text-[11px] text-[#a39765]">流浪者準備出讓的武器與積分</p>
                                </div>
                            </div>
                            <button type="button" onclick="clearSide('my')" class="text-xs font-bold text-[#9c9163] hover:text-red-400 transition px-2 py-1 rounded hover:bg-red-950/30">清空此欄</button>
                        </div>

                        <div class="p-4 sm:p-5 space-y-5 flex-grow flex flex-col">
                            <div class="bg-[#121008]/90 p-3.5 rounded-xl border border-[#4d4423]">
                                <label class="block text-xs font-bold text-[#d6c67c] mb-2 flex items-center justify-between">
                                    <span><i class="fa-solid fa-coins text-amber-400 mr-1.5"></i>我方附加積分 (支持 k / m 簡寫)</span>
                                    <span id="my-points-formatted" class="font-mono-tech text-[#f2e394]">0</span>
                                </label>
                                <div class="flex gap-2">
                                    <input type="text" id="my-points-input" oninput="handlePointsInput('my', this.value)" placeholder="例如: 5m 或 800k..." 
                                        class="w-full bg-[#0a0904] border border-[#594e25] focus:border-[#d9c462] rounded-lg px-3 py-2 text-sm font-mono-tech text-[#faf2c3] placeholder-[#5e5534] outline-none transition">
                                </div>
                                <div class="flex flex-wrap gap-1.5 mt-2.5">
                                    <button type="button" onclick="addQuickPoints('my', 100000)" class="px-2 py-1 rounded bg-[#241f0f] hover:bg-[#383018] text-[#d6c67c] border border-[#574d28] text-[11px] font-mono-tech font-bold transition">+100k</button>
                                    <button type="button" onclick="addQuickPoints('my', 500000)" class="px-2 py-1 rounded bg-[#241f0f] hover:bg-[#383018] text-[#d6c67c] border border-[#574d28] text-[11px] font-mono-tech font-bold transition">+500k</button>
                                    <button type="button" onclick="addQuickPoints('my', 1000000)" class="px-2 py-1 rounded bg-[#241f0f] hover:bg-[#383018] text-[#d6c67c] border border-[#574d28] text-[11px] font-mono-tech font-bold transition">+1m</button>
                                    <button type="button" onclick="addQuickPoints('my', 5000000)" class="px-2 py-1 rounded bg-[#241f0f] hover:bg-[#383018] text-[#d6c67c] border border-[#574d28] text-[11px] font-mono-tech font-bold transition">+5m</button>
                                    <button type="button" onclick="addQuickPoints('my', 10000000)" class="px-2 py-1 rounded bg-[#241f0f] hover:bg-[#383018] text-[#d6c67c] border border-[#574d28] text-[11px] font-mono-tech font-bold transition">+10m</button>
                                </div>
                            </div>

                            <div class="flex-grow flex flex-col">
                                <div class="flex items-center justify-between mb-2.5">
                                    <span class="text-xs font-bold text-[#d6c67c]">已選物資清單</span>
                                    <button type="button" onclick="openWeaponModal('my')" class="px-3 py-1.5 rounded-lg bg-[#8c7b38] hover:bg-[#a69244] text-[#141208] text-xs font-black shadow-md transition flex items-center gap-1.5 active:scale-95">
                                        <i class="fa-solid fa-plus"></i> 加入武器
                                    </button>
                                </div>
                                <div id="my-weapons-list" class="space-y-2 flex-grow"></div>
                            </div>
                        </div>
                    </div>

                    <!-- 右欄：對方交易欄位 -->
                    <div class="backrooms-panel rounded-2xl border border-[#2a5945]/80 flex flex-col overflow-hidden">
                        <div class="bg-gradient-to-r from-[#13261e] via-[#182118] to-[#1a170b] px-5 py-4 border-b border-[#2a5945] flex items-center justify-between">
                            <div class="flex items-center gap-3">
                                <div class="w-9 h-9 rounded-lg bg-[#173629] text-[#6ee7b7] flex items-center justify-center border border-[#34755a]">
                                    <i class="fa-solid fa-scale-unbalanced text-base"></i>
                                </div>
                                <div>
                                    <h2 class="text-base sm:text-lg font-black text-[#faf2c3]">對方拿出 (THEIR OFFER)</h2>
                                    <p class="text-[11px] text-[#7fb89e]">對方提供的武器與積分籌碼</p>
                                </div>
                            </div>
                            <button type="button" onclick="clearSide('their')" class="text-xs font-bold text-[#9c9163] hover:text-red-400 transition px-2 py-1 rounded hover:bg-red-950/30">清空此欄</button>
                        </div>

                        <div class="p-4 sm:p-5 space-y-5 flex-grow flex flex-col">
                            <div class="bg-[#121008]/90 p-3.5 rounded-xl border border-[#2a5945]">
                                <label class="block text-xs font-bold text-[#6ee7b7] mb-2 flex items-center justify-between">
                                    <span><i class="fa-solid fa-coins text-emerald-400 mr-1.5"></i>對方附加積分 (支持 k / m 簡寫)</span>
                                    <span id="their-points-formatted" class="font-mono-tech text-[#6ee7b7]">0</span>
                                </label>
                                <div class="flex gap-2">
                                    <input type="text" id="their-points-input" oninput="handlePointsInput('their', this.value)" placeholder="例如: 10m 或 1.5m..." 
                                        class="w-full bg-[#0a0904] border border-[#2a5945] focus:border-emerald-400 rounded-lg px-3 py-2 text-sm font-mono-tech text-[#faf2c3] placeholder-[#486357] outline-none transition">
                                </div>
                                <div class="flex flex-wrap gap-1.5 mt-2.5">
                                    <button type="button" onclick="addQuickPoints('their', 100000)" class="px-2 py-1 rounded bg-[#13261e] hover:bg-[#1c382c] text-[#6ee7b7] border border-[#2a5945] text-[11px] font-mono-tech font-bold transition">+100k</button>
                                    <button type="button" onclick="addQuickPoints('their', 500000)" class="px-2 py-1 rounded bg-[#13261e] hover:bg-[#1c382c] text-[#6ee7b7] border border-[#2a5945] text-[11px] font-mono-tech font-bold transition">+500k</button>
                                    <button type="button" onclick="addQuickPoints('their', 1000000)" class="px-2 py-1 rounded bg-[#13261e] hover:bg-[#1c382c] text-[#6ee7b7] border border-[#2a5945] text-[11px] font-mono-tech font-bold transition">+1m</button>
                                    <button type="button" onclick="addQuickPoints('their', 5000000)" class="px-2 py-1 rounded bg-[#13261e] hover:bg-[#1c382c] text-[#6ee7b7] border border-[#2a5945] text-[11px] font-mono-tech font-bold transition">+5m</button>
                                    <button type="button" onclick="addQuickPoints('their', 10000000)" class="px-2 py-1 rounded bg-[#13261e] hover:bg-[#1c382c] text-[#6ee7b7] border border-[#2a5945] text-[11px] font-mono-tech font-bold transition">+10m</button>
                                </div>
                            </div>

                            <div class="flex-grow flex flex-col">
                                <div class="flex items-center justify-between mb-2.5">
                                    <span class="text-xs font-bold text-[#6ee7b7]">對方物資清單</span>
                                    <button type="button" onclick="openWeaponModal('their')" class="px-3 py-1.5 rounded-lg bg-emerald-600 hover:bg-emerald-500 text-stone-950 text-xs font-black shadow-md transition flex items-center gap-1.5 active:scale-95">
                                        <i class="fa-solid fa-plus"></i> 加入武器
                                    </button>
                                </div>
                                <div id="their-weapons-list" class="space-y-2 flex-grow"></div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- M.E.G. 官方市價資料庫參考表 -->
            <section class="backrooms-panel rounded-2xl p-5 sm:p-6">
                <div class="flex items-center justify-between pb-3 mb-4 border-b border-[#4a4122]">
                    <h3 class="text-sm sm:text-base font-black text-[#faf2c3] flex items-center gap-2">
                        <i class="fa-solid fa-book-skull text-amber-400"></i> M.E.G. 最新物資市價基準資料庫
                    </h3>
                    <span class="text-[11px] font-mono-tech text-[#9c9163]">SYNCED // VER 2.4</span>
                </div>
                <div class="grid grid-cols-1 lg:grid-cols-2 gap-4">
                    <div class="bg-[#121008]/80 p-4 rounded-xl border border-emerald-600/40">
                        <h4 class="text-xs font-black tracking-wider text-emerald-400 mb-3 flex items-center gap-2">
                            <span class="w-2 h-2 rounded-full bg-emerald-400 shadow-[0_0_6px_#34d399]"></span> 獨家武器（綠色）
                        </h4>
                        <div class="grid grid-cols-2 sm:grid-cols-3 gap-2" id="ref-green-list"></div>
                    </div>
                    <div class="bg-[#121008]/80 p-4 rounded-xl border border-amber-600/40">
                        <h4 class="text-xs font-black tracking-wider text-amber-400 mb-3 flex items-center gap-2">
                            <span class="w-2 h-2 rounded-full bg-amber-400 shadow-[0_0_6px_#fbbf24]"></span> 違禁品（金色）
                        </h4>
                        <div class="grid grid-cols-2 sm:grid-cols-3 gap-2" id="ref-gold-list"></div>
                    </div>
                </div>
            </section>
        </div>

        <!-- ==================== 分頁 2：玩家討論區 (即時公共聊天室) ==================== -->
        <div id="view-chat" class="hidden">
            <div id="chat-panel-box" class="backrooms-panel rounded-2xl border border-[#8c7b38]/60 overflow-hidden flex flex-col h-[72vh] min-h-[520px] transition-all duration-200">
                <div id="chat-top-bar" class="bg-gradient-to-r from-[#2e2812] via-[#211d0d] to-[#1a170b] px-5 py-4 border-b border-[#594e25] flex flex-col sm:flex-row sm:items-center justify-between gap-2 shrink-0">
                    <div id="chat-header-info" class="flex items-center gap-3">
                        <div class="w-10 h-10 rounded-xl bg-[#3d3518] text-amber-400 flex items-center justify-center border border-[#857537] shrink-0">
                            <i class="fa-solid fa-tower-broadcast text-lg flicker-text"></i>
                        </div>
                        <div>
                            <div class="flex items-center gap-2 flex-wrap">
                                <h2 class="text-base sm:text-lg font-black text-[#faf2c3]">後室流浪者公共頻道 // 玩家討論區</h2>
                                <span id="chat-connection-badge" class="px-2 py-0.5 rounded bg-emerald-950 text-emerald-400 border border-emerald-600/50 text-[10px] font-mono-tech font-bold flex items-center gap-1.5">
                                    <span class="w-1.5 h-1.5 rounded-full bg-emerald-400 animate-ping"></span> 雲端同步中
                                </span>
                            </div>
                            <p class="text-xs text-[#a39765]">免登入即時同步：所有連上本網頁的玩家都能看到與傳送訊息</p>
                        </div>
                    </div>

                    <div class="flex items-center justify-between sm:justify-end gap-2 w-full sm:w-auto">
                        <div class="text-xs font-mono-tech text-[#9c9163] bg-[#121008] px-3 py-1.5 rounded-lg border border-[#4d4423]">
                            近期訊息: <span id="chat-msg-count" class="text-[#f2e394] font-bold">0</span>
                        </div>
                        <div class="flex items-center gap-1.5">
                            <button type="button" onclick="openAdminModal()" id="admin-toggle-btn" title="管理員權限" class="px-2.5 py-1.5 rounded-lg bg-[#1c180b] hover:bg-[#2e2712] text-[#a39765] hover:text-amber-300 border border-[#524926] text-xs font-bold transition flex items-center gap-1.5 active:scale-95">
                                <i class="fa-solid fa-shield-halved"></i>
                                <span id="admin-status-label" class="hidden sm:inline">管理員</span>
                            </button>
                            <button type="button" onclick="toggleChatFullscreen()" id="chat-fullscreen-btn" class="px-3 py-1.5 rounded-lg bg-[#2e2712] hover:bg-[#3f3619] text-[#f2e394] border border-[#8c7b38] text-xs font-black transition flex items-center gap-1.5 active:scale-95">
                                <i id="chat-fullscreen-icon" class="fa-solid fa-expand"></i>
                                <span id="chat-fullscreen-label">全螢幕模式</span>
                            </button>
                        </div>
                    </div>
                </div>

                <div id="chat-messages-container" class="flex-grow overflow-y-auto p-4 sm:p-6 space-y-3.5 bg-[#0f0d06]/70">
                    <div class="py-16 text-center text-[#7d734b]">
                        <i class="fa-solid fa-spinner fa-spin text-2xl mb-2 text-amber-400"></i>
                        <p class="text-xs font-mono-tech">正在連線 Firebase 雲端聊天室...</p>
                    </div>
                </div>

                <form onsubmit="handleSendChatMessage(event)" class="p-3 sm:p-4 bg-[#1a170c] border-t border-[#594e25] flex flex-col sm:flex-row gap-2.5 shrink-0">
                    <div class="flex items-center gap-2 sm:w-56 shrink-0">
                        <!-- 自訂頭像上傳按鈕 -->
                        <input type="file" id="chat-avatar-file" accept="image/*" class="hidden" onchange="handleAvatarUpload(event)">
                        <button type="button" onclick="document.getElementById('chat-avatar-file').click()" title="點擊上傳玩家頭像" class="relative w-10 h-10 rounded-full border-2 border-[#8c7b38] hover:border-amber-400 overflow-hidden shrink-0 bg-[#0d0b05] group transition">
                            <img id="my-avatar-preview" src="" alt="我的頭像" class="w-full h-full object-cover">
                            <span class="absolute inset-0 bg-black/55 opacity-0 group-hover:opacity-100 flex items-center justify-center text-[10px] text-amber-300 transition">
                                <i class="fa-solid fa-camera"></i>
                            </span>
                        </button>
                        <div class="relative flex-grow">
                            <i class="fa-solid fa-user-astronaut absolute left-3 top-1/2 -translate-y-1/2 text-amber-400 text-xs"></i>
                            <input type="text" id="chat-nickname-input" maxlength="20" placeholder="您的暱稱 (選填)" 
                                class="w-full bg-[#0d0b05] border border-[#594e25] focus:border-amber-400 rounded-xl pl-8 pr-3 py-2.5 text-xs sm:text-sm text-[#faf2c3] placeholder-[#5e5534] outline-none transition">
                        </div>
                    </div>
                    <div class="flex-grow flex gap-2">
                        <input type="text" id="chat-text-input" maxlength="300" placeholder="輸入要傳送給所有玩家的訊息（例如：收 Aegis-01、出 ASH-12...）" 
                            class="w-full bg-[#0d0b05] border border-[#594e25] focus:border-amber-400 rounded-xl px-4 py-2.5 text-xs sm:text-sm text-[#faf2c3] placeholder-[#5e5534] outline-none transition">
                        <button type="submit" id="chat-send-btn" class="px-5 py-2.5 rounded-xl bg-gradient-to-r from-amber-500 to-yellow-600 hover:from-amber-400 hover:to-yellow-500 text-stone-950 font-black text-xs sm:text-sm shadow-lg transition flex items-center gap-2 shrink-0 active:scale-95">
                            <i class="fa-solid fa-paper-plane"></i>
                            <span>發送</span>
                        </button>
                    </div>
                </form>
            </div>
        </div>

    </main>

    <div id="toast-container" class="fixed bottom-5 right-5 z-50 flex flex-col gap-2 pointer-events-none"></div>

    <footer class="border-t border-[#423a1e] py-4 text-center text-xs font-mono-tech text-[#8a7f56] bg-[#121008]">
        LEVEL-0 TRADING TERMINAL // 謹慎評估每一筆物資交換，願你在後室平安生存。
    </footer>

    <!-- ==================== 管理員驗證彈出視窗 ==================== -->
    <div id="admin-modal" class="fixed inset-0 z-50 hidden items-center justify-center bg-black/85 backdrop-blur-sm p-4">
        <div class="bg-[#1a170c] border-2 border-amber-500/70 rounded-2xl w-full max-w-sm p-6 shadow-2xl space-y-4">
            <div class="flex items-center justify-between">
                <h3 class="text-base font-black text-[#faf2c3] flex items-center gap-2">
                    <i class="fa-solid fa-shield-halved text-amber-400"></i> M.E.G. 管理員權限驗證
                </h3>
                <button type="button" onclick="closeAdminModal()" class="text-[#9c9163] hover:text-[#faf2c3]">
                    <i class="fa-solid fa-xmark"></i>
                </button>
            </div>
            <p class="text-xs text-[#a39765]">驗證通過後可刪除任意訊息、禁言違規玩家，且發言時自動開啟彩虹特效。</p>
            <input type="password" id="admin-password-input" onkeydown="if(event.key==='Enter') verifyAdminLogin()" placeholder="輸入管理員金鑰..." 
                class="w-full bg-[#0d0b05] border border-[#594e25] focus:border-amber-400 rounded-xl px-3.5 py-2.5 text-sm font-mono-tech text-[#faf2c3] outline-none">
            <div class="flex justify-end gap-2">
                <button type="button" id="admin-logout-btn" onclick="logoutAdmin()" class="hidden px-3.5 py-2 rounded-xl bg-red-950 text-red-300 border border-red-700/60 text-xs font-bold">登出管理員</button>
                <button type="button" onclick="verifyAdminLogin()" class="px-4 py-2 rounded-xl bg-amber-500 hover:bg-amber-400 text-stone-950 text-xs font-black">確認啟用</button>
            </div>
        </div>
    </div>

    <!-- ==================== 選擇武器彈出視窗 (Modal) ==================== -->
    <div id="weapon-modal" class="fixed inset-0 z-50 hidden items-center justify-center bg-black/85 backdrop-blur-sm p-4 transition-all">
        <div class="bg-[#1a170c] border-2 border-[#756733] rounded-2xl w-full max-w-2xl max-h-[85vh] flex flex-col shadow-[0_0_50px_rgba(0,0,0,0.9)] overflow-hidden">
            <div class="px-6 py-4 border-b border-[#4a4122] flex items-center justify-between bg-[#211d0f]">
                <div>
                    <h3 class="text-lg font-black text-[#faf2c3] flex items-center gap-2.5" id="modal-title">選擇要加入的物資</h3>
                    <p class="text-xs text-[#9c9163]">點擊物資卡片即可加入交易清單（可重複點擊累加數量）</p>
                </div>
                <button type="button" onclick="closeWeaponModal()" class="w-9 h-9 rounded-lg bg-[#2e2815] hover:bg-[#423a1f] text-[#d6c67c] flex items-center justify-center transition">
                    <i class="fa-solid fa-xmark text-lg"></i>
                </button>
            </div>

            <div class="p-4 bg-[#141209] border-b border-[#4a4122] flex flex-col sm:flex-row gap-3">
                <div class="relative flex-grow">
                    <i class="fa-solid fa-magnifying-glass absolute left-3.5 top-1/2 -translate-y-1/2 text-[#8a7f56] text-sm"></i>
                    <input type="text" id="weapon-search" oninput="renderModalWeapons()" placeholder="檢索物資名稱..." 
                        class="w-full bg-[#0d0b05] border border-[#594e25] focus:border-[#d9c462] rounded-xl pl-10 pr-4 py-2 text-sm text-[#faf2c3] placeholder-[#5e5534] outline-none transition">
                </div>
                <div class="flex gap-1.5 bg-[#0d0b05] p-1 rounded-xl border border-[#4a4122]">
                    <button type="button" onclick="setModalFilter('all')" id="filter-all" class="px-3 py-1.5 rounded-lg text-xs font-black bg-[#8c7b38] text-[#141208] transition">全部</button>
                    <button type="button" onclick="setModalFilter('green')" id="filter-green" class="px-3 py-1.5 rounded-lg text-xs font-bold text-emerald-400 hover:bg-[#242010] transition">獨家(綠)</button>
                    <button type="button" onclick="setModalFilter('gold')" id="filter-gold" class="px-3 py-1.5 rounded-lg text-xs font-bold text-amber-400 hover:bg-[#242010] transition">違禁品(金)</button>
                </div>
            </div>

            <div id="modal-weapon-grid" class="p-4 overflow-y-auto grid grid-cols-1 sm:grid-cols-2 gap-2.5 flex-grow"></div>

            <div class="px-6 py-3.5 border-t border-[#4a4122] bg-[#211d0f] flex justify-end">
                <button type="button" onclick="closeWeaponModal()" class="px-6 py-2 rounded-xl bg-[#9e8b3f] hover:bg-[#b5a04a] text-[#141208] text-sm font-black shadow-lg transition">確認並返回終端</button>
            </div>
        </div>
    </div>

    <!-- 交易評估與 UI 核心邏輯 -->
    <script>
        // ★ 管理員金鑰（可自行修改成你專屬的密碼）
        const ADMIN_SECRET_CODE = 'MEG2026';

        // 預設後室探員 SVG 頭像
        const DEFAULT_AVATAR_URL = "data:image/svg+xml;utf8," + encodeURIComponent(
            `<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 80 80"><rect width="80" height="80" fill="#241f0f"/><circle cx="40" cy="30" r="14" fill="#d6c67c"/><path d="M16 70c2-14 14-20 24-20s22 6 24 20" fill="#8c7b38"/></svg>`
        );

        const WEAPONS_DB = [
            { id: 'aegis01', name: 'Aegis-01', category: 'green', min: 30000000, max: 40000000, priceText: '30m ~ 40m' },
            { id: 'n23', name: 'N-23', category: 'green', min: 15000000, max: 15000000, priceText: '15m' },
            { id: 'akv', name: 'AK-V', category: 'green', min: 10000000, max: 10000000, priceText: '10m' },
            { id: 'patrion', name: 'Patrion', category: 'green', min: 14000000, max: 20000000, priceText: '14m ~ 20m' },
            { id: 'pink_glock', name: '粉色格洛克', category: 'green', min: 8000000, max: 10000000, priceText: '8m ~ 10m' },
            { id: 'clicker', name: '點擊器', category: 'green', min: 6500000, max: 9000000, priceText: '6.5m ~ 9m' },
            { id: 'lancelot', name: '蘭斯洛特佩劍', category: 'green', min: 20000000, max: 20000000, priceText: '20m' },
            { id: 'yellow_glowstick', name: '黃色螢光棒', category: 'green', min: 21000000, max: 25000000, priceText: '21m ~ 25m' },
            { id: 'blue_lightsaber', name: '藍色光劍', category: 'green', min: 11000000, max: 17000000, priceText: '11m ~ 17m' },
            { id: 'pathogen', name: 'pathogen', category: 'green', min: 18000000, max: 18000000, priceText: '18m' },
            { id: 'key_knife', name: '鑰匙刀', category: 'green', min: 18000000, max: 18000000, priceText: '18m' },
            { id: 'ash12', name: 'ASH-12', category: 'gold', min: 600000, max: 800000, priceText: '600k ~ 800k' },
            { id: 'bizon', name: 'Bizon (Bizza)', category: 'gold', min: 1200000, max: 1200000, priceText: '1.2m' },
            { id: 'l98', name: 'L98', category: 'gold', min: 45000, max: 60000, priceText: '45k ~ 60k' },
            { id: 'rsh12', name: 'RSH-12', category: 'gold', min: 100000, max: 100000, priceText: '100k' },
            { id: 'saiga12', name: 'Saiga-12 (賽加-12)', category: 'gold', min: 150000, max: 250000, priceText: '150k ~ 250k' },
            { id: 'mx17', name: 'MX17', category: 'gold', min: 75000, max: 75000, priceText: '75k' },
            { id: 'katana', name: '武士刀', category: 'gold', min: 40000, max: 70000, priceText: '40k ~ 70k' },
            { id: 'ssvd', name: 'SSV-D', category: 'gold', min: 65000, max: 95000, priceText: '65k ~ 95k' },
            { id: 'bager300', name: 'Bager-300', category: 'gold', min: 60000, max: 60000, priceText: '60k' }
        ];

        // 產生並保存穩定的裝置玩家 ID，方便管理員針對該玩家進行禁言管理
        function getOrCreatePlayerUid() {
            try {
                let uid = localStorage.getItem('backrooms_player_uid');
                if (!uid) {
                    uid = 'user-' + Math.random().toString(36).substring(2, 10);
                    localStorage.setItem('backrooms_player_uid', uid);
                }
                return uid;
            } catch (e) {
                return 'user-' + Math.random().toString(36).substring(2, 10);
            }
        }

        window.currentUserUid = getOrCreatePlayerUid();

        window.state = {
            activeTab: 'calculator',
            valuationMode: 'avg',
            my: { points: 0, weapons: {} },
            their: { points: 0, weapons: {} },
            chatMessages: [],
            mutedUsers: {},
            chatFullscreen: false,
            isAdmin: false,
            userAvatar: DEFAULT_AVATAR_URL,
            activeModalSide: 'my',
            modalFilter: 'all'
        };

        // 初始化本機保存的頭像與管理員狀態
        function initUserProfile() {
            try {
                const savedAvatar = localStorage.getItem('backrooms_user_avatar');
                if (savedAvatar && savedAvatar.startsWith('data:image/')) {
                    window.state.userAvatar = savedAvatar;
                }
                const savedNick = localStorage.getItem('backrooms_user_nickname');
                const nickEl = document.getElementById('chat-nickname-input');
                if (savedNick && nickEl) nickEl.value = savedNick;

                if (localStorage.getItem('backrooms_is_admin') === '1') {
                    window.state.isAdmin = true;
                }
            } catch (e) {}
            const previewEl = document.getElementById('my-avatar-preview');
            if (previewEl) previewEl.src = window.state.userAvatar;
            updateAdminUI();
        }

        // 頭像上傳與自動壓縮成 96x96
        window.handleAvatarUpload = function(event) {
            const file = event.target.files?.[0];
            if (!file) return;
            if (!file.type.startsWith('image/')) {
                window.showToast('請選擇圖片檔案！', 'error');
                return;
            }
            const reader = new FileReader();
            reader.onload = function(e) {
                const img = new Image();
                img.onload = function() {
                    const canvas = document.createElement('canvas');
                    const size = 96;
                    canvas.width = size;
                    canvas.height = size;
                    const ctx = canvas.getContext('2d');
                    const minSide = Math.min(img.width, img.height);
                    const sx = (img.width - minSide) / 2;
                    const sy = (img.height - minSide) / 2;
                    ctx.drawImage(img, sx, sy, minSide, minSide, 0, 0, size, size);
                    const compressed = canvas.toDataURL('image/jpeg', 0.72);
                    window.state.userAvatar = compressed;
                    const previewEl = document.getElementById('my-avatar-preview');
                    if (previewEl) previewEl.src = compressed;
                    try {
                        localStorage.setItem('backrooms_user_avatar', compressed);
                    } catch (err) {}
                    window.showToast('玩家頭像已更新！', 'success');
                };
                img.src = e.target.result;
            };
            reader.readAsDataURL(file);
        };

        // 頂端新訊息提示橫幅
        let topBannerTimer = null;
        window.showTopMessageBanner = function(msg) {
            const banner = document.getElementById('top-msg-banner');
            const avatarEl = document.getElementById('top-banner-avatar');
            const nickEl = document.getElementById('top-banner-nick');
            const textEl = document.getElementById('top-banner-text');
            if (!banner || !nickEl || !textEl) return;

            if (avatarEl) {
                avatarEl.src = (msg.avatar && msg.avatar.startsWith('data:image/')) ? msg.avatar : DEFAULT_AVATAR_URL;
            }
            nickEl.textContent = (msg.isAdmin ? '🌈 [管理員] ' : '💬 ') + (msg.nickname || '無名流浪者');
            textEl.textContent = msg.text || '';

            banner.classList.remove('-translate-y-28', 'opacity-0', 'pointer-events-none');
            if (topBannerTimer) clearTimeout(topBannerTimer);
            topBannerTimer = setTimeout(() => {
                banner.classList.add('-translate-y-28', 'opacity-0', 'pointer-events-none');
            }, 4000);
        };

        window.handleTopBannerClick = function() {
            const banner = document.getElementById('top-msg-banner');
            if (banner) banner.classList.add('-translate-y-28', 'opacity-0', 'pointer-events-none');
            if (window.state.activeTab !== 'chat') {
                window.switchTab('chat');
            }
        };

        // 管理員驗證與 UI 切換
        function updateAdminUI() {
            const btn = document.getElementById('admin-toggle-btn');
            const label = document.getElementById('admin-status-label');
            const logoutBtn = document.getElementById('admin-logout-btn');
            if (!btn) return;
            if (window.state.isAdmin) {
                btn.className = 'px-2.5 py-1.5 rounded-lg bg-gradient-to-r from-fuchsia-950/90 via-amber-950/90 to-cyan-950/90 text-amber-300 border border-amber-400 text-xs font-black transition flex items-center gap-1.5 shadow-[0_0_12px_rgba(251,191,36,0.35)]';
                if (label) label.textContent = '管理員已啟用';
                if (logoutBtn) logoutBtn.classList.remove('hidden');
            } else {
                btn.className = 'px-2.5 py-1.5 rounded-lg bg-[#1c180b] hover:bg-[#2e2712] text-[#a39765] hover:text-amber-300 border border-[#524926] text-xs font-bold transition flex items-center gap-1.5 active:scale-95';
                if (label) label.textContent = '管理員';
                if (logoutBtn) logoutBtn.classList.add('hidden');
            }
        }

        window.openAdminModal = function() {
            const modal = document.getElementById('admin-modal');
            const input = document.getElementById('admin-password-input');
            if (input) input.value = '';
            if (modal) {
                modal.classList.remove('hidden');
                modal.classList.add('flex');
                setTimeout(() => input?.focus(), 50);
            }
        };

        window.closeAdminModal = function() {
            const modal = document.getElementById('admin-modal');
            if (modal) {
                modal.classList.add('hidden');
                modal.classList.remove('flex');
            }
        };

        window.verifyAdminLogin = function() {
            const input = document.getElementById('admin-password-input');
            const pwd = input ? input.value.trim() : '';
            if (pwd === ADMIN_SECRET_CODE) {
                window.state.isAdmin = true;
                try { localStorage.setItem('backrooms_is_admin', '1'); } catch (e) {}
                updateAdminUI();
                window.closeAdminModal();
                window.renderChatMessages();
                window.showToast('管理員權限已開啟！說話將自帶彩虹特效', 'success');
            } else {
                window.showToast('管理員金鑰錯誤！', 'error');
            }
        };

        window.logoutAdmin = function() {
            window.state.isAdmin = false;
            try { localStorage.removeItem('backrooms_is_admin'); } catch (e) {}
            updateAdminUI();
            window.closeAdminModal();
            window.renderChatMessages();
            window.showToast('已登出管理員模式', 'info');
        };

        window.adminDeleteMessage = async function(msgId) {
            if (!window.state.isAdmin || !msgId) return;
            if (!window.chatCloudAPI?.deleteMessage) return;
            try {
                await window.chatCloudAPI.deleteMessage(msgId);
                window.showToast('已刪除該則訊息', 'success');
            } catch (err) {
                console.error(err);
                window.showToast('刪除失敗，請稍後再試', 'error');
            }
        };

        window.adminToggleMute = async function(targetUid, nickname) {
            if (!window.state.isAdmin || !targetUid) return;
            if (targetUid === window.currentUserUid) {
                window.showToast('無法禁言管理員自己', 'error');
                return;
            }
            if (!window.chatCloudAPI?.setMuteUser) return;
            const currentlyMuted = Boolean(window.state.mutedUsers[targetUid]);
            try {
                await window.chatCloudAPI.setMuteUser(targetUid, !currentlyMuted, nickname);
                window.showToast(
                    !currentlyMuted ? `已禁言玩家「${nickname || '無名流浪者'}」` : `已解除「${nickname || '無名流浪者'}」的禁言`,
                    'info'
                );
            } catch (err) {
                console.error(err);
                window.showToast('禁言操作失敗，請稍後再試', 'error');
            }
        };

        window.setChatFullscreen = function(enable) {
            window.state.chatFullscreen = Boolean(enable);
            const panel = document.getElementById('chat-panel-box');
            const label = document.getElementById('chat-fullscreen-label');
            const icon = document.getElementById('chat-fullscreen-icon');
            const container = document.getElementById('chat-messages-container');

            if (window.state.chatFullscreen) {
                document.body.classList.add('chat-fullscreen-active');
                if (panel) panel.classList.add('is-fullscreen');
                if (label) label.textContent = '退出放大';
                if (icon) icon.className = 'fa-solid fa-compress';
            } else {
                document.body.classList.remove('chat-fullscreen-active');
                if (panel) panel.classList.remove('is-fullscreen');
                if (label) label.textContent = '全螢幕模式';
                if (icon) icon.className = 'fa-solid fa-expand';
            }

            if (container) {
                requestAnimationFrame(() => {
                    container.scrollTop = container.scrollHeight;
                });
            }
        };

        window.toggleChatFullscreen = function() {
            window.setChatFullscreen(!window.state.chatFullscreen);
        };

        window.formatValue = function(num) {
            if (num === 0) return '0';
            const absNum = Math.abs(num);
            const sign = num < 0 ? '-' : '';
            if (absNum >= 1000000) {
                const mVal = absNum / 1000000;
                return sign + (Number.isInteger(mVal) ? mVal : mVal.toFixed(2).replace(/\.?0+$/, '')) + 'm';
            }
            if (absNum >= 1000) {
                const kVal = absNum / 1000;
                return sign + (Number.isInteger(kVal) ? kVal : kVal.toFixed(1).replace(/\.?0+$/, '')) + 'k';
            }
            return sign + Math.round(absNum).toLocaleString();
        };

        window.parsePointsString = function(str) {
            if (!str) return 0;
            const cleaned = str.toString().trim().toLowerCase().replace(/,/g, '');
            const match = cleaned.match(/^([0-9]*\.?[0-9]+)\s*([km]?)$/);
            if (!match) {
                const fallback = parseFloat(cleaned.replace(/[^0-9.]/g, ''));
                return isNaN(fallback) ? 0 : Math.round(fallback);
            }
            let num = parseFloat(match[1]);
            const unit = match[2];
            if (unit === 'm') num *= 1000000;
            if (unit === 'k') num *= 1000;
            return Math.round(num);
        };

        window.getWeaponUnitValue = function(weapon, mode = window.state.valuationMode) {
            if (mode === 'min') return weapon.min;
            if (mode === 'max') return weapon.max;
            return (weapon.min + weapon.max) / 2;
        };

        window.showToast = function(message, type = 'info') {
            const container = document.getElementById('toast-container');
            if (!container) return;
            const toast = document.createElement('div');
            const colors = {
                success: 'bg-emerald-950/95 border-emerald-500 text-emerald-200 shadow-[0_0_20px_rgba(16,185,129,0.3)]',
                error: 'bg-red-950/95 border-red-500 text-red-200 shadow-[0_0_20px_rgba(239,68,68,0.3)]',
                info: 'bg-[#211d0f]/95 border-[#a89852] text-[#faf2c3] shadow-[0_0_20px_rgba(212,196,118,0.25)]'
            };
            const icons = {
                success: 'fa-circle-check text-emerald-400',
                error: 'fa-triangle-exclamation text-red-400',
                info: 'fa-bell text-amber-400'
            };
            toast.className = `pointer-events-auto px-4 py-3 rounded-xl border ${colors[type] || colors.info} text-xs sm:text-sm font-bold flex items-center gap-2.5 transition-all duration-300 transform translate-y-2 opacity-0`;
            toast.innerHTML = `<i class="fa-solid ${icons[type] || icons.info} text-base"></i><span>${message}</span>`;
            container.appendChild(toast);
            requestAnimationFrame(() => toast.classList.remove('translate-y-2', 'opacity-0'));
            setTimeout(() => {
                toast.classList.add('opacity-0', 'translate-y-2');
                setTimeout(() => toast.remove(), 300);
            }, 3500);
        };

        window.switchTab = function(tabName) {
            if (tabName !== 'chat' && window.state.chatFullscreen) {
                window.setChatFullscreen(false);
            }
            window.state.activeTab = tabName;
            ['calculator', 'chat'].forEach(t => {
                const view = document.getElementById(`view-${t}`);
                const btn = document.getElementById(`tab-${t}`);
                if (!view || !btn) return;
                if (t === tabName) {
                    view.classList.remove('hidden');
                    btn.className = 'px-3.5 py-1.5 rounded-lg transition-all bg-[#8c7b38] text-[#141208] shadow-md font-black flex items-center gap-1.5 relative';
                } else {
                    view.classList.add('hidden');
                    btn.className = 'px-3.5 py-1.5 rounded-lg transition-all text-[#9c9163] hover:text-[#f5eab0] font-bold flex items-center gap-1.5 relative';
                }
            });

            const valControls = document.getElementById('valuation-controls');
            if (valControls) {
                if (tabName === 'calculator') {
                    valControls.classList.remove('hidden');
                    valControls.classList.add('flex');
                } else {
                    valControls.classList.add('hidden');
                    valControls.classList.remove('flex');
                }
            }

            if (tabName === 'chat') {
                const badge = document.getElementById('unread-chat-badge');
                if (badge) badge.classList.add('hidden');
                window.renderChatMessages();
            }
        };

        window.setValuationMode = function(mode) {
            window.state.valuationMode = mode;
            ['min', 'avg', 'max'].forEach(m => {
                const btn = document.getElementById(`mode-${m}`);
                if (!btn) return;
                btn.className = m === mode
                    ? 'px-2.5 py-1.5 rounded-lg transition-all bg-[#8c7b38] text-[#141208] shadow-md font-black'
                    : 'px-2.5 py-1.5 rounded-lg transition-all text-[#9c9163] hover:text-[#f5eab0]';
            });
            window.updateAllUI();
        };

        window.handlePointsInput = function(side, val) {
            window.state[side].points = window.parsePointsString(val);
            window.updateAllUI();
        };

        window.addQuickPoints = function(side, amount) {
            window.state[side].points += amount;
            const inputEl = document.getElementById(`${side}-points-input`);
            if (inputEl) inputEl.value = window.formatValue(window.state[side].points);
            window.updateAllUI();
        };

        window.addWeapon = function(side, weaponId) {
            if (!window.state[side].weapons[weaponId]) window.state[side].weapons[weaponId] = 0;
            window.state[side].weapons[weaponId] += 1;
            window.updateAllUI();
            window.renderModalWeapons();
        };

        window.updateWeaponCount = function(side, weaponId, delta) {
            if (!window.state[side].weapons[weaponId]) return;
            window.state[side].weapons[weaponId] += delta;
            if (window.state[side].weapons[weaponId] <= 0) delete window.state[side].weapons[weaponId];
            window.updateAllUI();
        };

        window.clearSide = function(side) {
            window.state[side].points = 0;
            window.state[side].weapons = {};
            const inputEl = document.getElementById(`${side}-points-input`);
            if (inputEl) inputEl.value = '';
            window.updateAllUI();
            window.showToast(side === 'my' ? '已清空我方交易欄' : '已清空對方交易欄', 'info');
        };

        window.resetAll = function() {
            window.state.my = { points: 0, weapons: {} };
            window.state.their = { points: 0, weapons: {} };
            const myInput = document.getElementById('my-points-input');
            const theirInput = document.getElementById('their-points-input');
            if (myInput) myInput.value = '';
            if (theirInput) theirInput.value = '';
            window.updateAllUI();
            window.showToast('已重置所有交易欄位數據', 'info');
        };

        window.openWeaponModal = function(side) {
            window.state.activeModalSide = side;
            const modal = document.getElementById('weapon-modal');
            const title = document.getElementById('modal-title');
            const searchInput = document.getElementById('weapon-search');
            if (title) {
                title.innerHTML = side === 'my'
                    ? '<i class="fa-solid fa-hand-holding-dollar text-amber-400"></i> 加入物資至【我方付出】'
                    : '<i class="fa-solid fa-scale-unbalanced text-emerald-400"></i> 加入物資至【對方拿出】';
            }
            if (searchInput) searchInput.value = '';
            window.setModalFilter('all');
            if (modal) {
                modal.classList.remove('hidden');
                modal.classList.add('flex');
            }
        };

        window.closeWeaponModal = function() {
            const modal = document.getElementById('weapon-modal');
            if (modal) {
                modal.classList.add('hidden');
                modal.classList.remove('flex');
            }
        };

        window.setModalFilter = function(filter) {
            window.state.modalFilter = filter;
            ['all', 'green', 'gold'].forEach(f => {
                const btn = document.getElementById(`filter-${f}`);
                if (!btn) return;
                if (f === filter) {
                    btn.className = 'px-3 py-1.5 rounded-lg text-xs font-black bg-[#8c7b38] text-[#141208] transition';
                } else {
                    const colorClass = f === 'green' ? 'text-emerald-400' : (f === 'gold' ? 'text-amber-400' : 'text-[#9c9163]');
                    btn.className = `px-3 py-1.5 rounded-lg text-xs font-bold ${colorClass} hover:bg-[#242010] transition`;
                }
            });
            window.renderModalWeapons();
        };

        window.renderModalWeapons = function() {
            const grid = document.getElementById('modal-weapon-grid');
            if (!grid) return;
            const searchInput = document.getElementById('weapon-search');
            const keyword = searchInput ? searchInput.value.trim().toLowerCase() : '';
            const side = window.state.activeModalSide;

            const filtered = WEAPONS_DB.filter(w => {
                const matchCat = window.state.modalFilter === 'all' || w.category === window.state.modalFilter;
                const matchText = w.name.toLowerCase().includes(keyword);
                return matchCat && matchText;
            });

            if (filtered.length === 0) {
                grid.innerHTML = `<div class="col-span-full py-10 text-center text-xs text-[#8a7f56]">找不到符合「${keyword}」的武器資料</div>`;
                return;
            }

            grid.innerHTML = filtered.map(w => {
                const isGreen = w.category === 'green';
                const currentCount = window.state[side].weapons[w.id] || 0;
                const unitVal = window.getWeaponUnitValue(w);
                return `
                    <button type="button" onclick="addWeapon('${side}', '${w.id}')" 
                        class="text-left p-3.5 rounded-xl bg-[#121008] hover:bg-[#211d0f] border ${isGreen ? 'border-emerald-700/50 hover:border-emerald-400' : 'border-amber-700/50 hover:border-amber-400'} transition-all flex items-center justify-between gap-3 group active:scale-95">
                        <div class="min-w-0">
                            <div class="flex items-center gap-2">
                                <span class="px-1.5 py-0.5 rounded text-[10px] font-black ${isGreen ? 'bg-emerald-950 text-emerald-400 border border-emerald-700/50' : 'bg-amber-950 text-amber-400 border border-amber-700/50'}">
                                    ${isGreen ? '獨家' : '違禁品'}
                                </span>
                                <span class="font-bold text-sm text-[#faf2c3] truncate">${w.name}</span>
                            </div>
                            <div class="text-xs font-mono-tech text-[#9c9163] mt-1">
                                行情: <span class="text-[#d6c67c] font-bold">${w.priceText}</span> (當前估價: ${window.formatValue(unitVal)})
                            </div>
                        </div>
                        <div class="flex items-center gap-2 shrink-0">
                            ${currentCount > 0 ? `<span class="px-2 py-0.5 rounded-full bg-[#8c7b38] text-[#141208] font-mono-tech font-black text-xs">x${currentCount}</span>` : ''}
                            <div class="w-8 h-8 rounded-lg bg-[#292311] group-hover:bg-[#8c7b38] text-[#d6c67c] group-hover:text-[#141208] flex items-center justify-center transition">
                                <i class="fa-solid fa-plus text-xs"></i>
                            </div>
                        </div>
                    </button>
                `;
            }).join('');
        };

        window.calculateSideTotals = function(side) {
            const points = window.state[side].points || 0;
            let currentTotal = points, minTotal = points, maxTotal = points, itemCount = 0;
            Object.entries(window.state[side].weapons).forEach(([id, count]) => {
                const w = WEAPONS_DB.find(item => item.id === id);
                if (!w || count <= 0) return;
                itemCount += count;
                currentTotal += window.getWeaponUnitValue(w) * count;
                minTotal += w.min * count;
                maxTotal += w.max * count;
            });
            return { points, currentTotal, minTotal, maxTotal, itemCount };
        };

        window.renderSideWeaponsList = function(side) {
            const container = document.getElementById(`${side}-weapons-list`);
            if (!container) return;
            const entries = Object.entries(window.state[side].weapons);
            if (entries.length === 0) {
                container.innerHTML = `
                    <div class="py-8 rounded-xl border border-dashed border-[#4d4423] text-center text-[#7d734b] bg-[#0d0b05]/60 flex flex-col items-center justify-center">
                        <i class="fa-solid fa-box-open text-xl mb-1.5 opacity-40"></i>
                        <p class="text-xs">尚未加入任何武器</p>
                    </div>`;
                return;
            }
            container.innerHTML = entries.map(([id, count]) => {
                const w = WEAPONS_DB.find(item => item.id === id);
                if (!w) return '';
                const isGreen = w.category === 'green';
                const unitVal = window.getWeaponUnitValue(w);
                return `
                    <div class="bg-[#121008]/95 border ${isGreen ? 'border-emerald-700/40' : 'border-amber-700/40'} rounded-xl p-3 flex items-center justify-between gap-2">
                        <div class="min-w-0">
                            <div class="flex items-center gap-1.5">
                                <span class="w-2 h-2 rounded-full ${isGreen ? 'bg-emerald-400' : 'bg-amber-400'} shrink-0"></span>
                                <span class="text-xs sm:text-sm font-bold text-[#faf2c3] truncate">${w.name}</span>
                            </div>
                            <div class="text-[11px] font-mono-tech text-[#9c9163] mt-0.5">
                                單價 ${window.formatValue(unitVal)} × ${count} = <span class="text-[#f2e394] font-bold">${window.formatValue(unitVal * count)}</span>
                            </div>
                        </div>
                        <div class="flex items-center gap-1.5 shrink-0 bg-[#0a0904] p-1 rounded-lg border border-[#3d3518]">
                            <button type="button" onclick="updateWeaponCount('${side}', '${id}', -1)" class="w-7 h-7 rounded bg-[#241f0f] hover:bg-red-950 text-[#d6c67c] hover:text-red-300 flex items-center justify-center transition active:scale-90"><i class="fa-solid fa-minus text-[10px]"></i></button>
                            <span class="font-mono-tech font-black text-xs text-[#faf2c3] min-w-[24px] text-center">${count}</span>
                            <button type="button" onclick="updateWeaponCount('${side}', '${id}', 1)" class="w-7 h-7 rounded bg-[#241f0f] hover:bg-[#383018] text-[#d6c67c] flex items-center justify-center transition active:scale-90"><i class="fa-solid fa-plus text-[10px]"></i></button>
                        </div>
                    </div>`;
            }).join('');
        };

        window.updateAllUI = function() {
            const myTotals = window.calculateSideTotals('my');
            const theirTotals = window.calculateSideTotals('their');

            document.getElementById('my-points-formatted').textContent = window.formatValue(myTotals.points);
            document.getElementById('my-item-count').textContent = `${myTotals.itemCount} 件物資`;
            document.getElementById('my-total-display').textContent = window.formatValue(myTotals.currentTotal);
            document.getElementById('my-range-display').innerHTML = `<span>區間: ${window.formatValue(myTotals.minTotal)} ~ ${window.formatValue(myTotals.maxTotal)}</span><span class="text-[#f2e394] font-bold">含積分: ${window.formatValue(myTotals.points)}</span>`;

            document.getElementById('their-points-formatted').textContent = window.formatValue(theirTotals.points);
            document.getElementById('their-item-count').textContent = `${theirTotals.itemCount} 件物資`;
            document.getElementById('their-total-display').textContent = window.formatValue(theirTotals.currentTotal);
            document.getElementById('their-range-display').innerHTML = `<span>區間: ${window.formatValue(theirTotals.minTotal)} ~ ${window.formatValue(theirTotals.maxTotal)}</span><span class="text-[#6ee7b7] font-bold">含積分: ${window.formatValue(theirTotals.points)}</span>`;

            window.renderSideWeaponsList('my');
            window.renderSideWeaponsList('their');

            const diff = theirTotals.currentTotal - myTotals.currentTotal;
            const verdictBox = document.getElementById('verdict-box');
            const verdictBadge = document.getElementById('verdict-badge');
            const verdictPercent = document.getElementById('verdict-percent');
            const verdictDiff = document.getElementById('verdict-diff');

            verdictBox.classList.remove('glow-win', 'glow-loss', 'glow-even');

            if (myTotals.currentTotal === 0 && theirTotals.currentTotal === 0) {
                verdictBadge.className = 'px-3.5 py-1 rounded text-xs font-black tracking-wider mb-2 bg-[#2b2613] text-[#c7ba81] border border-[#574d28]';
                verdictBadge.textContent = '[ 系統待機：請輸入交易物資 ]';
                verdictPercent.className = 'text-4xl sm:text-5xl font-mono-tech font-bold tracking-tight text-[#d6c67c] my-1';
                verdictPercent.textContent = '0.0%';
                verdictDiff.textContent = '淨差額: 0';
                return;
            }

            if (myTotals.currentTotal === 0 && theirTotals.currentTotal > 0) {
                verdictBox.classList.add('glow-win');
                verdictBadge.className = 'px-3.5 py-1 rounded text-xs font-black tracking-wider mb-2 bg-emerald-950 text-emerald-300 border border-emerald-500';
                verdictBadge.textContent = '▲ 絕對賺翻 (無本獲利)';
                verdictPercent.className = 'text-4xl sm:text-5xl font-mono-tech font-bold tracking-tight text-emerald-400 my-1';
                verdictPercent.textContent = '+∞%';
                verdictDiff.textContent = `淨賺: +${window.formatValue(theirTotals.currentTotal)}`;
                return;
            }

            const percent = (diff / myTotals.currentTotal) * 100;
            const absPercentStr = Math.abs(percent).toFixed(1) + '%';

            if (diff > 0) {
                verdictBox.classList.add('glow-win');
                verdictBadge.className = 'px-3.5 py-1 rounded text-xs font-black tracking-wider mb-2 bg-emerald-950 text-emerald-300 border border-emerald-500';
                verdictBadge.textContent = `▲ 交易評估：賺了 ${absPercentStr}`;
                verdictPercent.className = 'text-4xl sm:text-5xl font-mono-tech font-bold tracking-tight text-emerald-400 my-1';
                verdictPercent.textContent = `+${absPercentStr}`;
                verdictDiff.textContent = `淨賺差額: +${window.formatValue(diff)}`;
            } else if (diff < 0) {
                verdictBox.classList.add('glow-loss');
                verdictBadge.className = 'px-3.5 py-1 rounded text-xs font-black tracking-wider mb-2 bg-red-950 text-red-300 border border-red-500';
                verdictBadge.textContent = `▼ 交易評估：虧了 ${absPercentStr}`;
                verdictPercent.className = 'text-4xl sm:text-5xl font-mono-tech font-bold tracking-tight text-red-400 my-1';
                verdictPercent.textContent = `-${absPercentStr}`;
                verdictDiff.textContent = `淨虧差額: ${window.formatValue(diff)}`;
            } else {
                verdictBox.classList.add('glow-even');
                verdictBadge.className = 'px-3.5 py-1 rounded text-xs font-black tracking-wider mb-2 bg-amber-950 text-amber-300 border border-amber-500';
                verdictBadge.textContent = '＝ 交易評估：等價交換';
                verdictPercent.className = 'text-4xl sm:text-5xl font-mono-tech font-bold tracking-tight text-amber-400 my-1';
                verdictPercent.textContent = '0.0%';
                verdictDiff.textContent = '淨差額: 0';
            }
        };

        function escapeHtml(str) {
            if (!str) return '';
            return str.replace(/[&<>"']/g, m => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;', "'": '&#39;' }[m]));
        }

        function formatChatTime(ts) {
            if (!ts) return '剛剛';
            const d = new Date(ts);
            const pad = n => n.toString().padStart(2, '0');
            return `${pad(d.getHours())}:${pad(d.getMinutes())}`;
        }

        window.renderChatMessages = function() {
            const container = document.getElementById('chat-messages-container');
            if (!container) return;
            const messages = window.state.chatMessages;
            const countEl = document.getElementById('chat-msg-count');
            if (countEl) countEl.textContent = messages.length;

            if (!messages || messages.length === 0) {
                container.innerHTML = `
                    <div class="py-16 text-center text-[#7d734b]">
                        <i class="fa-solid fa-comment-dots text-3xl mb-2 opacity-40"></i>
                        <p class="text-sm font-bold text-[#9c9163]">目前頻道尚無任何對話紀錄</p>
                        <p class="text-xs text-[#69603d] mt-1">成為第一位在後室公共頻道發言的流浪者吧！</p>
                    </div>`;
                return;
            }
            const myUid = window.currentUserUid;
            const isAdminViewer = window.state.isAdmin;

            container.innerHTML = messages.map(msg => {
                const isMine = msg.senderUid === myUid;
                const isSenderAdmin = Boolean(msg.isAdmin);
                const isSenderMuted = Boolean(window.state.mutedUsers[msg.senderUid]);
                const safeAvatar = (msg.avatar && msg.avatar.startsWith('data:image/')) ? msg.avatar : DEFAULT_AVATAR_URL;
                const safeNick = escapeHtml(msg.nickname || '無名流浪者');

                const nameHtml = isSenderAdmin
                    ? `<span class="px-1.5 py-0.5 rounded bg-gradient-to-r from-pink-500 via-amber-400 to-cyan-400 text-stone-950 text-[10px] font-black">M.E.G. 管理員</span> <span class="text-xs font-black admin-rainbow-text">${safeNick}</span>`
                    : `<span class="text-xs font-black ${isMine ? 'text-amber-400' : 'text-emerald-400'}">${isMine ? '我 (' + safeNick + ')' : safeNick}</span>`;

                const mutedTagHtml = isSenderMuted
                    ? `<span class="px-1.5 py-0.5 rounded bg-red-950 text-red-400 border border-red-700/60 text-[10px] font-bold">已禁言</span>`
                    : '';

                const adminActionsHtml = isAdminViewer ? `
                    <div class="flex items-center gap-1 opacity-85 hover:opacity-100">
                        <button type="button" onclick="adminDeleteMessage('${msg.id}')" title="刪除此訊息" class="px-1.5 py-0.5 rounded bg-red-950/90 hover:bg-red-800 text-red-300 border border-red-700/60 text-[10px] font-bold transition">
                            <i class="fa-solid fa-trash-can"></i> 刪除
                        </button>
                        ${!isMine && msg.senderUid ? `
                        <button type="button" onclick="adminToggleMute('${msg.senderUid}', '${safeNick.replace(/'/g, '')}')" title="${isSenderMuted ? '解除禁言' : '禁言此玩家'}" class="px-1.5 py-0.5 rounded ${isSenderMuted ? 'bg-emerald-950 text-emerald-300 border-emerald-700/60' : 'bg-amber-950/90 text-amber-300 border-amber-700/60'} border text-[10px] font-bold transition">
                            <i class="fa-solid fa-ban"></i> ${isSenderMuted ? '解禁' : '禁言'}
                        </button>` : ''}
                    </div>` : '';

                const bubbleClass = isSenderAdmin
                    ? `admin-rainbow-bubble text-[#fff9db] ${isMine ? 'rounded-tr-sm' : 'rounded-tl-sm'}`
                    : (isMine
                        ? 'bg-[#362f14] border border-amber-500/50 text-[#faf2c3] rounded-tr-sm'
                        : 'bg-[#17150c] border border-[#4d4423] text-[#e6dfb8] rounded-tl-sm');

                return `
                    <div class="flex items-start gap-2.5 ${isMine ? 'flex-row-reverse' : 'flex-row'}">
                        <img src="${safeAvatar}" alt="頭像" class="w-9 h-9 rounded-full object-cover shrink-0 border ${isSenderAdmin ? 'border-amber-400 shadow-[0_0_10px_rgba(251,191,36,0.6)]' : 'border-[#594e25]'} bg-[#1a170c] mt-0.5">
                        <div class="flex flex-col ${isMine ? 'items-end' : 'items-start'} max-w-[82%] sm:max-w-[70%]">
                            <div class="flex items-center flex-wrap gap-1.5 mb-1 px-1 ${isMine ? 'justify-end' : 'justify-start'}">
                                ${nameHtml}
                                ${mutedTagHtml}
                                <span class="text-[10px] font-mono-tech text-[#8a7f56]">${formatChatTime(msg.createdAt)}</span>
                                ${adminActionsHtml}
                            </div>
                            <div class="px-4 py-2.5 rounded-2xl text-xs sm:text-sm leading-relaxed break-words shadow-md ${bubbleClass}">
                                ${escapeHtml(msg.text)}
                            </div>
                        </div>
                    </div>`;
            }).join('');
            container.scrollTop = container.scrollHeight;
        };

        let lastChatSentAt = 0;
        window.handleSendChatMessage = async function(event) {
            event.preventDefault();
            const nickInput = document.getElementById('chat-nickname-input');
            const textInput = document.getElementById('chat-text-input');
            const sendBtn = document.getElementById('chat-send-btn');
            const nickname = nickInput ? nickInput.value.trim().slice(0, 20) : '';
            const text = textInput ? textInput.value.trim().slice(0, 300) : '';

            // 檢查目前玩家是否被管理員禁言
            if (!window.state.isAdmin && window.state.mutedUsers[window.currentUserUid]) {
                window.showToast('你目前已被管理員禁言，暫時無法發送訊息。', 'error');
                return;
            }

            if (!text) {
                window.showToast('請輸入要發送的訊息內容！', 'error');
                if (textInput) textInput.focus();
                return;
            }

            const now = Date.now();
            if (now - lastChatSentAt < 1200) {
                window.showToast('傳送太快了，請稍等 1 秒再發送', 'info');
                return;
            }

            if (!window.chatCloudAPI || typeof window.chatCloudAPI.sendMessage !== 'function') {
                window.showToast('雲端聊天室尚未初始化完成，請稍後再試', 'error');
                return;
            }

            try {
                if (nickname) localStorage.setItem('backrooms_user_nickname', nickname);
            } catch (e) {}

            if (sendBtn) sendBtn.disabled = true;
            try {
                lastChatSentAt = now;
                await window.chatCloudAPI.sendMessage(nickname, text, window.state.userAvatar, window.state.isAdmin);
                if (textInput) textInput.value = '';
            } catch (err) {
                console.error(err);
                window.showToast('訊息發送失敗，請確認 Firebase Realtime Database 規則已設為 true', 'error');
            } finally {
                if (sendBtn) sendBtn.disabled = false;
                if (textInput) textInput.focus();
            }
        };

        function initReferenceTable() {
            const greenContainer = document.getElementById('ref-green-list');
            const goldContainer = document.getElementById('ref-gold-list');
            if (!greenContainer || !goldContainer) return;
            greenContainer.innerHTML = '';
            goldContainer.innerHTML = '';
            WEAPONS_DB.forEach(w => {
                const isGreen = w.category === 'green';
                const itemHtml = `
                    <div class="bg-[#1a170c] border ${isGreen ? 'border-emerald-700/40' : 'border-amber-700/40'} rounded-lg p-2.5 flex flex-col justify-between">
                        <span class="text-xs font-bold ${isGreen ? 'text-emerald-300' : 'text-amber-300'} truncate">${w.name}</span>
                        <span class="text-[11px] font-mono-tech text-[#d6c67c] mt-1 font-bold">${w.priceText}</span>
                    </div>`;
                if (isGreen) greenContainer.innerHTML += itemHtml;
                else goldContainer.innerHTML += itemHtml;
            });
        }

        window.addEventListener('DOMContentLoaded', () => {
            initUserProfile();
            initReferenceTable();
            window.updateAllUI();
        });
    </script>

    <!-- Firebase Realtime Database 免登入多人同步聊天模組 -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
        import {
            getDatabase,
            ref,
            push,
            remove,
            set,
            onValue,
            query,
            limitToLast
        } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-database.js";

        const firebaseConfig = {
            apiKey: "AIzaSyA6DmVSZtOQ4uzJY6IjFLnOV8LQ-DMhUX4",
            authDomain: "backrooms-chat.firebaseapp.com",
            databaseURL: "https://backrooms-chat-default-rtdb.firebaseio.com",
            projectId: "backrooms-chat",
            storageBucket: "backrooms-chat.firebasestorage.app",
            messagingSenderId: "16982267920",
            appId: "1:16982267920:web:306f59881100f2cb549295",
            measurementId: "G-1SLPSR7Q9T"
        };

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);
        const messagesQuery = query(ref(db, "messages"), limitToLast(80));
        const mutedUsersRef = ref(db, "mutedUsers");

        window.chatCloudAPI = {
            sendMessage: async (nickname, text, avatar, isAdmin) => {
                await push(ref(db, "messages"), {
                    nickname: nickname || "無名流浪者",
                    text: text,
                    avatar: (avatar && avatar.startsWith("data:image/")) ? avatar : "",
                    isAdmin: Boolean(isAdmin),
                    senderUid: window.currentUserUid,
                    createdAt: Date.now()
                });
            },
            deleteMessage: async (msgId) => {
                await remove(ref(db, `messages/${msgId}`));
            },
            setMuteUser: async (targetUid, shouldMute, nickname) => {
                if (shouldMute) {
                    await set(ref(db, `mutedUsers/${targetUid}`), {
                        nickname: nickname || "無名流浪者",
                        mutedAt: Date.now()
                    });
                } else {
                    await remove(ref(db, `mutedUsers/${targetUid}`));
                }
            }
        };

        // 監聽雲端禁言名單
        onValue(mutedUsersRef, (snapshot) => {
            window.state.mutedUsers = snapshot.val() || {};
            if (window.state.activeTab === "chat") {
                window.renderChatMessages();
            }
        });

        let isFirstLoad = true;
        let lastKnownMsgId = null;

        onValue(messagesQuery, (snapshot) => {
            const list = [];
            snapshot.forEach((childSnap) => {
                const val = childSnap.val();
                if (val && typeof val.text === "string") {
                    list.push({
                        id: childSnap.key,
                        nickname: typeof val.nickname === "string" ? val.nickname : "無名流浪者",
                        text: val.text,
                        avatar: typeof val.avatar === "string" ? val.avatar : "",
                        isAdmin: Boolean(val.isAdmin),
                        senderUid: val.senderUid || "",
                        createdAt: typeof val.createdAt === "number" ? val.createdAt : Date.now()
                    });
                }
            });

            const latestMsg = list.length > 0 ? list[list.length - 1] : null;
            window.state.chatMessages = list;

            if (window.state.activeTab === "chat") {
                window.renderChatMessages();
            } else {
                const countEl = document.getElementById("chat-msg-count");
                if (countEl) countEl.textContent = list.length;
                if (!isFirstLoad && latestMsg && latestMsg.id !== lastKnownMsgId && latestMsg.senderUid !== window.currentUserUid) {
                    const badge = document.getElementById("unread-chat-badge");
                    if (badge) badge.classList.remove("hidden");
                }
            }

            // 收到其他玩家的新訊息時，在螢幕頂端彈出即時提示橫幅
            if (!isFirstLoad && latestMsg && latestMsg.id !== lastKnownMsgId && latestMsg.senderUid !== window.currentUserUid) {
                window.showTopMessageBanner(latestMsg);
            }

            if (latestMsg) lastKnownMsgId = latestMsg.id;
            isFirstLoad = false;
        }, (error) => {
            console.error("Firebase Realtime Database 讀取錯誤：", error);
            const badge = document.getElementById("chat-connection-badge");
            if (badge) {
                badge.className = "px-2 py-0.5 rounded bg-red-950 text-red-300 border border-red-600/50 text-[10px] font-mono-tech font-bold";
                badge.textContent = "連線受限（請檢查 Rules）";
            }
        });
    </script>
</body>
</html>
