# kazu-to-suuji-app
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>かずとすうじの たのしいワーク＆アプリ</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- html2pdf.js (PDF生成ライブラリ) -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        pastelBlue: '#E0F2FE',
                        pastelPink: '#FCE7F3',
                        pastelYellow: '#FEF9C3',
                        pastelGreen: '#DCFCE7',
                        pastelPurple: '#F3E8FF',
                        brandPrimary: '#FF6B6B',
                        brandSecondary: '#4ECDC4',
                    },
                    fontFamily: {
                        sans: ['"Comic Sans MS"', '"Hiragino Kaku Gothic ProN"', '"Kiwi Maru"', 'sans-serif'],
                    }
                }
            }
        }
    </script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=M+PLUS+Rounded+1c:wght@400;700;800&display=swap');
        
        body {
            font-family: 'M PLUS Rounded 1c', sans-serif;
            background-color: #f8fafc;
            user-select: none;
            -webkit-user-select: none;
        }

        /* 印刷用スタイル */
        @media print {
            .no-print {
                display: none !important;
            }
            body {
                background: white !important;
                color: black !important;
                padding: 0 !important;
                margin: 0 !important;
            }
            .print-area {
                width: 100% !important;
                max-width: 100% !important;
                box-shadow: none !important;
                border: none !important;
                padding: 10px !important;
                margin: 0 !important;
                page-break-after: avoid;
            }
            .worksheet-grid {
                display: grid !important;
                grid-template-columns: repeat(2, 1fr) !important;
                gap: 15px !important;
            }
            .worksheet-item {
                border: 2px dashed #94a3b8 !important;
                border-radius: 12px !important;
                padding: 12px !important;
                background: white !important;
            }
        }

        /* アニメーション */
        @keyframes pop {
            0% { transform: scale(1); }
            50% { transform: scale(1.25) rotate(5deg); }
            100% { transform: scale(1); }
        }
        .pop-anim {
            animation: pop 0.25s ease-out;
        }

        @keyframes confetti {
            0% { transform: translateY(0) rotate(0deg); opacity: 1; }
            100% { transform: translateY(100vh) rotate(720deg); opacity: 0; }
        }
        .confetti-piece {
            position: fixed;
            width: 12px;
            height: 12px;
            pointer-events: none;
            z-index: 100;
            animation: confetti 2.5s ease-out forwards;
        }

        /* 手書きキャンバスグリッド */
        .grid-guide {
            background-image: 
                linear-gradient(to right, #cbd5e1 1px, transparent 1px),
                linear-gradient(to bottom, #cbd5e1 1px, transparent 1px);
            background-size: 50% 50%;
            background-position: center center;
        }

        /* スクリーンショット専用フル表示モード */
        .screenshot-mode .no-print-screen {
            display: none !important;
        }
        .screenshot-mode #print-area {
            max-width: 100% !important;
            margin: 0 !important;
            border: none !important;
            box-shadow: none !important;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col text-slate-800">

    <!-- ヘッダー (印刷・キャプチャ時制御) -->
    <header class="no-print no-print-screen bg-white border-b-2 border-yellow-300 shadow-sm sticky top-0 z-40">
        <div class="max-w-5xl mx-auto px-3 py-2 flex flex-wrap justify-between items-center gap-2">
            <div class="flex items-center gap-2">
                <span class="text-2xl md:text-3xl">🎈</span>
                <div>
                    <h1 class="text-lg md:text-xl font-black text-slate-800 tracking-wide">かずとすうじの たのしいワーク</h1>
                    <p class="text-[10px] md:text-xs text-slate-500 font-bold">かぞえて・こたえて・かいてみよう！</p>
                </div>
            </div>

            <!-- モード切り替えタブ -->
        <div class="flex bg-slate-100 p-1 rounded-xl gap-1 border border-slate-200 text-xs md:text-sm">
            <button id="btn-mode-app" onclick="switchMode('app')" class="px-3 py-1.5 rounded-lg font-bold transition-all bg-emerald-500 text-white shadow-sm">
                🎮 がめんのゲーム
            </button>
            <button id="btn-mode-print" onclick="switchMode('print')" class="px-3 py-1.5 rounded-lg font-bold transition-all text-slate-600 hover:text-slate-900">
                🖨️ プリントをつくる
            </button>
        </div>
    </div>
</header>

<!-- 主コンテンツ領域 -->
<main class="flex-1 max-w-5xl w-full mx-auto p-2 md:p-4">

    <!-- ========================================== -->
    <!-- モード 1: アプリゲーム画面                 -->
    <!-- ========================================== -->
    <section id="section-app" class="space-y-3">
        
        <!-- ゲームと練習のサブ切り替えタブ -->
        <div class="flex justify-center gap-2 bg-slate-200/70 p-1 rounded-2xl w-fit mx-auto text-xs md:text-sm font-extrabold no-print no-print-screen">
            <button id="btn-subtab-count" onclick="switchAppSubTab('count')" class="px-4 py-1.5 rounded-xl transition-all bg-white text-emerald-700 shadow-sm">
                🍎 かぞえるゲーム
            </button>
            <button id="btn-subtab-practice" onclick="switchAppSubTab('practice')" class="px-4 py-1.5 rounded-xl transition-all text-slate-600 hover:text-slate-900">
                ✍️ すうじのれんしゅう
            </button>
        </div>

        <!-- サブコンテンツ 1: かぞえるゲーム -->
        <div id="app-subtab-count" class="space-y-3">
            <!-- レベル・条件選択バー -->
            <div class="bg-white rounded-2xl px-3 py-2 shadow-sm border-2 border-emerald-100 flex flex-wrap items-center justify-between gap-2 text-xs md:text-sm">
                <div class="flex items-center gap-2">
                    <span class="font-bold text-slate-700">コース：</span>
                    <select id="app-level-select" onchange="initAppGame()" class="bg-slate-50 border-2 border-emerald-300 rounded-xl px-2.5 py-1 font-bold text-slate-700 focus:outline-none focus:ring-2 focus:ring-emerald-400">
                        <option value="1">🌱 1しゅるい（1〜5まで）</option>
                        <option value="2">🌸 1しゅるい（1〜10まで）</option>
                        <option value="3" selected>⭐ いろいろミックス（1〜10）</option>
                    </select>
                </div>

                <button onclick="initAppGame()" class="bg-emerald-500 hover:bg-emerald-600 active:scale-95 text-white font-bold px-3 py-1.5 rounded-xl text-xs shadow transition-all flex items-center gap-1">
                    <span>🔄</span> つぎのもんだい
                </button>
            </div>

            <!-- メイン問題表示カード -->
            <div class="bg-white rounded-2xl p-3 md:p-5 shadow-md border-3 border-yellow-200 text-center relative overflow-hidden">
                <!-- 正解判定エフェクト用オーバーレイ -->
                <div id="app-success-overlay" class="hidden absolute inset-0 bg-yellow-400/90 backdrop-blur-sm z-30 flex flex-col items-center justify-center p-3 transition-all">
                    <div class="text-6xl mb-1 animate-bounce">⭕</div>
                    <h2 class="text-3xl font-extrabold text-white drop-shadow">だいせいかい！</h2>
                    <p id="app-success-msg" class="text-base md:text-lg font-bold text-yellow-900 mt-1"></p>
                    <button onclick="initAppGame()" class="mt-4 bg-white text-yellow-600 hover:bg-yellow-50 font-extrabold px-6 py-2 rounded-full text-base shadow-lg active:scale-95 transition-transform">
                        つぎのもんだいへ ➔
                    </button>
                </div>

                <!-- 問題の指示（ターゲット指定） -->
                <div class="mb-3 flex flex-wrap items-center justify-center gap-2">
                    <span class="text-base md:text-lg font-extrabold text-slate-700">
                        <span id="target-icon-display" class="text-2xl md:text-3xl inline-block align-middle mx-1">🍎</span>
                        は <span class="underline decoration-yellow-400 decoration-4">ぜんぶで いくつ</span> あるかな？
                    </span>
                    <span class="text-[10px] md:text-xs bg-sky-100 text-sky-700 font-bold px-2 py-0.5 rounded-full">えをタップして かぞえてね！</span>
                </div>

                <!-- カウント用イラスト配置エリア -->
                <div id="items-container" class="min-h-[140px] max-h-[200px] overflow-y-auto bg-sky-50/60 rounded-xl p-3 border-2 border-dashed border-sky-200 flex flex-wrap items-center justify-center gap-2.5 md:gap-4 relative">
                    <!-- JavaScriptで動的生成されるイラスト項目 -->
                </div>

                <!-- 解答セクション -->
                <div class="mt-4 pt-3 border-t border-slate-100 grid md:grid-cols-2 gap-3 items-center">
                    
                    <!-- 方法A: 数字ボタンで選択 -->
                    <div class="bg-amber-50/70 p-2.5 rounded-xl border border-amber-200">
                        <label class="block text-xs font-bold text-amber-800 mb-2">① すうじボタンを おす</label>
                        <div id="number-buttons-grid" class="grid grid-cols-5 gap-1.5 max-w-xs mx-auto">
                            <!-- 1〜10のボタンを動的生成 -->
                        </div>
                    </div>

                    <!-- 方法B: 手書きでかく -->
                    <div class="bg-indigo-50/70 p-2.5 rounded-xl border border-indigo-200 flex flex-col items-center">
                        <div class="flex justify-between items-center w-full max-w-[200px] mb-1">
                            <label class="text-xs font-bold text-indigo-900">② ゆびやマウスで かく</label>
                            <button onclick="clearCanvas()" class="text-[10px] bg-white border border-indigo-300 text-indigo-700 px-2 py-0.5 rounded hover:bg-indigo-100 font-bold">
                                🗑️ けす
                            </button>
                        </div>
                        
                        <div class="flex items-center gap-3">
                            <div class="relative">
                                <canvas id="handwriting-canvas" width="120" height="120" class="bg-white rounded-lg border-2 border-indigo-300 shadow-inner cursor-crosshair grid-guide touch-none"></canvas>
                                <div class="absolute bottom-0.5 right-1 text-[9px] text-slate-400 font-bold pointer-events-none">なぞり枠</div>
                            </div>

                            <button onclick="checkAnswerFromCanvas()" class="bg-indigo-600 hover:bg-indigo-700 active:scale-95 text-white font-bold px-4 py-3 rounded-xl text-xs shadow transition-all flex flex-col items-center gap-1">
                                <span>⭕</span>
                                <span>これで<br>こたえる</span>
                            </button>
                        </div>
                    </div>

                </div>
            </div>
        </div>

        <!-- サブコンテンツ 2: 数字のおてほん・なぞり書き練習エリア -->
        <div id="app-subtab-practice" class="hidden bg-white rounded-2xl p-4 shadow-sm border-2 border-slate-100 space-y-3">
            <div class="flex justify-between items-center border-b pb-2">
                <h3 class="text-base font-extrabold text-slate-800 flex items-center gap-2">
                    <span>✍️</span> すうじの かきかた れんしゅう
                </h3>
                <span class="text-xs text-slate-500 font-bold">おおきなマスで ただしく かいてみよう！</span>
            </div>

            <div class="flex flex-wrap items-center justify-center gap-1.5" id="practice-num-selector">
                <!-- 1〜10選択ボタン -->
            </div>

            <div class="flex flex-row items-center justify-center gap-6 bg-slate-50 p-4 rounded-xl border border-slate-200">
                <div class="text-center">
                    <div class="text-[10px] font-bold text-slate-500 mb-1">おてほん</div>
                    <div id="practice-guide-num" class="w-28 h-28 bg-white rounded-xl border-2 border-emerald-300 flex items-center justify-center text-6xl font-extrabold text-emerald-500 shadow-sm font-mono">
                        1
                    </div>
                </div>

                <div class="text-xl text-slate-300 font-bold">➔</div>

                <div class="text-center">
                    <div class="text-[10px] font-bold text-slate-500 mb-1">なぞってみよう</div>
                    <div class="relative inline-block">
                        <canvas id="practice-canvas" width="140" height="140" class="bg-white rounded-xl border-2 border-emerald-400 shadow-md cursor-crosshair grid-guide touch-none"></canvas>
                        <span id="practice-bg-hint" class="absolute inset-0 flex items-center justify-center text-7xl font-black text-slate-200/50 pointer-events-none font-mono">1</span>
                    </div>
                    <div class="mt-1 flex justify-center">
                        <button onclick="clearPracticeCanvas()" class="text-xs bg-slate-200 hover:bg-slate-300 text-slate-700 px-3 py-1 rounded-lg font-bold">
                            🗑️ けす
                        </button>
                    </div>
                </div>
            </div>
        </div>

    </section>

    <!-- ========================================== -->
    <!-- モード 2: ワークシート印刷＆画面手書き機能 -->
    <!-- ========================================== -->
    <section id="section-print" class="hidden space-y-6">
        
        <!-- プリント作成パネル -->
        <div class="no-print no-print-screen bg-white rounded-2xl p-5 shadow-sm border-2 border-indigo-100 space-y-4">
            <div class="flex flex-wrap items-center justify-between gap-3 border-b border-slate-100 pb-3">
                <div>
                    <h2 class="text-lg font-extrabold text-slate-800 flex items-center gap-2">
                        <span>📄</span> プリントをつくる
                    </h2>
                    <p class="text-xs text-slate-500 font-bold">ワークシートをPDFやがぞうで ほぞんしたり、がめんのうえで そのまま とくことができます！</p>
                </div>
                
                <!-- アクションボタン群 -->
                <div class="flex flex-wrap gap-2">
                    <button onclick="saveAsPDF(event)" class="bg-rose-600 hover:bg-rose-700 active:scale-95 text-white font-extrabold px-3 py-2 rounded-xl shadow-md transition-all flex items-center gap-1.5 text-xs md:text-sm">
                        <span>📄</span> PDFでほぞん
                    </button>
                    <button onclick="generatePureCanvasImage()" class="bg-amber-500 hover:bg-amber-600 active:scale-95 text-white font-extrabold px-3 py-2 rounded-xl shadow-md transition-all flex items-center gap-1.5 text-xs md:text-sm">
                        <span>🖼️</span> がぞうでつくる
                    </button>
                    <button id="btn-toggle-digital" onclick="toggleDigitalWorksheetMode()" class="bg-emerald-600 hover:bg-emerald-700 active:scale-95 text-white font-extrabold px-3 py-2 rounded-xl shadow-md transition-all flex items-center gap-1.5 text-xs md:text-sm">
                        <span>🖊️</span> がめんでとく
                    </button>
                </div>
            </div>

            <!-- 設定項目 -->
            <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-3">
                <div>
                    <label class="block text-xs font-bold text-slate-600 mb-1">かたちの しゅるい</label>
                    <select id="print-type" onchange="generateWorksheet()" class="w-full bg-slate-50 border border-slate-300 rounded-xl p-2 font-bold text-xs md:text-sm">
                        <option value="single">1しゅるいを かぞえる</option>
                        <option value="multi" selected>いくつかの しゅるいから かぞえる</option>
                    </select>
                </div>

                <div>
                    <label class="block text-xs font-bold text-slate-600 mb-1">かずの はんい</label>
                    <select id="print-range" onchange="generateWorksheet()" class="w-full bg-slate-50 border border-slate-300 rounded-xl p-2 font-bold text-xs md:text-sm">
                        <option value="5">1 〜 5 まで</option>
                        <option value="10" selected>1 〜 10 まで</option>
                    </select>
                </div>

                <div>
                    <label class="block text-xs font-bold text-slate-600 mb-1">テーマ</label>
                    <select id="print-theme" onchange="generateWorksheet()" class="w-full bg-slate-50 border border-slate-300 rounded-xl p-2 font-bold text-xs md:text-sm">
                        <option value="mix">いろいろMIX 🍎⭐🚗</option>
                        <option value="fruit">たべもの 🍎🍌🍓</option>
                        <option value="shapes">ずけい 🔺🔵🟩</option>
                        <option value="animals">どうぶつ 🐶🐱🐰</option>
                    </select>
                </div>

                <div>
                    <label class="block text-xs font-bold text-slate-600 mb-1">もんだいのかず</label>
                    <select id="print-count" onchange="generateWorksheet()" class="w-full bg-slate-50 border border-slate-300 rounded-xl p-2 font-bold text-xs md:text-sm">
                        <option value="4">4 もんだい (ゆったり)</option>
                        <option value="6" selected>6 もんだい (ひょうじゅん)</option>
                    </select>
                </div>
            </div>

            <div class="pt-1 flex justify-end items-center">
                <button onclick="generateWorksheet()" class="bg-slate-800 hover:bg-slate-900 text-white font-bold px-3.5 py-1.5 rounded-xl text-xs shadow-sm transition-all">
                    🎲 べつの もんだいに いれかえる
                </button>
            </div>
        </div>

        <!-- デジタル書き込みモード用のコントロールバー -->
        <div id="digital-controls" class="hidden no-print no-print-screen bg-emerald-50 border-2 border-emerald-300 p-3 rounded-2xl flex flex-wrap items-center justify-between gap-3">
            <div class="flex items-center gap-2">
                <span class="text-xl">✍️</span>
                <span class="font-extrabold text-xs md:text-sm text-emerald-900">「がめんで とく」モード：すべての こたえのマスに ゆびやペンで ちょくせつ かきこめます！</span>
            </div>
            <div class="flex items-center gap-2">
                <button onclick="checkDigitalAnswers()" class="bg-emerald-600 hover:bg-emerald-700 text-white font-extrabold px-3 py-1.5 rounded-lg text-xs shadow">
                    ⭕ こたえあわせを ひょうじ
                </button>
                <button onclick="clearDigitalWorksheetCanvases()" class="bg-white border border-emerald-400 text-emerald-800 hover:bg-emerald-100 font-bold px-3 py-1.5 rounded-lg text-xs">
                    🗑️ かきこみを けす
                </button>
            </div>
        </div>

        <!-- 印刷・画面表示エリア (A4相当) -->
        <div id="print-area" class="print-area bg-white rounded-2xl shadow-md border-2 border-slate-200 p-6 md:p-8 max-w-4xl mx-auto my-0">
            
            <!-- ワークシート ヘッダー -->
            <div class="border-b-2 border-slate-800 pb-3 mb-4 flex justify-between items-end">
                <div>
                    <div class="text-xs font-bold text-slate-500">さんすう ワークシート</div>
                    <h1 class="text-2xl font-black text-slate-900 tracking-wider">かずを かぞえて すうじを かこう</h1>
                </div>
                <div class="flex items-center gap-4 md:gap-6 border-2 border-slate-800 rounded-xl px-3 md:px-4 py-1.5 md:py-2 bg-slate-50">
                    <div class="text-xs font-bold">なまえ：<span class="inline-block w-24 md:w-36 border-b border-slate-400"></span></div>
                    <div class="text-xs font-bold">てんすう：<span class="inline-block w-8 md:w-12 border-b border-slate-400"></span> てん</div>
                </div>
            </div>

            <!-- すうじのおてほん帯 -->
            <div class="bg-amber-50/80 border-2 border-amber-200 rounded-xl px-4 py-2 mb-6 flex items-center justify-between">
                <span class="text-xs font-extrabold text-amber-900">すうじのおてほん：</span>
                <span class="font-mono text-base md:text-xl font-black tracking-[0.3em] md:tracking-[0.5em] text-slate-700">1 2 3 4 5 6 7 8 9 10</span>
            </div>

            <!-- 問題グリッド -->
            <div id="worksheet-grid" class="worksheet-grid grid grid-cols-1 md:grid-cols-2 gap-6">
                <!-- JavaScriptでワークシート問題を自動生成 -->
            </div>

            <!-- ワークシート フッター -->
            <div class="mt-8 pt-4 border-t border-slate-300 text-center text-xs text-slate-400 font-bold flex justify-between">
                <span>かずとすうじの たのしいワーク</span>
                <span>つかったあとは「ぜんぶでいくつ？」とおはなししてみよう！</span>
            </div>

        </div>

    </section>

</main>

<!-- 純粋Canvas画像モーダル -->
<div id="image-modal" class="hidden fixed inset-0 bg-slate-900/80 backdrop-blur-md z-50 flex items-center justify-center p-4 overflow-y-auto">
    <div class="bg-white rounded-3xl max-w-2xl w-full p-5 shadow-2xl border-4 border-amber-300 space-y-3 my-auto">
        <div class="flex justify-between items-center border-b pb-2">
            <div>
                <h3 class="text-base md:text-lg font-extrabold text-slate-800 flex items-center gap-2">
                    <span>🖼️</span> ワークシートの がぞうが できました
                </h3>
                <p class="text-[11px] text-slate-500 font-bold">がぞうを ながおし や みぎクリックで ほぞんしてください。</p>
            </div>
            <button onclick="closeImageModal()" class="text-slate-400 hover:text-slate-600 font-black text-xl px-2">✕</button>
        </div>

        <div class="bg-slate-100 rounded-xl p-2 border border-slate-200 max-h-[65vh] overflow-auto flex justify-center items-center">
            <canvas id="pure-generated-canvas" class="max-w-full h-auto rounded shadow bg-white border"></canvas>
        </div>

        <div class="flex justify-end items-center gap-3 pt-1">
            <button onclick="closeImageModal()" class="bg-slate-700 hover:bg-slate-800 text-white font-bold px-6 py-2 rounded-xl text-xs md:text-sm">
                とじる
            </button>
        </div>
    </div>
</div>

<script>
        // ==========================================
        // アイコン・イラスト素材データ定義
        // ==========================================
        const ITEM_SETS = {
            fruit: [
                { name: 'りんご', icon: '🍎' },
                { name: 'みかん', icon: '🍊' },
                { name: 'ぶどう', icon: '🍇' },
                { name: 'いちご', icon: '🍓' },
                { name: 'ばなな', icon: '🍌' }
            ],
            shapes: [
                { name: 'あかいしかく', icon: '🟥' },
                { name: 'あおいまる', icon: '🔵' },
                { name: 'きいろのほし', icon: '⭐' },
                { name: 'みどりのさんかく', icon: '🔺' },
                { name: 'ハート', icon: '💖' }
            ],
            animals: [
                { name: 'いぬ', icon: '🐶' },
                { name: 'ねこ', icon: '🐱' },
                { name: 'うさぎ', icon: '🐰' },
                { name: 'ひよこ', icon: '🐥' },
                { name: 'くま', icon: '🐻' }
            ],
            vehicles: [
                { name: 'くるま', icon: '🚗' },
                { name: 'ひこうき', icon: '✈️' },
                { name: 'でんしゃ', icon: '🚃' },
                { name: 'ふね', icon: '🚢' },
                { name: 'バス', icon: '🚌' }
            ]
        };

        // アプリの状態
        let currentAppTarget = null;
        let currentTargetCount = 0;
        let userTappedCount = 0;
        let appItemsList = [];
        let selectedPracticeNum = 1;
        let isDigitalMode = false;
        let worksheetQuestionsData = [];

        // WEB AUDIO API 効果音生成器
        const AudioCtx = window.AudioContext || window.webkitAudioContext;
        let audioCtx = null;

        function playSound(type) {
            try {
                if (!audioCtx) audioCtx = new AudioCtx();
                if (audioCtx.state === 'suspended') audioCtx.resume();

                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.connect(gain);
                gain.connect(audioCtx.destination);

                const now = audioCtx.currentTime;

                if (type === 'tap') {
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(400 + (userTappedCount * 50), now);
                    osc.frequency.exponentialRampToValueAtTime(800, now + 0.08);
                    gain.gain.setValueAtTime(0.3, now);
                    gain.gain.linearRampToValueAtTime(0, now + 0.08);
                    osc.start(now);
                    osc.stop(now + 0.08);
                } else if (type === 'correct') {
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(523.25, now);
                    osc.frequency.setValueAtTime(659.25, now + 0.12);
                    gain.gain.setValueAtTime(0.3, now);
                    gain.gain.linearRampToValueAtTime(0, now + 0.35);
                    osc.start(now);
                    osc.stop(now + 0.35);
                } else if (type === 'wrong') {
                    osc.type = 'sawtooth';
                    osc.frequency.setValueAtTime(220, now);
                    osc.frequency.linearRampToValueAtTime(180, now + 0.2);
                    gain.gain.setValueAtTime(0.2, now);
                    gain.gain.linearRampToValueAtTime(0, now + 0.2);
                    osc.start(now);
                    osc.stop(now + 0.2);
                }
            } catch (e) {
                console.log('Audio init error:', e);
            }
        }

        // ==========================================
        // モード切替制御
        // ==========================================
        function switchMode(mode) {
            const appSection = document.getElementById('section-app');
            const printSection = document.getElementById('section-print');
            const btnApp = document.getElementById('btn-mode-app');
            const btnPrint = document.getElementById('btn-mode-print');

            if (mode === 'app') {
                appSection.classList.remove('hidden');
                printSection.classList.add('hidden');
                btnApp.className = 'px-3 py-1.5 rounded-lg font-bold transition-all bg-emerald-500 text-white shadow-sm';
                btnPrint.className = 'px-3 py-1.5 rounded-lg font-bold transition-all text-slate-600 hover:text-slate-900';
            } else {
                appSection.classList.add('hidden');
                printSection.classList.remove('hidden');
                btnPrint.className = 'px-3 py-1.5 rounded-lg font-bold transition-all bg-indigo-600 text-white shadow-sm';
                btnApp.className = 'px-3 py-1.5 rounded-lg font-bold transition-all text-slate-600 hover:text-slate-900';
                generateWorksheet();
            }
        }

        function initAppGame() {
            document.getElementById('app-success-overlay').classList.add('hidden');
            clearCanvas();
            userTappedCount = 0;

            const level = parseInt(document.getElementById('app-level-select').value);
            const container = document.getElementById('items-container');
            container.innerHTML = '';

            const allCategories = Object.values(ITEM_SETS);
            const flatItems = allCategories.flat();

            let maxCount = (level === 1) ? 5 : 10;
            let targetItem = flatItems[Math.floor(Math.random() * flatItems.length)];
            currentAppTarget = targetItem;

            currentTargetCount = Math.floor(Math.random() * maxCount) + 1;
            let displayItems = [];

            for (let i = 0; i < currentTargetCount; i++) {
                displayItems.push({ ...targetItem, isTarget: true, id: i });
            }

            if (level === 3) {
                let otherItems = flatItems.filter(item => item.name !== targetItem.name);
                let dummyTotal = Math.floor(Math.random() * 4) + 2;
                for (let i = 0; i < dummyTotal; i++) {
                    let dummy = otherItems[Math.floor(Math.random() * otherItems.length)];
                    displayItems.push({ ...dummy, isTarget: false, id: 100 + i });
                }
            }

            displayItems.sort(() => Math.random() - 0.5);
            appItemsList = displayItems;

            document.getElementById('target-icon-display').textContent = targetItem.icon;

            displayItems.forEach((item, index) => {
                const btn = document.createElement('button');
                btn.className = "w-12 h-12 md:w-16 md:h-16 bg-white rounded-xl shadow-md border-2 border-slate-200 flex items-center justify-center text-2xl md:text-3xl hover:scale-105 active:scale-95 transition-all relative group";
                btn.innerHTML = `
                    <span>${item.icon}</span>
                    <span id="badge-${index}" class="hidden absolute -top-1.5 -right-1.5 bg-yellow-400 text-slate-900 font-extrabold w-5 h-5 rounded-full text-xs flex items-center justify-center border-2 border-white shadow-sm animate-bounce">0</span>
                `;

                btn.onclick = () => {
                    if (!btn.classList.contains('counted')) {
                        userTappedCount++;
                        playSound('tap');
                        const badge = document.getElementById(`badge-${index}`);
                        badge.textContent = userTappedCount;
                        badge.classList.remove('hidden');
                        btn.classList.add('counted', 'ring-4', 'ring-yellow-300', 'bg-yellow-50');
                    }
                };

                container.appendChild(btn);
            });

            setupNumberButtons(maxCount);
        }

        function setupNumberButtons(max) {
            const grid = document.getElementById('number-buttons-grid');
            grid.innerHTML = '';
            for (let i = 1; i <= 10; i++) {
                const btn = document.createElement('button');
                btn.className = "h-9 bg-white border-2 border-amber-300 hover:bg-amber-100 text-amber-900 font-black rounded-lg text-base shadow-sm active:scale-95 transition-transform flex items-center justify-center";
                btn.textContent = i;
                btn.onclick = () => checkAnswer(i);
                grid.appendChild(btn);
            }
        }

        function switchAppSubTab(tab) {
            const countTab = document.getElementById('app-subtab-count');
            const practiceTab = document.getElementById('app-subtab-practice');
            const btnCount = document.getElementById('btn-subtab-count');
            const btnPractice = document.getElementById('btn-subtab-practice');

            if (tab === 'count') {
                countTab.classList.remove('hidden');
                practiceTab.classList.add('hidden');
                btnCount.className = "px-4 py-1.5 rounded-xl transition-all bg-white text-emerald-700 shadow-sm";
                btnPractice.className = "px-4 py-1.5 rounded-xl transition-all text-slate-600 hover:text-slate-900";
            } else {
                countTab.classList.add('hidden');
                practiceTab.classList.remove('hidden');
                btnPractice.className = "px-4 py-1.5 rounded-xl transition-all bg-white text-emerald-700 shadow-sm";
                btnCount.className = "px-4 py-1.5 rounded-xl transition-all text-slate-600 hover:text-slate-900";
            }
        }

        function checkAnswer(userNum) {
            if (userNum === currentTargetCount) {
                playSound('correct');
                triggerConfetti();
                const overlay = document.getElementById('app-success-overlay');
                const msg = document.getElementById('app-success-msg');
                msg.textContent = `${currentAppTarget.icon} は ぜんぶで ${currentTargetCount} つ でした！`;
                overlay.classList.remove('hidden');
            } else {
                playSound('wrong');
                const hintContainer = document.getElementById('items-container');
                hintContainer.classList.add('pop-anim');
                setTimeout(() => hintContainer.classList.remove('pop-anim'), 300);
            }
        }

        function triggerConfetti() {
            const colors = ['#FF6B6B', '#4ECDC4', '#FFE66D', '#1A535C', '#FF9F1C'];
            for (let i = 0; i < 30; i++) {
                const piece = document.createElement('div');
                piece.className = 'confetti-piece';
                piece.style.left = Math.random() * 100 + 'vw';
                piece.style.top = '-10px';
                piece.style.backgroundColor = colors[Math.floor(Math.random() * colors.length)];
                piece.style.borderRadius = Math.random() > 0.5 ? '50%' : '2px';
                document.body.appendChild(piece);
                setTimeout(() => piece.remove(), 2500);
            }
        }

        let isDrawing = false;
        let canvas, ctx;
        let practiceCanvas, practiceCtx;

        function initCanvases() {
            canvas = document.getElementById('handwriting-canvas');
            if (canvas) {
                ctx = canvas.getContext('2d');
                setupCanvasEvents(canvas, ctx);
            }

            practiceCanvas = document.getElementById('practice-canvas');
            if (practiceCanvas) {
                practiceCtx = practiceCanvas.getContext('2d');
                setupCanvasEvents(practiceCanvas, practiceCtx);
            }

            setupPracticeSelector();
        }

        function setupCanvasEvents(cvs, context) {
            context.lineWidth = 10;
            context.lineCap = 'round';
            context.lineJoin = 'round';
            context.strokeStyle = '#312E81';

            const startDraw = (e) => {
                isDrawing = true;
                const rect = cvs.getBoundingClientRect();
                const clientX = e.touches ? e.touches[0].clientX : e.clientX;
                const clientY = e.touches ? e.touches[0].clientY : e.clientY;
                context.beginPath();
                context.moveTo(clientX - rect.left, clientY - rect.top);
            };

            const draw = (e) => {
                if (!isDrawing) return;
                e.preventDefault();
                const rect = cvs.getBoundingClientRect();
                const clientX = e.touches ? e.touches[0].clientX : e.clientX;
                const clientY = e.touches ? e.touches[0].clientY : e.clientY;
                context.lineTo(clientX - rect.left, clientY - rect.top);
                context.stroke();
            };

            const stopDraw = () => { isDrawing = false; };

            cvs.addEventListener('mousedown', startDraw);
            cvs.addEventListener('mousemove', draw);
            cvs.addEventListener('mouseup', stopDraw);
            cvs.addEventListener('mouseleave', stopDraw);

            cvs.addEventListener('touchstart', startDraw);
            cvs.addEventListener('touchmove', draw);
            cvs.addEventListener('touchend', stopDraw);
        }

        function clearCanvas() {
            if (ctx && canvas) ctx.clearRect(0, 0, canvas.width, canvas.height);
        }

        function clearPracticeCanvas() {
            if (practiceCtx && practiceCanvas) practiceCtx.clearRect(0, 0, practiceCanvas.width, practiceCanvas.height);
        }

        function checkAnswerFromCanvas() {
            if (userTappedCount > 0) {
                checkAnswer(userTappedCount);
            } else {
                checkAnswer(currentTargetCount);
            }
        }

        function setupPracticeSelector() {
            const container = document.getElementById('practice-num-selector');
            if (!container) return;
            container.innerHTML = '';
            for (let i = 1; i <= 10; i++) {
                const btn = document.createElement('button');
                btn.className = `w-9 h-9 rounded-xl font-extrabold text-sm border-2 transition-all ${
                    i === selectedPracticeNum 
                    ? 'bg-emerald-500 text-white border-emerald-600 shadow-sm' 
                    : 'bg-slate-100 text-slate-700 border-slate-200 hover:bg-slate-200'
                }`;
                btn.textContent = i;
                btn.onclick = () => selectPracticeNum(i);
                container.appendChild(btn);
            }
        }

        function selectPracticeNum(num) {
            selectedPracticeNum = num;
            document.getElementById('practice-guide-num').textContent = num;
            document.getElementById('practice-bg-hint').textContent = num;
            clearPracticeCanvas();
            setupPracticeSelector();
        }

        function generateWorksheet() {
            const type = document.getElementById('print-type').value;
            const range = parseInt(document.getElementById('print-range').value);
            const themeKey = document.getElementById('print-theme').value;
            const count = parseInt(document.getElementById('print-count').value);

            const grid = document.getElementById('worksheet-grid');
            grid.innerHTML = '';
            worksheetQuestionsData = [];

            let pool = (themeKey === 'mix') ? Object.values(ITEM_SETS).flat() : (ITEM_SETS[themeKey] || ITEM_SETS.fruit);

            for (let q = 1; q <= count; q++) {
                const qData = createWorksheetQuestion(q, type, range, pool);
                worksheetQuestionsData.push(qData);
                grid.appendChild(qData.element);
            }

            if (isDigitalMode) {
                setupDigitalWorksheetCanvases();
            }
        }

        function createWorksheetQuestion(qIndex, type, maxRange, pool) {
        const card = document.createElement('div');
        card.className = "worksheet-item bg-slate-50 border-2 border-slate-300 rounded-2xl p-4 flex flex-col justify-between relative";

        const targetItem = pool[Math.floor(Math.random() * pool.length)];
        const answerCount = Math.floor(Math.random() * maxRange) + 1;

        let itemsToDisplay = [];
        for (let i = 0; i < answerCount; i++) {
            itemsToDisplay.push(targetItem);
        }

        if (type === 'multi') {
            const otherPool = pool.filter(p => p.name !== targetItem.name);
            const dummyCount = Math.floor(Math.random() * 4) + 1;
            for (let i = 0; i < dummyCount; i++) {
                itemsToDisplay.push(otherPool[Math.floor(Math.random() * otherPool.length)]);
            }
        }

        itemsToDisplay.sort(() => Math.random() - 0.5);

        let itemsHtml = itemsToDisplay.map(item => `<span class="text-3xl">${item.icon}</span>`).join('');

        card.innerHTML = `
            <div>
                <div class="flex justify-between items-center mb-2 border-b border-slate-200 pb-1">
                    <span class="text-sm font-extrabold text-slate-700">もん ${qIndex}</span>
                    <div class="text-sm font-bold text-slate-800 flex items-center gap-1">
                        <span>${targetItem.icon}</span> は ぜんぶで いくつ？
                    </div>
                </div>

                <div class="min-h-[110px] bg-white rounded-xl p-3 border border-slate-200 flex flex-wrap items-center justify-center gap-3">
                    ${itemsHtml}
                </div>
            </div>

            <div class="mt-4 flex items-center justify-end bg-white p-2.5 rounded-xl border border-slate-200 relative">
                <div class="flex items-center gap-2">
                    <span class="text-xs font-black text-slate-700">こたえ：</span>
                    <div id="answer-box-${qIndex}" class="w-14 h-14 border-2 border-slate-800 rounded-xl flex items-center justify-center font-bold text-2xl text-slate-800 relative bg-white grid-guide">
                        <span id="answer-text-${qIndex}" class="hidden font-mono text-emerald-600 font-extrabold text-2xl">${answerCount}</span>
                    </div>
                </div>
            </div>
        `;

        return {
            index: qIndex,
            target: targetItem,
            answer: answerCount,
            items: itemsToDisplay,
            element: card
        };
    }

    // デジタル手書きモードの切り替え
    function toggleDigitalWorksheetMode() {
        isDigitalMode = !isDigitalMode;
        const btn = document.getElementById('btn-toggle-digital');
        const controls = document.getElementById('digital-controls');

        if (isDigitalMode) {
            btn.className = "bg-emerald-800 text-white font-extrabold px-3 py-2 rounded-xl shadow-md flex items-center gap-1.5 text-xs md:text-sm ring-2 ring-emerald-300";
            controls.classList.remove('hidden');
            setupDigitalWorksheetCanvases();
        } else {
            btn.className = "bg-emerald-600 hover:bg-emerald-700 text-white font-extrabold px-3 py-2 rounded-xl shadow-md flex items-center gap-1.5 text-xs md:text-sm";
            controls.classList.add('hidden');
            generateWorksheet();
        }
    }

    function setupDigitalWorksheetCanvases() {
        worksheetQuestionsData.forEach(q => {
            const box = document.getElementById(`answer-box-${q.index}`);
            if (!box) return;

            box.innerHTML = `<canvas id="digital-cvs-${q.index}" width="56" height="56" class="cursor-crosshair touch-none rounded-lg"></canvas><span id="answer-text-${q.index}" class="hidden font-mono text-emerald-600 font-extrabold text-3xl absolute inset-0 flex items-center justify-center bg-yellow-100/90 rounded-lg"><sup>⭕</sup>${q.answer}</span>`;

            const cvs = document.getElementById(`digital-cvs-${q.index}`);
            const context = cvs.getContext('2d');
            setupCanvasEvents(cvs, context);
        });
    }

    function clearDigitalWorksheetCanvases() {
        worksheetQuestionsData.forEach(q => {
            const cvs = document.getElementById(`digital-cvs-${q.index}`);
            if (cvs) {
                const ctx = cvs.getContext('2d');
                ctx.clearRect(0, 0, cvs.width, cvs.height);
            }
            const ansText = document.getElementById(`answer-text-${q.index}`);
            if (ansText) ansText.classList.add('hidden');
        });
    }

    function checkDigitalAnswers() {
        playSound('correct');
        worksheetQuestionsData.forEach(q => {
            const ansText = document.getElementById(`answer-text-${q.index}`);
            if (ansText) ansText.classList.remove('hidden');
        });
    }

    // Canvas画像生成処理
    function generatePureCanvasImage() {
        const cvs = document.getElementById('pure-generated-canvas');
        if (!cvs) return;

        // A4縦比率 (800 x 1130 px)
        cvs.width = 800;
        cvs.height = 1130;
        const ctx = cvs.getContext('2d');

        // 背景描画
        ctx.fillStyle = '#FFFFFF';
        ctx.fillRect(0, 0, cvs.width, cvs.height);

        // ヘッダー枠
        ctx.fillStyle = '#0F172A';
        ctx.font = 'bold 14px sans-serif';
        ctx.fillText('さんすう ワークシート', 40, 45);

        ctx.font = 'bold 28px sans-serif';
        ctx.fillText('かずを かぞえて すうじを かこう', 40, 80);

        // 名前・点数欄
        ctx.lineWidth = 2;
        ctx.strokeStyle = '#0F172A';
        ctx.strokeRect(500, 30, 260, 55);
        ctx.font = 'bold 14px sans-serif';
        ctx.fillText('なまえ：________________', 510, 65);

        // 区切り線
        ctx.beginPath();
        ctx.moveTo(40, 100);
        ctx.lineTo(760, 100);
        ctx.stroke();

        // 数字のおてほん帯
        ctx.fillStyle = '#FEF3C7';
        ctx.fillRect(40, 110, 720, 36);
        ctx.strokeStyle = '#FDE68A';
        ctx.strokeRect(40, 110, 720, 36);
        ctx.fillStyle = '#78350F';
        ctx.font = 'bold 13px sans-serif';
        ctx.fillText('すうじのおてほん：', 55, 133);
        ctx.fillStyle = '#334155';
        ctx.font = 'bold 18px monospace';
        ctx.fillText('1   2   3   4   5   6   7   8   9   10', 210, 133);

        // 問題描画 (2列レイアウト)
        const count = worksheetQuestionsData.length;
        const colWidth = 355;
        const rowHeight = count > 4 ? 280 : 410;
        const startY = 160;

        worksheetQuestionsData.forEach((q, idx) => {
            const col = idx % 2;
            const row = Math.floor(idx / 2);

            const x = 40 + col * (colWidth + 10);
            const y = startY + row * (rowHeight + 10);

            // カード外枠
            ctx.fillStyle = '#F8FAFC';
            ctx.fillRect(x, y, colWidth, rowHeight);
            ctx.strokeStyle = '#CBD5E1';
            ctx.lineWidth = 2;
            ctx.strokeRect(x, y, colWidth, rowHeight);

            // 問題見出し
            ctx.fillStyle = '#1E293B';
            ctx.font = 'bold 16px sans-serif';
            ctx.fillText(`もん ${q.index}   ${q.target.icon} は ぜんぶで いくつ？`, x + 15, y + 30);

            // イラストボックス
            const boxY = y + 45;
            const boxH = rowHeight - 110;
            ctx.fillStyle = '#FFFFFF';
            ctx.fillRect(x + 15, boxY, colWidth - 30, boxH);
            ctx.strokeStyle = '#E2E8F0';
            ctx.strokeRect(x + 15, boxY, colWidth - 30, boxH);

            // イラストの配置
            ctx.font = '30px sans-serif';
            const items = q.items;
            const itemsPerRow = 4;
            items.forEach((item, itemIdx) => {
                const ix = x + 30 + (itemIdx % itemsPerRow) * 70;
                const iy = boxY + 40 + Math.floor(itemIdx / itemsPerRow) * 45;
                ctx.fillText(item.icon, ix, iy);
            });

            // 解答領域
            const ansY = y + rowHeight - 55;
            ctx.fillStyle = '#0F172A';
            ctx.font = 'bold 14px sans-serif';
            ctx.fillText('こたえ：', x + 210, ansY + 25);

            // 解答マス
            ctx.strokeStyle = '#0F172A';
            ctx.lineWidth = 2;
            ctx.strokeRect(x + 265, ansY - 5, 45, 45);
        });

        // フッター
        ctx.fillStyle = '#94A3B8';
        ctx.font = 'bold 12px sans-serif';
        ctx.fillText('かずとすうじの たのしいワーク', 40, 1100);
        ctx.fillText('つかったあとは「ぜんぶでいくつ？」とおはなししてみよう！', 410, 1100);

        // モーダル表示
        document.getElementById('image-modal').classList.remove('hidden');
    }

    function closeImageModal() {
        document.getElementById('image-modal').classList.add('hidden');
    }

    // PDF保存処理
    function saveAsPDF(event) {
        const printArea = document.getElementById('print-area');
        if (!printArea) return;

        const btn = event ? event.currentTarget : null;
        const originalText = btn ? btn.innerHTML : '';
        if (btn) btn.innerHTML = '<span>⏳</span> つくっています...';

        const opt = {
            margin:       [8, 8, 8, 8],
            filename:     'かずとすうじのワークシート.pdf',
            image:        { type: 'jpeg', quality: 0.98 },
            html2canvas:  { scale: 2, useCORS: true, logging: false },
            jsPDF:        { unit: 'mm', format: 'a4', orientation: 'portrait' }
        };

        if (typeof html2pdf !== 'undefined') {
            html2pdf().set(opt).from(printArea).save().then(() => {
                if (btn) btn.innerHTML = originalText;
            }).catch(err => {
                console.warn('PDF creation failed:', err);
                if (btn) btn.innerHTML = originalText;
            });
        } else {
            if (btn) btn.innerHTML = originalText;
        }
    }

    // 初期化処理
    window.onload = function() {
        initAppGame();
        initCanvases();
    };
</script>
</body>
</html>
