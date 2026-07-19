<!DOCTYPE html>
<html lang="uk">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>PUMBA OS • Windows Desktop</title>
  <style>
    :root {
      --win-blue: #2563eb;
      --win-cyan: #38bdf8;
      --text: #f8fafc;
      --muted: #cbd5e1;
      --panel: rgba(10, 16, 30, 0.78);
      --border: rgba(255,255,255,0.16);
      --shadow: 0 20px 50px rgba(0, 0, 0, 0.3);
      --glow: rgba(56, 189, 248, 0.35);
    }

    * { box-sizing: border-box; }
    html, body { margin: 0; height: 100%; }

    body {
      font-family: "Segoe UI", Roboto, Arial, sans-serif;
      color: var(--text);
      background: linear-gradient(180deg, #dff2ff 0%, #cde7fb 100%);
      overflow: hidden;
    }

    .desktop {
      position: relative;
      width: 100vw;
      height: 100vh;
      overflow: hidden;
      transform: translateZ(0);
      backface-visibility: hidden;
      background:
        radial-gradient(circle at 18% 16%, rgba(255,255,255,0.78), transparent 24%),
        radial-gradient(circle at 82% 14%, rgba(255,255,255,0.28), transparent 20%),
        linear-gradient(180deg, #8ecbff 0%, #4f96e7 42%, #1c5d9b 100%);
      isolation: isolate;
      contain: paint;
      transition: background 0.25s ease;
    }

    .desktop.powering-off {
      background: black;
    }

    .desktop.powering-off > :not(.power-off-screen) {
      opacity: 0;
      pointer-events: none;
      transition: opacity 0.2s ease;
    }

    .low-power * {
      animation-duration: 0.01ms !important;
      animation-iteration-count: 1 !important;
      transition-duration: 0.01ms !important;
      scroll-behavior: auto !important;
    }

    .low-power .ambient-orb,
    .low-power .desktop::before,
    .low-power .desktop::after {
      display: none !important;
    }

    .low-power .desktop {
      background: linear-gradient(135deg, #0f4b8b 0%, #1f71c7 100%) !important;
    }

    .low-power .window,
    .low-power .taskbar,
    .low-power .start-menu,
    .low-power .widget-card,
    .low-power .card,
    .low-power .lock-card,
    .low-power .power-off-card,
    .low-power .boot-screen,
    .low-power .lock-screen,
    .low-power .power-off-screen,
    .low-power .activation-card,
    .low-power .activation-reminder {
      backdrop-filter: none !important;
      box-shadow: none !important;
    }

    .window, .taskbar, .start-menu, .widget-card, .card, .lock-card, .power-off-card, .boot-screen, .lock-screen, .power-off-screen {
      transform: translateZ(0);
      backface-visibility: hidden;
      will-change: transform, opacity;
    }

    .ambient-orb {
      position: absolute;
      border-radius: 50%;
      filter: blur(24px);
      opacity: 0.28;
      pointer-events: none;
      animation: floatOrb 9s ease-in-out infinite alternate;
      transform: translateZ(0);
      will-change: transform;
    }

    .ambient-orb.a {
      width: 240px;
      height: 240px;
      background: rgba(255, 255, 255, 0.26);
      top: 8%;
      left: 8%;
    }

    .ambient-orb.b {
      width: 320px;
      height: 320px;
      background: rgba(255, 255, 255, 0.16);
      right: 6%;
      bottom: 16%;
      animation-duration: 11s;
    }

    @keyframes floatOrb {
      from { transform: translate3d(0, 0, 0) scale(1); }
      to { transform: translate3d(20px, -20px, 0) scale(1.05); }
    }

    .desktop::before,
    .desktop::after {
      content: "";
      position: absolute;
      inset: 0;
      pointer-events: none;
    }

    .desktop::before {
      background:
        radial-gradient(ellipse at 50% 0%, rgba(255,255,255,0.42), transparent 38%),
        linear-gradient(180deg, rgba(255,255,255,0.16) 0%, rgba(255,255,255,0.06) 32%, rgba(4,12,28,0.10) 100%);
      mix-blend-mode: screen;
      opacity: 0.95;
    }

    .desktop::after {
      background:
        radial-gradient(circle at 78% 18%, rgba(255,255,255,0.24), transparent 24%),
        linear-gradient(180deg, transparent 0%, rgba(3,16,31,0.18) 100%);
    }

    .desktop-icons {
      position: absolute;
      top: 18px;
      left: 18px;
      display: grid;
      grid-template-columns: repeat(1, minmax(84px, 84px));
      gap: 12px;
      z-index: 2;
    }

    .icon {
      width: 84px;
      text-align: center;
      padding: 8px 4px;
      border-radius: 12px;
      cursor: pointer;
      transition: background 0.2s ease, transform 0.2s ease;
    }

    .icon:hover {
      background: rgba(255,255,255,0.12);
      transform: translateY(-2px);
    }

    .icon-box {
      width: 56px;
      height: 56px;
      margin: 0 auto 6px;
      border-radius: 16px;
      display: grid;
      place-items: center;
      font-size: 1.35rem;
      background: linear-gradient(135deg, rgba(255,255,255,0.26), rgba(255,255,255,0.10));
      border: 1px solid rgba(255,255,255,0.22);
      box-shadow: inset 0 1px 0 rgba(255,255,255,0.24), 0 10px 24px rgba(0,0,0,0.22);
      transition: transform 0.2s ease, box-shadow 0.2s ease;
    }

    .icon:hover .icon-box { transform: scale(1.04); }

    .icon-label {
      font-size: 0.8rem;
      font-weight: 600;
      text-shadow: 0 1px 2px rgba(0,0,0,0.35);
    }

    .widget-panel {
      position: absolute;
      top: 18px;
      right: 18px;
      width: min(320px, 34vw);
      display: grid;
      gap: 12px;
      z-index: 2;
    }

    .widget-card {
      background: rgba(8, 15, 30, 0.56);
      border: 1px solid rgba(255,255,255,0.12);
      border-radius: 16px;
      padding: 12px;
      backdrop-filter: blur(10px);
      box-shadow: 0 8px 20px rgba(0,0,0,0.16);
    }

    .widget-title {
      font-size: 0.82rem;
      color: #cbd5e1;
      text-transform: uppercase;
      letter-spacing: 0.12em;
      margin-bottom: 8px;
    }

    .widget-big {
      font-size: 1.2rem;
      font-weight: 700;
      color: white;
      margin-bottom: 4px;
    }

    .widget-sub {
      font-size: 0.85rem;
      color: #cbd5e1;
      min-height: 1em;
    }

    .quick-actions {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 8px;
    }

    .quick-btn {
      border: none;
      border-radius: 12px;
      padding: 10px 0;
      background: rgba(255,255,255,0.1);
      color: white;
      font-size: 1rem;
      cursor: pointer;
      border: 1px solid rgba(255,255,255,0.12);
      transition: transform 0.2s ease, background 0.2s ease;
    }

    .quick-btn:hover {
      background: rgba(255,255,255,0.16);
      transform: translateY(-1px);
    }

    .taskbar {
      position: absolute;
      left: 0;
      bottom: 0;
      width: 100%;
      height: 62px;
      contain: paint;
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 8px 14px;
      background: linear-gradient(90deg, rgba(10, 20, 38, 0.9), rgba(16, 28, 48, 0.82));
      backdrop-filter: blur(20px);
      border-top: 1px solid rgba(255,255,255,0.14);
      box-shadow: 0 -10px 30px rgba(0,0,0,0.22);
      z-index: 8;
    }

    .taskbar-left, .taskbar-right {
      display: flex;
      align-items: center;
      gap: 8px;
    }

    .start-btn, .tray-btn, .app-btn {
      border: none;
      border-radius: 10px;
      color: white;
      background: rgba(255,255,255,0.08);
      cursor: pointer;
      transition: background 0.2s ease, transform 0.2s ease, box-shadow 0.2s ease;
      border: 1px solid rgba(255,255,255,0.08);
    }

    .start-btn {
      width: 46px;
      height: 46px;
      font-size: 1.1rem;
      background: linear-gradient(135deg, var(--win-blue), var(--win-cyan));
      box-shadow: inset 0 1px 0 rgba(255,255,255,0.25), 0 6px 16px rgba(37,99,235,0.24);
    }

    .app-btn {
      padding: 8px 12px;
      font-weight: 600;
      color: #e2e8f0;
    }

    .app-btn.active {
      background: rgba(255,255,255,0.16);
      box-shadow: inset 0 0 0 1px rgba(255,255,255,0.12);
    }

    .start-btn:hover, .tray-btn:hover, .app-btn:hover {
      transform: translateY(-1px);
      background: rgba(255,255,255,0.16);
      box-shadow: inset 0 0 0 1px rgba(255,255,255,0.16);
    }

    .clock {
      padding: 8px 10px;
      border-radius: 10px;
      font-size: 0.9rem;
      color: var(--muted);
      background: rgba(255,255,255,0.07);
      min-width: 92px;
      text-align: center;
      border: 1px solid rgba(255,255,255,0.08);
    }

    .window {
      position: absolute;
      left: 50%;
      top: 48%;
      transform: translate(-50%, -50%) scale(0.96);
      will-change: transform, opacity;
      transform: translateZ(0);
      backface-visibility: hidden;
      width: min(900px, 94vw);
      height: min(560px, 84vh);
      background: linear-gradient(145deg, rgba(8, 15, 30, 0.86), rgba(12, 24, 40, 0.94));
      color: var(--text);
      border-radius: 18px;
      box-shadow: var(--shadow);
      border: 1px solid rgba(255,255,255,0.12);
      overflow: hidden;
      opacity: 0;
      pointer-events: none;
      transition: all 0.24s ease;
      z-index: 6;
      backdrop-filter: blur(18px);
    }

    .window.open {
      opacity: 1;
      pointer-events: auto;
      transform: translate(-50%, -50%) scale(1);
    }

    .window.minimized {
      opacity: 0;
      pointer-events: none;
      transform: translate(-50%, -50%) scale(0.94);
    }

    .window-titlebar {
      height: 44px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 0 12px;
      background: linear-gradient(90deg, rgba(255,255,255,0.12), rgba(255,255,255,0.04));
      border-bottom: 1px solid rgba(255,255,255,0.08);
    }

    .window-title {
      display: flex;
      align-items: center;
      gap: 8px;
      font-weight: 700;
      font-size: 0.98rem;
    }

    .window-controls button {
      margin-left: 6px;
      border: none;
      width: 28px;
      height: 28px;
      border-radius: 50%;
      cursor: pointer;
      font-weight: 700;
    }

    .window-controls .close { background: #f87171; color: white; }
    .window-controls .mini { background: #fbbf24; color: #78350f; }
    .window-controls .max { background: #34d399; color: #064e3b; }

    .window-body {
      height: calc(100% - 44px);
      padding: 18px;
      overflow: auto;
      background: linear-gradient(180deg, rgba(255,255,255,0.04), rgba(255,255,255,0.02));
    }

    .hero-card {
      background: rgba(255,255,255,0.05);
      border: 1px solid rgba(255,255,255,0.1);
      border-radius: 14px;
      padding: 14px 16px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      gap: 12px;
      margin-bottom: 12px;
    }

    .hero-card h2 { margin: 0 0 4px; font-size: 1.08rem; }
    .hero-card p { margin: 0; color: #cbd5e1; font-size: 0.9rem; }

    .pill {
      display: inline-block;
      padding: 6px 10px;
      border-radius: 999px;
      background: rgba(255,255,255,0.12);
      color: #dbeafe;
      font-size: 0.84rem;
      font-weight: 700;
      white-space: nowrap;
      box-shadow: inset 0 0 0 1px rgba(255,255,255,0.08);
    }

    .grid-2 {
      display: grid;
      grid-template-columns: repeat(2, minmax(0, 1fr));
      gap: 10px;
      margin-top: 10px;
    }

    .card {
      background: rgba(255,255,255,0.045);
      border: 1px solid rgba(255,255,255,0.08);
      border-radius: 12px;
      padding: 12px;
    }

    .card h3 { margin: 0 0 8px; font-size: 0.95rem; font-weight: 700; }

    .meter {
      width: 100%;
      height: 8px;
      border-radius: 999px;
      background: rgba(255,255,255,0.12);
      overflow: hidden;
      margin-top: 8px;
    }

    .meter span {
      display: block;
      height: 100%;
      border-radius: inherit;
      background: linear-gradient(90deg, var(--win-blue), var(--win-cyan));
    }

    .list {
      list-style: none;
      padding: 0;
      margin: 0;
    }

    .list li {
      padding: 8px 0;
      border-bottom: 1px solid rgba(255,255,255,0.08);
      display: flex;
      align-items: center;
      justify-content: space-between;
      color: #e2e8f0;
      font-size: 0.95rem;
    }

    .list li:last-child { border-bottom: 0; }

    .theme-controls {
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
      margin-top: 8px;
    }

    .theme-btn {
      border: 1px solid rgba(255,255,255,0.12);
      background: rgba(255,255,255,0.08);
      color: white;
      padding: 7px 10px;
      border-radius: 999px;
      cursor: pointer;
      font-weight: 700;
      font-size: 0.86rem;
    }

    .theme-btn.active {
      background: linear-gradient(135deg, var(--win-blue), var(--win-cyan));
      border-color: transparent;
    }

    .settings-shell {
      display: grid;
      gap: 10px;
    }

    .settings-grid {
      display: grid;
      grid-template-columns: repeat(2, minmax(0, 1fr));
      gap: 10px;
    }

    .settings-row {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 8px 0;
      border-top: 1px solid rgba(255,255,255,0.08);
      color: #e2e8f0;
      font-size: 0.92rem;
    }

    .settings-row:first-of-type {
      border-top: 0;
      padding-top: 0;
    }

    .setting-pill {
      display: inline-block;
      padding: 4px 8px;
      border-radius: 999px;
      background: rgba(255,255,255,0.08);
      color: #cbd5e1;
      font-size: 0.8rem;
      font-weight: 700;
    }

    .setting-pill.active {
      background: rgba(56, 189, 248, 0.16);
      color: #dbeafe;
    }

    .toggle-btn {
      border: 1px solid rgba(255,255,255,0.12);
      background: rgba(255,255,255,0.08);
      color: #f8fafc;
      border-radius: 999px;
      padding: 5px 10px;
      font-size: 0.8rem;
      font-weight: 700;
      cursor: pointer;
    }

    .toggle-btn.active {
      background: rgba(34, 197, 94, 0.18);
      color: #dcfce7;
      border-color: rgba(34, 197, 94, 0.28);
    }

    .low-power .settings-grid,
    .low-power .grid-2 {
      grid-template-columns: 1fr;
    }

    .start-menu {
      position: absolute;
      left: 12px;
      bottom: 68px;
      width: min(420px, calc(100vw - 24px));
      padding: 14px;
      border-radius: 24px;
      background: linear-gradient(145deg, rgba(8, 15, 30, 0.96), rgba(13, 24, 42, 0.95));
      border: 1px solid rgba(255,255,255,0.14);
      box-shadow: 0 24px 50px rgba(0,0,0,0.35);
      display: none;
      z-index: 7;
      backdrop-filter: blur(16px);
    }

    .start-menu.open { display: block; }

    .start-hero {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 10px;
      padding: 12px;
      border-radius: 16px;
      background: linear-gradient(135deg, rgba(37,99,235,0.24), rgba(56,189,248,0.10));
      border: 1px solid rgba(255,255,255,0.12);
      margin-bottom: 10px;
    }

    .start-hero-title { font-weight: 800; letter-spacing: 0.02em; }
    .start-hero-sub { font-size: 0.82rem; color: #cbd5e1; margin-top: 2px; }
    .start-hero-badge {
      width: 42px;
      height: 42px;
      border-radius: 14px;
      display: grid;
      place-items: center;
      background: linear-gradient(135deg, rgba(255,255,255,0.24), rgba(255,255,255,0.12));
      box-shadow: inset 0 1px 0 rgba(255,255,255,0.22);
    }

    .start-search {
      width: 100%;
      border: none;
      outline: none;
      border-radius: 12px;
      padding: 10px 12px;
      background: rgba(255,255,255,0.08);
      color: white;
      margin-bottom: 10px;
    }

    .start-section-title {
      font-size: 0.78rem;
      text-transform: uppercase;
      letter-spacing: 0.12em;
      color: #94a3b8;
      margin: 8px 0 6px;
    }

    .start-grid {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 10px;
      margin: 8px 0 10px;
    }

    .start-item {
      display: flex;
      align-items: center;
      gap: 8px;
      padding: 10px;
      border-radius: 12px;
      background: rgba(255,255,255,0.06);
      cursor: pointer;
      font-weight: 600;
      border: none;
      color: white;
      text-align: left;
      box-shadow: inset 0 1px 0 rgba(255,255,255,0.03);
    }

    .start-item:hover { background: rgba(255,255,255,0.13); transform: translateY(-1px); }
    .start-item.hide { display: none; }
    .start-item-icon {
      width: 30px;
      height: 30px;
      border-radius: 10px;
      display: grid;
      place-items: center;
      background: linear-gradient(135deg, rgba(37,99,235,0.24), rgba(56,189,248,0.16));
      flex-shrink: 0;
    }

    .start-footer {
      color: #94a3b8;
      font-size: 0.9rem;
      padding-top: 8px;
      border-top: 1px solid rgba(255,255,255,0.08);
    }

    .power-group {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 8px;
      margin-top: 10px;
    }

    .power-btn {
      border: none;
      border-radius: 12px;
      padding: 10px 6px;
      color: white;
      font-weight: 700;
      cursor: pointer;
      background: rgba(255,255,255,0.08);
      font-size: 0.9rem;
    }

    .power-btn.shutdown { background: rgba(248, 113, 113, 0.16); }
    .power-btn.restart { background: rgba(56, 189, 248, 0.16); }
    .power-btn.sleep { background: rgba(139, 92, 246, 0.16); }

    .toast {
      position: absolute;
      right: 16px;
      bottom: 74px;
      padding: 10px 14px;
      border-radius: 12px;
      background: rgba(8, 15, 30, 0.82);
      color: white;
      border: 1px solid rgba(255,255,255,0.12);
      box-shadow: var(--shadow);
      opacity: 0;
      transform: translateY(8px);
      pointer-events: none;
      transition: all 0.25s ease;
      z-index: 9;
      backdrop-filter: blur(12px);
    }

    .toast.show {
      opacity: 1;
      transform: translateY(0);
    }

    .boot-screen,
    .lock-screen,
    .power-off-screen,
    .sleep-screen {
      position: absolute;
      inset: 0;
      z-index: 30;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: opacity 0.55s ease, transform 0.55s ease;
    }

    .boot-screen {
      background: radial-gradient(circle at 20% 20%, rgba(255,255,255,0.16), transparent 25%), linear-gradient(135deg, #07111f 0%, #123a6b 45%, #0f172a 100%);
      color: white;
      flex-direction: column;
      gap: 12px;
      text-align: center;
    }

    .boot-screen.hide {
      opacity: 0;
      pointer-events: none;
      transform: scale(1.02);
    }

    .boot-logo {
      width: 90px;
      height: 90px;
      border-radius: 24px;
      display: grid;
      place-items: center;
      background: linear-gradient(135deg, rgba(255,255,255,0.26), rgba(255,255,255,0.10));
      border: 1px solid rgba(255,255,255,0.22);
      box-shadow: 0 20px 40px rgba(0, 0, 0, 0.28);
      animation: pulse 1.2s ease-in-out infinite alternate;
    }

    .pumba-mark {
      width: 46px;
      height: 46px;
      border-radius: 14px;
      position: relative;
      background: linear-gradient(135deg, #ffffff, #dbeafe);
      box-shadow: inset 0 1px 0 rgba(255,255,255,0.35), 0 8px 18px rgba(0,0,0,0.2);
    }

    .pumba-mark::before {
      content: 'P';
      color: #0f172a;
      font-size: 1.2rem;
      font-weight: 900;
      position: absolute;
      inset: 0;
      display: grid;
      place-items: center;
    }

    .pumba-mark::after {
      content: '';
      position: absolute;
      inset: 4px;
      border: 2px solid rgba(37, 99, 235, 0.28);
      border-radius: 10px;
    }

    .pumba-mark.small {
      width: 28px;
      height: 28px;
      border-radius: 9px;
    }

    .pumba-mark.small::before {
      font-size: 0.85rem;
    }

    @keyframes pulse {
      from { transform: scale(0.95); }
      to { transform: scale(1.03); }
    }

    .boot-title {
      font-size: 1.7rem;
      font-weight: 700;
      letter-spacing: 0.6px;
    }

    .boot-sub {
      font-size: 0.98rem;
      color: #cbd5e1;
      margin-bottom: 8px;
    }

    .boot-progress {
      width: min(320px, 78vw);
      height: 8px;
      border-radius: 999px;
      background: rgba(255,255,255,0.16);
      overflow: hidden;
      margin-top: 6px;
    }

    .boot-progress span {
      display: block;
      height: 100%;
      width: 0;
      background: linear-gradient(90deg, var(--win-blue), var(--win-cyan));
      border-radius: inherit;
      transition: width 0.2s ease;
    }

    .lock-screen {
      background: linear-gradient(135deg, rgba(2,6,23,0.65), rgba(31,41,55,0.45)), radial-gradient(circle at 20% 15%, rgba(56,189,248,0.3), transparent 24%), linear-gradient(120deg, #0f172a, #1d4ed8 55%, #0f172a 100%);
      color: white;
      opacity: 0;
      pointer-events: none;
      transform: scale(1.02);
      backdrop-filter: blur(4px);
    }

    .lock-screen.show {
      opacity: 1;
      pointer-events: auto;
      transform: scale(1);
    }

    .lock-card {
      text-align: center;
      padding: 24px 28px;
      border-radius: 24px;
      background: rgba(15, 23, 42, 0.36);
      border: 1px solid rgba(255,255,255,0.16);
      box-shadow: 0 20px 50px rgba(0,0,0,0.26);
      backdrop-filter: blur(12px);
      max-width: 420px;
      width: min(88vw, 420px);
    }

    .lock-icon {
      width: 78px;
      height: 78px;
      border-radius: 22px;
      margin: 0 auto 12px;
      display: grid;
      place-items: center;
      font-size: 1.8rem;
      background: linear-gradient(135deg, rgba(255,255,255,0.24), rgba(255,255,255,0.08));
      border: 1px solid rgba(255,255,255,0.18);
    }

    .lock-title {
      font-size: 1.35rem;
      font-weight: 700;
      margin-bottom: 6px;
    }

    .lock-sub {
      color: #e2e8f0;
      font-size: 0.96rem;
      margin-bottom: 14px;
    }

    .lock-hint {
      font-size: 0.9rem;
      color: #cbd5e1;
      margin-top: 8px;
    }

    .power-off-screen {
      background: black;
      opacity: 0;
      pointer-events: none;
      transform: scale(1.01);
      color: white;
      flex-direction: column;
      gap: 16px;
      text-align: center;
      justify-content: center;
      align-items: center;
      padding: 24px;
    }

    .power-off-screen.show {
      opacity: 1;
      pointer-events: auto;
      transform: scale(1);
    }

    .power-off-console {
      width: min(720px, 92vw);
      height: min(280px, 56vh);
      border-radius: 14px;
      border: 1px solid rgba(255,255,255,0.14);
      background: rgba(0,0,0,0.92);
      color: #d1fae5;
      font-family: Consolas, "Courier New", monospace;
      font-size: 0.95rem;
      padding: 14px;
      display: flex;
      flex-direction: column;
      gap: 8px;
      box-shadow: 0 0 0 1px rgba(255,255,255,0.04) inset;
      overflow: hidden;
    }

    .power-off-console-output {
      flex: 1;
      overflow: auto;
      white-space: pre-wrap;
      line-height: 1.5;
    }

    .power-off-console-prompt {
      display: flex;
      align-items: center;
      gap: 8px;
      color: #38bdf8;
    }

    .power-off-console-input {
      flex: 1;
      border: none;
      outline: none;
      background: transparent;
      color: white;
      font-family: inherit;
      font-size: inherit;
    }

    .sleep-screen {
      background: linear-gradient(135deg, rgba(4,10,24,0.96), rgba(15,23,42,0.94));
      opacity: 0;
      pointer-events: none;
      transform: scale(1.01);
      color: white;
      text-align: center;
    }

    .sleep-screen.show {
      opacity: 1;
      pointer-events: auto;
      transform: scale(1);
    }

    .power-off-card,
    .sleep-card {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 12px;
      padding: 28px 32px;
      border-radius: 24px;
      background: rgba(255, 255, 255, 0.06);
      border: 1px solid rgba(255, 255, 255, 0.14);
      box-shadow: 0 20px 45px rgba(0, 0, 0, 0.4);
      backdrop-filter: blur(12px);
    }

    .power-off-icon,
    .sleep-icon {
      width: 72px;
      height: 72px;
      border-radius: 22px;
      display: grid;
      place-items: center;
      font-size: 1.8rem;
      background: rgba(255,255,255,0.12);
    }

    .power-off-title,
    .sleep-title {
      font-size: 1.2rem;
      font-weight: 700;
      letter-spacing: 0.3px;
    }

    .power-off-btn {
      margin-top: 6px;
      border: none;
      border-radius: 999px;
      padding: 10px 18px;
      font-weight: 700;
      color: black;
      background: linear-gradient(135deg, #ffffff, #dbeafe);
      cursor: pointer;
      box-shadow: 0 10px 24px rgba(255,255,255,0.18);
    }

    .power-off-btn:hover {
      transform: translateY(-1px);
    }

    .sleep-hint {
      font-size: 0.9rem;
      color: #cbd5e1;
      margin-top: 4px;
    }

    .activation-screen {
      position: absolute;
      inset: 0;
      z-index: 40;
      display: flex;
      align-items: center;
      justify-content: center;
      background: rgba(2, 6, 23, 0.38);
      backdrop-filter: blur(8px);
      color: white;
      opacity: 0;
      pointer-events: none;
      transform: scale(1.01);
      transition: opacity 0.35s ease, transform 0.35s ease;
      padding: 20px;
    }

    .activation-screen.show {
      opacity: 1;
      pointer-events: auto;
      transform: scale(1);
    }

    .activation-card {
      width: min(92vw, 460px);
      padding: 24px 24px 20px;
      border-radius: 22px;
      background: rgba(15, 23, 42, 0.72);
      border: 1px solid rgba(255,255,255,0.16);
      box-shadow: 0 20px 50px rgba(0,0,0,0.3);
      backdrop-filter: blur(12px);
      text-align: center;
    }

    .activation-title {
      font-size: 1.2rem;
      font-weight: 700;
      margin-bottom: 8px;
    }

    .activation-sub {
      font-size: 0.95rem;
      color: #cbd5e1;
      margin-bottom: 16px;
    }

    .activation-input {
      width: 100%;
      border: 1px solid rgba(255,255,255,0.16);
      border-radius: 12px;
      padding: 12px 14px;
      font-size: 0.95rem;
      background: rgba(255,255,255,0.08);
      color: white;
      outline: none;
      margin-bottom: 10px;
    }

    .activation-actions {
      display: flex;
      justify-content: center;
      gap: 10px;
      flex-wrap: wrap;
    }

    .activation-btn {
      border: none;
      border-radius: 999px;
      padding: 10px 18px;
      font-weight: 700;
      color: black;
      background: linear-gradient(135deg, #ffffff, #dbeafe);
      cursor: pointer;
      box-shadow: 0 10px 24px rgba(255,255,255,0.18);
    }

    .activation-btn.secondary {
      color: white;
      background: rgba(255,255,255,0.12);
      box-shadow: none;
      border: 1px solid rgba(255,255,255,0.16);
    }

    .activation-btn:hover {
      transform: translateY(-1px);
    }

    .activation-hint {
      font-size: 0.82rem;
      color: #94a3b8;
      margin-top: 10px;
      line-height: 1.5;
    }

    .activation-success {
      color: #86efac;
      font-weight: 600;
      margin-top: 8px;
      min-height: 1.25rem;
    }

    .activation-reminder {
      position: absolute;
      left: 50%;
      bottom: 84px;
      transform: translateX(-50%) translateY(12px);
      z-index: 32;
      display: flex;
      align-items: center;
      gap: 12px;
      padding: 10px 14px;
      border-radius: 999px;
      background: rgba(15, 23, 42, 0.62);
      border: 1px solid rgba(255,255,255,0.16);
      box-shadow: 0 12px 30px rgba(0,0,0,0.22);
      backdrop-filter: blur(10px);
      color: white;
      font-size: 0.92rem;
      opacity: 0;
      pointer-events: none;
      transition: opacity 0.25s ease, transform 0.25s ease;
    }

    .activation-reminder.show {
      opacity: 1;
      pointer-events: auto;
      transform: translateX(-50%) translateY(0);
    }

    .activation-reminder button {
      border: none;
      border-radius: 999px;
      padding: 7px 12px;
      font-weight: 700;
      background: linear-gradient(135deg, #ffffff, #dbeafe);
      color: #0f172a;
      cursor: pointer;
    }

    .terminal-shell {
      display: flex;
      flex-direction: column;
      height: 100%;
      gap: 10px;
      padding: 12px;
      background: linear-gradient(135deg, rgba(2,6,23,0.95), rgba(15,23,42,0.92));
      border-radius: 16px;
      border: 1px solid rgba(255,255,255,0.12);
      overflow: hidden;
    }

    .terminal-output {
      flex: 1;
      overflow: auto;
      font-family: Consolas, "Courier New", monospace;
      font-size: 0.92rem;
      line-height: 1.5;
      color: #d1fae5;
      white-space: pre-wrap;
      padding: 8px;
      background: rgba(0,0,0,0.28);
      border-radius: 12px;
    }

    .terminal-line {
      margin-bottom: 6px;
    }

    .terminal-prompt {
      display: flex;
      align-items: center;
      gap: 8px;
      font-family: Consolas, "Courier New", monospace;
      font-size: 0.95rem;
      color: #38bdf8;
    }

    .terminal-input {
      flex: 1;
      border: none;
      outline: none;
      background: transparent;
      color: white;
      font-family: Consolas, "Courier New", monospace;
      font-size: 0.95rem;
    }

    .terminal-hint {
      font-size: 0.82rem;
      color: #94a3b8;
      margin-top: 4px;
    }

    @media (max-width: 760px) {
      .grid-2 { grid-template-columns: 1fr; }
      .taskbar { height: auto; padding: 8px; }
      .desktop { padding-bottom: 80px; }
      .desktop-icons { top: 12px; left: 12px; gap: 10px; }
    }

    @media (prefers-reduced-motion: reduce) {
      *, *::before, *::after {
        animation-duration: 0.01ms !important;
        animation-iteration-count: 1 !important;
        transition-duration: 0.01ms !important;
        scroll-behavior: auto !important;
      }
    }
  </style>
</head>
<body>
  <div class="desktop">
    <div class="ambient-orb a"></div>
    <div class="ambient-orb b"></div>
    <div class="boot-screen" id="bootScreen">
      <div class="boot-logo"><span class="pumba-mark"></span></div>
      <div class="boot-title">PUMBA OS</div>
      <div class="boot-sub">Завантаження системи…</div>
      <div class="boot-progress"><span id="bootBar"></span></div>
    </div>

    <div class="lock-screen" id="lockScreen">
      <div class="lock-card">
        <div class="lock-icon">🔐</div>
        <div class="lock-title">PUMBA OS</div>
        <div class="lock-sub">Сучасний Windows-style інтерфейс</div>
        <div class="lock-hint">Натисніть будь-де, щоб розблокувати</div>
      </div>
    </div>

    <div class="power-off-screen" id="powerOffScreen">
      <div class="power-off-console" id="powerOffConsole">
        <div class="power-off-console-output" id="powerOffConsoleOutput"></div>
        <div class="power-off-console-prompt">
          <span>PS C:\PUMBA&gt;</span>
          <input class="power-off-console-input" id="powerOffConsoleInput" spellcheck="false" autocomplete="off" />
        </div>
      </div>
      <button class="power-off-btn" id="powerOnBtn">Запустить</button>
    </div>

    <div class="sleep-screen" id="sleepScreen">
      <div class="sleep-card">
        <div class="sleep-icon">🌙</div>
        <div class="sleep-title">Режим сну</div>
        <div class="sleep-hint">Натисніть Enter, щоб пробудити систему</div>
      </div>
    </div>

    <div class="activation-screen" id="activationScreen">
      <div class="activation-card">
        <div class="activation-title">Активація PUMBA</div>
        <div class="activation-sub">Введіть ключ активації, щоб активувати систему PUMBA</div>
        <input class="activation-input" id="activationInput" placeholder="Введіть ключ" autocomplete="off" />
        <div class="activation-actions">
          <button class="activation-btn" id="activationBtn">Активувати</button>
          <button class="activation-btn secondary" id="skipActivationBtn">Пізніше</button>
        </div>
        <div class="activation-hint">Введіть правильний ключ активації, щоб продовжити. Можна пропустити і повернутися пізніше.</div>
        <div class="activation-success" id="activationSuccess"></div>
      </div>
    </div>

    <div class="activation-reminder" id="activationReminder">
      <span>Активуйте PUMBA OS, щоб розблокувати кастомізацію</span>
      <button id="reminderActivateBtn">Активувати</button>
    </div>

    <div class="desktop-icons">
      <div class="icon" data-app="Панель">
        <div class="icon-box">🖥️</div>
        <div class="icon-label">Панель</div>
      </div>
      <div class="icon" data-app="Параметри">
        <div class="icon-box">⚙️</div>
        <div class="icon-label">Параметри</div>
      </div>
      <div class="icon" data-app="Провідник">
        <div class="icon-box">📁</div>
        <div class="icon-label">Провідник</div>
      </div>
      <div class="icon" data-app="Мережа">
        <div class="icon-box">🌐</div>
        <div class="icon-label">Мережа</div>
      </div>
      <div class="icon" data-app="Терминал">
        <div class="icon-box">⌨️</div>
        <div class="icon-label">Терминал</div>
      </div>
    </div>

    <div class="widget-panel">
      <div class="widget-card">
        <div class="widget-title">Сьогодні</div>
        <div class="widget-big" id="dateWidget">--</div>
        <div class="widget-sub"></div>
      </div>
      <div class="widget-card">
        <div class="widget-title">Швидкі дії</div>
        <div class="quick-actions">
          <button class="quick-btn" data-app="Панель">🖥️</button>
          <button class="quick-btn" data-app="Параметри">⚙️</button>
          <button class="quick-btn" data-app="Провідник">📁</button>
        </div>
      </div>
    </div>

    <div class="start-menu" id="startMenu">
      <div class="start-hero">
        <div>
          <div class="start-hero-title">PUMBA OS</div>
          <div class="start-hero-sub">Windows-style desktop • premium shell</div>
        </div>
        <div class="start-hero-badge"><span class="pumba-mark small"></span></div>
      </div>
      <input class="start-search" id="startSearch" placeholder="Шукати програму..." />
      <div class="start-section-title">Закріплені</div>
      <div class="start-grid">
        <button class="start-item" data-app="Панель"><span class="start-item-icon">🖥️</span>Панель</button>
        <button class="start-item" data-app="Параметри"><span class="start-item-icon">⚙️</span>Параметри</button>
        <button class="start-item" data-app="Провідник"><span class="start-item-icon">📁</span>Провідник</button>
        <button class="start-item" data-app="Мережа"><span class="start-item-icon">🌐</span>Мережа</button>
        <button class="start-item" data-app="Терминал"><span class="start-item-icon">⌨️</span>Терминал</button>
      </div>
      <div class="power-group">
        <button class="power-btn shutdown" data-action="shutdown">⏻ Вимкнути</button>
        <button class="power-btn restart" data-action="restart">🔄 Перезавантажити</button>
        <button class="power-btn sleep" data-action="sleep">🌙 Сон</button>
      </div>
      <div class="power-group">
        <button class="power-btn" data-action="lock">🔒 Блокування</button>
        <button class="power-btn" data-action="signout">🚪 Вийти</button>
        <button class="power-btn" data-action="restart">⚡ Швидкий старт</button>
      </div>
      <div class="start-footer">Преміум інтерфейс • оптимізовано для перегляду</div>
    </div>

    <div class="window open" id="window">
      <div class="window-titlebar">
        <div class="window-title" id="windowTitle">🖥️ Панель PUMBA</div>
        <div class="window-controls">
          <button class="mini">—</button>
          <button class="max">▢</button>
          <button class="close" id="closeBtn">×</button>
        </div>
      </div>
      <div class="window-body" id="windowBody"></div>
    </div>

    <div class="toast" id="toast"></div>

    <div class="taskbar">
      <div class="taskbar-left">
        <button class="start-btn" id="startBtn"><span class="pumba-mark small"></span></button>
        <button class="app-btn active" data-app="Панель">Панель</button>
        <button class="app-btn" data-app="Провідник">Провідник</button>
        <button class="app-btn" data-app="Параметри">Параметри</button>
      </div>
      <div class="taskbar-right">
        <button class="tray-btn app-btn">🔋</button>
        <button class="tray-btn app-btn">🔊</button>
        <div class="clock" id="clock">00:00</div>
      </div>
    </div>
  </div>

  <script>
    const startBtn = document.getElementById('startBtn');
    const startMenu = document.getElementById('startMenu');
    const startSearch = document.getElementById('startSearch');
    const windowEl = document.getElementById('window');
    const windowTitleEl = document.getElementById('windowTitle');
    const windowBodyEl = document.getElementById('windowBody');
    const clockEl = document.getElementById('clock');
    const dateWidgetEl = document.getElementById('dateWidget');
    const bootScreen = document.getElementById('bootScreen');
    const lockScreen = document.getElementById('lockScreen');
    const powerOffScreen = document.getElementById('powerOffScreen');
    const sleepScreen = document.getElementById('sleepScreen');
    const activationScreen = document.getElementById('activationScreen');
    const activationInput = document.getElementById('activationInput');
    const activationBtn = document.getElementById('activationBtn');
    const skipActivationBtn = document.getElementById('skipActivationBtn');
    const activationSuccess = document.getElementById('activationSuccess');
    const activationReminder = document.getElementById('activationReminder');
    const reminderActivateBtn = document.getElementById('reminderActivateBtn');
    const powerOnBtn = document.getElementById('powerOnBtn');
    const powerOffConsoleInput = document.getElementById('powerOffConsoleInput');
    const powerOffConsoleOutput = document.getElementById('powerOffConsoleOutput');
    const bootBar = document.getElementById('bootBar');
    const desktopEl = document.querySelector('.desktop');
    const toastEl = document.getElementById('toast');
    const interactiveItems = Array.from(document.querySelectorAll('.app-btn, .icon, .start-item'));
    const startItems = Array.from(document.querySelectorAll('.start-item'));
    const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
    const lowPowerMode = prefersReducedMotion || (navigator.hardwareConcurrency && navigator.hardwareConcurrency <= 4) || (navigator.deviceMemory && navigator.deviceMemory <= 4);
    let bootFrameId = null;
    let terminalInitialized = false;
    let isActivated = false;
    let pumbaJsonMode = false;

    const apps = {
      'Панель': {
        title: 'Панель PUMBA',
        icon: '🖥️',
        html: `
          <div class="hero-card">
            <div>
              <h2>Система готова</h2>
              <p>Преміальний Windows-подібний інтерфейс з плавною анімацією та сучасною панеллю.</p>
            </div>
            <span class="pill">Online • 24/7</span>
          </div>
          <div class="grid-2">
            <div class="card">
              <h3>CPU</h3>
              <strong>42%</strong>
              <div class="meter"><span style="width:42%"></span></div>
            </div>
            <div class="card">
              <h3>RAM</h3>
              <strong>68%</strong>
              <div class="meter"><span style="width:68%"></span></div>
            </div>
            <div class="card">
              <h3>SSD</h3>
              <strong>720 GB</strong>
              <div class="meter"><span style="width:80%"></span></div>
            </div>
            <div class="card">
              <h3>Мережа</h3>
              <strong>120 Mbps</strong>
              <div class="meter"><span style="width:90%"></span></div>
            </div>
          </div>`
      },
      'Параметри': {
        title: 'Параметри',
        icon: '⚙️',
        html: `
          <div class="settings-shell">
            <div class="hero-card">
              <div>
                <h2>Параметри системи</h2>
                <p>Базові налаштування, продуктивність і безпека.</p>
              </div>
              <span class="pill">Система • Готово</span>
            </div>
            <div class="settings-grid">
              <div class="card">
                <h3>Вигляд</h3>
                <div class="theme-controls">
                  <button class="theme-btn" data-theme="dark">Темна</button>
                  <button class="theme-btn" data-theme="light">Світла</button>
                  <button class="theme-btn" data-theme="neon">Неон</button>
                </div>
                <div class="settings-row">
                  <span>Поточна тема</span>
                  <span class="setting-pill active" id="themeLabel">Темна</span>
                </div>
                <div class="settings-row">
                  <span>Авто-тема</span>
                  <button class="toggle-btn active" data-setting="autoTheme">Вкл</button>
                </div>
              </div>
              <div class="card">
                <h3>Продуктивність</h3>
                <div class="settings-row">
                  <span>Режим</span>
                  <span class="setting-pill active" id="perfMode">Сбаланс.</span>
                </div>
                <div class="settings-row">
                  <span>Енергозбереження</span>
                  <button class="toggle-btn" data-setting="powerSave">Выкл</button>
                </div>
                <div class="settings-row">
                  <span>Очистка памʼяті</span>
                  <button class="toggle-btn active" data-setting="memoryClean">Вкл</button>
                </div>
              </div>
              <div class="card">
                <h3>Уведомлення</h3>
                <div class="settings-row">
                  <span>Системні</span>
                  <button class="toggle-btn active" data-setting="systemNoti">Вкл</button>
                </div>
                <div class="settings-row">
                  <span>Поради</span>
                  <button class="toggle-btn" data-setting="tips">Выкл</button>
                </div>
              </div>
              <div class="card">
                <h3>Безпека</h3>
                <div class="settings-row">
                  <span>Блокування</span>
                  <span class="setting-pill active">Активна</span>
                </div>
                <div class="settings-row">
                  <span>Приватність</span>
                  <button class="toggle-btn active" data-setting="privacy">Вкл</button>
                </div>
              </div>
            </div>
          </div>`
      },
      'Провідник': {
        title: 'Провідник',
        icon: '📁',
        html: `
          <div class="hero-card">
            <div>
              <h2>Провідник файлів</h2>
              <p>Гарно організований доступ до основних директорій.</p>
            </div>
            <span class="pill">Файли</span>
          </div>
          <div class="card">
            <h3>Останні папки</h3>
            <ul class="list">
              <li>Документи <span>12</span></li>
              <li>Зображення <span>8</span></li>
              <li>Проєкти <span>4</span></li>
            </ul>
          </div>`
      },
      'Мережа': {
        title: 'Мережа',
        icon: '🌐',
        html: `
          <div class="hero-card">
            <div>
              <h2>Мережева активність</h2>
              <p>Підключення, швидкість та стан з'єднань у одному місці.</p>
            </div>
            <span class="pill">Wi-Fi</span>
          </div>
          <div class="grid-2">
            <div class="card">
              <h3>Сигнал</h3>
              <strong>95%</strong>
              <div class="meter"><span style="width:95%"></span></div>
            </div>
            <div class="card">
              <h3>Швидкість</h3>
              <strong>120 Mbps</strong>
              <div class="meter"><span style="width:90%"></span></div>
            </div>
          </div>`
      },
      'Терминал': {
        title: 'Терминал',
        icon: '⌨️',
        html: `
          <div class="terminal-shell">
            <div class="terminal-output" id="terminalOutput"></div>
            <div class="terminal-prompt">
              <span>PS C:\PUMBA&gt;</span>
              <input class="terminal-input" id="terminalInput" spellcheck="false" autocomplete="off" />
            </div>
            <div class="terminal-hint">Доступні команди: help, clear, date, time, echo, ls, theme, open, shutdown, restart, lock, sleep, whoami, pwd, version</div>
          </div>`
      }
    };

    const rootEl = document.documentElement;
    const themes = {
      dark: {
        label: 'Темна',
        '--win-blue': '#2563eb',
        '--win-cyan': '#38bdf8',
        '--text': '#f8fafc',
        '--muted': '#cbd5e1',
        '--panel': 'rgba(10, 16, 30, 0.78)',
        '--border': 'rgba(255,255,255,0.16)',
        '--shadow': '0 20px 50px rgba(0, 0, 0, 0.3)',
        '--glow': 'rgba(56, 189, 248, 0.35)'
      },
      light: {
        label: 'Світла',
        '--win-blue': '#2563eb',
        '--win-cyan': '#0ea5e9',
        '--text': '#0f172a',
        '--muted': '#475569',
        '--panel': 'rgba(255, 255, 255, 0.78)',
        '--border': 'rgba(15, 23, 42, 0.12)',
        '--shadow': '0 18px 45px rgba(15, 23, 42, 0.14)',
        '--glow': 'rgba(37, 99, 235, 0.16)'
      },
      neon: {
        label: 'Неон',
        '--win-blue': '#8b5cf6',
        '--win-cyan': '#22d3ee',
        '--text': '#f8fafc',
        '--muted': '#d8b4fe',
        '--panel': 'rgba(17, 24, 39, 0.82)',
        '--border': 'rgba(255,255,255,0.16)',
        '--shadow': '0 20px 50px rgba(17, 24, 39, 0.34)',
        '--glow': 'rgba(34, 211, 238, 0.28)'
      }
    };

    let currentTheme = localStorage.getItem('pumba-theme') || 'dark';

    function getThemeButtons() {
      return Array.from(windowBodyEl.querySelectorAll('.theme-btn'));
    }

    function bindCustomizationControls() {
      const buttons = getThemeButtons();
      buttons.forEach((btn) => {
        btn.onclick = (event) => {
          event.preventDefault();
          if (!isActivated) {
            showToast('Потрібно активувати PUMBA для налаштувань');
            return;
          }
          applyTheme(btn.dataset.theme);
        };
      });

      const toggles = Array.from(windowBodyEl.querySelectorAll('.toggle-btn'));
      toggles.forEach((toggle) => {
        toggle.onclick = (event) => {
          event.preventDefault();
          if (!isActivated) {
            showToast('Потрібно активувати PUMBA для налаштувань');
            return;
          }
          toggle.classList.toggle('active');
          toggle.textContent = toggle.classList.contains('active') ? 'Вкл' : 'Выкл';
        };
      });

      const statusEl = document.getElementById('settingsStatus');
      if (statusEl) {
        statusEl.textContent = isActivated ? 'Активація завершена • налаштування доступні' : 'Потрібна активація • доступ до теми заблоковано';
      }
    }

    function applyTheme(themeName) {
      const theme = themes[themeName] || themes.dark;
      currentTheme = themeName;
      Object.entries(theme).forEach(([key, value]) => {
        if (key.startsWith('--')) rootEl.style.setProperty(key, value);
      });
      getThemeButtons().forEach((btn) => {
        btn.classList.toggle('active', btn.dataset.theme === themeName);
      });
      const labelEl = document.getElementById('themeLabel');
      if (labelEl) labelEl.textContent = theme.label;
      localStorage.setItem('pumba-theme', themeName);
    }

    function updateActiveState(activeName) {
      interactiveItems.forEach((el) => {
        el.classList.toggle('active', el.dataset.app === activeName);
      });
    }

    function updateClock() {
      const now = new Date();
      clockEl.textContent = now.toLocaleTimeString('uk-UA', { hour: '2-digit', minute: '2-digit' });
      dateWidgetEl.textContent = now.toLocaleDateString('uk-UA', { day: 'numeric', month: 'long', year: 'numeric' });
    }

    function showBootSequence() {
      if (prefersReducedMotion || lowPowerMode) {
        bootBar.style.width = '100%';
        setTimeout(() => {
          bootScreen.classList.add('hide');
          setTimeout(() => {
            lockScreen.classList.add('show');
          }, 80);
        }, 80);
        return;
      }

      const start = performance.now();
      const duration = 1600;

      function tick(now) {
        const progress = Math.min(100, ((now - start) / duration) * 100);
        bootBar.style.width = `${progress}%`;

        if (progress < 100) {
          bootFrameId = requestAnimationFrame(tick);
        } else {
          setTimeout(() => {
            bootScreen.classList.add('hide');
            setTimeout(() => {
              lockScreen.classList.add('show');
            }, 160);
          }, 120);
        }
      }

      bootFrameId = requestAnimationFrame(tick);
    }

    function unlockScreen() {
      lockScreen.classList.remove('show');
      showToast('Розблоковано • PUMBA OS готовий');
    }

    function wakeFromSleep() {
      sleepScreen.classList.remove('show');
      desktopEl.classList.remove('powering-off');
      showToast('Пробудження • PUMBA OS готовий');
    }

    function startSystemFromOff() {
      if (bootFrameId) {
        cancelAnimationFrame(bootFrameId);
        bootFrameId = null;
      }
      powerOffScreen.classList.remove('show');
      desktopEl.classList.remove('powering-off');
      bootScreen.classList.remove('hide');
      bootBar.style.width = '0%';
      lockScreen.classList.remove('show');
      showBootSequence();
      showToast('Запуск PUMBA OS...');
    }

    function updateActivationReminder() {
      if (activationReminder) {
        activationReminder.classList.toggle('show', !isActivated);
      }
    }

    function openActivationPrompt() {
      activationScreen.classList.add('show');
      activationInput.value = '';
      activationSuccess.textContent = '';
      requestAnimationFrame(() => activationInput.focus());
    }

    function activatePumba() {
      const key = activationInput.value.trim().toUpperCase();
      const validKeys = ['WEGSGVHU-JFFDFCCVB5', 'FRRTFT-BGHFGFF66'];
      if (validKeys.includes(key)) {
        isActivated = true;
        updateActivationReminder();
        activationSuccess.textContent = 'Активація успішна. PUMBA активовано.';
        activationInput.value = '';
        setTimeout(() => {
          activationScreen.classList.remove('show');
          activationSuccess.textContent = '';
        }, 1000);
        if (windowBodyEl.innerHTML.includes('theme-btn')) {
          bindCustomizationControls();
        }
      } else {
        activationSuccess.textContent = 'Невірний ключ. Спробуйте ще раз.';
      }
    }

    function skipActivation() {
      activationScreen.classList.remove('show');
      updateActivationReminder();
      showToast('Активацію відкладено');
    }

    function bindCustomizationControls() {
      const buttons = getThemeButtons();
      buttons.forEach((btn) => {
        btn.onclick = (event) => {
          event.preventDefault();
          if (!isActivated) {
            showToast('Потрібно активувати PUMBA для налаштувань');
            return;
          }
          applyTheme(btn.dataset.theme);
        };
      });

      const statusEl = document.getElementById('settingsStatus');
      if (statusEl) {
        statusEl.textContent = isActivated ? 'Активація завершена • налаштування доступні' : 'Потрібна активація • доступ до теми заблоковано';
      }
    }

    function showToast(message) {
      toastEl.textContent = message;
      toastEl.classList.add('show');
      clearTimeout(showToast.timeout);
      showToast.timeout = setTimeout(() => {
        toastEl.classList.remove('show');
      }, 1800);
    }

    function appendTerminalLine(text) {
      const outputEl = document.getElementById('terminalOutput');
      if (!outputEl) return;
      const line = document.createElement('div');
      line.className = 'terminal-line';
      line.textContent = text;
      outputEl.appendChild(line);
      outputEl.scrollTop = outputEl.scrollHeight;
    }

    function runTerminalCommand(command) {
      const trimmed = command.trim();
      if (!trimmed) return '';

      const [cmd, ...args] = trimmed.split(/\s+/);
      const lower = cmd.toLowerCase();

      if (lower === 'help') {
        return 'Команди: help, clear, date, time, echo, ls, theme dark|light|neon, open panel|parameters|explorer|network|terminal, shutdown, restart, lock, sleep, whoami, pwd, version';
      }

      if (lower === 'clear') {
        const outputEl = document.getElementById('terminalOutput');
        if (outputEl) outputEl.innerHTML = '';
        return '';
      }

      if (lower === 'date') {
        return new Date().toLocaleDateString('uk-UA');
      }

      if (lower === 'time') {
        return new Date().toLocaleTimeString('uk-UA');
      }

      if (lower === 'echo') {
        return args.join(' ') || '';
      }

      if (lower === 'ls') {
        return 'Папки: Desktop, Documents, Projects, Downloads\nПрограми: Panel, Settings, Explorer, Network, Terminal';
      }

      if (lower === 'whoami') {
        return 'pumba';
      }

      if (lower === 'pwd') {
        return '/home/pumba';
      }

      if (lower === 'version') {
        return 'PUMBA OS 1.0';
      }

      if (lower === 'theme') {
        const themeName = (args[0] || '').toLowerCase();
        if (themeName && themes[themeName]) {
          applyTheme(themeName);
          return `Тему змінено на ${themes[themeName].label}`;
        }
        return 'Використання: theme dark|light|neon';
      }

      if (lower === 'pumbajson') {
        pumbaJsonMode = true;
        if (windowBodyEl.innerHTML.includes('terminal-shell')) {
          const outputEl = document.getElementById('terminalOutput');
          if (outputEl) {
            outputEl.innerHTML = '';
            appendTerminalLine('PUMBA JSON mode enabled');
            appendTerminalLine('{"status":"active","shell":"pumba","mode":"json"}');
          }
        }
        return 'PUMBA JSON mode enabled';
      }

      if (lower === 'open') {
        const appName = (args.join(' ') || '').toLowerCase();
        const mapping = {
          'panel': 'Панель',
          'parameters': 'Параметри',
          'settings': 'Параметри',
          'explorer': 'Провідник',
          'network': 'Мережа',
          'terminal': 'Терминал'
        };
        const target = mapping[appName] || null;
        if (target) {
          openApp(target);
          return `Відкрито ${target}`;
        }
        return 'Невідомий додаток';
      }

      if (lower === 'shutdown') {
        handlePowerAction('shutdown');
        return 'Вимкнення...';
      }

      if (lower === 'restart') {
        handlePowerAction('restart');
        return 'Перезавантаження...';
      }

      if (lower === 'lock') {
        handlePowerAction('lock');
        return 'Блокування екрана';
      }

      if (lower === 'sleep') {
        handlePowerAction('sleep');
        return 'Перехід у сон';
      }

      return `Команда не знайдена: ${cmd}`;
    }

    function setupTerminalApp() {
      if (terminalInitialized) return;
      terminalInitialized = true;

      const inputEl = document.getElementById('terminalInput');
      const outputEl = document.getElementById('terminalOutput');
      if (!inputEl || !outputEl) return;

      outputEl.innerHTML = '';
      appendTerminalLine('PUMBA OS Terminal');
      appendTerminalLine('Введіть help для списку команд.');

      inputEl.addEventListener('keydown', (event) => {
        if (event.key !== 'Enter') return;
        event.preventDefault();
        const command = inputEl.value;
        if (!command.trim()) return;

        appendTerminalLine(`> ${command}`);
        const result = runTerminalCommand(command);
        if (result) {
          appendTerminalLine(result);
        }
        inputEl.value = '';
      });

      requestAnimationFrame(() => inputEl.focus());
    }

    function handlePowerAction(action) {
      startMenu.classList.remove('open');
      if (action === 'shutdown') {
        desktopEl.classList.add('powering-off');
        powerOffScreen.classList.add('show');
        sleepScreen.classList.remove('show');
        return;
      }

      if (action === 'signout') {
        desktopEl.classList.add('powering-off');
        powerOffScreen.classList.add('show');
        sleepScreen.classList.remove('show');
        if (powerOffConsoleOutput) {
          powerOffConsoleOutput.innerHTML = '';
          powerOffConsoleOutput.textContent = 'PUMBA OS\nСистема в режимі очікування.\nВведіть PUMBAJSON для активації.';
        }
        if (powerOffConsoleInput) {
          powerOffConsoleInput.value = '';
          setTimeout(() => powerOffConsoleInput.focus(), 80);
        }
        showToast('Вихід із сеансу');
        return;
      }

      if (action === 'restart') {
        powerOffScreen.classList.remove('show');
        sleepScreen.classList.remove('show');
        bootScreen.classList.remove('hide');
        bootBar.style.width = '0%';
        lockScreen.classList.remove('show');
        showBootSequence();
        showToast('Перезавантаження системи...');
        return;
      }

      if (action === 'sleep') {
        desktopEl.classList.add('powering-off');
        sleepScreen.classList.add('show');
        powerOffScreen.classList.remove('show');
        showToast('Режим сну');
        return;
      }

      let message = 'Система готова';
      if (action === 'lock') {
        message = 'Блокування екрана';
        lockScreen.classList.add('show');
      } else if (action === 'signout') {
        message = 'Вихід із сеансу';
      }
      showToast(message);
    }

    function openApp(name) {
      const app = apps[name];
      if (!app) return;
      windowTitleEl.innerHTML = `${app.icon} ${app.title}`;
      windowBodyEl.innerHTML = app.html;
      windowEl.classList.remove('minimized');
      windowEl.classList.add('open');

      if (name === 'Параметри') {
        const statusHtml = `
          <div class="card" style="margin-bottom: 12px;">
            <h3>Активація</h3>
            <div id="settingsStatus" class="activation-hint">${isActivated ? 'Активація завершена • налаштування доступні' : 'Потрібна активація • доступ до теми заблоковано'}</div>
          </div>`;
        windowBodyEl.insertAdjacentHTML('afterbegin', statusHtml);
        bindCustomizationControls();
        applyTheme(currentTheme);
      }

      if (name === 'Терминал') {
        setupTerminalApp();
      }

      updateActiveState(name);

      startMenu.classList.remove('open');
      showToast(`Відкрито: ${name}`);
    }

    function closeWindow() {
      windowEl.classList.remove('open');
      windowEl.classList.remove('minimized');
    }

    function minimizeWindow() {
      windowEl.classList.add('minimized');
      windowEl.classList.remove('open');
    }

    document.addEventListener('click', (event) => {
      const appEl = event.target.closest('[data-app]');
      if (appEl) {
        event.stopPropagation();
        openApp(appEl.dataset.app);
        return;
      }

      const actionEl = event.target.closest('[data-action]');
      if (actionEl) {
        event.stopPropagation();
        handlePowerAction(actionEl.dataset.action);
      }
    });

    document.getElementById('closeBtn').addEventListener('click', closeWindow);
    document.querySelector('.mini').addEventListener('click', minimizeWindow);
    startBtn.addEventListener('click', () => startMenu.classList.toggle('open'));

    startSearch.addEventListener('input', () => {
      const value = startSearch.value.toLowerCase();
      startItems.forEach((item) => {
        const text = item.textContent.toLowerCase();
        item.classList.toggle('hide', value && !text.includes(value));
      });
    });

    powerOnBtn.addEventListener('click', startSystemFromOff);
    powerOffConsoleInput.addEventListener('keydown', (event) => {
      if (event.key !== 'Enter') return;
      event.preventDefault();
      const command = powerOffConsoleInput.value.trim().toLowerCase();
      if (command === 'pumbajson') {
        pumbaJsonMode = true;
        if (powerOffConsoleOutput) {
          powerOffConsoleOutput.textContent += '\n\nPUMBA JSON mode enabled';
        }
        powerOffConsoleInput.value = '';
      } else if (command) {
        if (powerOffConsoleOutput) {
          powerOffConsoleOutput.textContent += `\n\nUnknown command: ${command}`;
        }
        powerOffConsoleInput.value = '';
      }
    });
    activationBtn.addEventListener('click', activatePumba);
    skipActivationBtn.addEventListener('click', skipActivation);
    reminderActivateBtn.addEventListener('click', openActivationPrompt);
    activationInput.addEventListener('keydown', (event) => {
      if (event.key === 'Enter') {
        event.preventDefault();
        activatePumba();
      }
    });
    lockScreen.addEventListener('click', unlockScreen);
    sleepScreen.addEventListener('click', wakeFromSleep);
    document.addEventListener('keydown', (event) => {
      if (lockScreen.classList.contains('show') && (event.key === 'Enter' || event.key === ' ' || event.key === 'Escape')) {
        unlockScreen();
      }
      if (sleepScreen.classList.contains('show') && (event.key === 'Enter' || event.key === ' ' || event.key === 'Escape')) {
        wakeFromSleep();
      }
    });

    document.addEventListener('click', (event) => {
      if (!startMenu.contains(event.target) && event.target !== startBtn) {
        startMenu.classList.remove('open');
      }
    });

    if (lowPowerMode) {
      document.body.classList.add('low-power');
    }

    activationScreen.classList.add('show');
    updateActivationReminder();
    applyTheme(currentTheme);
    updateClock();
    setInterval(updateClock, 1000);
    showBootSequence();
    showToast('PUMBA OS запущено');
    openApp('Панель');
  </script>
</body>
</html>

