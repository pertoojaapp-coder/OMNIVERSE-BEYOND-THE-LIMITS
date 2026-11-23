<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>OMNIVERSE — BEYOND THE LIMITS</title>

  <!-- Fontes Sci-Fi AAA: Orbitron (títulos), Rajdhani (HUD/botões), Inter (textos) -->
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600&family=Orbitron:wght@500;700;800;900&family=Rajdhani:wght@400;500;600;700&display=swap" rel="stylesheet" />

  <!-- Tailwind CSS via CDN -->
  <script src="https://cdn.tailwindcss.com"></script>

  <!-- Three.js r128 via CDN -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>

  <style>
    :root {
      --ui-scale: 1;
      --accent-cyan: #38bdf8;
      --accent-blue: #2563eb;
      --accent-amber: #f59e0b;
      --bg-void: #03060d;
      --panel-bg: rgba(7, 13, 26, 0.72);
      --panel-border: rgba(56, 189, 248, 0.25);
    }

    * {
      box-sizing: border-box;
      user-select: none;
      -webkit-tap-highlight-color: transparent;
    }

    html, body {
      margin: 0;
      padding: 0;
      width: 100%;
      height: 100%;
      overflow: hidden;
      background-color: var(--bg-void);
      color: #f8fafc;
      font-family: 'Inter', sans-serif;
    }

    /* Escala dinâmica da interface ajustável nas Configurações */
    #ui-root {
      position: fixed;
      inset: 0;
      width: 100%;
      height: 100%;
      z-index: 10;
      pointer-events: none;
      transform-origin: center center;
    }

    .font-orbitron { font-family: 'Orbitron', sans-serif; }
    .font-rajdhani { font-family: 'Rajdhani', sans-serif; }
    .font-inter { font-family: 'Inter', sans-serif; }

    /* Canvas 3D em tela cheia */
    #universe-canvas {
      position: fixed;
      inset: 0;
      width: 100%;
      height: 100%;
      z-index: 1;
      display: block;
    }

    /* Vinheta cinematográfica e scanlines ultra sutis */
    .cinematic-vignette {
      position: fixed;
      inset: 0;
      z-index: 3;
      pointer-events: none;
      background:
        radial-gradient(circle at 50% 50%, transparent 45%, rgba(2, 5, 12, 0.78) 100%),
        linear-gradient(180deg, rgba(2, 6, 15, 0.55) 0%, transparent 18%, transparent 82%, rgba(2, 6, 15, 0.75) 100%);
    }

    .subtle-grid-overlay {
      position: fixed;
      inset: 0;
      z-index: 4;
      pointer-events: none;
      opacity: 0.04;
      background-image:
        linear-gradient(to right, rgba(56, 189, 248, 0.5) 1px, transparent 1px),
        linear-gradient(to bottom, rgba(56, 189, 248, 0.5) 1px, transparent 1px);
      background-size: 64px 64px;
    }

    /* Barras Letterbox para a abertura cinematográfica */
    .letterbox-bar {
      position: fixed;
      left: 0;
      width: 100%;
      height: 9vh;
      background: #010308;
      z-index: 25;
      transition: transform 1.4s cubic-bezier(0.22, 1, 0.36, 1);
      pointer-events: none;
    }
    .letterbox-top { top: 0; transform: translateY(0); border-bottom: 1px solid rgba(56, 189, 248, 0.14); }
    .letterbox-bottom { bottom: 0; transform: translateY(0); border-top: 1px solid rgba(56, 189, 248, 0.14); }
    body.in-menu .letterbox-top { transform: translateY(-100%); }
    body.in-menu .letterbox-bottom { transform: translateY(100%); }

    /* Cortes angulares futuristas (Clip-path estilo Mass Effect / Starfield) */
    .sci-clip {
      clip-path: polygon(0 0, calc(100% - 14px) 0, 100% 14px, 100% 100%, 14px 100%, 0 calc(100% - 14px));
    }
    .sci-clip-sm {
      clip-path: polygon(0 0, calc(100% - 9px) 0, 100% 9px, 100% 100%, 9px 100%, 0 calc(100% - 9px));
    }

    /* Botões do Menu Principal */
    .menu-btn {
      position: relative;
      display: flex;
      align-items: center;
      justify-content: space-between;
      width: 100%;
      padding: 0.85rem 1.35rem;
      background: linear-gradient(90deg, rgba(15, 23, 42, 0.72) 0%, rgba(15, 23, 42, 0.35) 100%);
      border-left: 3px solid rgba(148, 163, 184, 0.35);
      border-top: 1px solid rgba(56, 189, 248, 0.12);
      border-bottom: 1px solid rgba(56, 189, 248, 0.12);
      color: #cbd5e1;
      font-family: 'Rajdhani', sans-serif;
      font-weight: 600;
      font-size: 1.15rem;
      letter-spacing: 0.16em;
      text-transform: uppercase;
      transition: all 0.25s cubic-bezier(0.16, 1, 0.3, 1);
      cursor: pointer;
      pointer-events: auto;
      overflow: hidden;
    }

    .menu-btn::before {
      content: '';
      position: absolute;
      inset: 0;
      background: linear-gradient(90deg, rgba(56, 189, 248, 0.22) 0%, rgba(37, 99, 235, 0.06) 75%, transparent 100%);
      transform: translateX(-100%);
      transition: transform 0.3s cubic-bezier(0.16, 1, 0.3, 1);
      z-index: 0;
    }

    .menu-btn:hover,
    .menu-btn.active {
      color: #ffffff;
      border-left-color: #38bdf8;
      border-top-color: rgba(56, 189, 248, 0.45);
      border-bottom-color: rgba(56, 189, 248, 0.45);
      transform: translateX(10px);
      text-shadow: 0 0 14px rgba(56, 189, 248, 0.65);
      box-shadow: -6px 0 24px -6px rgba(56, 189, 248, 0.5);
    }

    .menu-btn:hover::before,
    .menu-btn.active::before {
      transform: translateX(0);
    }

    .menu-btn > * {
      position: relative;
      z-index: 1;
    }

    /* Painéis Glassmorphism Escuro */
    .glass-panel {
      background: rgba(6, 12, 24, 0.82);
      backdrop-filter: blur(16px);
      -webkit-backdrop-filter: blur(16px);
      border: 1px solid rgba(56, 189, 248, 0.24);
      box-shadow: 0 24px 60px rgba(0, 0, 0, 0.75), inset 0 0 32px rgba(56, 189, 248, 0.05);
    }

    /* Composição Cinematográfica das Imagens Oficiais */
    .hero-artwork-wrapper {
      position: relative;
      transition: transform 0.25s ease-out, opacity 1.2s ease, filter 1.2s ease;
      will-change: transform, opacity;
    }

    .hero-glow-aura {
      position: absolute;
      inset: 8%;
      background: radial-gradient(circle at 52% 40%, rgba(56, 189, 248, 0.32), rgba(37, 99, 235, 0.14) 55%, transparent 75%);
      filter: blur(36px);
      z-index: 0;
      pointer-events: none;
      animation: pulseAura 6s ease-in-out infinite alternate;
    }

    @keyframes pulseAura {
      0% { opacity: 0.65; transform: scale(0.96); }
      100% { opacity: 1; transform: scale(1.04); }
    }

    .official-hero-img {
      position: relative;
      z-index: 1;
      object-fit: contain;
      filter: drop-shadow(0 18px 38px rgba(0, 0, 0, 0.85)) drop-shadow(0 0 22px rgba(56, 189, 248, 0.28));
      mask-image: linear-gradient(to bottom, black 82%, transparent 100%);
      -webkit-mask-image: linear-gradient(to bottom, black 82%, transparent 100%);
    }

    .official-logo-img {
      object-fit: contain;
      filter: drop-shadow(0 10px 30px rgba(0, 0, 0, 0.9)) drop-shadow(0 0 24px rgba(56, 189, 248, 0.35));
    }

    /* Animações suaves de entrada e transição */
    .fade-layer {
      transition: opacity 1s cubic-bezier(0.22, 1, 0.36, 1), transform 1.2s cubic-bezier(0.22, 1, 0.36, 1);
    }

    /* Custom Scrollbar para a grade de 10 missões */
    .custom-scroll::-webkit-scrollbar {
      width: 6px;
      height: 6px;
    }
    .custom-scroll::-webkit-scrollbar-track {
      background: rgba(15, 23, 42, 0.5);
    }
    .custom-scroll::-webkit-scrollbar-thumb {
      background: rgba(56, 189, 248, 0.45);
      border-radius: 999px;
    }
    .custom-scroll::-webkit-scrollbar-thumb:hover {
      background: rgba(56, 189, 248, 0.8);
    }

    /* Range Sliders Futuristas */
    input[type="range"].sci-slider {
      -webkit-appearance: none;
      appearance: none;
      width: 100%;
      height: 6px;
      background: rgba(30, 41, 59, 0.9);
      border-radius: 999px;
      outline: none;
      border: 1px solid rgba(56, 189, 248, 0.25);
    }
    input[type="range"].sci-slider::-webkit-slider-thumb {
      -webkit-appearance: none;
      appearance: none;
      width: 16px;
      height: 16px;
      border-radius: 2px;
      background: #38bdf8;
      cursor: pointer;
      box-shadow: 0 0 12px rgba(56, 189, 248, 0.85);
      transform: rotate(45deg);
    }
    input[type="range"].sci-slider::-moz-range-thumb {
      width: 16px;
      height: 16px;
      border-radius: 2px;
      background: #38bdf8;
      cursor: pointer;
      border: none;
      box-shadow: 0 0 12px rgba(56, 189, 248, 0.85);
    }

    /* Animação de scanline tática */
    @keyframes scanSweep {
      0% { transform: translateY(-100%); opacity: 0; }
      50% { opacity: 0.4; }
      100% { transform: translateY(400%); opacity: 0; }
    }
    .hud-scanline {
      position: absolute;
      left: 0;
      right: 0;
      height: 2px;
      background: linear-gradient(90deg, transparent, rgba(56, 189, 248, 0.8), transparent);
      animation: scanSweep 5s linear infinite;
      pointer-events: none;
    }
  </style>
</head>
<body>

  <!-- Canvas 3D Principal (Three.js: Estrelas, Nebulosa, Planeta, Estação Orbital, Aeronaves) -->
  <canvas id="universe-canvas"></canvas>

  <!-- Camadas de Atmosfera Cinematográfica -->
  <div class="cinematic-vignette"></div>
  <div class="subtle-grid-overlay"></div>

  <!-- Barras Letterbox da Abertura -->
  <div class="letterbox-bar letterbox-top"></div>
  <div class="letterbox-bar letterbox-bottom"></div>

  <!-- Raiz da Interface Escalável -->
  <div id="ui-root">

    <!-- =====================================================================
         HUD SUPERIOR GLOBAL (Áudio & Status de Sistema)
    ====================================================================== -->
    <header class="fixed top-0 left-0 right-0 z-40 flex items-center justify-between px-4 sm:px-8 py-4 pointer-events-none">
      <!-- Indicador Discreto de Telemetria -->
      <div class="flex items-center gap-3 opacity-80">
        <span class="w-2 h-2 rounded-full bg-sky-400 animate-ping"></span>
        <span class="font-rajdhani text-xs sm:text-sm tracking-[0.25em] text-sky-300/90 uppercase">
          SYS // OMNIVERSE CORE v1.0
        </span>
      </div>

      <!-- Controles Rápidos (Áudio + Pular Intro) -->
      <div class="flex items-center gap-3 pointer-events-auto">
        <button id="btn-audio-toggle"
          class="sci-clip-sm flex items-center gap-2 px-3.5 py-2 bg-slate-900/75 hover:bg-sky-950/80 border border-sky-400/30 text-sky-200 font-rajdhani text-xs sm:text-sm font-semibold tracking-widest uppercase transition-all cursor-pointer"
          title="Ativar / Desativar Áudio Espacial">
          <span id="audio-icon-wrapper" class="flex items-center">
            <!-- Ícone SVG dinâmico -->
            <svg id="icon-audio-muted" class="w-4 h-4 text-amber-400" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" d="M11 5L6 9H2v6h4l5 4V5zM23 9l-6 6M17 9l6 6" />
            </svg>
            <svg id="icon-audio-active" class="w-4 h-4 text-sky-400 hidden" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" d="M11 5L6 9H2v6h4l5 4V5zM15.54 8.46a5 5 0 010 7.07M19.07 4.93a10 10 0 010 14.14" />
            </svg>
          </span>
          <span id="audio-status-label">ATIVAR ÁUDIO</span>
        </button>

        <button id="btn-skip-intro"
          class="sci-clip-sm hidden items-center gap-2 px-4 py-2 bg-slate-950/85 hover:bg-sky-500/20 border border-sky-400/50 text-white font-rajdhani text-xs sm:text-sm font-bold tracking-[0.2em] uppercase transition-all cursor-pointer shadow-lg">
          <span>PULAR INTRO</span>
          <span class="text-[10px] px-1.5 py-0.5 bg-sky-400/20 text-sky-300 border border-sky-400/30 rounded">ESC</span>
        </button>
      </div>
    </header>

    <!-- =====================================================================
         CAMADA 1: SEQUÊNCIA DE ABERTURA CINEMATOGRÁFICA (24 SEGUNDOS)
    ====================================================================== -->
    <section id="intro-layer" class="fixed inset-0 z-20 flex flex-col justify-center items-center px-6 pointer-events-none">

      <!-- CENA 1: O UNIVERSO -->
      <div id="intro-scene-1" class="fade-layer opacity-0 translate-y-4 text-center max-w-2xl">
        <p class="font-rajdhani text-xs sm:text-sm tracking-[0.45em] text-sky-400/80 uppercase mb-3">
          SETOR CÓSMICO DESCONHECIDO // ANO 2284
        </p>
        <h2 class="font-orbitron text-lg sm:text-2xl md:text-3xl font-light tracking-[0.28em] text-slate-200 uppercase">
          Além das fronteiras conhecidas...
        </h2>
      </div>

      <!-- CENA 2: A HUMANIDADE -->
      <div id="intro-scene-2" class="fade-layer opacity-0 translate-y-4 fixed bottom-[14vh] left-6 sm:left-14 max-w-xl text-left hidden">
        <div class="border-l-2 border-sky-400 pl-4 py-1 bg-slate-950/40 backdrop-blur-sm">
          <p class="font-rajdhani text-xs tracking-[0.35em] text-sky-400 uppercase font-semibold">
            ÓRBITA TERRESTRE BAIXA // ESTAÇÃO AEGIS-01
          </p>
          <p class="font-inter text-sm sm:text-base text-slate-200/95 mt-1 leading-relaxed">
            A humanidade expandiu seu alcance até as estrelas, mas uma anomalia dimensional ameaça colapsar múltiplos mundos.
          </p>
        </div>
      </div>

      <!-- CENA 3: O PROTAGONISTA (ALEX CARTER) -->
      <div id="intro-scene-3" class="fade-layer opacity-0 fixed inset-0 flex flex-col md:flex-row items-center justify-center md:justify-between max-w-6xl mx-auto px-6 sm:px-12 hidden">
        <!-- Imagem Oficial Alex Carter -->
        <div class="hero-artwork-wrapper w-full md:w-1/2 flex justify-center items-center order-1 md:order-2 mb-4 md:mb-0">
          <div class="hero-glow-aura"></div>
          <img
            id="intro-alex-img"
            src="https://res.cloudinary.com/dk8ta3dfy/image/upload/v1795442441/Alex_Carter__Her%C3%B3i_do_Omniverse_fsuw1i.png"
            alt="Alex Carter — Protagonista de Omniverse"
            class="official-hero-img max-h-[42vh] sm:max-h-[54vh] md:max-h-[68vh] w-auto rounded-lg"
            onerror="this.style.opacity='0.2'"
          />
        </div>

        <!-- Ficha Tática do Protagonista -->
        <div class="w-full md:w-1/2 order-2 md:order-1 text-center md:text-left z-10">
          <div class="inline-flex items-center gap-2 px-3 py-1 mb-3 bg-sky-500/15 border border-sky-400/40 sci-clip-sm">
            <span class="w-1.5 h-1.5 bg-sky-400 rounded-full"></span>
            <span class="font-rajdhani text-xs sm:text-sm font-bold tracking-[0.32em] text-sky-300 uppercase">
              PROTAGONISTA
            </span>
          </div>
          <h1 class="font-orbitron text-3xl sm:text-5xl md:text-6xl font-black tracking-[0.12em] text-white uppercase drop-shadow-[0_0_25px_rgba(56,189,248,0.45)]">
            ALEX CARTER
          </h1>
          <div class="h-0.5 w-28 bg-gradient-to-r from-sky-400 to-transparent my-4 mx-auto md:mx-0"></div>
          <p class="font-inter text-sm sm:text-base md:text-lg text-slate-200/95 leading-relaxed max-w-lg mx-auto md:mx-0 bg-slate-950/50 backdrop-blur-md p-4 border-l-2 border-sky-400/70 rounded-r-lg">
            "Um ex-piloto militar recrutado para uma unidade especial internacional. Sua missão o levará a atravessar mundos, enfrentar guerras e descobrir uma ameaça que pode mudar o destino da humanidade."
          </p>
        </div>
      </div>

      <!-- CENA 4: A IDENTIDADE DO JOGO (OMNIVERSE) -->
      <div id="intro-scene-4" class="fade-layer opacity-0 fixed inset-0 flex flex-col items-center justify-center px-6 text-center hidden">
        <div class="relative max-w-2xl w-full flex flex-col items-center">
          <div class="hero-glow-aura"></div>
          <img
            id="intro-logo-img"
            src="https://res.cloudinary.com/dk8ta3dfy/image/upload/v1795442446/Omniverse__Al%C3%A9m_dos_Limites_b87k9u.png"
            alt="Omniverse — Beyond The Limits"
            class="official-logo-img max-h-[34vh] sm:max-h-[42vh] w-auto mb-5 rounded-xl"
            onerror="this.style.opacity='0.2'"
          />
          <h1 class="font-orbitron text-3xl sm:text-5xl md:text-6xl font-black tracking-[0.22em] text-white uppercase">
            OMNIVERSE
          </h1>
          <p class="font-rajdhani text-base sm:text-xl font-bold tracking-[0.42em] text-sky-400 uppercase mt-1">
            BEYOND THE LIMITS
          </p>
          <p class="font-inter text-sm sm:text-lg text-slate-200/90 mt-5 tracking-wide max-w-xl">
            "Uma jornada sem limites. Diversos mundos. Um só herói."
          </p>
        </div>
      </div>

      <!-- Barra de Progresso Discreta da Abertura -->
      <div id="intro-progress-container" class="fixed bottom-[10.5vh] left-1/2 -translate-x-1/2 w-48 sm:w-64 h-0.5 bg-slate-800/80 overflow-hidden rounded-full">
        <div id="intro-progress-bar" class="h-full w-0 bg-sky-400 shadow-[0_0_10px_#38bdf8]"></div>
      </div>
    </section>

    <!-- =====================================================================
         CAMADA 2: MENU PRINCIPAL INTERATIVO (ESTILO AAA SCI-FI)
    ====================================================================== -->
    <main id="main-menu-layer" class="fixed inset-0 z-20 hidden opacity-0 transition-opacity duration-1000">
      <div class="relative w-full h-full max-w-7xl mx-auto px-5 sm:px-10 md:px-14 flex flex-col lg:flex-row items-center justify-between pt-16 pb-10">

        <!-- COLUNA ESQUERDA: Identidade Visual & Navegação Principal -->
        <div class="w-full lg:w-[46%] flex flex-col justify-center z-20 pointer-events-auto">
          <!-- Cabeçalho / Emblema Oficial Omniverse -->
          <div class="mb-6 sm:mb-8 flex flex-col items-start">
            <div class="flex items-center gap-3 mb-3">
              <img
                src="https://res.cloudinary.com/dk8ta3dfy/image/upload/v1795442446/Omniverse__Al%C3%A9m_dos_Limites_b87k9u.png"
                alt="Omniverse Emblema"
                class="h-14 sm:h-20 w-auto object-contain rounded-lg drop-shadow-[0_0_16px_rgba(56,189,248,0.45)]"
              />
              <div>
                <span class="block font-rajdhani text-xs tracking-[0.35em] text-sky-400 uppercase font-semibold">
                  UNIDADE ESPECIAL INTERNACIONAL
                </span>
                <h1 class="font-orbitron text-3xl sm:text-5xl font-black tracking-[0.14em] text-white leading-none mt-1">
                  OMNIVERSE
                </h1>
                <span class="block font-rajdhani text-sm sm:text-base tracking-[0.38em] text-sky-300/90 uppercase font-bold mt-1">
                  BEYOND THE LIMITS
                </span>
              </div>
            </div>
            <div class="w-full max-w-md h-[1px] bg-gradient-to-r from-sky-400/70 via-sky-400/20 to-transparent"></div>
          </div>

          <!-- Status do Save / Última Missão -->
          <div id="save-status-banner" class="mb-4 px-3.5 py-2 bg-slate-900/65 border-l-2 border-sky-400/70 text-xs font-rajdhani tracking-widest text-slate-300 uppercase flex items-center justify-between max-w-md">
            <span id="save-status-text">REGISTRO DE CAMPANHA: NENHUM PROGRESSO SALVO</span>
            <span class="text-sky-400 font-bold">PRONTO</span>
          </div>

          <!-- Lista de Botões do Menu Principal -->
          <nav id="main-menu-nav" class="flex flex-col gap-2.5 max-w-md w-full" aria-label="Menu Principal">
            <button class="menu-btn active" data-action="continue" data-index="0">
              <span>CONTINUAR</span>
              <span class="text-xs text-sky-400/80 font-mono">01</span>
            </button>
            <button class="menu-btn" data-action="new-campaign" data-index="1">
              <span>NOVA CAMPANHA</span>
              <span class="text-xs text-sky-400/80 font-mono">02</span>
            </button>
            <button class="menu-btn" data-action="select-mission" data-index="2">
              <span>SELECIONAR MISSÃO</span>
              <span class="text-xs text-sky-400/80 font-mono">03</span>
            </button>
            <button class="menu-btn" data-action="settings" data-index="3">
              <span>CONFIGURAÇÕES</span>
              <span class="text-xs text-sky-400/80 font-mono">04</span>
            </button>
            <button class="menu-btn" data-action="credits" data-index="4">
              <span>CRÉDITOS</span>
              <span class="text-xs text-sky-400/80 font-mono">05</span>
            </button>
          </nav>

          <!-- Rodapé Tático do Menu (Atalhos + Rever Intro) -->
          <div class="mt-6 flex flex-wrap items-center justify-between gap-4 max-w-md text-xs font-rajdhani tracking-widest text-slate-400">
            <div class="flex items-center gap-3">
              <span>[↑↓] NAVEGAR</span>
              <span>[ENTER] CONFIRMAR</span>
            </div>
            <button id="btn-replay-intro"
              class="text-sky-400/80 hover:text-sky-300 underline underline-offset-4 uppercase tracking-widest cursor-pointer transition-colors">
              REVER ABERTURA
            </button>
          </div>
        </div>

        <!-- COLUNA DIREITA: Alex Carter em Destaque Cinematográfico -->
        <div class="w-full lg:w-[52%] h-[42vh] sm:h-[52vh] lg:h-[82vh] flex items-center justify-center lg:justify-end relative pointer-events-none mt-4 lg:mt-0">
          <div id="menu-hero-parallax" class="hero-artwork-wrapper relative h-full flex items-center justify-center">
            <div class="hero-glow-aura"></div>

            <!-- Anéis HUD Orbitais Sutis atrás do Protagonista -->
            <div class="absolute w-[280px] h-[280px] sm:w-[420px] sm:h-[420px] rounded-full border border-sky-400/15 animate-[spin_45s_linear_infinite]"></div>
            <div class="absolute w-[220px] h-[220px] sm:w-[340px] sm:h-[340px] rounded-full border border-dashed border-sky-300/20 animate-[spin_30s_linear_infinite_reverse]"></div>

            <img
              src="https://res.cloudinary.com/dk8ta3dfy/image/upload/v1795442441/Alex_Carter__Her%C3%B3i_do_Omniverse_fsuw1i.png"
              alt="Alex Carter — Comandante Omniverse"
              class="official-hero-img h-full max-h-[38vh] sm:max-h-[48vh] lg:max-h-[78vh] w-auto"
            />

            <!-- Tag Tática Flutuante -->
            <div class="hidden sm:flex flex-col items-end absolute bottom-8 right-2 glass-panel sci-clip-sm px-4 py-2.5 border-r-2 border-r-sky-400">
              <span class="font-rajdhani text-[11px] tracking-[0.3em] text-sky-400 uppercase">OPERATIVO CLASSE-OMEGA</span>
              <span class="font-orbitron text-sm font-bold tracking-widest text-white">ALEX CARTER</span>
            </div>
          </div>
        </div>
      </div>
    </main>

    <!-- =====================================================================
         CAMADA 3: TELA DE SELEÇÃO DE MISSÕES (10 CAPÍTULOS)
    ====================================================================== -->
    <section id="missions-layer" class="fixed inset-0 z-30 hidden opacity-0 transition-opacity duration-500 flex items-center justify-center p-4 sm:p-8 pointer-events-auto">
      <div class="glass-panel sci-clip w-full max-w-6xl h-[88vh] flex flex-col overflow-hidden relative">
        <div class="hud-scanline"></div>

        <!-- Cabeçalho da Tela de Missões -->
        <div class="flex flex-wrap items-center justify-between gap-4 px-6 py-4 border-b border-sky-400/20 bg-slate-950/60">
          <div>
            <span class="font-rajdhani text-xs tracking-[0.35em] text-sky-400 uppercase font-semibold">
              ARQUIVO TÁTICO DE CAMPANHA // 10 CAPÍTULOS
            </span>
            <h2 class="font-orbitron text-xl sm:text-3xl font-bold tracking-wider text-white uppercase">
              SELECIONAR MISSÃO
            </h2>
          </div>

          <div class="flex items-center gap-3">
            <!-- Resumo de missões disponíveis -->
            <div id="missions-counter-badge" class="hidden sm:block px-3 py-1.5 bg-sky-950/70 border border-sky-400/30 rounded text-xs font-rajdhani tracking-widest text-sky-300 uppercase">
              0 / 10 DISPONÍVEIS
            </div>
            <button data-close-modal
              class="sci-clip-sm px-4 py-2 bg-slate-900 hover:bg-sky-500/20 border border-sky-400/40 text-white font-rajdhani text-sm font-bold tracking-widest uppercase transition-all cursor-pointer">
              VOLTAR [ESC]
            </button>
          </div>
        </div>

        <!-- Corpo: Lista/Grade de Capítulos + Painel de Detalhes -->
        <div class="flex-1 grid grid-cols-1 lg:grid-cols-12 overflow-hidden">
          <!-- Lista de 10 Capítulos -->
          <div id="missions-grid" class="lg:col-span-7 p-4 sm:p-6 overflow-y-auto custom-scroll grid grid-cols-1 sm:grid-cols-2 gap-3.5 content-start">
            <!-- Renderizado dinamicamente via JS a partir de MISSIONS_CONFIG -->
          </div>

          <!-- Painel Lateral de Briefing da Missão Selecionada -->
          <div class="lg:col-span-5 border-t lg:border-t-0 lg:border-l border-sky-400/20 bg-slate-950/55 p-5 sm:p-6 flex flex-col justify-between overflow-y-auto custom-scroll">
            <div>
              <!-- Ilustração Temática Dinâmica do Capítulo -->
              <div class="relative w-full h-40 sm:h-48 rounded-lg overflow-hidden border border-sky-400/30 mb-4 bg-slate-900">
                <canvas id="mission-preview-canvas" class="w-full h-full block"></canvas>
                <div class="absolute inset-0 bg-gradient-to-t from-slate-950 via-transparent to-transparent"></div>
                <div class="absolute top-3 left-3 px-2.5 py-1 bg-slate-950/80 border border-sky-400/40 rounded text-xs font-orbitron text-sky-300" id="detail-mission-num">
                  MISSÃO 01
                </div>
                <div class="absolute top-3 right-3 px-2.5 py-1 rounded text-xs font-rajdhani font-bold tracking-widest uppercase" id="detail-mission-status">
                  EM DESENVOLVIMENTO
                </div>
              </div>

              <p class="font-rajdhani text-xs tracking-[0.3em] text-sky-400 uppercase" id="detail-mission-sector">
                SETOR 01 // ÓRBITA TERRESTRE
              </p>
              <h3 class="font-orbitron text-xl sm:text-2xl font-bold text-white tracking-wide uppercase mt-1" id="detail-mission-title">
                OPERAÇÃO TERRA
              </h3>
              <div class="w-16 h-0.5 bg-sky-400 my-3"></div>
              <p class="font-inter text-sm text-slate-300 leading-relaxed" id="detail-mission-desc">
                Descrição tática da missão selecionada.
              </p>
            </div>

            <!-- Área de Ação / Iniciar Missão -->
            <div class="mt-6 pt-4 border-t border-sky-400/15">
              <button id="btn-launch-mission"
                disabled
                class="sci-clip w-full py-3.5 px-6 font-orbitron text-sm sm:text-base font-bold tracking-[0.2em] uppercase transition-all flex items-center justify-center gap-3 disabled:opacity-50 disabled:cursor-not-allowed bg-slate-800 text-slate-400 border border-slate-600">
                <span id="btn-launch-label">EM BREVE</span>
              </button>
              <p id="detail-mission-hint" class="text-[11px] font-rajdhani tracking-widest text-slate-400 uppercase text-center mt-2">
                CAPÍTULO BLOQUEADO — AGUARDANDO ENDEREÇO DE EXECUÇÃO
              </p>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- =====================================================================
         CAMADA 4: TELA DE CONFIGURAÇÕES
    ====================================================================== -->
    <section id="settings-layer" class="fixed inset-0 z-30 hidden opacity-0 transition-opacity duration-500 flex items-center justify-center p-4 sm:p-8 pointer-events-auto">
      <div class="glass-panel sci-clip w-full max-w-2xl overflow-hidden relative">
        <div class="flex items-center justify-between px-6 py-4 border-b border-sky-400/20 bg-slate-950/60">
          <div>
            <span class="font-rajdhani text-xs tracking-[0.35em] text-sky-400 uppercase">SISTEMA // PARÂMETROS</span>
            <h2 class="font-orbitron text-xl sm:text-2xl font-bold text-white uppercase">CONFIGURAÇÕES</h2>
          </div>
          <button data-close-modal
            class="sci-clip-sm px-4 py-2 bg-slate-900 hover:bg-sky-500/20 border border-sky-400/40 text-white font-rajdhani text-sm font-bold tracking-widest uppercase cursor-pointer">
            VOLTAR [ESC]
          </button>
        </div>

        <div class="p-6 space-y-5 max-h-[75vh] overflow-y-auto custom-scroll">
          <!-- Volume Geral -->
          <div>
            <div class="flex justify-between font-rajdhani text-sm font-semibold tracking-widest uppercase mb-1.5">
              <span class="text-slate-200">VOLUME GERAL</span>
              <span id="val-master-vol" class="text-sky-400">80%</span>
            </div>
            <input id="opt-master-vol" type="range" min="0" max="100" value="80" class="sci-slider" />
          </div>

          <!-- Volume Música -->
          <div>
            <div class="flex justify-between font-rajdhani text-sm font-semibold tracking-widest uppercase mb-1.5">
              <span class="text-slate-200">MÚSICA AMBIENTE ESPACIAL</span>
              <span id="val-music-vol" class="text-sky-400">70%</span>
            </div>
            <input id="opt-music-vol" type="range" min="0" max="100" value="70" class="sci-slider" />
          </div>

          <!-- Volume Efeitos Sonoros -->
          <div>
            <div class="flex justify-between font-rajdhani text-sm font-semibold tracking-widest uppercase mb-1.5">
              <span class="text-slate-200">EFEITOS SONOROS (SFX)</span>
              <span id="val-sfx-vol" class="text-sky-400">85%</span>
            </div>
            <input id="opt-sfx-vol" type="range" min="0" max="100" value="85" class="sci-slider" />
          </div>

          <div class="h-px bg-sky-400/15 my-2"></div>

          <!-- Qualidade Gráfica 3D -->
          <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-2">
            <div>
              <span class="block font-rajdhani text-sm font-semibold tracking-widest uppercase text-slate-200">QUALIDADE GRÁFICA 3D</span>
              <span class="text-xs text-slate-400">Ajusta densidade estelar, atmosfera e resolução do renderizador</span>
            </div>
            <div class="flex gap-2" id="quality-selector">
              <button data-quality="low" class="quality-btn px-3 py-1.5 rounded border border-slate-700 bg-slate-900 text-xs font-rajdhani font-bold tracking-widest uppercase text-slate-300 cursor-pointer">BAIXA</button>
              <button data-quality="medium" class="quality-btn px-3 py-1.5 rounded border border-slate-700 bg-slate-900 text-xs font-rajdhani font-bold tracking-widest uppercase text-slate-300 cursor-pointer">MÉDIA</button>
              <button data-quality="high" class="quality-btn px-3 py-1.5 rounded border border-sky-400 bg-sky-500/20 text-xs font-rajdhani font-bold tracking-widest uppercase text-white cursor-pointer">ALTA</button>
            </div>
          </div>

          <!-- Escala da Interface -->
          <div>
            <div class="flex justify-between font-rajdhani text-sm font-semibold tracking-widest uppercase mb-1.5">
              <span class="text-slate-200">ESCALA DA INTERFACE (HUD)</span>
              <span id="val-ui-scale" class="text-sky-400">100%</span>
            </div>
            <input id="opt-ui-scale" type="range" min="85" max="115" step="5" value="100" class="sci-slider" />
          </div>

          <!-- Modo Tela Cheia -->
          <div class="flex items-center justify-between pt-2">
            <div>
              <span class="block font-rajdhani text-sm font-semibold tracking-widest uppercase text-slate-200">EXIBIÇÃO EM TELA CHEIA</span>
              <span class="text-xs text-slate-400">Imersão cinematográfica completa</span>
            </div>
            <button id="btn-fullscreen-toggle"
              class="sci-clip-sm px-4 py-2 bg-sky-500/20 hover:bg-sky-500/30 border border-sky-400/50 text-sky-200 font-rajdhani text-xs font-bold tracking-widest uppercase cursor-pointer">
              ALTERNAR TELA CHEIA
            </button>
          </div>
        </div>
      </div>
    </section>

    <!-- =====================================================================
         CAMADA 5: TELA DE CRÉDITOS
    ====================================================================== -->
    <section id="credits-layer" class="fixed inset-0 z-30 hidden opacity-0 transition-opacity duration-500 flex items-center justify-center p-4 sm:p-8 pointer-events-auto">
      <div class="glass-panel sci-clip w-full max-w-2xl overflow-hidden relative text-center">
        <div class="flex items-center justify-between px-6 py-4 border-b border-sky-400/20 bg-slate-950/60 text-left">
          <div>
            <span class="font-rajdhani text-xs tracking-[0.35em] text-sky-400 uppercase">ARQUIVO DE PRODUÇÃO</span>
            <h2 class="font-orbitron text-xl sm:text-2xl font-bold text-white uppercase">CRÉDITOS</h2>
          </div>
          <button data-close-modal
            class="sci-clip-sm px-4 py-2 bg-slate-900 hover:bg-sky-500/20 border border-sky-400/40 text-white font-rajdhani text-sm font-bold tracking-widest uppercase cursor-pointer">
            VOLTAR [ESC]
          </button>
        </div>

        <div class="p-8 space-y-6 max-h-[75vh] overflow-y-auto custom-scroll">
          <img
            src="https://res.cloudinary.com/dk8ta3dfy/image/upload/v1795442446/Omniverse__Al%C3%A9m_dos_Limites_b87k9u.png"
            alt="Omniverse Logo"
            class="h-24 mx-auto object-contain rounded-lg drop-shadow-[0_0_20px_rgba(56,189,248,0.4)]"
          />
          <div>
            <h3 class="font-orbitron text-2xl font-black tracking-[0.2em] text-white">OMNIVERSE</h3>
            <p class="font-rajdhani text-sm tracking-[0.4em] text-sky-400 uppercase">BEYOND THE LIMITS</p>
          </div>

          <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 text-left bg-slate-950/50 p-5 rounded-lg border border-sky-400/15">
            <div>
              <p class="font-rajdhani text-xs tracking-widest text-sky-400 uppercase">CRIAÇÃO, DIREÇÃO & UNIVERSO</p>
              <p class="font-inter text-sm text-white font-medium mt-0.5">Estúdio Omniverse Interactive</p>
            </div>
            <div>
              <p class="font-rajdhani text-xs tracking-widest text-sky-400 uppercase">PROTAGONISTA</p>
              <p class="font-inter text-sm text-white font-medium mt-0.5">Comandante Alex Carter</p>
            </div>
            <div>
              <p class="font-rajdhani text-xs tracking-widest text-sky-400 uppercase">MOTOR ESPACIAL & RENDERIZAÇÃO</p>
              <p class="font-inter text-sm text-white font-medium mt-0.5">WebGL / Three.js Orbital Engine</p>
            </div>
            <div>
              <p class="font-rajdhani text-xs tracking-widest text-sky-400 uppercase">DESIGN SONORO PROCEDURAL</p>
              <p class="font-inter text-sm text-white font-medium mt-0.5">Web Audio API Synthesizer</p>
            </div>
          </div>

          <p class="font-inter text-xs text-slate-400 italic">
            "Uma jornada sem limites. Diversos mundos. Um só herói."
          </p>
        </div>
      </div>
    </section>

    <!-- =====================================================================
         CAMADA 6: TRANSIÇÃO DE LANÇAMENTO DE MISSÃO & NOTIFICAÇÕES HUD
    ====================================================================== -->
    <div id="launch-transition-overlay" class="fixed inset-0 z-50 hidden bg-slate-950/95 backdrop-blur-xl flex flex-col items-center justify-center p-6 text-center pointer-events-auto">
      <div class="w-16 h-16 border-2 border-sky-400 border-t-transparent rounded-full animate-spin mb-6"></div>
      <p class="font-rajdhani text-xs tracking-[0.4em] text-sky-400 uppercase mb-2">
        INICIALIZANDO TELEMETRIA DE SALTO
      </p>
      <h2 id="launch-mission-title" class="font-orbitron text-2xl sm:text-4xl font-black tracking-widest text-white uppercase">
        CARREGANDO MISSÃO...
      </h2>
      <div class="w-64 sm:w-80 h-1 bg-slate-800 rounded-full overflow-hidden mt-6">
        <div id="launch-progress-bar" class="h-full w-0 bg-sky-400 transition-all duration-300"></div>
      </div>
    </div>

    <!-- Toast / Mensagem Tática Substituindo alert() -->
    <div id="hud-toast" class="fixed bottom-6 right-6 z-50 hidden glass-panel sci-clip-sm px-5 py-3.5 border-l-4 border-l-sky-400 max-w-sm pointer-events-none transition-all duration-300">
      <p id="hud-toast-title" class="font-rajdhani text-xs font-bold tracking-[0.25em] text-sky-400 uppercase">SISTEMA</p>
      <p id="hud-toast-msg" class="font-inter text-xs sm:text-sm text-slate-200 mt-0.5"></p>
    </div>

  </div>

  <!-- =========================================================================
       SCRIPT PRINCIPAL — ARQUITETURA MODULAR OMNIVERSE
  ========================================================================== -->
  <script>
    /**
     * =========================================================================
     * 1. CADASTRO CENTRALIZADO DE MISSÕES (10 CAPÍTULOS DE OMNIVERSE)
     * =========================================================================
     * INSTRUÇÕES PARA ATIVAR FUTURAMENTE UMA MISSÃO:
     * Basta preencher a propriedade `url` com o caminho do arquivo HTML ou link
     * (ex: "missoes/operacao-terra.html" ou "https://seu-dominio.com/missao1").
     * A interface detecta automaticamente URLs válidas e ativa o botão
     * "INICIAR MISSÃO" sem necessidade de alterar o restante do código.
     */
    const MISSIONS_CONFIG = [
      {
        id: 1,
        number: "01",
        title: "OPERAÇÃO TERRA",
        sector: "SETOR 01 // DEFESA PLANETÁRIA",
        description: "Combate terrestre e operações especiais em zonas urbanas sob cerco. Alex Carter lidera a vanguarda para conter a primeira incursão.",
        themeColor: "#38bdf8",
        visualType: "planet_defense",
        url: null, // <-- Insira aqui o link futuro (ex: "missao01.html")
        available: false
      },
      {
        id: 2,
        number: "02",
        title: "CÉU EM CHAMAS",
        sector: "SETOR 02 // SUPERIORIDADE AÉREA",
        description: "Combate aéreo de alta velocidade na estratosfera e escolta de transportes aliados sob fogo cruzado de interceptadores inimigos.",
        themeColor: "#fb923c",
        visualType: "aerial_combat",
        url: null,
        available: false
      },
      {
        id: 3,
        number: "03",
        title: "SILÊNCIO NO ESPAÇO",
        sector: "SETOR 03 // ÓRBITA PROFUNDA",
        description: "Exploração e pilotagem espacial em gravidade zero entre destroços de uma frota perdida nos limites do sistema solar.",
        themeColor: "#818cf8",
        visualType: "deep_space",
        url: null,
        available: false
      },
      {
        id: 4,
        number: "04",
        title: "ESTAÇÃO FANTASMA",
        sector: "SETOR 04 // COMPLEXO ORBITAL",
        description: "Abordagem tática a uma estação de pesquisa abandonada que voltou a transmitir sinais criptografados de origem desconhecida.",
        themeColor: "#2dd4bf",
        visualType: "ghost_station",
        url: null,
        available: false
      },
      {
        id: 5,
        number: "05",
        title: "TERRITÓRIO HOSTIL",
        sector: "SETOR 05 // EXOPLANETA KRATOS-IV",
        description: "Sobrevivência e reconhecimento em um mundo alienígena instável, enfrentando tempestades eletromagnéticas e forças hostis.",
        themeColor: "#f43f5e",
        visualType: "hostile_world",
        url: null,
        available: false
      },
      {
        id: 6,
        number: "06",
        title: "ZONA DE GUERRA",
        sector: "SETOR 06 // FRENTE DE BATALHA",
        description: "Ofensiva conjunta em larga escala com apoio orbital para romper a linha de defesa principal da coalizão adversária.",
        themeColor: "#f59e0b",
        visualType: "warzone",
        url: null,
        available: false
      },
      {
        id: 7,
        number: "07",
        title: "ALÉM DA ATMOSFERA",
        sector: "SETOR 07 // SALTO EXOSFÉRICO",
        description: "Lançamento orbital de emergência e interceptação de cruzadores pesados antes que ativem o salto hiperespacial.",
        themeColor: "#60a5fa",
        visualType: "beyond_atmosphere",
        url: null,
        available: false
      },
      {
        id: 8,
        number: "08",
        title: "INFILTRAÇÃO",
        sector: "SETOR 08 // CIDADELA SUBTERRÂNEA",
        description: "Operação furtiva de alta precisão no núcleo de comando inimigo para extrair os códigos da matriz dimensional.",
        themeColor: "#a78bfa",
        visualType: "infiltration",
        url: null,
        available: false
      },
      {
        id: 9,
        number: "09",
        title: "ÚLTIMA FRONTEIRA",
        sector: "SETOR 09 // LIMIAR DA FENDA",
        description: "A batalha decisiva na borda do espaço conhecido, onde todas as frotas convergem diante do colapso dimensional.",
        themeColor: "#e879f9",
        visualType: "last_frontier",
        url: null,
        available: false
      },
      {
        id: 10,
        number: "10",
        title: "OMNIVERSE",
        sector: "SETOR 10 // CONVERGÊNCIA FINAL",
        description: "Além de todos os limites. Alex Carter atravessa o portal central para selar o destino de todos os mundos.",
        themeColor: "#38bdf8",
        visualType: "omniverse_core",
        url: null,
        available: false
      }
    ];

    /**
     * Normaliza a lista de missões verificando automaticamente se há URL válida.
     */
    function getProcessedMissions() {
      return MISSIONS_CONFIG.map((mission) => {
        const hasValidUrl = typeof mission.url === "string" && mission.url.trim().length > 0;
        return {
          ...mission,
          isPlayable: hasValidUrl || mission.available === true
        };
      });
    }

    /**
     * =========================================================================
     * 2. MOTOR DE ÁUDIO PROCEDURAL (WEB AUDIO API)
     * =========================================================================
     * Gera trilha ambiente espacial profunda, risers cinematográficos e SFX táteis
     * sem depender de arquivos externos de áudio.
     */
    class OmniverseAudioEngine {
      constructor() {
        this.ctx = null;
        this.isEnabled = false;
        this.masterGain = null;
        this.musicGain = null;
        this.sfxGain = null;
        this.droneStarted = false;

        this.volumes = {
          master: 0.80,
          music: 0.70,
          sfx: 0.85
        };
      }

      initContext() {
        if (!this.ctx) {
          const AudioCtx = window.AudioContext || window.webkitAudioContext;
          this.ctx = new AudioCtx();

          this.masterGain = this.ctx.createGain();
          this.musicGain = this.ctx.createGain();
          this.sfxGain = this.ctx.createGain();

          this.musicGain.connect(this.masterGain);
          this.sfxGain.connect(this.masterGain);
          this.masterGain.connect(this.ctx.destination);

          this.applyVolumes();
        }
        if (this.ctx.state === "suspended") {
          this.ctx.resume();
        }
      }

      enable() {
        this.initContext();
        this.isEnabled = true;
        this.applyVolumes();
        if (!this.droneStarted) {
          this.startSpaceAmbientDrone();
          this.droneStarted = true;
        }
        this.updateUI();
      }

      disable() {
        this.isEnabled = false;
        if (this.masterGain && this.ctx) {
          this.masterGain.gain.setTargetAtTime(0.0001, this.ctx.currentTime, 0.25);
        }
        this.updateUI();
      }

      toggle() {
        if (this.isEnabled) {
          this.disable();
        } else {
          this.enable();
          this.playClick();
        }
      }

      applyVolumes() {
        if (!this.ctx || !this.masterGain) return;
        const now = this.ctx.currentTime;
        const targetMaster = this.isEnabled ? this.volumes.master : 0.0001;
        this.masterGain.gain.setTargetAtTime(targetMaster, now, 0.15);
        this.musicGain.gain.setTargetAtTime(this.volumes.music * 0.35, now, 0.15);
        this.sfxGain.gain.setTargetAtTime(this.volumes.sfx * 0.5, now, 0.15);
      }

      setVolume(type, percent) {
        this.volumes[type] = Math.max(0, Math.min(1, percent / 100));
        this.applyVolumes();
      }

      updateUI() {
        const label = document.getElementById("audio-status-label");
        const iconMuted = document.getElementById("icon-audio-muted");
        const iconActive = document.getElementById("icon-audio-active");
        if (!label) return;

        if (this.isEnabled) {
          label.textContent = "ÁUDIO ATIVO";
          iconMuted.classList.add("hidden");
          iconActive.classList.remove("hidden");
        } else {
          label.textContent = "ATIVAR ÁUDIO";
          iconMuted.classList.remove("hidden");
          iconActive.classList.add("hidden");
        }
      }

      /**
       * Sintetiza atmosfera espacial profunda com acorde orbital e filtro modulado
       */
      startSpaceAmbientDrone() {
        if (!this.ctx) return;
        const now = this.ctx.currentTime;

        const filter = this.ctx.createBiquadFilter();
        filter.type = "lowpass";
        filter.frequency.setValueAtTime(240, now);
        filter.Q.setValueAtTime(2.5, now);
        filter.connect(this.musicGain);

        // LFO lento para modular o brilho da nebulosa sonora
        const lfo = this.ctx.createOscillator();
        const lfoGain = this.ctx.createGain();
        lfo.frequency.setValueAtTime(0.08, now);
        lfoGain.gain.setValueAtTime(110, now);
        lfo.connect(lfoGain);
        lfoGain.connect(filter.frequency);
        lfo.start(now);

        // Frequências harmônicas profundas (Ré menor espacial: D1, A1, D2, F2)
        const freqs = [36.71, 55.00, 73.42, 87.31];
        freqs.forEach((f, idx) => {
          const osc = this.ctx.createOscillator();
          const oscGain = this.ctx.createGain();
          osc.type = idx % 2 === 0 ? "sawtooth" : "triangle";
          osc.frequency.setValueAtTime(f, now);
          osc.detune.setValueAtTime((idx - 1.5) * 6, now);

          oscGain.gain.setValueAtTime(0.001, now);
          oscGain.gain.exponentialRampToValueAtTime(0.16 / (idx + 1), now + 3.5);

          osc.connect(oscGain);
          oscGain.connect(filter);
          osc.start(now);
        });
      }

      playHover() {
        if (!this.isEnabled || !this.ctx) return;
        const now = this.ctx.currentTime;
        const osc = this.ctx.createOscillator();
        const gain = this.ctx.createGain();
        osc.type = "sine";
        osc.frequency.setValueAtTime(620, now);
        osc.frequency.exponentialRampToValueAtTime(980, now + 0.055);

        gain.gain.setValueAtTime(0.14, now);
        gain.gain.exponentialRampToValueAtTime(0.001, now + 0.06);

        osc.connect(gain);
        gain.connect(this.sfxGain);
        osc.start(now);
        osc.stop(now + 0.065);
      }

      playClick() {
        if (!this.isEnabled || !this.ctx) return;
        const now = this.ctx.currentTime;
        const osc = this.ctx.createOscillator();
        const gain = this.ctx.createGain();
        osc.type = "triangle";
        osc.frequency.setValueAtTime(420, now);
        osc.frequency.exponentialRampToValueAtTime(1180, now + 0.09);

        gain.gain.setValueAtTime(0.28, now);
        gain.gain.exponentialRampToValueAtTime(0.001, now + 0.12);

        osc.connect(gain);
        gain.connect(this.sfxGain);
        osc.start(now);
        osc.stop(now + 0.13);
      }

      playBlocked() {
        if (!this.isEnabled || !this.ctx) return;
        const now = this.ctx.currentTime;
        const osc = this.ctx.createOscillator();
        const gain = this.ctx.createGain();
        osc.type = "sawtooth";
        osc.frequency.setValueAtTime(160, now);
        osc.frequency.linearRampToValueAtTime(110, now + 0.16);

        gain.gain.setValueAtTime(0.22, now);
        gain.gain.exponentialRampToValueAtTime(0.001, now + 0.18);

        osc.connect(gain);
        gain.connect(this.sfxGain);
        osc.start(now);
        osc.stop(now + 0.19);
      }

      playSceneTransition() {
        if (!this.isEnabled || !this.ctx) return;
        const now = this.ctx.currentTime;
        const osc = this.ctx.createOscillator();
        const filter = this.ctx.createBiquadFilter();
        const gain = this.ctx.createGain();

        osc.type = "sine";
        osc.frequency.setValueAtTime(95, now);
        osc.frequency.exponentialRampToValueAtTime(280, now + 0.85);

        filter.type = "bandpass";
        filter.frequency.setValueAtTime(320, now);

        gain.gain.setValueAtTime(0.001, now);
        gain.gain.linearRampToValueAtTime(0.24, now + 0.35);
        gain.gain.exponentialRampToValueAtTime(0.001, now + 1.1);

        osc.connect(filter);
        filter.connect(gain);
        gain.connect(this.sfxGain);
        osc.start(now);
        osc.stop(now + 1.15);
      }
    }

    /**
     * =========================================================================
     * 3. MOTOR ESPACIAL 3D (THREE.JS)
     * =========================================================================
     * Renderiza o campo de estrelas em múltiplas profundidades, nebulosa volumétrica,
     * planeta estilo Terra com atmosfera e luzes noturnas, estação espacial orbital
     * detalhada e esquadrão de aeronaves cruzando o espaço.
     */
    class OmniverseSpaceScene {
      constructor(canvas) {
        this.canvas = canvas;
        this.quality = "high";
        this.mode = "intro"; // 'intro' ou 'menu'
        this.introProgress = 0; // 0 a 1 ao longo dos 24s
        this.mouse = { x: 0, y: 0, targetX: 0, targetY: 0 };

        this.scene = new THREE.Scene();
        this.scene.fog = new THREE.FogExp2(0x02050c, 0.0018);

        this.camera = new THREE.PerspectiveCamera(
          52,
          window.innerWidth / window.innerHeight,
          0.1,
          2000
        );
        this.camera.position.set(0, 4, 95);

        this.renderer = new THREE.WebGLRenderer({
          canvas: this.canvas,
          antialias: true,
          powerPreference: "high-performance"
        });
        this.renderer.setSize(window.innerWidth, window.innerHeight);
        this.renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

        this.clock = new THREE.Clock();
        this.ships = [];

        this.initLights();
        this.initStarfield();
        this.initNebulaClouds();
        this.initEarthAndStation();
        this.initStarfighters();

        window.addEventListener("resize", () => this.onResize());
      }

      initLights() {
        const ambient = new THREE.AmbientLight(0x1e3a5f, 0.65);
        this.scene.add(ambient);

        // Luz solar cinematográfica lateral
        this.sunLight = new THREE.DirectionalLight(0xe0f2fe, 2.2);
        this.sunLight.position.set(60, 28, 45);
        this.scene.add(this.sunLight);

        // Luz de preenchimento ciano (rim light)
        const rimLight = new THREE.PointLight(0x38bdf8, 1.8, 180);
        rimLight.position.set(-45, -18, 25);
        this.scene.add(rimLight);
      }

      /**
       * Cria texturas procedurais em memória (sem URLs externas) para estrelas,
       * nebulosas e superfície planetária.
       */
      createRadialTexture(innerColor, outerColor, size = 128) {
        const c = document.createElement("canvas");
        c.width = size;
        c.height = size;
        const ctx = c.getContext("2d");
        const grad = ctx.createRadialGradient(size / 2, size / 2, 0, size / 2, size / 2, size / 2);
        grad.addColorStop(0, innerColor);
        grad.addColorStop(0.4, outerColor);
        grad.addColorStop(1, "rgba(0,0,0,0)");
        ctx.fillStyle = grad;
        ctx.fillRect(0, 0, size, size);
        return new THREE.CanvasTexture(c);
      }

      createPlanetTexture() {
        const c = document.createElement("canvas");
        c.width = 512;
        c.height = 256;
        const ctx = c.getContext("2d");

        // Oceano profundo
        const oceanGrad = ctx.createLinearGradient(0, 0, 0, 256);
        oceanGrad.addColorStop(0, "#0c2d48");
        oceanGrad.addColorStop(0.5, "#071e3d");
        oceanGrad.addColorStop(1, "#092642");
        ctx.fillStyle = oceanGrad;
        ctx.fillRect(0, 0, 512, 256);

        // Continentes estilizados e luzes de metrópoles orbitais
        for (let i = 0; i < 48; i++) {
          const x = (Math.sin(i * 19.3) * 0.5 + 0.5) * 512;
          const y = (Math.cos(i * 11.7) * 0.38 + 0.5) * 256;
          const r = 14 + (i % 5) * 8;

          const landGrad = ctx.createRadialGradient(x, y, 2, x, y, r);
          landGrad.addColorStop(0, "rgba(30, 82, 92, 0.85)");
          landGrad.addColorStop(0.7, "rgba(18, 52, 68, 0.55)");
          landGrad.addColorStop(1, "rgba(7, 30, 61, 0)");
          ctx.fillStyle = landGrad;
          ctx.beginPath();
          ctx.arc(x, y, r, 0, Math.PI * 2);
          ctx.fill();

          // Pontos dourados de cidades iluminadas no lado noturno
          if (i % 2 === 0) {
            ctx.fillStyle = "rgba(251, 191, 36, 0.75)";
            ctx.fillRect(x + 2, y - 2, 1.8, 1.8);
            ctx.fillRect(x - 4, y + 3, 1.4, 1.4);
          }
        }

        // Camada sutil de nuvens atmosféricas
        ctx.fillStyle = "rgba(224, 242, 254, 0.14)";
        for (let j = 0; j < 35; j++) {
          const cx = (j * 47) % 512;
          const cy = 24 + ((j * 31) % 208);
          ctx.beginPath();
          ctx.ellipse(cx, cy, 34, 5, 0.1, 0, Math.PI * 2);
          ctx.fill();
        }

        return new THREE.CanvasTexture(c);
      }

      initStarfield() {
        if (this.starGroup) this.scene.remove(this.starGroup);
        this.starGroup = new THREE.Group();

        const countMultiplier = this.quality === "low" ? 0.45 : this.quality === "medium" ? 0.75 : 1.0;
        const starCount = Math.floor(3200 * countMultiplier);
        const geometry = new THREE.BufferGeometry();
        const positions = new Float32Array(starCount * 3);
        const colors = new Float32Array(starCount * 3);

        const palette = [
          new THREE.Color(0xffffff),
          new THREE.Color(0xbae6fd),
          new THREE.Color(0x38bdf8),
          new THREE.Color(0xfef3c7)
        ];

        for (let i = 0; i < starCount; i++) {
          const i3 = i * 3;
          positions[i3] = (Math.random() - 0.5) * 650;
          positions[i3 + 1] = (Math.random() - 0.5) * 420;
          positions[i3 + 2] = (Math.random() - 0.5) * 650;

          const col = palette[i % palette.length];
          colors[i3] = col.r;
          colors[i3 + 1] = col.g;
          colors[i3 + 2] = col.b;
        }

        geometry.setAttribute("position", new THREE.BufferAttribute(positions, 3));
        geometry.setAttribute("color", new THREE.BufferAttribute(colors, 3));

        const starTex = this.createRadialTexture("rgba(255,255,255,1)", "rgba(186,230,253,0.4)", 64);
        const material = new THREE.PointsMaterial({
          size: 1.35,
          map: starTex,
          vertexColors: true,
          transparent: true,
          opacity: 0.9,
          depthWrite: false,
          blending: THREE.AdditiveBlending
        });

        this.starsPoints = new THREE.Points(geometry, material);
        this.starGroup.add(this.starsPoints);
        this.scene.add(this.starGroup);
      }

      initNebulaClouds() {
        this.nebulaGroup = new THREE.Group();
        const texBlue = this.createRadialTexture("rgba(56, 189, 248, 0.24)", "rgba(30, 64, 175, 0.08)", 256);
        const texViolet = this.createRadialTexture("rgba(129, 140, 248, 0.18)", "rgba(76, 29, 149, 0.05)", 256);

        const configs = [
          { tex: texBlue, pos: [-45, 18, -140], scale: 170 },
          { tex: texViolet, pos: [52, -14, -160], scale: 190 },
          { tex: texBlue, pos: [0, 8, -190], scale: 220 }
        ];

        configs.forEach((cfg) => {
          const mat = new THREE.SpriteMaterial({
            map: cfg.tex,
            transparent: true,
            opacity: 0.75,
            blending: THREE.AdditiveBlending,
            depthWrite: false
          });
          const sprite = new THREE.Sprite(mat);
          sprite.position.set(...cfg.pos);
          sprite.scale.set(cfg.scale, cfg.scale * 0.65, 1);
          this.nebulaGroup.add(sprite);
        });

        this.scene.add(this.nebulaGroup);
      }

      initEarthAndStation() {
        this.planetarySystem = new THREE.Group();
        this.planetarySystem.position.set(-14, -5, -18);

        // Planeta estilo Terra
        const planetGeo = new THREE.SphereGeometry(16, 48, 48);
        const planetMat = new THREE.MeshStandardMaterial({
          map: this.createPlanetTexture(),
          roughness: 0.68,
          metalness: 0.12
        });
        this.planetMesh = new THREE.Mesh(planetGeo, planetMat);
        this.planetarySystem.add(this.planetMesh);

        // Halo Atmosférico Brilhante
        const atmoGeo = new THREE.SphereGeometry(16.85, 48, 48);
        const atmoMat = new THREE.MeshBasicMaterial({
          color: 0x38bdf8,
          transparent: true,
          opacity: 0.14,
          side: THREE.BackSide,
          blending: THREE.AdditiveBlending
        });
        const atmoMesh = new THREE.Mesh(atmoGeo, atmoMat);
        this.planetarySystem.add(atmoMesh);

        // Estação Espacial Orbital AEGIS-01
        this.stationPivot = new THREE.Group();
        this.stationPivot.rotation.z = 0.32;
        this.stationPivot.rotation.x = 0.22;

        const station = new THREE.Group();
        station.position.set(22.5, 2.5, 4);

        // Eixo central da estação
        const coreGeo = new THREE.CylinderGeometry(0.35, 0.35, 4.4, 12);
        const hullMat = new THREE.MeshStandardMaterial({
          color: 0xe2e8f0,
          metalness: 0.75,
          roughness: 0.25
        });
        const core = new THREE.Mesh(coreGeo, hullMat);
        core.rotation.z = Math.PI / 2;
        station.add(core);

        // Anel gravitacional
        const ringGeo = new THREE.TorusGeometry(1.45, 0.14, 10, 32);
        const ring = new THREE.Mesh(ringGeo, hullMat);
        ring.rotation.y = Math.PI / 2;
        station.add(ring);

        // Painéis solares azul-cobalto
        const panelGeo = new THREE.BoxGeometry(0.06, 3.4, 0.85);
        const panelMat = new THREE.MeshStandardMaterial({
          color: 0x0284c7,
          emissive: 0x0369a1,
          emissiveIntensity: 0.4,
          metalness: 0.85,
          roughness: 0.2
        });
        const panelLeft = new THREE.Mesh(panelGeo, panelMat);
        panelLeft.position.x = -1.6;
        const panelRight = new THREE.Mesh(panelGeo, panelMat);
        panelRight.position.x = 1.6;
        station.add(panelLeft, panelRight);

        // Farol de sinalização orbital
        const beaconGeo = new THREE.SphereGeometry(0.14, 8, 8);
        const beaconMat = new THREE.MeshBasicMaterial({ color: 0x38bdf8 });
        const beacon = new THREE.Mesh(beaconGeo, beaconMat);
        beacon.position.set(0, 1.5, 0);
        station.add(beacon);

        this.stationMesh = station;
        this.stationPivot.add(station);
        this.planetarySystem.add(this.stationPivot);

        this.scene.add(this.planetarySystem);
      }

      /**
       * Cria aeronaves espaciais com propulsores luminosos que cruzam a cena
       */
      initStarfighters() {
        const shipCount = 5;
        for (let i = 0; i < shipCount; i++) {
          const ship = new THREE.Group();

          // Fuselagem cônica futurista
          const bodyGeo = new THREE.ConeGeometry(0.22, 1.35, 6);
          bodyGeo.rotateZ(-Math.PI / 2);
          const bodyMat = new THREE.MeshStandardMaterial({
            color: 0xcbd5e1,
            metalness: 0.8,
            roughness: 0.2
          });
          const body = new THREE.Mesh(bodyGeo, bodyMat);
          ship.add(body);

          // Brilho do motor iônico
          const thrusterGeo = new THREE.SphereGeometry(0.18, 8, 8);
          const thrusterMat = new THREE.MeshBasicMaterial({ color: 0x38bdf8 });
          const thruster = new THREE.Mesh(thrusterGeo, thrusterMat);
          thruster.position.x = -0.7;
          ship.add(thruster);

          // Rastro luminoso alongado
          const trailGeo = new THREE.BoxGeometry(2.6, 0.05, 0.05);
          const trailMat = new THREE.MeshBasicMaterial({
            color: 0x38bdf8,
            transparent: true,
            opacity: 0.55
          });
          const trail = new THREE.Mesh(trailGeo, trailMat);
          trail.position.x = -2.0;
          ship.add(trail);

          this.resetShipPosition(ship, i * 18 - 45);
          this.scene.add(ship);
          this.ships.push({
            mesh: ship,
            speed: 14 + (i % 3) * 4.5
          });
        }
      }

      resetShipPosition(ship, startX = -65) {
        ship.position.set(
          startX,
          (Math.random() - 0.45) * 22,
          -12 + (Math.random() - 0.5) * 24
        );
      }

      setQuality(level) {
        this.quality = level;
        const pixelRatio = level === "low" ? 1 : level === "medium" ? 1.5 : Math.min(window.devicePixelRatio, 2);
        this.renderer.setPixelRatio(pixelRatio);
        this.initStarfield();
      }

      setMouseParallax(nx, ny) {
        this.mouse.targetX = nx;
        this.mouse.targetY = ny;
      }

      onResize() {
        this.camera.aspect = window.innerWidth / window.innerHeight;
        this.camera.updateProjectionMatrix();
        this.renderer.setSize(window.innerWidth, window.innerHeight);
      }

      update() {
        const dt = this.clock.getDelta();
        const elapsed = this.clock.getElapsedTime();

        // Suavização do movimento do mouse
        this.mouse.x += (this.mouse.targetX - this.mouse.x) * 0.05;
        this.mouse.y += (this.mouse.targetY - this.mouse.y) * 0.05;

        // Rotação contínua das estrelas e nebulosa
        if (this.starGroup) {
          this.starGroup.rotation.y += dt * 0.008;
        }
        if (this.nebulaGroup) {
          this.nebulaGroup.children.forEach((sprite, idx) => {
            sprite.material.rotation += dt * (idx % 2 === 0 ? 0.012 : -0.01);
          });
        }

        // Rotação do Planeta e Órbita da Estação Espacial
        if (this.planetMesh) {
          this.planetMesh.rotation.y += dt * 0.045;
        }
        if (this.stationPivot) {
          this.stationPivot.rotation.y += dt * 0.14;
        }
        if (this.stationMesh) {
          this.stationMesh.rotation.x += dt * 0.25;
        }

        // Movimentação das aeronaves
        this.ships.forEach((item) => {
          item.mesh.position.x += item.speed * dt;
          if (item.mesh.position.x > 68) {
            this.resetShipPosition(item.mesh, -68);
          }
        });

        // Coreografia da Câmera: Abertura vs Menu Principal
        if (this.mode === "intro") {
          // Navegação cinematográfica progressiva
          const targetZ = 92 - this.introProgress * 54;
          const targetX = -4 + Math.sin(this.introProgress * Math.PI) * 6;
          const targetY = 3 - this.introProgress * 2.5;

          this.camera.position.x += (targetX - this.camera.position.x) * 0.04;
          this.camera.position.y += (targetY - this.camera.position.y) * 0.04;
          this.camera.position.z += (targetZ - this.camera.position.z) * 0.04;
          this.camera.lookAt(-6, -1, -16);
        } else {
          // Modo Menu Principal: câmera estável com paralaxe suave
          const targetX = -4 + this.mouse.x * 2.4;
          const targetY = 0.8 - this.mouse.y * 1.6 + Math.sin(elapsed * 0.4) * 0.35;
          const targetZ = 36;

          this.camera.position.x += (targetX - this.camera.position.x) * 0.05;
          this.camera.position.y += (targetY - this.camera.position.y) * 0.05;
          this.camera.position.z += (targetZ - this.camera.position.z) * 0.04;
          this.camera.lookAt(-5, -1, -15);
        }

        this.renderer.render(this.scene, this.camera);
      }
    }

    /**
     * =========================================================================
     * 4. GERADOR DE ILUSTRAÇÕES TEMÁTICAS DOS 10 CAPÍTULOS (CANVAS 2D)
     * =========================================================================
     */
    function renderMissionArtwork(canvas, mission) {
      if (!canvas) return;
      const rect = canvas.getBoundingClientRect();
      const w = (canvas.width = Math.max(320, Math.floor(rect.width || 400)));
      const h = (canvas.height = Math.max(160, Math.floor(rect.height || 190)));
      const ctx = canvas.getContext("2d");

      // Fundo espacial temático
      const bg = ctx.createLinearGradient(0, 0, w, h);
      bg.addColorStop(0, "#030712");
      bg.addColorStop(0.6, "#09152b");
      bg.addColorStop(1, "#020617");
      ctx.fillStyle = bg;
      ctx.fillRect(0, 0, w, h);

      // Brilho atmosférico com a cor temática do capítulo
      const glow = ctx.createRadialGradient(w * 0.65, h * 0.45, 8, w * 0.65, h * 0.45, w * 0.55);
      glow.addColorStop(0, mission.themeColor + "55");
      glow.addColorStop(1, "transparent");
      ctx.fillStyle = glow;
      ctx.fillRect(0, 0, w, h);

      // Estrelas de fundo determinísticas pelo ID da missão
      ctx.fillStyle = "#e2e8f0";
      for (let i = 0; i < 42; i++) {
        const sx = ((i * 73 + mission.id * 29) % w);
        const sy = ((i * 41 + mission.id * 17) % h);
        const sr = (i % 3 === 0) ? 1.5 : 0.8;
        ctx.globalAlpha = 0.35 + (i % 5) * 0.12;
        ctx.beginPath();
        ctx.arc(sx, sy, sr, 0, Math.PI * 2);
        ctx.fill();
      }
      ctx.globalAlpha = 1;

      // Grade tática de radar
      ctx.strokeStyle = mission.themeColor + "33";
      ctx.lineWidth = 1;
      ctx.beginPath();
      ctx.arc(w * 0.68, h * 0.5, h * 0.36, 0, Math.PI * 2);
      ctx.stroke();

      ctx.setLineDash([4, 4]);
      ctx.beginPath();
      ctx.arc(w * 0.68, h * 0.5, h * 0.52, 0, Math.PI * 2);
      ctx.stroke();
      ctx.setLineDash([]);

      // Elemento central temático (Planeta / Anel / Núcleo Omniverse)
      const planetGrad = ctx.createRadialGradient(w * 0.64, h * 0.44, 6, w * 0.68, h * 0.5, h * 0.32);
      planetGrad.addColorStop(0, mission.themeColor);
      planetGrad.addColorStop(0.7, "#0f172a");
      planetGrad.addColorStop(1, "#020617");
      ctx.fillStyle = planetGrad;
      ctx.beginPath();
      ctx.arc(w * 0.68, h * 0.5, h * 0.28, 0, Math.PI * 2);
      ctx.fill();

      // Trajetória de interceptação
      ctx.strokeStyle = "#f8fafc";
      ctx.lineWidth = 1.5;
      ctx.beginPath();
      ctx.moveTo(w * 0.15, h * 0.72);
      ctx.quadraticCurveTo(w * 0.42, h * 0.28, w * 0.68, h * 0.5);
      ctx.stroke();
    }

    /**
     * =========================================================================
     * 5. CONTROLADOR PRINCIPAL DA EXPERIÊNCIA (INTRO, MENU, NAVEGAÇÃO)
     * =========================================================================
     */
    class OmniverseApp {
      constructor() {
        this.audio = new OmniverseAudioEngine();
        this.spaceScene = new OmniverseSpaceScene(document.getElementById("universe-canvas"));

        this.state = {
          currentView: "intro", // 'intro' | 'menu' | 'missions' | 'settings' | 'credits'
          selectedMenuIndex: 0,
          selectedMissionId: 1,
          introTimer: null,
          introStartTime: 0,
          introDuration: 24000 // 24 segundos cinematográficos
        };

        this.missions = getProcessedMissions();
        this.bindGlobalEvents();
        this.initSettingsControls();
        this.updateSaveBanner();
        this.renderMissionsList();

        // Verifica se o jogador já assistiu à intro nesta sessão
        const introSeen = sessionStorage.getItem("omniverse_intro_seen") === "true";
        if (introSeen) {
          this.enterMainMenu(true);
        } else {
          this.startIntroSequence();
        }
      }

      /**
       * Inicia a sequência cinematográfica de 24 segundos (5 Cenas)
       */
      startIntroSequence() {
        this.state.currentView = "intro";
        this.spaceScene.mode = "intro";
        document.body.classList.remove("in-menu");

        const introLayer = document.getElementById("intro-layer");
        const menuLayer = document.getElementById("main-menu-layer");
        const skipBtn = document.getElementById("btn-skip-intro");

        menuLayer.classList.add("hidden", "opacity-0");
        introLayer.classList.remove("hidden");
        skipBtn.classList.remove("hidden");
        skipBtn.classList.add("flex");

        // Reseta visibilidade das 4 cenas de introdução
        const s1 = document.getElementById("intro-scene-1");
        const s2 = document.getElementById("intro-scene-2");
        const s3 = document.getElementById("intro-scene-3");
        const s4 = document.getElementById("intro-scene-4");
        const progressBar = document.getElementById("intro-progress-bar");

        [s1, s2, s3, s4].forEach((el) => {
          el.classList.add("opacity-0");
        });
        s1.classList.remove("hidden");
        s2.classList.add("hidden");
        s3.classList.add("hidden");
        s4.classList.add("hidden");

        this.state.introStartTime = performance.now();
        let lastTriggeredScene = 0;

        if (this.state.introTimer) cancelAnimationFrame(this.state.introTimer);

        const stepIntro = (now) => {
          if (this.state.currentView !== "intro") return;

          const elapsed = now - this.state.introStartTime;
          const progress = Math.min(1, elapsed / this.state.introDuration);
          this.spaceScene.introProgress = progress;
          progressBar.style.width = `${progress * 100}%`;

          // CENA 1 (0s - 6s): O Universo
          if (elapsed < 6000) {
            if (lastTriggeredScene !== 1) {
              lastTriggeredScene = 1;
              setTimeout(() => {
                if (this.state.currentView === "intro") {
                  s1.classList.remove("opacity-0", "translate-y-4");
                }
              }, 500);
            }
            if (elapsed > 5000) {
              s1.classList.add("opacity-0");
            }
          }
          // CENA 2 (6s - 12s): A Humanidade
          else if (elapsed < 12000) {
            if (lastTriggeredScene !== 2) {
              lastTriggeredScene = 2;
              s1.classList.add("hidden");
              s2.classList.remove("hidden");
              this.audio.playSceneTransition();
              requestAnimationFrame(() => s2.classList.remove("opacity-0", "translate-y-4"));
            }
            if (elapsed > 11000) {
              s2.classList.add("opacity-0");
            }
          }
          // CENA 3 (12s - 18s): O Protagonista (Alex Carter)
          else if (elapsed < 18000) {
            if (lastTriggeredScene !== 3) {
              lastTriggeredScene = 3;
              s2.classList.add("hidden");
              s3.classList.remove("hidden");
              this.audio.playSceneTransition();
              requestAnimationFrame(() => s3.classList.remove("opacity-0"));
            }
            if (elapsed > 17000) {
              s3.classList.add("opacity-0");
            }
          }
          // CENA 4 (18s - 23.2s): A Identidade do Jogo (Omniverse)
          else if (elapsed < 23200) {
            if (lastTriggeredScene !== 4) {
              lastTriggeredScene = 4;
              s3.classList.add("hidden");
              s4.classList.remove("hidden");
              this.audio.playSceneTransition();
              requestAnimationFrame(() => s4.classList.remove("opacity-0"));
            }
            if (elapsed > 22400) {
              s4.classList.add("opacity-0");
            }
          }
          // CENA 5 (23.2s+): Transição fluida para o Menu Principal
          else {
            this.enterMainMenu(false);
            return;
          }

          this.state.introTimer = requestAnimationFrame(stepIntro);
        };

        this.state.introTimer = requestAnimationFrame(stepIntro);
      }

      /**
       * Finaliza a abertura e exibe o Menu Principal sem cortes bruscos
       */
      enterMainMenu(instant = false) {
        if (this.state.introTimer) cancelAnimationFrame(this.state.introTimer);
        sessionStorage.setItem("omniverse_intro_seen", "true");

        this.state.currentView = "menu";
        this.spaceScene.mode = "menu";
        document.body.classList.add("in-menu");

        const introLayer = document.getElementById("intro-layer");
        const skipBtn = document.getElementById("btn-skip-intro");
        const menuLayer = document.getElementById("main-menu-layer");

        introLayer.classList.add("hidden");
        skipBtn.classList.add("hidden");
        skipBtn.classList.remove("flex");

        menuLayer.classList.remove("hidden");
        if (instant) {
          menuLayer.classList.remove("opacity-0");
        } else {
          this.audio.playSceneTransition();
          requestAnimationFrame(() => {
            menuLayer.classList.remove("opacity-0");
          });
        }

        this.highlightMenuButton(this.state.selectedMenuIndex);
      }

      /**
       * Gerencia abertura e fechamento de subtelas (Missões, Configurações, Créditos)
       */
      openSubView(viewName) {
        this.audio.playClick();
        this.state.currentView = viewName;

        const menuLayer = document.getElementById("main-menu-layer");
        menuLayer.classList.add("opacity-0", "pointer-events-none");

        const targetId = `${viewName}-layer`;
        const targetEl = document.getElementById(targetId);
        if (targetEl) {
          targetEl.classList.remove("hidden");
          requestAnimationFrame(() => targetEl.classList.remove("opacity-0"));
        }

        if (viewName === "missions") {
          this.selectMissionDetail(this.state.selectedMissionId);
        }
      }

      closeSubView() {
        this.audio.playClick();
        ["missions-layer", "settings-layer", "credits-layer"].forEach((id) => {
          const el = document.getElementById(id);
          el.classList.add("opacity-0");
          setTimeout(() => el.classList.add("hidden"), 350);
        });

        this.state.currentView = "menu";
        const menuLayer = document.getElementById("main-menu-layer");
        menuLayer.classList.remove("hidden", "pointer-events-none");
        requestAnimationFrame(() => menuLayer.classList.remove("opacity-0"));
      }

      highlightMenuButton(index) {
        const buttons = document.querySelectorAll("#main-menu-nav .menu-btn");
        if (!buttons.length) return;
        this.state.selectedMenuIndex = (index + buttons.length) % buttons.length;

        buttons.forEach((btn, i) => {
          btn.classList.toggle("active", i === this.state.selectedMenuIndex);
        });
      }

      executeMenuAction(action) {
        switch (action) {
          case "continue": {
            const savedId = localStorage.getItem("omniverse_last_mission");
            const playableMissions = this.missions.filter((m) => m.isPlayable);

            if (savedId) {
              const found = this.missions.find((m) => m.id === Number(savedId));
              if (found && found.isPlayable) {
                this.launchMission(found);
                return;
              }
            }

            if (playableMissions.length > 0) {
              this.launchMission(playableMissions[0]);
            } else {
              this.audio.playBlocked();
              this.showToast(
                "NENHUM SAVE ATIVO",
                "Nenhuma campanha em andamento ou capítulo publicado ainda. Acesse Selecionar Missão para ver os 10 capítulos."
              );
            }
            break;
          }

          case "new-campaign": {
            this.state.selectedMissionId = 1;
            this.openSubView("missions");
            this.showToast(
              "NOVA CAMPANHA INICIADA",
              "Selecione o Capítulo 01 — OPERAÇÃO TERRA para iniciar a jornada."
            );
            break;
          }

          case "select-mission":
            this.openSubView("missions");
            break;

          case "settings":
            this.openSubView("settings");
            break;

          case "credits":
            this.openSubView("credits");
            break;
        }
      }

      /**
       * Renderiza os 10 capítulos na tela de Seleção de Missões
       */
      renderMissionsList() {
        const grid = document.getElementById("missions-grid");
        const counterBadge = document.getElementById("missions-counter-badge");
        if (!grid) return;

        this.missions = getProcessedMissions();
        const playableCount = this.missions.filter((m) => m.isPlayable).length;
        if (counterBadge) {
          counterBadge.textContent = `${playableCount} / ${this.missions.length} DISPONÍVEIS`;
        }

        grid.innerHTML = "";
        this.missions.forEach((mission) => {
          const card = document.createElement("button");
          card.type = "button";
          card.dataset.missionId = mission.id;
          card.className = `mission-card sci-clip-sm text-left p-3.5 border transition-all cursor-pointer flex flex-col justify-between bg-slate-900/70 hover:bg-slate-800/85 ${
            mission.id === this.state.selectedMissionId
              ? "border-sky-400 shadow-[0_0_20px_rgba(56,189,248,0.25)]"
              : "border-sky-400/20"
          }`;

          const badgeHtml = mission.isPlayable
            ? `<span class="px-2 py-0.5 text-[10px] font-rajdhani font-bold tracking-widest uppercase rounded bg-emerald-500/20 text-emerald-300 border border-emerald-400/40">DISPONÍVEL</span>`
            : `<span class="px-2 py-0.5 text-[10px] font-rajdhani font-bold tracking-widest uppercase rounded bg-amber-500/15 text-amber-300 border border-amber-400/30">EM BREVE</span>`;

          card.innerHTML = `
            <div class="flex items-center justify-between gap-2 mb-1.5">
              <span class="font-orbitron text-xs font-bold tracking-widest text-sky-400">CAP. ${mission.number}</span>
              ${badgeHtml}
            </div>
            <h4 class="font-orbitron text-sm sm:text-base font-bold text-white tracking-wide uppercase">
              ${mission.title}
            </h4>
            <p class="font-inter text-xs text-slate-400 line-clamp-2 mt-1">
              ${mission.description}
            </p>
          `;

          card.addEventListener("mouseenter", () => this.audio.playHover());
          card.addEventListener("click", () => {
            this.audio.playClick();
            this.selectMissionDetail(mission.id);
          });

          grid.appendChild(card);
        });
      }

      /**
       * Atualiza o painel lateral de Briefing da Missão selecionada
       */
      selectMissionDetail(missionId) {
        this.state.selectedMissionId = missionId;
        const mission = this.missions.find((m) => m.id === missionId) || this.missions[0];

        // Destaque visual no card ativo
        document.querySelectorAll(".mission-card").forEach((el) => {
          const isSelected = Number(el.dataset.missionId) === mission.id;
          el.classList.toggle("border-sky-400", isSelected);
          el.classList.toggle("bg-slate-800/90", isSelected);
          el.classList.toggle("border-sky-400/20", !isSelected);
        });

        document.getElementById("detail-mission-num").textContent = `MISSÃO ${mission.number}`;
        document.getElementById("detail-mission-sector").textContent = mission.sector;
        document.getElementById("detail-mission-title").textContent = mission.title;
        document.getElementById("detail-mission-desc").textContent = mission.description;

        const statusBadge = document.getElementById("detail-mission-status");
        const launchBtn = document.getElementById("btn-launch-mission");
        const launchLabel = document.getElementById("btn-launch-label");
        const hintEl = document.getElementById("detail-mission-hint");

        if (mission.isPlayable) {
          statusBadge.textContent = "DISPONÍVEL";
          statusBadge.className = "absolute top-3 right-3 px-2.5 py-1 rounded text-xs font-rajdhani font-bold tracking-widest uppercase bg-emerald-500/25 text-emerald-300 border border-emerald-400/50";

          launchBtn.disabled = false;
          launchBtn.className = "sci-clip w-full py-3.5 px-6 font-orbitron text-sm sm:text-base font-bold tracking-[0.2em] uppercase transition-all flex items-center justify-center gap-3 cursor-pointer bg-gradient-to-r from-sky-500 to-blue-600 hover:from-sky-400 hover:to-blue-500 text-white shadow-[0_0_25px_rgba(56,189,248,0.45)]";
          launchLabel.textContent = "INICIAR MISSÃO";
          hintEl.textContent = "TELEMETRIA PRONTA — CLIQUE PARA INICIAR O CAPÍTULO";
          launchBtn.onclick = () => this.launchMission(mission);
        } else {
          statusBadge.textContent = "EM DESENVOLVIMENTO";
          statusBadge.className = "absolute top-3 right-3 px-2.5 py-1 rounded text-xs font-rajdhani font-bold tracking-widest uppercase bg-amber-500/20 text-amber-300 border border-amber-400/40";

          launchBtn.disabled = true;
          launchBtn.className = "sci-clip w-full py-3.5 px-6 font-orbitron text-sm sm:text-base font-bold tracking-[0.2em] uppercase transition-all flex items-center justify-center gap-3 opacity-55 cursor-not-allowed bg-slate-800 text-slate-400 border border-slate-600";
          launchLabel.textContent = "EM BREVE";
          hintEl.textContent = "ESTE CAPÍTULO SERÁ LIBERADO EM UMA PRÓXIMA ATUALIZAÇÃO";
          launchBtn.onclick = null;
        }

        // Renderiza ilustração temática exclusiva no canvas de preview
        renderMissionArtwork(document.getElementById("mission-preview-canvas"), mission);
      }

      /**
       * Executa a transição visual cinematográfica ao iniciar uma missão com URL válida
       */
      launchMission(mission) {
        if (!mission || !mission.url) {
          this.audio.playBlocked();
          return;
        }

        this.audio.playSceneTransition();
        localStorage.setItem("omniverse_last_mission", String(mission.id));
        this.updateSaveBanner();

        const overlay = document.getElementById("launch-transition-overlay");
        const titleEl = document.getElementById("launch-mission-title");
        const barEl = document.getElementById("launch-progress-bar");

        titleEl.textContent = `CAPÍTULO ${mission.number} — ${mission.title}`;
        overlay.classList.remove("hidden");

        let pct = 0;
        const interval = setInterval(() => {
          pct += 14;
          barEl.style.width = `${Math.min(100, pct)}%`;
          if (pct >= 100) {
            clearInterval(interval);
            window.location.href = mission.url;
          }
        }, 140);
      }

      updateSaveBanner() {
        const savedId = localStorage.getItem("omniverse_last_mission");
        const bannerText = document.getElementById("save-status-text");
        if (!bannerText) return;

        if (savedId) {
          const found = this.missions.find((m) => m.id === Number(savedId));
          if (found) {
            bannerText.textContent = `ÚLTIMA MISSÃO: CAP. ${found.number} — ${found.title}`;
            return;
          }
        }
        bannerText.textContent = "REGISTRO DE CAMPANHA: NENHUM PROGRESSO SALVO";
      }

      showToast(title, message) {
        const toast = document.getElementById("hud-toast");
        document.getElementById("hud-toast-title").textContent = title;
        document.getElementById("hud-toast-msg").textContent = message;
        toast.classList.remove("hidden");

        clearTimeout(this.toastTimeout);
        this.toastTimeout = setTimeout(() => {
          toast.classList.add("hidden");
        }, 4200);
      }

      initSettingsControls() {
        const masterSlider = document.getElementById("opt-master-vol");
        const musicSlider = document.getElementById("opt-music-vol");
        const sfxSlider = document.getElementById("opt-sfx-vol");
        const scaleSlider = document.getElementById("opt-ui-scale");

        masterSlider.addEventListener("input", (e) => {
          const val = Number(e.target.value);
          document.getElementById("val-master-vol").textContent = `${val}%`;
          this.audio.setVolume("master", val);
        });

        musicSlider.addEventListener("input", (e) => {
          const val = Number(e.target.value);
          document.getElementById("val-music-vol").textContent = `${val}%`;
          this.audio.setVolume("music", val);
        });

        sfxSlider.addEventListener("input", (e) => {
          const val = Number(e.target.value);
          document.getElementById("val-sfx-vol").textContent = `${val}%`;
          this.audio.setVolume("sfx", val);
        });

        scaleSlider.addEventListener("input", (e) => {
          const val = Number(e.target.value);
          document.getElementById("val-ui-scale").textContent = `${val}%`;
          document.getElementById("ui-root").style.transform = `scale(${val / 100})`;
        });

        // Seletor de Qualidade Gráfica
        document.querySelectorAll(".quality-btn").forEach((btn) => {
          btn.addEventListener("click", () => {
            this.audio.playClick();
            const q = btn.dataset.quality;
            this.spaceScene.setQuality(q);

            document.querySelectorAll(".quality-btn").forEach((b) => {
              const active = b.dataset.quality === q;
              b.classList.toggle("border-sky-400", active);
              b.classList.toggle("bg-sky-500/20", active);
              b.classList.toggle("text-white", active);
              b.classList.toggle("border-slate-700", !active);
              b.classList.toggle("bg-slate-900", !active);
              b.classList.toggle("text-slate-300", !active);
            });
          });
        });

        // Tela Cheia
        document.getElementById("btn-fullscreen-toggle").addEventListener("click", () => {
          this.audio.playClick();
          if (!document.fullscreenElement) {
            document.documentElement.requestFullscreen().catch(() => {
              this.showToast("AVISO DE EXIBIÇÃO", "Seu navegador bloqueou a ativação automática de tela cheia.");
            });
          } else {
            document.exitFullscreen().catch(() => {});
          }
        });
      }

      bindGlobalEvents() {
        // Desbloqueio suave de áudio na primeira interação do usuário
        const unlockAudioOnce = () => {
          if (!this.audio.isEnabled) {
            this.audio.enable();
          }
          window.removeEventListener("pointerdown", unlockAudioOnce);
        };
        window.addEventListener("pointerdown", unlockAudioOnce);

        // Toggle manual de áudio
        document.getElementById("btn-audio-toggle").addEventListener("click", (e) => {
          e.stopPropagation();
          this.audio.toggle();
        });

        // Botão Pular Intro
        document.getElementById("btn-skip-intro").addEventListener("click", () => {
          this.audio.playClick();
          this.enterMainMenu(false);
        });

        // Botão Rever Intro
        document.getElementById("btn-replay-intro").addEventListener("click", () => {
          this.audio.playClick();
          this.startIntroSequence();
        });

        // Botões do Menu Principal
        document.querySelectorAll("#main-menu-nav .menu-btn").forEach((btn, index) => {
          btn.addEventListener("mouseenter", () => {
            this.highlightMenuButton(index);
            this.audio.playHover();
          });
          btn.addEventListener("click", () => {
            this.audio.playClick();
            this.executeMenuAction(btn.dataset.action);
          });
        });

        // Botões de fechar subtelas
        document.querySelectorAll("[data-close-modal]").forEach((btn) => {
          btn.addEventListener("click", () => this.closeSubView());
        });

        // Paralaxe sutil do mouse no Menu Principal
        window.addEventListener("mousemove", (e) => {
          const nx = (e.clientX / window.innerWidth) * 2 - 1;
          const ny = (e.clientY / window.innerHeight) * 2 - 1;
          this.spaceScene.setMouseParallax(nx, ny);

          const heroWrapper = document.getElementById("menu-hero-parallax");
          if (heroWrapper && this.state.currentView === "menu") {
            heroWrapper.style.transform = `translate3d(${-nx * 14}px, ${-ny * 10}px, 0)`;
          }
        });

        // Navegação por Teclado (ESC, ENTER, SETAS)
        window.addEventListener("keydown", (e) => {
          if (e.key === "Escape") {
            if (this.state.currentView === "intro") {
              this.audio.playClick();
              this.enterMainMenu(false);
            } else if (["missions", "settings", "credits"].includes(this.state.currentView)) {
              this.closeSubView();
            }
            return;
          }

          if (this.state.currentView === "menu") {
            if (e.key === "ArrowDown") {
              e.preventDefault();
              this.highlightMenuButton(this.state.selectedMenuIndex + 1);
              this.audio.playHover();
            } else if (e.key === "ArrowUp") {
              e.preventDefault();
              this.highlightMenuButton(this.state.selectedMenuIndex - 1);
              this.audio.playHover();
            } else if (e.key === "Enter") {
              e.preventDefault();
              const buttons = document.querySelectorAll("#main-menu-nav .menu-btn");
              const activeBtn = buttons[this.state.selectedMenuIndex];
              if (activeBtn) {
                this.audio.playClick();
                this.executeMenuAction(activeBtn.dataset.action);
              }
            }
          }
        });
      }

      startRenderLoop() {
        const animate = () => {
          requestAnimationFrame(animate);
          this.spaceScene.update();
        };
        animate();
      }
    }

    // Inicializa a aplicação após o carregamento completo da janela
    window.onload = function () {
      const app = new OmniverseApp();
      app.startRenderLoop();
    };
  </script>
</body>
</html># OMNIVERSE-BEYOND-THE-LIMITS
