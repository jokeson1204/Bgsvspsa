<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover" />
  <meta name="color-scheme" content="dark" />
  <title>BGS 評分模擬器 (Beckett Grading Simulator)</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&family=Noto+Sans+TC:wght@400;500;700;900&display=swap" rel="stylesheet" />
  <style>
    body {
      font-family: 'Inter', 'Noto Sans TC', sans-serif;
      background-color: #0b0d12;
      color: #f3f4f6;
      background-image: 
        radial-gradient(circle at 15% 15%, rgba(212, 175, 55, 0.08) 0%, transparent 45%),
        radial-gradient(circle at 85% 80%, rgba(148, 163, 184, 0.07) 0%, transparent 45%);
      overflow-x: hidden;
      padding-bottom: env(safe-area-inset-bottom);
      -webkit-tap-highlight-color: transparent;
    }

    /* 針對 iPhone 16 動態島與底部 Home 指示條的 Safe Area 適配 */
    .safe-top {
      padding-top: max(0.625rem, env(safe-area-inset-top));
    }

    /* 隱藏手機橫向滑動列捲軸但保留滑動功能 */
    .no-scrollbar::-webkit-scrollbar {
      display: none;
    }
    .no-scrollbar {
      -ms-overflow-style: none;
      scrollbar-width: none;
    }

    /* 金屬標籤漸層與光澤效果 */
    .slab-black {
      background: linear-gradient(135deg, #1c1c1e 0%, #09090b 50%, #27272a 100%);
      border: 2px solid #d4af37;
      box-shadow: 0 12px 35px -5px rgba(212, 175, 55, 0.25), inset 0 1px 1px rgba(255, 255, 255, 0.2);
      color: #f5d061;
    }

    .slab-gold {
      background: linear-gradient(135deg, #f6e27a 0%, #d4af37 45%, #aa7c11 85%, #e5c158 100%);
      border: 2px solid #fef08a;
      box-shadow: 0 12px 35px -5px rgba(212, 175, 55, 0.35), inset 0 1px 2px rgba(255, 255, 255, 0.65);
      color: #18181b;
    }

    .slab-silver {
      background: linear-gradient(135deg, #f1f5f9 0%, #cbd5e1 45%, #94a3b8 85%, #e2e8f0 100%);
      border: 2px solid #f8fafc;
      box-shadow: 0 12px 35px -5px rgba(148, 163, 184, 0.25), inset 0 1px 2px rgba(255, 255, 255, 0.8);
      color: #0f172a;
    }

    /* 經典 PSA 紅框白底標籤質感 */
    .slab-psa {
      background: linear-gradient(180deg, #ffffff 0%, #f8fafc 100%);
      border: 3px solid #dc2626;
      box-shadow: 0 12px 30px -5px rgba(220, 38, 38, 0.22), inset 0 0 0 1px rgba(220, 38, 38, 0.25);
      color: #0f172a;
    }

    /* 細微髮絲紋質感 */
    .brushed-overlay {
      background-image: repeating-linear-gradient(
        90deg,
        rgba(255, 255, 255, 0.03) 0px,
        rgba(255, 255, 255, 0.03) 1px,
        transparent 1px,
        transparent 3px
      );
    }

    /* 自訂捲軸 */
    ::-webkit-scrollbar {
      width: 8px;
      height: 8px;
    }
    ::-webkit-scrollbar-track {
      background: #111827;
    }
    ::-webkit-scrollbar-thumb {
      background: #374151;
      border-radius: 4px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #4b5563;
    }
  </style>
</head>
<body class="m-0 p-0 antialiased selection:bg-amber-500/30 selection:text-amber-200">

  <!-- 頂部導覽列：適配 iPhone 16 動態島安全距離，精簡垂直高度 -->
  <header class="w-full border-b border-slate-800/80 bg-slate-950/95 backdrop-blur-md sticky top-0 z-30 safe-top">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 pb-2.5 sm:py-3.5 flex items-center justify-between gap-2">
      <div class="flex items-center gap-2.5 min-w-0">
        <div class="w-8 h-8 sm:w-9 sm:h-9 shrink-0 rounded-xl bg-gradient-to-br from-amber-300 via-amber-500 to-amber-700 flex items-center justify-center text-slate-950 font-black text-base sm:text-lg shadow-lg shadow-amber-500/20">
          B
        </div>
        <div class="min-w-0">
          <h1 class="text-[15px] sm:text-xl font-extrabold tracking-tight text-white flex items-center gap-1.5 truncate">
            BGS 評分模擬器
            <span class="hidden md:inline-block text-xs font-semibold px-2.5 py-0.5 rounded-full bg-amber-500/15 text-amber-400 border border-amber-500/30">
              Beckett Grading Simulator
            </span>
            <span class="inline-block text-[11px] sm:text-xs font-semibold px-2 py-0.5 rounded-full bg-rose-500/15 text-rose-400 border border-rose-500/30">
              + PSA 對照
            </span>
          </h1>
          <p class="hidden sm:block text-xs text-slate-400 leading-snug mt-0.5">支援四項細分演算、木桶效應判定、卡盒標籤預覽與 PSA 跨評等級對照</p>
        </div>
      </div>
      <div class="shrink-0 flex items-center gap-1.5 text-[11px] sm:text-xs text-slate-400">
        <span class="inline-flex items-center gap-1.5 px-2.5 py-1 rounded-lg bg-slate-900 border border-slate-800">
          <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span>
          <span class="hidden sm:inline">BGS / PSA 雙系統即時演算</span>
          <span class="sm:hidden">即時演算</span>
        </span>
      </div>
    </div>
  </header>

  <!-- 主要操作與展示區 -->
  <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 pt-4 pb-10 sm:py-6 space-y-6 sm:space-y-8">

    <!-- 核心互動區：左輸入 / 右預覽 -->
    <div class="grid grid-cols-1 lg:grid-cols-12 gap-5 sm:gap-8 items-start">

      <!-- 左側：分數控制面板 (佔 7 欄) -->
      <section class="lg:col-span-7 bg-slate-900/75 border border-slate-800 rounded-2xl p-4 sm:p-6 shadow-2xl backdrop-blur-sm space-y-4 sm:space-y-5">
        <div class="flex items-center justify-between gap-2 border-b border-slate-800 pb-3">
          <div>
            <h2 class="text-base sm:text-lg font-bold text-white flex items-center gap-2">
              <svg class="w-5 h-5 shrink-0 text-amber-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6V4m0 2a2 2 0 100 4m0-4a2 2 0 110 4m-6 8a2 2 0 100-4m0 4a2 2 0 110-4m0 4v2m0-6V4m6 6v10m6-2a2 2 0 100-4m0 4a2 2 0 110-4m0 4v2m0-6V4" />
              </svg>
              四項細分分數設定（Subgrades）
            </h2>
            <p class="text-[11px] sm:text-xs text-slate-400 mt-0.5">點選按鈕調整各項卡況分數（支援 7 至 10 分）</p>
          </div>
          <button type="button" onclick="applyPreset(9.5, 9.5, 9.5, 9.5)" class="shrink-0 text-xs font-semibold px-3 py-2 rounded-xl bg-slate-800 hover:bg-slate-700 active:scale-95 text-slate-200 border border-slate-700 transition">
            重設全 9.5
          </button>
        </div>

        <!-- 常見組合一鍵代入快捷列（iPhone 16 支援單行橫向滑動，節省垂直空間） -->
        <div class="space-y-2">
          <div class="flex items-center justify-between">
            <span class="text-xs font-semibold uppercase tracking-wider text-slate-400">常見組合一鍵代入</span>
            <span class="text-[11px] text-slate-500">左右滑動選擇組合</span>
          </div>
          <div class="flex sm:flex-wrap gap-2 overflow-x-auto no-scrollbar pb-1 -mx-1 px-1">
            <button type="button" onclick="applyPreset(10, 10, 10, 10)" class="shrink-0 px-3 py-2 rounded-xl text-xs font-semibold bg-zinc-950 text-amber-400 border border-amber-500/60 hover:bg-amber-500/10 active:scale-95 transition shadow-sm">
              黑標 10 (10/10/10/10)
            </button>
            <button type="button" onclick="applyPreset(10, 10, 10, 9.5)" class="shrink-0 px-3 py-2 rounded-xl text-xs font-semibold bg-amber-500/15 text-amber-300 border border-amber-400/50 hover:bg-amber-500/25 active:scale-95 transition">
              金標 10 (10/10/10/9.5)
            </button>
            <button type="button" onclick="applyPreset(10, 10, 9.5, 9.5)" class="shrink-0 px-3 py-2 rounded-xl text-xs font-semibold bg-amber-500/10 text-amber-200 border border-amber-500/30 hover:bg-amber-500/20 active:scale-95 transition">
              強 9.5 Quad+ (10/10/9.5/9.5)
            </button>
            <button type="button" onclick="applyPreset(9.5, 9.5, 9.5, 9.5)" class="shrink-0 px-3 py-2 rounded-xl text-xs font-semibold bg-amber-500/10 text-amber-200 border border-amber-500/30 hover:bg-amber-500/20 active:scale-95 transition">
              標準 9.5 True Gem (全 9.5)
            </button>
            <button type="button" onclick="applyPreset(9.5, 9.5, 9.5, 9)" class="shrink-0 px-3 py-2 rounded-xl text-xs font-semibold bg-amber-500/10 text-amber-200 border border-amber-500/30 hover:bg-amber-500/20 active:scale-95 transition">
              弱 9.5 Min Gem (9.5/9.5/9.5/9)
            </button>
            <button type="button" onclick="applyPreset(10, 10, 9, 9)" class="shrink-0 px-3 py-2 rounded-xl text-xs font-semibold bg-slate-800 text-slate-200 border border-slate-600 hover:bg-slate-700 active:scale-95 transition">
              雙 9 降級 (10/10/9/9)
            </button>
            <button type="button" onclick="applyPreset(10, 10, 10, 8.5)" class="shrink-0 px-3 py-2 rounded-xl text-xs font-semibold bg-slate-800 text-slate-200 border border-slate-600 hover:bg-slate-700 active:scale-95 transition">
              單項 8.5 壓制 (10/10/10/8.5)
            </button>
          </div>
        </div>

        <!-- 四大細項輸入容器 -->
        <div id="subgradesContainer" class="space-y-3 sm:space-y-4 pt-1">
          <!-- 由 JS 動態渲染四個維度的控制列 -->
        </div>
      </section>

      <!-- 右側：即時卡盒標籤預覽 & 評分邏輯解析 (佔 5 欄) -->
      <section class="lg:col-span-5 space-y-5 sm:space-y-6">

        <!-- 模擬 BGS 卡盒頂部標籤 (Slab Label) -->
        <div class="bg-slate-900/75 border border-slate-800 rounded-2xl p-4 sm:p-6 shadow-2xl">
          <div class="flex items-center justify-between mb-3.5 sm:mb-4">
            <span class="text-xs font-bold uppercase tracking-wider text-slate-400 flex items-center gap-1.5">
              <svg class="w-4 h-4 text-amber-400" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
              BGS 鑑定卡盒標籤即時預覽
            </span>
            <span id="subTierPill" class="text-xs font-bold px-2.5 py-0.5 rounded-full bg-amber-500/20 text-amber-300 border border-amber-500/40">
              True Gem
            </span>
          </div>

          <!-- 模擬塑膠鑑定殼外框 -->
          <div class="p-2.5 sm:p-3 rounded-xl bg-slate-950/90 border border-slate-700/80 shadow-inner">
            <!-- 金屬標籤本體 -->
            <div id="slabLabelCard" class="slab-gold relative overflow-hidden rounded-lg p-3.5 sm:p-4 transition-all duration-300">
              <div class="brushed-overlay absolute inset-0 pointer-events-none"></div>

              <!-- 標籤頂部資訊列 -->
              <div class="relative flex items-center justify-between border-b border-current/20 pb-2 mb-2.5">
                <div class="flex items-center gap-2">
                  <span class="font-black tracking-tighter text-sm sm:text-base px-1.5 py-0.5 rounded bg-black/15">BGS</span>
                  <div class="leading-tight">
                    <div class="text-[9px] sm:text-[10px] font-extrabold tracking-widest uppercase opacity-90">BECKETT GRADING SERVICES</div>
                    <div class="text-[10px] sm:text-[11px] font-bold opacity-80">AUTHENTIC COLLECTIBLE CARD</div>
                  </div>
                </div>
                <div class="text-right text-[9px] sm:text-[10px] font-mono font-bold opacity-75">
                  #0016889527
                </div>
              </div>

              <!-- 標籤主體：左側四項細分 + 右側總分框（針對 iPhone 16 393px 寬度優化間距避免擠壓） -->
              <div class="relative grid grid-cols-12 gap-2 sm:gap-3 items-center">
                <!-- 左側：四項細分兩欄排列 -->
                <div class="col-span-8 sm:col-span-7 grid grid-cols-2 gap-x-2 sm:gap-x-3 gap-y-1.5 text-[11px] sm:text-xs font-extrabold tracking-tight bg-black/10 rounded-md p-2 sm:p-2.5">
                  <div class="flex items-center justify-between gap-1">
                    <span class="opacity-85 truncate">CENTERING</span>
                    <span id="labelC" class="font-mono text-xs sm:text-sm shrink-0">9.5</span>
                  </div>
                  <div class="flex items-center justify-between gap-1">
                    <span class="opacity-85 truncate">EDGES</span>
                    <span id="labelE" class="font-mono text-xs sm:text-sm shrink-0">9.5</span>
                  </div>
                  <div class="flex items-center justify-between gap-1">
                    <span class="opacity-85 truncate">CORNERS</span>
                    <span id="labelCo" class="font-mono text-xs sm:text-sm shrink-0">9.5</span>
                  </div>
                  <div class="flex items-center justify-between gap-1">
                    <span class="opacity-85 truncate">SURFACE</span>
                    <span id="labelS" class="font-mono text-xs sm:text-sm shrink-0">9.5</span>
                  </div>
                </div>

                <!-- 右側：總分與等級名稱 -->
                <div class="col-span-4 sm:col-span-5 flex flex-col items-center justify-center bg-black/15 rounded-md py-2 px-1.5 sm:px-2 text-center border border-current/20">
                  <span id="labelGradeTitle" class="text-[9px] sm:text-[10px] font-black tracking-wider uppercase leading-none mb-1">
                    GEM MINT
                  </span>
                  <span id="labelFinalScore" class="text-3xl sm:text-4xl font-black leading-none tracking-tighter">
                    9.5
                  </span>
                  <span id="labelColorTag" class="mt-1 text-[8px] sm:text-[9px] font-bold uppercase tracking-wider opacity-85">
                    GOLD LABEL
                  </span>
                </div>
              </div>
            </div>
          </div>

          <!-- 算術平均 vs 實際總分 對比面板 -->
          <div class="mt-5 grid grid-cols-2 gap-3">
            <div class="bg-slate-950/70 border border-slate-800 rounded-xl p-3.5">
              <div class="text-xs text-slate-400">四項算術平均分數</div>
              <div class="mt-1 flex items-baseline gap-2">
                <span id="statAverage" class="text-2xl font-extrabold text-slate-200 font-mono">9.50</span>
                <span class="text-xs text-slate-500">/ 10.0</span>
              </div>
              <div class="text-[11px] text-slate-500 mt-1">僅供參考，非官方總分</div>
            </div>

            <div class="bg-slate-950/70 border border-slate-800 rounded-xl p-3.5 relative overflow-hidden">
              <div class="text-xs text-slate-400">BGS 實際最終總分</div>
              <div class="mt-1 flex items-baseline gap-2">
                <span id="statFinal" class="text-2xl font-black text-amber-400 font-mono">9.5</span>
                <span id="statDiffBadge" class="text-[11px] font-semibold px-1.5 py-0.5 rounded bg-emerald-500/15 text-emerald-400">
                  無低分壓制
                </span>
              </div>
              <div id="statLabelName" class="text-[11px] text-slate-400 mt-1">金標 Gem Mint</div>
            </div>
          </div>

          <!-- 評分規則詳細解析框 -->
          <div class="mt-4 bg-slate-950/80 border border-slate-800/90 rounded-xl p-4 space-y-2">
            <div class="flex items-center gap-2 text-xs font-bold text-amber-400">
              <svg class="w-4 h-4 shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
              </svg>
              <span id="analysisHeadline">標準全 9.5（True Gem）</span>
            </div>
            <p id="analysisDetail" class="text-xs text-slate-300 leading-relaxed">
              四項細分皆為 9.5，無任何短版，屬於市場流動性最穩健的標準金標 9.5。
            </p>
            <div id="analysisMarketTip" class="pt-2 border-t border-slate-800/80 text-[11px] text-slate-400 leading-relaxed">
              💡 市場估價參考：四項均衡無瑕疵，是高階玩家收藏與交易的標準基準線。
            </div>
          </div>
        </div>

        <!-- 新增：PSA 顯示對照與跨評模擬卡片 -->
        <div class="bg-slate-900/75 border border-rose-500/30 rounded-2xl p-6 shadow-2xl space-y-4">
          <div class="flex items-center justify-between">
            <span class="text-xs font-bold uppercase tracking-wider text-rose-400 flex items-center gap-1.5">
              <span class="w-2 h-2 rounded-full bg-rose-500"></span>
              PSA 顯示對照（跨評 / 同級品相推估）
            </span>
            <span id="psaProbBadge" class="text-xs font-bold px-2.5 py-0.5 rounded-full bg-emerald-500/15 text-emerald-300 border border-emerald-500/40">
              PSA 10 機率約 75%
            </span>
          </div>

          <!-- 模擬 PSA 鑑定卡盒標籤 -->
          <div class="p-3 rounded-xl bg-slate-950/90 border border-slate-700/80 shadow-inner">
            <div class="slab-psa relative overflow-hidden rounded-md p-3.5">
              <!-- PSA 頂部紅色橫幅 -->
              <div class="bg-red-600 text-white px-2.5 py-1 rounded-sm flex items-center justify-between text-[10px] font-black tracking-widest uppercase mb-2.5">
                <span>PROFESSIONAL SPORTS AUTHENTICATOR</span>
                <span class="font-mono">PSA</span>
              </div>

              <!-- PSA 標籤主內容 -->
              <div class="grid grid-cols-12 gap-2 items-center px-1">
                <div class="col-span-8 space-y-1">
                  <div class="text-[11px] font-extrabold text-slate-800 tracking-tight">
                    2026 COLLECTIBLE TRADING CARD
                  </div>
                  <div id="psaLabelSubtext" class="text-[10px] font-bold text-slate-600 uppercase">
                    CROSSOVER EQUIVALENT ESTIMATE
                  </div>
                  <div class="flex items-center gap-2 pt-0.5">
                    <span class="inline-block px-1.5 py-0.5 bg-red-600/10 text-red-700 border border-red-600/30 rounded text-[9px] font-black">
                      PSA DNA / AUTH
                    </span>
                    <span class="text-[10px] font-mono font-bold text-slate-500">CERT #94827105</span>
                  </div>
                </div>

                <!-- 右側 PSA 等級與大數字 -->
                <div class="col-span-4 border-l-2 border-red-600/25 pl-2.5 flex flex-col items-end justify-center text-right">
                  <span id="psaGradeTitle" class="text-[11px] font-black text-red-600 tracking-wider uppercase leading-none">
                    GEM MT
                  </span>
                  <span id="psaGradeNumber" class="text-4xl font-black text-slate-900 leading-none tracking-tighter mt-1">
                    10
                  </span>
                </div>
              </div>
            </div>
          </div>

          <!-- PSA 跨評機率與細節解析 -->
          <div class="bg-slate-950/80 border border-slate-800/90 rounded-xl p-4 space-y-2.5">
            <div class="flex items-center justify-between text-xs">
              <span class="font-bold text-rose-300" id="psaHeadline">對應 PSA 10（Gem Mint 10）</span>
              <span class="font-mono text-[11px] text-slate-400" id="psaValueCompare">市場價值：PSA 10 ≈ BGS 9.5</span>
            </div>
            <p id="psaDetail" class="text-xs text-slate-300 leading-relaxed">
              標準全 9.5（True Gem）四項均衡無短版，置中度與邊角皆符合 PSA 10 要求，跨評成功率高。
            </p>
            <div id="psaWarningTip" class="pt-2 border-t border-slate-800/80 text-[11px] text-slate-400 leading-relaxed">
              ⚖️ 標準差異提醒：PSA 沒有細分分數，對正面置中容錯較寬（55/45 至 60/40），但對「表面壓痕（Dent）」極為嚴格（有壓痕會直接降至 PSA 6）。
            </div>
          </div>
        </div>

      </section>
    </div>

    <!-- 下方附加實用參考資訊區 -->
    <section class="grid grid-cols-1 lg:grid-cols-12 gap-8 pt-2">

      <!-- 左側：BGS vs PSA 評分與置中度量化標準對照表 (佔 7 欄) -->
      <div class="lg:col-span-7 bg-slate-900 border border-slate-800 rounded-2xl p-4 sm:p-6 shadow-xl">
        <div class="mb-4 flex flex-col sm:flex-row sm:items-center justify-between gap-2">
          <div>
            <h3 class="text-base font-bold text-white flex items-center gap-2">
              <span class="w-2 h-5 rounded-full bg-amber-400"></span>
              BGS 與 PSA 置中度及等級標準對照表
            </h3>
            <p class="text-xs text-slate-400 mt-1">BGS 高分段置中門檻遠高於 PSA，因此部分 BGS 9（僅因置中扣分）在 PSA 有機會拿 10</p>
          </div>
        </div>

        <div class="overflow-x-auto rounded-xl border border-slate-800 bg-slate-950">
          <table class="w-full text-left border-collapse text-xs bg-slate-950 text-slate-300">
            <thead>
              <tr class="bg-slate-900 text-slate-200 border-b border-slate-800">
                <th class="py-3 px-3 font-bold bg-slate-900">BGS 細項分數</th>
                <th class="py-3 px-3 font-bold bg-slate-900">BGS 正面置中要求</th>
                <th class="py-3 px-3 font-bold bg-slate-900">BGS 背面要求</th>
                <th class="py-3 px-3 font-bold text-rose-400 bg-slate-900">PSA 對應等級與置中標準</th>
              </tr>
            </thead>
            <tbody class="divide-y divide-slate-800/80 bg-slate-950 text-slate-300">
              <tr class="bg-slate-950 hover:bg-slate-900/80 transition">
                <td class="py-3 px-3 font-black text-amber-400 whitespace-nowrap bg-slate-950">10 (Pristine)</td>
                <td class="py-3 px-3 font-semibold text-white bg-slate-950">四邊皆須 50/50</td>
                <td class="py-3 px-3 bg-slate-950">55/45 或更好</td>
                <td class="py-3 px-3 text-rose-300 font-semibold bg-slate-950">PSA 10（遠超標準，價值遠高於 PSA 10）</td>
              </tr>
              <tr class="bg-slate-950 hover:bg-slate-900/80 transition">
                <td class="py-3 px-3 font-black text-amber-300 whitespace-nowrap bg-slate-950">9.5 (Gem Mint)</td>
                <td class="py-3 px-3 font-semibold text-white bg-slate-950">一軸 50/50，另一軸 55/45</td>
                <td class="py-3 px-3 bg-slate-950">60/40 或更好</td>
                <td class="py-3 px-3 text-rose-300 font-semibold bg-slate-950">PSA 10（PSA 10 允許正面 55/45～60/40）</td>
              </tr>
              <tr class="bg-slate-950 hover:bg-slate-900/80 transition">
                <td class="py-3 px-3 font-bold text-slate-200 whitespace-nowrap bg-slate-950">9 (Mint)</td>
                <td class="py-3 px-3 bg-slate-950">雙軸皆 55/45 或更好</td>
                <td class="py-3 px-3 bg-slate-950">70/30 或更好</td>
                <td class="py-3 px-3 text-amber-200 bg-slate-950">若僅置中為 9 仍可拚 PSA 10；邊角/表面瑕疵則為 PSA 9</td>
              </tr>
              <tr class="bg-slate-950 hover:bg-slate-900/80 transition">
                <td class="py-3 px-3 font-bold text-slate-300 whitespace-nowrap bg-slate-950">8.5 (NM-MT+)</td>
                <td class="py-3 px-3 bg-slate-950">介於 55/45 至 60/40</td>
                <td class="py-3 px-3 bg-slate-950">70/30 至 80/20</td>
                <td class="py-3 px-3 text-slate-300 bg-slate-950">PSA 8 至 PSA 9（PSA 無 8.5 常見溢價，多歸入 8 或 9）</td>
              </tr>
              <tr class="bg-slate-950 hover:bg-slate-900/80 transition">
                <td class="py-3 px-3 font-bold text-slate-400 whitespace-nowrap bg-slate-950">8 (NM-MT)</td>
                <td class="py-3 px-3 bg-slate-950">雙軸皆 60/40 或更好</td>
                <td class="py-3 px-3 bg-slate-950">80/20 或更好</td>
                <td class="py-3 px-3 text-slate-400 bg-slate-950">PSA 8 (NM-MT 8)</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <!-- 右側：9.5 階梯市場估價參考 (佔 5 欄) -->
      <div class="lg:col-span-5 bg-slate-900/75 border border-slate-800 rounded-2xl p-6 shadow-xl flex flex-col justify-between">
        <div>
          <div class="mb-4">
            <h3 class="text-base font-bold text-white flex items-center gap-2">
              <span class="w-2 h-5 rounded-full bg-amber-400"></span>
              BGS 9.5 階梯市場估價與跨評參考
            </h3>
            <p class="text-xs text-slate-400 mt-1">同為金標 9.5，細分組合直接影響轉手溢價與 PSA 跨評潛力</p>
          </div>

          <div class="space-y-3">
            <div class="p-3.5 rounded-xl bg-slate-950/80 border border-amber-500/30">
              <div class="flex items-center justify-between mb-1">
                <span class="text-xs font-extrabold text-amber-300">1. Quad+（強 9.5）</span>
                <span class="text-[11px] font-mono text-amber-400/90">10/10/9.5/9.5 或 10/9.5/9.5/9.5</span>
              </div>
              <p class="text-xs text-slate-300 leading-relaxed">
                距離金標 10 僅一步之遙。在 PTCG 與運動卡市場中具備極高的「跨評（Crossover）PSA 10」成功率，成交價最接近甚至超越 PSA 10。
              </p>
            </div>

            <div class="p-3.5 rounded-xl bg-slate-950/80 border border-slate-800">
              <div class="flex items-center justify-between mb-1">
                <span class="text-xs font-extrabold text-amber-200">2. True Gem（全 9.5）</span>
                <span class="text-[11px] font-mono text-slate-400">9.5 / 9.5 / 9.5 / 9.5</span>
              </div>
              <p class="text-xs text-slate-300 leading-relaxed">
                四項均衡無任何 9 分短版，是市場公認最穩健的標準 9.5 定價基準。
              </p>
            </div>

            <div class="p-3.5 rounded-xl bg-slate-950/80 border border-slate-800">
              <div class="flex items-center justify-between mb-1">
                <span class="text-xs font-extrabold text-slate-200">3. Min Gem（弱 9.5）</span>
                <span class="text-[11px] font-mono text-slate-400">9.5 / 9.5 / 9.5 / 9</span>
              </div>
              <p class="text-xs text-slate-300 leading-relaxed">
                俗稱「三 9.5 一 9」。若單項 9 分落在<strong class="text-slate-200">邊角（Corners）</strong>或<strong class="text-slate-200">表面（Surface）</strong>有微瑕，轉手行情通常較 True Gem 低約 10%～15%。
              </p>
            </div>
          </div>
        </div>

        <div class="mt-4 pt-3 border-t border-slate-800/80 text-[11px] text-slate-500 flex items-center justify-between">
          <span>核心規則：木桶效應（總分受最低單項限制）</span>
          <span>BGS Grading Reference</span>
        </div>
      </div>

    </section>

  </main>

  <script>
    // 支援的細分分數選項
    const SCORE_OPTIONS = [10, 9.5, 9, 8.5, 8, 7.5, 7];

    // 四個評分維度定義
    const DIMENSIONS = [
      { key: 'C', labelEn: 'Centering', labelZh: '置中度', hint: '正背面圖案與邊框對稱比例' },
      { key: 'Co', labelEn: 'Corners', labelZh: '邊角', hint: '四個角的銳利度、有無白點或磨圓' },
      { key: 'E', labelEn: 'Edges', labelZh: '邊緣', hint: '裁切邊平整度、有無毛邊或白邊' },
      { key: 'S', labelEn: 'Surface', labelZh: '表面', hint: '正反面光澤、印刷線、刮痕或壓痕' }
    ];

    // 目前狀態（預設全 9.5）
    const state = {
      C: 9.5,
      Co: 9.5,
      E: 9.5,
      S: 9.5
    };

    // 初始化渲染控制面板（專為 iPhone 16 觸控手感優化：7顆按鈕等寬滿版網格，移除下拉選單）
    function initControls() {
      const container = document.getElementById('subgradesContainer');
      container.innerHTML = DIMENSIONS.map(dim => {
        const buttonsHtml = SCORE_OPTIONS.map(val => {
          const isSelected = state[dim.key] === val;
          return `
            <button
              type="button"
              data-dim="${dim.key}"
              data-val="${val}"
              onclick="setScore('${dim.key}', ${val})"
              class="score-btn w-full py-2.5 sm:py-2 rounded-xl text-xs sm:text-[13px] font-mono font-bold transition active:scale-95 border text-center ${
                isSelected
                  ? 'bg-amber-400 text-slate-950 border-amber-300 shadow-md shadow-amber-500/20'
                  : 'bg-slate-950 text-slate-300 border-slate-800 hover:border-slate-600 hover:bg-slate-800'
              }"
            >
              ${val}
            </button>
          `;
        }).join('');

        return `
          <div class="bg-slate-950/70 border border-slate-800/90 rounded-xl p-3.5 sm:p-4 transition hover:border-slate-700 space-y-2.5">
            <div class="flex items-baseline justify-between gap-2">
              <div class="flex items-center gap-1.5">
                <span class="text-sm font-bold text-white">${dim.labelZh}</span>
                <span class="text-xs font-semibold text-amber-400/90">(${dim.labelEn})</span>
              </div>
              <span class="text-[11px] text-slate-400 truncate">${dim.hint}</span>
            </div>

            <div class="grid grid-cols-7 gap-1.5">
              ${buttonsHtml}
            </div>
          </div>
        `;
      }).join('');
    }

    // 更新單一維度分數
    function setScore(dimKey, val) {
      state[dimKey] = val;
      syncControlUI();
      calculateAndRender();
    }

    // 一鍵套用預設組合
    function applyPreset(c, co, e, s) {
      state.C = c;
      state.Co = co;
      state.E = e;
      state.S = s;
      syncControlUI();
      calculateAndRender();
    }

    // 同步按鈕高亮狀態
    function syncControlUI() {
      DIMENSIONS.forEach(dim => {
        const currentVal = state[dim.key];

        const btns = document.querySelectorAll(`.score-btn[data-dim="${dim.key}"]`);
        btns.forEach(btn => {
          const btnVal = parseFloat(btn.getAttribute('data-val'));
          if (btnVal === currentVal) {
            btn.className = 'score-btn w-full py-2.5 sm:py-2 rounded-xl text-xs sm:text-[13px] font-mono font-bold transition active:scale-95 border text-center bg-amber-400 text-slate-950 border-amber-300 shadow-md shadow-amber-500/20';
          } else {
            btn.className = 'score-btn w-full py-2.5 sm:py-2 rounded-xl text-xs sm:text-[13px] font-mono font-bold transition active:scale-95 border text-center bg-slate-950 text-slate-300 border-slate-800 hover:border-slate-600 hover:bg-slate-800';
          }
        });
      });
    }

    // 取得等級英文名稱
    function getGradeTitle(score) {
      if (score === 10) return 'PRISTINE';
      if (score === 9.5) return 'GEM MINT';
      if (score === 9) return 'MINT';
      if (score === 8.5) return 'NM-MT+';
      if (score === 8) return 'NM-MT';
      if (score === 7.5) return 'NEAR MINT+';
      return 'NEAR MINT';
    }

    // 核心 BGS 總分計算演算法與畫面渲染
    function calculateAndRender() {
      const scores = [state.C, state.Co, state.E, state.S];
      const sorted = [...scores].sort((a, b) => a - b); // 由小到大排序 [min1, min2, min3, max]
      const [min1, min2, min3, max] = sorted;

      const sum = scores.reduce((acc, v) => acc + v, 0);
      const avg = sum / 4;

      let finalScore = 0;
      let slabTheme = 'slab-silver'; // 'slab-black' | 'slab-gold' | 'slab-silver'
      let colorTagText = 'SILVER LABEL';
      let labelNameZh = '';
      let subTierText = '';
      let headline = '';
      let detail = '';
      let marketTip = '';

      const count10 = scores.filter(s => s === 10).length;
      const count95 = scores.filter(s => s === 9.5).length;
      const count9 = scores.filter(s => s === 9).length;

      // 1. 黑標 10：四項全 10
      if (count10 === 4) {
        finalScore = 10;
        slabTheme = 'slab-black';
        colorTagText = 'BLACK LABEL';
        labelNameZh = '黑標 Pristine 10';
        subTierText = 'Black Label 10';
        headline = '最高殿堂：全 10 黑標（Black Label Pristine 10）';
        detail = '四項細分全數獲得 10 分滿分，零瑕疵的極致藝術品。';
        marketTip = '👑 市場估價參考：鑑定界最高榮譽，市場溢價遠超金標 10 與 PSA 10，具備極高收藏價值。';
      }
      // 2. 金標 10：三項 10 + 一項 9.5
      else if (count10 === 3 && count95 === 1) {
        finalScore = 10;
        slabTheme = 'slab-gold';
        colorTagText = 'GOLD LABEL';
        labelNameZh = '金標 Pristine 10';
        subTierText = 'Pristine 10';
        headline = '極致品相：金標 Pristine 10（三項 10 + 一項 9.5）';
        detail = '符合 BGS 金標 10 的嚴格門檻，僅單一維度有極微小的 0.5 差距。';
        marketTip = '🏆 市場估價參考：稀缺度極高，價格通常顯著高於一般 BGS 9.5 與 PSA 10。';
      }
      // 3. 金標 9.5 區間：最低單項 >= 9 且最多只能有一項 9
      else if (min1 >= 9 && count9 <= 1) {
        finalScore = 9.5;
        slabTheme = 'slab-gold';
        colorTagText = 'GOLD LABEL';

        if (count9 === 1) {
          // 含一項 9（例如 10/10/10/9, 10/10/9.5/9, 10/9.5/9.5/9, 9.5/9.5/9.5/9）
          labelNameZh = '金標 Gem Mint 9.5';
          subTierText = 'Min Gem (弱 9.5)';
          headline = count10 > 0
            ? `高分拉抬達標：金標 9.5（含 ${count10} 項 10 與 1 項 9）`
            : '最低門檻達標：金標 9.5（Min Gem / 三 9.5 一 9）';
          detail = '雖然存在一項 9 分，但因其他三項皆達 9.5 以上且僅有一項 9，成功守住金標 9.5 總分。';
          marketTip = '💡 市場估價參考：同為金標 9.5，但轉手價格通常較全 9.5（True Gem）略低約 10%～15%。';
        } else if (count10 >= 1) {
          // 無 9 且含至少一項 10（例如 10/10/9.5/9.5 或 10/9.5/9.5/9.5）
          labelNameZh = '金標 Gem Mint 9.5';
          subTierText = 'Quad+ (強 9.5)';
          headline = `強勢高分：金標 9.5（Quad+ / 含 ${count10} 項 10）`;
          detail = '雖未達「三項 10」的金標 10 門檻，但全數細分都在 9.5 以上，品相極為優異。';
          marketTip = '🔥 市場估價參考：在高端玩家眼中具備極高的跨評（Crossover）PSA 10 成功率，溢價表現亮眼。';
        } else {
          // 四項全 9.5
          labelNameZh = '金標 Gem Mint 9.5';
          subTierText = 'True Gem (全 9.5)';
          headline = '標準均衡：金標 9.5（True Gem / 全 9.5）';
          detail = '四項細分皆為 9.5，無任何短版，屬於市場流動性最穩健的標準金標 9.5。';
          marketTip = '💎 市場估價參考：四項均衡無瑕疵，是現代高階卡交易與收藏的標準基準線。';
        }
      }
      // 4. 銀標 9：最低單項 >= 9 但出現兩項（或以上）9
      else if (min1 >= 9 && count9 >= 2) {
        finalScore = 9;
        slabTheme = 'slab-silver';
        colorTagText = 'SILVER LABEL';
        labelNameZh = '銀標 Mint 9';
        subTierText = 'Mint 9 (多項 9 降級)';
        headline = `觸發降級規則：銀標 9（出現 ${count9} 項 9 分）`;
        detail = avg >= 9.5
          ? `即使四項算術平均高達 ${avg.toFixed(2)}（如 10/10/9/9），BGS 規定只要出現兩項或以上 9 分，總分即降為 9。`
          : '因有兩項或以上細分為 9 分，未達金標 9.5 最多僅容許一項 9 的限制，最終總分為 9。';
        marketTip = '📌 市場估價參考：銀標 9 在現代卡市場與金標 9.5 存在明顯價差，建議送評前重點檢視弱項。';
      }
      // 5. 最低單項 <= 8.5：低分壓制原則（木桶效應）
      else {
        slabTheme = 'slab-silver';
        colorTagText = 'SILVER LABEL';

        // BGS 低分壓制邏輯：
        // 若最低為 8.5，且其他三項平均足夠拉抬（例如三項皆 >= 9.5，或整體平均 >= 9.0），最高可給到 9.0；否則為 8.5
        if (min1 === 8.5) {
          const otherThreeAvg = (min2 + min3 + max) / 3;
          if (otherThreeAvg >= 9.33 || avg >= 9.125) {
            finalScore = 9;
            labelNameZh = '銀標 Mint 9';
            subTierText = 'Mint 9 (受 8.5 壓制)';
            headline = '低分壓制上限：銀標 9（受單項 8.5 限制）';
            detail = `儘管其他三項分數極高（算術平均達 ${avg.toFixed(2)}），但受限於 BGS「總分不超過最低單項 +0.5」的木桶效應，總分最高只能給到 9，無法取得 9.5。`;
            marketTip = '⚠️ 實務提醒：單一維度（如嚴重白邊或壓痕）拿到 8.5 就會直接鎖死金標 9.5 的機會。';
          } else {
            finalScore = 8.5;
            labelNameZh = '銀標 NM-MT+ 8.5';
            subTierText = 'NM-MT+ 8.5';
            headline = '銀標 8.5（NM-MT+）';
            detail = `最低單項為 8.5，且其餘三項分數不足以將總分拉抬至 9，最終總分落在 8.5。`;
            marketTip = '📌 實務提醒：通常代表肉眼或放大鏡下有較明顯的邊角白點、細紋或置中偏移。';
          }
        } else {
          // 最低單項 <= 8.0 的通用 BGS 低分壓制公式（總分不超過 min1 + 0.5）
          const otherThreeAvg = (min2 + min3 + max) / 3;
          const canBumpHalf = (otherThreeAvg - min1) >= 1.0 && avg >= (min1 + 0.5);
          finalScore = canBumpHalf ? min1 + 0.5 : min1;

          const gradeTitle = getGradeTitle(finalScore);
          labelNameZh = `銀標 ${gradeTitle} ${finalScore}`;
          subTierText = `${gradeTitle} ${finalScore}`;
          headline = `低分壓制判定：總分 ${finalScore}（最低單項為 ${min1}）`;
          detail = `依據 BGS 木桶效應規則，最終總分受最低單項（${min1} 分）嚴格限制，不會超過最低細項 +0.5 分。`;
          marketTip = '🔍 建議：若卡片本身非高稀有度老卡，此分數區間通常較適合裸卡收藏。';
        }
      }

      // 更新標籤卡片樣式與數值
      const slabCard = document.getElementById('slabLabelCard');
      slabCard.className = `${slabTheme} relative overflow-hidden rounded-lg p-4 transition-all duration-300`;

      document.getElementById('labelC').textContent = state.C;
      document.getElementById('labelCo').textContent = state.Co;
      document.getElementById('labelE').textContent = state.E;
      document.getElementById('labelS').textContent = state.S;

      document.getElementById('labelGradeTitle').textContent = getGradeTitle(finalScore);
      document.getElementById('labelFinalScore').textContent = finalScore;
      document.getElementById('labelColorTag').textContent = colorTagText;

      // 更新右上角子等級膠囊
      const pill = document.getElementById('subTierPill');
      pill.textContent = subTierText;
      if (slabTheme === 'slab-black') {
        pill.className = 'text-xs font-bold px-2.5 py-0.5 rounded-full bg-zinc-950 text-amber-400 border border-amber-400/70';
      } else if (slabTheme === 'slab-gold') {
        pill.className = 'text-xs font-bold px-2.5 py-0.5 rounded-full bg-amber-500/20 text-amber-300 border border-amber-500/40';
      } else {
        pill.className = 'text-xs font-bold px-2.5 py-0.5 rounded-full bg-slate-800 text-slate-300 border border-slate-600';
      }

      // 更新對比統計區
      document.getElementById('statAverage').textContent = avg.toFixed(2);
      const statFinalEl = document.getElementById('statFinal');
      statFinalEl.textContent = finalScore;
      statFinalEl.className = `text-2xl font-black font-mono ${
        slabTheme === 'slab-silver' ? 'text-slate-200' : 'text-amber-400'
      }`;
      document.getElementById('statLabelName').textContent = labelNameZh;

      const diffBadge = document.getElementById('statDiffBadge');
      const diff = finalScore - avg;
      if (diff < -0.25) {
        diffBadge.textContent = `低分壓制 (${diff.toFixed(2)})`;
        diffBadge.className = 'text-[11px] font-semibold px-1.5 py-0.5 rounded bg-rose-500/15 text-rose-400 border border-rose-500/30';
      } else if (diff > 0.1) {
        diffBadge.textContent = `高分拉抬 (+${diff.toFixed(2)})`;
        diffBadge.className = 'text-[11px] font-semibold px-1.5 py-0.5 rounded bg-amber-500/15 text-amber-300 border border-amber-500/30';
      } else {
        diffBadge.textContent = '標準符合';
        diffBadge.className = 'text-[11px] font-semibold px-1.5 py-0.5 rounded bg-emerald-500/15 text-emerald-400 border border-emerald-500/30';
      }

      // 更新評析文字
      document.getElementById('analysisHeadline').textContent = headline;
      document.getElementById('analysisDetail').textContent = detail;
      document.getElementById('analysisMarketTip').textContent = marketTip;

      // 同步計算並更新 PSA 顯示對照
      updatePSAComparison(finalScore, slabTheme, count10, count95, count9, min1);
    }

    // 計算並渲染 PSA 對應等級與跨評分析
    function updatePSAComparison(bgsFinal, slabTheme, count10, count95, count9, min1) {
      let psaNum = '10';
      let psaTitle = 'GEM MT';
      let probBadge = 'PSA 10 機率 99%+';
      let probBadgeClass = 'text-xs font-bold px-2.5 py-0.5 rounded-full bg-emerald-500/20 text-emerald-300 border border-emerald-500/40';
      let psaHeadline = '穩拿 PSA 10（Gem Mint 10）';
      let psaValueCompare = '市場價值：BGS 10 遠高於 PSA 10';
      let psaDetail = '';
      let psaWarningTip = '💡 收藏建議：BGS 10（黑標或金標）的稀有度與市場成交價皆顯著高於 PSA 10，實務上極少有人會將 BGS 10 拆盒轉評 PSA。';

      const c = state.C;
      const co = state.Co;
      const e = state.E;
      const s = state.S;

      // 1. BGS 10 (黑標或金標)
      if (bgsFinal === 10) {
        psaNum = '10';
        psaTitle = 'GEM MT';
        probBadge = 'PSA 10 機率 99%+';
        psaHeadline = '穩拿 PSA 10（Gem Mint 10）';
        psaValueCompare = slabTheme === 'slab-black'
          ? '市場價值：黑標 10 遠高於 PSA 10 (數倍溢價)'
          : '市場價值：金標 10 明顯高於 PSA 10';
        psaDetail = '四項細分皆達到 9.5 至 10 的頂尖水準，完全超越 PSA 10 的各項門檻。';
      }
      // 2. BGS 9.5 區間
      else if (bgsFinal === 9.5) {
        if (count9 === 0 && count10 >= 1) {
          // Quad+ 9.5
          psaNum = '10';
          psaTitle = 'GEM MT';
          probBadge = 'PSA 10 機率約 85%～90%';
          probBadgeClass = 'text-xs font-bold px-2.5 py-0.5 rounded-full bg-emerald-500/20 text-emerald-300 border border-emerald-500/40';
          psaHeadline = '高機率對應 PSA 10（Gem Mint 10）';
          psaValueCompare = '市場價值：Quad+ 9.5 接近或略高於 PSA 10';
          psaDetail = 'Quad+ 級別（含 10 分細項且無任何 9 分短版）在跨評 PSA 10 時具備極高成功率，是玩家最常嘗試 Crossover 的黃金組合。';
          psaWarningTip = '⚖️ 跨評提示：若送 PSA 帶殼跨評（Crossover），設定最低接受分數為 PSA 10 的過關率極高。';
        } else if (count9 === 0) {
          // True Gem (全 9.5)
          psaNum = '10';
          psaTitle = 'GEM MT';
          probBadge = 'PSA 10 機率約 75%～80%';
          probBadgeClass = 'text-xs font-bold px-2.5 py-0.5 rounded-full bg-emerald-500/15 text-emerald-300 border border-emerald-500/40';
          psaHeadline = '標準對應 PSA 10（Gem Mint 10）';
          psaValueCompare = '市場價值：PSA 10 ≈ BGS 9.5 (依卡種微幅浮動)';
          psaDetail = '標準全 9.5（True Gem）四項均衡無短版，置中度與邊角皆符合 PSA 10 要求，整體品相與 PSA 10 處於同一水準。';
          psaWarningTip = '⚖️ 標準差異提醒：PSA 鑑定時對「表面微細印刷線或極小壓痕」有時比 BGS 更嚴格，送評前仍需確認表面無壓痕。';
        } else {
          // Min Gem (三 9.5 一 9)
          const isCenteringNine = (c === 9);
          const isCornerOrSurfaceNine = (co === 9 || s === 9);

          if (isCenteringNine) {
            psaNum = '10';
            psaTitle = 'GEM MT';
            probBadge = 'PSA 10 機率約 60%～70%';
            probBadgeClass = 'text-xs font-bold px-2.5 py-0.5 rounded-full bg-amber-500/20 text-amber-300 border border-amber-500/40';
            psaHeadline = '有機會對應 PSA 10（受惠於 PSA 置中寬容度）';
            psaValueCompare = '市場價值：若成功轉 PSA 10 可提升約 15% 價值';
            psaDetail = '您唯一的 9 分落在「置中度（Centering）」。由於 BGS 9 置中（55/45）仍在 PSA 10 容許範圍內，且邊角/邊緣/表面皆達 9.5，仍具相當高的 PSA 10 潛力！';
            psaWarningTip = '🎯 關鍵優勢：PSA 對正面置中最高容許至 55/45（部分案例放寬至 60/40），因此「唯獨置中 9 分」是最有利於轉評 PSA 10 的 Min Gem。';
          } else {
            psaNum = '9';
            psaTitle = 'MINT';
            probBadge = 'PSA 10 機率約 30%～40%（偏 PSA 9）';
            probBadgeClass = 'text-xs font-bold px-2.5 py-0.5 rounded-full bg-rose-500/20 text-rose-300 border border-rose-500/40';
            psaHeadline = '邊緣風險：對應 PSA 9（Mint 9）機率較高';
            psaValueCompare = '市場價值：BGS 9.5 弱卡 通常高於 PSA 9';
            psaDetail = isCornerOrSurfaceNine
              ? '您唯一的 9 分落在「邊角」或「表面」，代表放大鏡下有可見白點、細刮痕或微瑕，這類瑕疵在 PSA 往往會直接被判為 PSA 9。'
              : '您唯一的 9 分落在「邊緣（Edges）」，若有明顯裁切毛邊或白邊，轉評 PSA 時容易落在 PSA 9。';
            psaWarningTip = '⚠️ 實務建議：此類 Min Gem 9.5 保留 BGS 9.5 金標通常比硬轉 PSA 更有價值，不建議盲目拆殼。';
          }
        }
      }
      // 3. BGS 9 區間
      else if (bgsFinal === 9) {
        // 檢查是否僅因置中為 9（或包含置中 9）而其他物理條件極佳
        if (c === 9 && co >= 9.5 && e >= 9.5 && s >= 9.5) {
          psaNum = '10';
          psaTitle = 'GEM MT';
          probBadge = '潛力 PSA 10（約 55%～65%）';
          probBadgeClass = 'text-xs font-bold px-2.5 py-0.5 rounded-full bg-amber-500/20 text-amber-300 border border-amber-500/40';
          psaHeadline = '特殊案例：有機會直上 PSA 10';
          psaValueCompare = '市場價值：若轉 PSA 10 價值將大幅躍升';
          psaDetail = '雖然 BGS 總分為 9，但若扣分主因是置中度（55/45），而邊角、邊緣與表面皆達 9.5 以上，在 PSA 標準下具備直接拿 PSA 10 的條件。';
          psaWarningTip = '🎯 跨評策略：這正是收藏圈常說「買 BGS 9 置中卡去轉 PSA 10」的典型套利情境。';
        } else if (c === 9 && count9 === 2 && (co === 9 || e === 9 || s === 9)) {
          psaNum = '9';
          psaTitle = 'MINT';
          probBadge = 'PSA 9 為主（約 25% 拚 PSA 10）';
          probBadgeClass = 'text-xs font-bold px-2.5 py-0.5 rounded-full bg-slate-700 text-slate-200 border border-slate-500';
          psaHeadline = '主要對應 PSA 9（Mint 9）';
          psaValueCompare = '市場價值：PSA 9 ≈ BGS 9';
          psaDetail = '因同時包含置中 9 與另一項物理條件 9（邊角/邊緣/表面），雖然置中在 PSA 過關，但另一項物理瑕疵大機率會讓卡況停留在 PSA 9。';
          psaWarningTip = '⚖️ 標準差異提醒：若另一項 9 分只是極輕微的裁切毛邊，仍有少數機會獲評 PSA 10。';
        } else {
          psaNum = '9';
          psaTitle = 'MINT';
          probBadge = '穩定對應 PSA 9（Mint 9）';
          probBadgeClass = 'text-xs font-bold px-2.5 py-0.5 rounded-full bg-slate-700 text-slate-200 border border-slate-500';
          psaHeadline = '對應 PSA 9（Mint 9）';
          psaValueCompare = '市場價值：現代卡市場 PSA 9 流動性略佳';
          psaDetail = min1 === 8.5
            ? '因存在 8.5 分弱項（如明顯白點、細刮痕或置中達 60/40），轉評 PSA 時通常會落在 PSA 9（若為表面壓痕甚至可能降至 PSA 6）。'
            : '整體卡況為標準 Mint 等級，無嚴重結構損傷但具備肉眼或放大鏡可見之輕微瑕疵，完全對應 PSA 9。';
          psaWarningTip = '⚠️ 壓痕警告：若 8.5 分是因「表面微小壓痕（Dent）」所致，PSA 規範會直接扣至 PSA 6 級別，切勿送 PSA。';
        }
      }
      // 4. BGS 8.5 及以下
      else {
        const mappedPsa = Math.round(bgsFinal);
        psaNum = String(mappedPsa);
        psaTitle = mappedPsa === 8 ? 'NM-MT' : (mappedPsa === 7 ? 'NM' : 'EX-MT');
        probBadge = `對應 PSA ${mappedPsa} 等級區間`;
        probBadgeClass = 'text-xs font-bold px-2.5 py-0.5 rounded-full bg-slate-800 text-slate-300 border border-slate-600';
        psaHeadline = `對應 PSA ${mappedPsa}（${psaTitle} ${mappedPsa}）`;
        psaValueCompare = `市場價值：PSA ${mappedPsa} 與 BGS ${bgsFinal} 相近`;
        psaDetail = `PSA 幾乎不常給予 .5 半分（僅極少數老卡有 8.5），BGS ${bgsFinal} 在 PSA 體系中通常會四捨五入歸入 PSA ${mappedPsa}。`;
        psaWarningTip = '⚠️ 表面壓痕提醒：BGS 對輕微表面壓痕（Indent/Dent）通常給 8～8.5 分，但 PSA 只要發現表面壓痕就會直接判為 PSA 6。';
      }

      // 寫入 PSA 卡片與分析區 DOM
      document.getElementById('psaGradeNumber').textContent = psaNum;
      document.getElementById('psaGradeTitle').textContent = psaTitle;
      const badgeEl = document.getElementById('psaProbBadge');
      badgeEl.textContent = probBadge;
      badgeEl.className = probBadgeClass;

      document.getElementById('psaHeadline').textContent = psaHeadline;
      document.getElementById('psaValueCompare').textContent = psaValueCompare;
      document.getElementById('psaDetail').textContent = psaDetail;
      document.getElementById('psaWarningTip').textContent = psaWarningTip;
    }

    // 頁面載入完成後啟動
    window.addEventListener('DOMContentLoaded', () => {
      initControls();
      calculateAndRender();
    });
  </script>
</body>
</html>
