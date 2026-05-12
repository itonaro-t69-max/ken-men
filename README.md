<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>顕微鏡・操作免許センター</title>

<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=BIZ+UDGothic:wght@400;700&display=swap" rel="stylesheet">

<style>
  body { 
    font-family: 'BIZ UDGothic', sans-serif; 
    background-color: #f0f7ff; color: #333; line-height: 1.6; margin: 0; padding: 15px; 
    -webkit-user-select: none; user-select: none; -webkit-touch-callout: none;
    -webkit-tap-highlight-color: transparent;
  }
  
  #app-container { max-width: 600px; margin: 0 auto; background: white; padding: 20px; border-radius: 12px; box-shadow: 0 4px 20px rgba(0,0,0,0.15); border: 2px solid #d0d0d0; transition: max-width 0.3s;}
  h1 { font-size: 22px; border-bottom: 3px solid #005bac; padding-bottom: 10px; margin-top: 0;}

  button { touch-action: manipulation; }

  .menu-btn { display: block; width: 100%; padding: 16px; margin-bottom: 15px; font-size: 18px; font-weight: bold; font-family: inherit; border-radius: 10px; border: none; cursor: pointer; color: white; transition: 0.2s; }
  .menu-btn:hover { filter: brightness(1.1); transform: scale(1.02); }
  #btn-mode1 { background: #005bac; } #btn-mode2 { background: #ff8c00; } #btn-mode3 { background: #555555; } #btn-tutorial { background: #4caf50; }
  .hidden { display: none !important; }
  
  #timer-board { font-size: 22px; color: #d32f2f; font-weight: bold; margin-bottom: 10px; background: #ffebee; padding: 8px; border-radius: 8px; border: 2px solid #ef5350; text-align: center;}
  
  #status-board { background: #333; color: #fff; padding: 10px 14px; border-radius: 8px; margin-bottom: 15px; font-weight: bold; border-left: 8px solid #ff8c00; transition: 0.3s; font-size: 14px; height: 84px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; text-align: left; line-height: 1.4; overflow: hidden;}
  
  .alert { border-left-color: #d32f2f !important; background: #fff5e6 !important; color: #d32f2f !important; }
  .success { border-left-color: #005bac !important; background: #e6f0ff !important; color: #005bac !important; }
  .warning { border-left-color: #ffeb3b !important; background: #fffde7 !important; color: #f57f17 !important; }

  #view-area { width: 100%; height: 320px; background: #f5f5f5; border-radius: 10px; position: relative; overflow: hidden; margin-bottom: 15px; border: 2px solid #ccc; display: flex; align-items: center; justify-content: center; transition: height 0.3s;}
  
  #side-view { width: 100%; height: 320px; position: relative; }
  #lens-wrapper { position: absolute; top: 40px; left: 50%; transform: translateX(-50%); z-index: 5; text-align: center; cursor: pointer; }
  #revolver-base { width: 60px; height: 15px; background: #444; border-radius: 10px 10px 0 0; margin: 0 auto;}
  #active-lens { width: 28px; border: 2px solid #222; border-radius: 0 0 5px 5px; color: white; font-size: 12px; font-weight: bold; display: flex; align-items: flex-end; justify-content: center; padding-bottom: 5px; margin: 0 auto; transition: all 0.3s;}
  /* レンズの長さ：4x(35px), 10x(55px), 40x(85px)。先端のY座標は最大140pxになる */
  .lens-type-0 { height: 35px; background: #888; } .lens-type-1 { height: 55px; background: #ffcc00; color: black !important; } .lens-type-2 { height: 85px; background: #d32f2f; } 
  .lens-guide { font-size: 12px; font-weight: bold; color: #005bac; margin-top: 5px; background: rgba(255,255,255,0.8); padding: 2px 5px; border-radius: 4px;}

  #stage-obj { width: 180px; height: 12px; background: #444; position: absolute; top: 240px; left: 50%; transform: translateX(-50%); transition: top 0.2s linear; }
  #slide-obj { width: 80px; height: 6px; background: rgba(180, 230, 255, 0.6); border: 1px solid #005bac; position: absolute; top: -7px; left: 50px; opacity: 0; transition: opacity 0.3s; }
  .slide-set { opacity: 1 !important; }
  
  #eye-view { display: none; width: 100%; height: 100%; background: black; align-items: center; justify-content: center; }
  #eyepiece-circle { width: 280px; height: 280px; background: white; border-radius: 50%; overflow: hidden; position: relative; display: flex; align-items: center; justify-content: center; transform: rotate(180deg); transition: width 0.3s, height 0.3s;}

  /* 描画エリア巨大化＋GPUアクセラレーションでモバイルもサクサク動くように */
  #slide-group {
    position: absolute;
    width: 2800px;
    height: 2800px;
    left: 50%;
    top: 50%;
    margin-left: -1400px;
    margin-top: -1400px;
    transition: transform 0.3s, filter 0.3s;
    transform-origin: 50% 50%;
    will-change: transform, filter; 
  }

  .control-group { margin-bottom: 10px; padding: 12px; background: #fdfdfd; border: 1px solid #eee; border-radius: 8px; text-align: left; font-size: 14px;}
  .btn-box { display: flex; flex-wrap: wrap; gap: 8px; }
  button.op-btn { flex: 1; min-width: 45px; padding: 10px 5px; border-radius: 6px; border: none; font-weight: bold; font-family: inherit; cursor: pointer; background: #eee; color: #333; border-bottom: 4px solid #ccc; transition: 0.1s;}
  button.op-btn:active { border-bottom: 0; transform: translateY(4px); }
  button:disabled { background: #e0e0e0 !important; border-bottom-color: #ccc !important; color: #999 !important; cursor: not-allowed; transform: none !important;}
  button.danger { background: #ff8c00; color: white; border-bottom-color: #cc7000; }
  button.action { background: #005bac; color: white; border-bottom-color: #00407a; }
  button.highlight { background: #9c27b0; color: white; border-bottom-color: #7b1fa2; animation: pulse 1.5s infinite;}
  
  @keyframes pulse { 0% { filter: brightness(1); } 50% { filter: brightness(1.3); } 100% { filter: brightness(1); } }
  
  input[type="range"] { -webkit-appearance: none; width: 100%; height: 30px; background: transparent; cursor: pointer; }
  input[type="range"]::-webkit-slider-thumb { -webkit-appearance: none; height: 24px; width: 24px; border-radius: 50%; background: #005bac; cursor: pointer; margin-top: -8px; box-shadow: 0 2px 5px rgba(0,0,0,0.3);}
  input[type="range"]::-webkit-slider-runnable-track { width: 100%; height: 8px; background: #ccc; border-radius: 4px; }
  
  .rescue-box { text-align: left; background: #fff5e6; border-left: 5px solid #ff8c00; padding: 15px; margin-bottom: 15px; }
  
  #license-screen { text-align: center; position: relative; overflow: hidden; padding: 20px 0;}
  .license-title { font-size: 28px; font-weight: bold; background: linear-gradient(45deg, #FF1493, #3f51b5, #00bcd4, #4caf50, #ff9800); -webkit-background-clip: text; color: transparent; animation: rainbow 3s infinite linear; margin-top:0;}
  .license-card { width: 90%; margin: 0 auto; border-radius: 15px; padding: 25px; text-align: left; position: relative; z-index: 10; box-shadow: 0 10px 30px rgba(0,0,0,0.3); font-size: 16px;}
  .license-card::before { content: "🔬"; position: absolute; top: 15px; right: 20px; font-size: 40px; opacity: 0.8; }
  .card-bronze { background: linear-gradient(135deg, #eceff1, #cfd8dc); border: 3px solid #b0bec5; }
  .card-silver { background: linear-gradient(135deg, #ffffff, #e0e0e0); border: 3px solid #9e9e9e;}
  .card-gold { background: linear-gradient(135deg, #fffde7, #fff59d, #ffd54f, #fffde7); background-size: 200% 200%; border: 4px solid #fbc02d; animation: shine 3s infinite linear;}
  @keyframes shine { 0% { background-position: 0% 50%; } 100% { background-position: 100% 50%; } }
  @keyframes rainbow { 0% { background-position: 0% 50%; } 100% { background-position: 100% 50%; } }
  .confetti { position: absolute; font-size: 24px; animation: fall 3s infinite linear; z-index: 1;}
  @keyframes fall { 0% { transform: translateY(-50px) rotate(0deg); opacity: 1;} 100% { transform: translateY(150vh) rotate(360deg); opacity: 0;} }

  @media (orientation: landscape) {
    #app-container { max-width: 900px; padding: 15px; }
    .simulator-layout { display: flex; gap: 15px; align-items: stretch; }
    .simulator-layout > .left-col { flex: 1; min-width: 0; }
    .simulator-layout > .right-col { flex: 1; min-width: 300px; display: flex; flex-direction: column; overflow-y: auto; max-height: calc(100vh - 150px); padding-right: 5px;}
    #view-area { margin-bottom: 0; height: calc(100vh - 180px); min-height: 250px;}
    #eyepiece-circle { width: 220px; height: 220px; }
  }

  @media (orientation: landscape) and (max-height: 500px) {
    body { padding: 5px; }
    #app-container { padding: 10px; }
    h1 { font-size: 18px; margin-bottom: 5px; padding-bottom: 5px;}
    #status-board { padding: 6px 10px; font-size: 12px; margin-bottom: 10px; height: 60px;}
    .control-group { padding: 8px; margin-bottom: 8px;}
    button.op-btn { padding: 6px 4px; font-size: 12px; }
    input[type="range"] { height: 20px; }
  }
</style>
</head>
<body>

<div id="app-container">
  <h1>🔬 顕微鏡・操作免許センター</h1>
  
  <div id="menu-screen">
    <button class="menu-btn" id="btn-tutorial" onclick="startTutorial()">🔰 操作チュートリアルを読む</button>
    <button class="menu-btn" id="btn-mode1" onclick="startMode(1)">🔰 【新規認定】 1年生 / タマネギ観察 (〜100倍)</button>
    <button class="menu-btn" id="btn-mode2" onclick="startMode(2)">⏱️ 【操作更新】 2・3年生 / ミドリムシ観察 (〜400倍)</button>
    <button class="menu-btn" id="btn-mode3" onclick="startMode(3)">🆘 【AIレスキュー】 トラブルガイド</button>
  </div>

  <div id="tutorial-screen" class="hidden">
    <h2 style="color: #4caf50;">🔰 顕微鏡の絶対ルール</h2>
    
    <div class="rescue-box" style="border-color:#4caf50;">
      <h3 style="margin-top:0; color:#2e7d32;">① プレパラートの動かし方</h3>
      <p>顕微鏡の視野は<strong>「上下左右が逆（倒立像）」</strong>になります。以下のルールを覚えましょう！<br><br>
      ・<strong>視野の中の像の動き：</strong>プレパラートを動かした方向と<strong>真逆の方向</strong>へ像が動きます。<br>
      ・<strong>中央への寄せ方：</strong>見たいものを視野の中央に移動させたい場合、レンズをのぞいて<strong>見えている方向と同じ方向</strong>へプレパラートを動かします。</p>
    </div>
    
    <div class="rescue-box" style="border-color:#4caf50;">
      <h3 style="margin-top:0; color:#2e7d32;">② ピント合わせの順序</h3>
      <p>1. まず「横から見て」粗動ねじでレンズを極限まで近づけます。<br>
      2. 次に「覗きながら」ねじを<strong>遠ざける方向</strong>に回してピントを探します。</p>
    </div>

    <div class="rescue-box" style="border-color:#d32f2f;">
      <h3 style="margin-top:0; color:#d32f2f;">🚫 一発免停（破損）になる操作</h3>
      <p>・覗きながら「近づける」方向にねじを回す<br>
      ・100倍、400倍の高倍率で「粗動ねじ（大きく動く）」を触る<br>
      ・対象を「ど真ん中」に置かないまま高倍率に切り替える（見失います）</p>
    </div>
    
    <button class="menu-btn" style="background:#555; margin-top:20px;" onclick="location.reload()">トップに戻る</button>
  </div>

  <div id="quiz-screen" class="hidden">
    <h2>📝 更新前リマインド・クイズ</h2>
    <div style="background:#f5f5f5; padding:20px; border-radius:8px; margin-bottom:20px;">
      <p id="quiz-text" style="font-size:18px; font-weight:bold; line-height: 1.8;"></p>
    </div>
    <button class="menu-btn" id="quiz-ans0" style="background:#005bac;" onclick="answerQuiz(0)"></button>
    <button class="menu-btn" id="quiz-ans1" style="background:#555;" onclick="answerQuiz(1)"></button>
  </div>

  <div id="rescue-screen" class="hidden">
    <h2>🆘 トラブル逆引きガイド</h2>
    <div class="rescue-box">
      <h3 style="margin-top:0; color:#d32f2f;">❓ ピントが合わない / 見えない</h3>
      <p>① 横から見て、レンズをギリギリまで近づける。<br>② 覗きながら「遠ざける方向」にねじを回す。</p>
    </div>
    <div class="rescue-box">
      <h3 style="margin-top:0; color:#d32f2f;">❓ 高倍率にしたら真っ暗 / 見えない</h3>
      <p>① 絞りを開いて光を多く入れる。<br>② 低倍率の時に対象を「ど真ん中」に置いていないと、高倍率では見失います。</p>
    </div>
    <button class="menu-btn" style="background:#673ab7; margin-top:20px;" onclick="window.open('https://app.studypocket.ai/', '_blank')">🤖 スタディポケットAIに相談する</button>
    <button class="menu-btn" style="background:#9e9e9e;" onclick="location.reload()">トップに戻る</button>
  </div>

  <div id="license-screen" class="hidden">
    <div id="confetti-container"></div>
    <h2 class="license-title">🎉 操作技能 認定！ 🎉</h2>
    <div id="license-card" class="license-card card-bronze">
      <h3 style="margin-top:0; border-bottom: 2px solid #333; padding-bottom:5px; font-size:20px;">顕微鏡操作 認定証</h3>
      <p><strong>氏名：</strong> 未来の科学者 殿</p>
      <p><strong>ランク：</strong> <span id="license-rank" style="font-size:26px; font-weight:bold; color:#005bac;">ブロンズ認定</span> <span id="crown-icon"></span></p>
      <p id="license-time-wrap" style="font-size:18px;"><strong>クリアタイム：</strong> <span id="license-time" style="font-size:22px; color:#d32f2f; font-weight:bold;">-</span> 秒</p>
      <p style="font-size:13px; color:#555; margin-top:15px; border-top: 1px dashed #999; padding-top:10px;">上記のとおり、安全かつ正確な顕微鏡操作の技能を有することを証明する。</p>
    </div>
    <button class="menu-btn" style="background:#555; margin-top:25px; position:relative; z-index:10;" onclick="location.reload()">トップに戻る</button>
  </div>

  <div id="simulator-screen" class="hidden">
    <div id="timer-board" class="hidden">⏱️ 残り時間：<span id="time-text">180</span>秒</div>
    <div id="status-board">【準備】プレパラートを置き、一番短いレンズ(4x)をセットせよ。</div>

    <div class="simulator-layout">
      <div class="left-col">
        <div id="view-area">
          <div id="side-view">
            <div id="lens-wrapper" onclick="rotateLens()">
              <div id="revolver-base"></div>
              <div id="active-lens" class="lens-type-1">10x</div>
              <div class="lens-guide">🔄 クリックで回転</div>
            </div>
            <div id="stage-obj"><div id="slide-obj"></div></div>
          </div>
          <div id="eye-view">
            <div id="eyepiece-circle">
              <div id="slide-group"></div>
            </div>
          </div>
        </div>
      </div>

      <div class="right-col">
        <div id="panel-prep" class="control-group">
          <button class="op-btn action" id="btn-set-slide" onclick="setSlide()" style="width:100%; margin-bottom:10px;">① プレパラートを置く</button>
          <button class="op-btn danger" id="btn-lens-ok" onclick="completePrep()" style="width:100%;">② セット完了（点検）</button>
        </div>

        <div id="panel-main" class="control-group hidden">
          <div class="btn-box" style="margin-bottom:10px;">
            <button class="op-btn action" id="btn-side" onclick="switchView('side')" disabled>横から見る</button>
            <button class="op-btn" id="btn-eye" onclick="switchView('eye')">レンズを覗く</button>
          </div>
          
          <div style="height: 45px; margin-bottom: 10px;">
            <button class="op-btn hidden" id="btn-to-10x" onclick="switchTo10x()" style="width:100%; height:100%;">🔒 100倍切替（ピントを合わせよ）</button>
            <button class="op-btn hidden" id="btn-to-40x" onclick="switchTo40x()" style="width:100%; height:100%;">🔒 400倍切替（ピントを合わせよ）</button>
          </div>

          <div style="display: flex; gap: 5px;">
            <div style="flex: 1.2;">
              <strong style="font-size:12px;">ねじ（ピント合わせ）:</strong>
              <div class="btn-box" style="margin-bottom:5px;">
                <button class="op-btn danger" onclick="moveFocus(-5, 1)">粗動 近</button>
                <button class="op-btn action" onclick="moveFocus(5, 1)">粗動 遠</button>
              </div>
              <div class="btn-box">
                <button class="op-btn danger" onclick="moveFocus(-1, 0)">微動 近</button>
                <button class="op-btn action" onclick="moveFocus(1, 0)">微動 遠</button>
              </div>
            </div>
            <div style="flex: 1; text-align:center; border-left: 1px solid #ccc; padding-left: 5px;">
              <strong style="font-size:12px;">プレパラート移動<br>(倒立像注意):</strong>
              <div class="btn-box" style="justify-content: center; margin-bottom: 5px;">
                <button class="op-btn" style="min-width:40px; font-size:12px;" onclick="moveStage(0, -10)">↑<br>奥へ</button>
              </div>
              <div class="btn-box" style="justify-content: center;">
                <button class="op-btn" style="min-width:40px; font-size:12px;" onclick="moveStage(-10, 0)">←<br>左</button>
                <button class="op-btn" style="min-width:40px; font-size:12px;" onclick="moveStage(0, 10)">↓<br>手前</button>
                <button class="op-btn" style="min-width:40px; font-size:12px;" onclick="moveStage(10, 0)">→<br>右</button>
              </div>
            </div>
          </div>
          
          <hr style="margin: 10px 0;">
          <strong>しぼり（明るさ調整）：</strong>
          <input type="range" id="iris" min="0" max="100" value="50" oninput="updateVisuals()">
          <button class="op-btn action" id="btn-judge" onclick="judge()" style="width:100%; margin-top:10px; padding:15px; font-size:16px;">🏁 操作試験 終了</button>
        </div>
      </div>
    </div>
  </div>
</div>

<script>
  // ★ 物理法則の崩壊（めり込みバグ）を完全修正した新・座標計算 ★
  // どの端末でも、ピントが合う位置(distance:15)の時、40xレンズの先端とプレパラートの隙間が必ず「2px」になるように逆算。
  const DEVICE_SETTINGS = {
    mobilePortrait: { stageBaseTop: 254, stageMultiplier: 3.0, crashLimits: [0, 7, 13] },
    mobileLandscape: { stageBaseTop: 219, stageMultiplier: 2.0, crashLimits: [0, 8, 13] },
    tabletPortrait: { stageBaseTop: 289, stageMultiplier: 4.0, crashLimits: [0, 8, 13] },
    tabletLandscape: { stageBaseTop: 306.5, stageMultiplier: 4.5, crashLimits: [0, 8, 13] }
  };

  let currentDevice = 'mobilePortrait';

  function updateDeviceMode() {
    const w = window.innerWidth;
    const h = window.innerHeight;
    const isLandscape = w > h;
    const isTablet = Math.min(w, h) >= 700;

    if (isTablet) currentDevice = isLandscape ? 'tabletLandscape' : 'tabletPortrait';
    else currentDevice = isLandscape ? 'mobileLandscape' : 'mobilePortrait';
    
    updateStagePosition();
  }

  window.addEventListener('resize', updateDeviceMode);
  window.addEventListener('DOMContentLoaded', updateDeviceMode);

  const svgOnion = `
    <svg viewBox="-1000 -1000 2000 2000" style="width: 100%; height: 100%; overflow: visible;">
      <defs>
        <pattern id="onion-cell" x="0" y="0" width="80" height="40" patternUnits="userSpaceOnUse">
          <rect x="0" y="0" width="80" height="40" fill="#fce4ec" stroke="#ad1457" stroke-width="2" />
          <path d="M 0,20 Q 40,25 80,20" fill="none" stroke="#d81b60" stroke-width="0.5" opacity="0.4"/>
          <circle cx="20" cy="15" r="4.5" fill="#880e4f" opacity="0.5" />
          <circle cx="60" cy="25" r="3.5" fill="#880e4f" opacity="0.4" />
        </pattern>
      </defs>
      <rect x="-4000" y="-4000" width="8000" height="8000" fill="url(#onion-cell)" />
      <g id="target-cell">
        <rect x="-40" y="-20" width="80" height="40" fill="#f8bbd0" stroke="#880e4f" stroke-width="3" />
        <circle cx="-10" cy="5" r="6" fill="#4a148c" />
        <circle cx="-11" cy="4" r="1.5" fill="#212121" />
        <circle cx="-8" cy="6" r="1" fill="#212121" />
        <path d="M -40,0 Q 0,-10 40,0" fill="none" stroke="#880e4f" stroke-width="1.5" opacity="0.6"/>
      </g>
    </svg>
  `;

  const svgEuglena = `
    <svg viewBox="-1000 -1000 2000 2000" style="width: 100%; height: 100%; overflow: visible;">
      <rect x="-4000" y="-4000" width="8000" height="8000" fill="#f5f5f5" />
      <defs>
        <g id="euglena">
          <path d="M -30,0 C -10,-15 20,-10 35,0 C 20,10 -10,15 -30,0 Z" fill="#c5e1a5" stroke="#558b2f" stroke-width="1" />
          <circle cx="-20" cy="-2" r="2" fill="#d32f2f" />
          <circle cx="-5" cy="5" r="3" fill="#33691e" opacity="0.6"/>
          <circle cx="5" cy="-4" r="2.5" fill="#33691e" opacity="0.6"/>
          <circle cx="15" cy="3" r="3.5" fill="#33691e" opacity="0.6"/>
          <circle cx="25" cy="-2" r="2" fill="#33691e" opacity="0.6"/>
          <path d="M -30,0 Q -45,-15 -60,5" fill="none" stroke="#777" stroke-width="0.8" />
        </g>
      </defs>
      <circle cx="40" cy="50" r="1.5" fill="#ccc" />
      <circle cx="-50" cy="40" r="1" fill="#bbb" />
      <circle cx="80" cy="-20" r="2" fill="#ddd" />
      <use href="#euglena" x="80" y="-60" transform="scale(0.7) rotate(-45)" opacity="0.4" />
      <use href="#euglena" x="-70" y="-80" transform="scale(0.8) rotate(70)" opacity="0.4" />
      <use href="#euglena" x="120" y="100" transform="scale(0.6) rotate(120)" opacity="0.3" />
      <use href="#euglena" x="0" y="0" transform="scale(1.4) rotate(25)" />
    </svg>
  `;

  let currentMode = 1;
  let distance = 50; 
  let targetDistance = 15; 
  let slideX = 60, slideY = 60; 
  let currentLensType = 1; 
  let isSlideSet = false, isGameOver = false, currentStep = 0;
  let timeLeft = 180, timerId = null;
  
  const quizzes = [
    { q: "倍率を上げる前に行うことは？", a: ["対象を視野の中央にする", "絞りを閉じる"], c: 0 },
    { q: "最初に見るべき対物レンズは？", a: ["10倍・40倍（長い）", "4倍（短い）"], c: 1 },
    { q: "顕微鏡で見える像の特徴は？", a: ["上下左右が逆（倒立像）", "そのまま（正立像）"], c: 0 },
    { q: "高倍率（400倍等）でピントを合わせる時は？", a: ["粗動ねじを使う", "微動ねじだけを使う"], c: 1 }
  ];

  function startTutorial() {
    document.getElementById('menu-screen').classList.add('hidden');
    document.getElementById('tutorial-screen').classList.remove('hidden');
  }

  function startMode(mode) {
    currentMode = mode;
    document.getElementById('menu-screen').classList.add('hidden');
    
    if (mode === 3) { document.getElementById('rescue-screen').classList.remove('hidden'); return; }
    
    document.getElementById('slide-group').innerHTML = (mode === 1) ? svgOnion : svgEuglena;

    if (mode === 2) {
      const q = quizzes[Math.floor(Math.random() * quizzes.length)];
      document.getElementById('quiz-text').innerText = "Q. " + q.q;
      document.getElementById('quiz-ans0').innerText = q.a[0];
      document.getElementById('quiz-ans1').innerText = q.a[1];
      document.getElementById('quiz-screen').dataset.correct = q.c;
      document.getElementById('quiz-screen').classList.remove('hidden');
    } else {
      document.getElementById('simulator-screen').classList.remove('hidden');
    }
  }

  function answerQuiz(idx) {
    if (idx == document.getElementById('quiz-screen').dataset.correct) {
      alert("⭕ 大正解！タイムアタックを開始します！");
      document.getElementById('quiz-screen').classList.add('hidden');
      document.getElementById('simulator-screen').classList.remove('hidden');
      document.getElementById('timer-board').classList.remove('hidden');
      startTimer();
    } else {
      alert("❌ 【不正解】基本を間違えています。やり直し！");
      location.reload();
    }
  }

  function startTimer() {
    if (timerId === null) {
      timerId = setInterval(() => {
        timeLeft--;
        document.getElementById('time-text').innerText = timeLeft;
        if (timeLeft <= 0) { clearInterval(timerId); gameOver("❌ タイムアップ！不合格です。"); }
      }, 1000);
    }
  }

  function setSlide() {
    isSlideSet = true;
    document.getElementById('btn-set-slide').disabled = true;
    document.getElementById('slide-obj').classList.add('slide-set');
    msgBoard("【準備】レンズをクリックして「一番短い4x」に合わせ、「セット完了」を押せ。");
  }

  function rotateLens() {
    if (currentStep > 0) return; 
    currentLensType = (currentLensType + 1) % 3; 
    const lensEl = document.getElementById('active-lens');
    lensEl.className = `lens-type-${currentLensType}`;
    if(currentLensType === 0) lensEl.innerText = "4x";
    if(currentLensType === 1) lensEl.innerText = "10x";
    if(currentLensType === 2) lensEl.innerText = "40x";
  }

  function msgBoard(txt) { 
    document.getElementById('status-board').innerText = txt; 
  }

  function completePrep() {
    if (!isSlideSet) { alert("プレパラートを置いてください。"); return; }
    if (currentLensType !== 0) { alert("⚠️ 最初の観察は「一番短い4xレンズ」で行うのが鉄則です！"); return; }
    
    currentStep = 1;
    document.getElementById('panel-prep').classList.add('hidden');
    document.getElementById('panel-main').classList.remove('hidden');
    msgBoard("⚠️【STEP 1】横から見て、粗動ねじでレンズを極限まで近づけよ！");
    updateStagePosition();
    updateVisuals();
  }

  function updateStagePosition() {
    const settings = DEVICE_SETTINGS[currentDevice];
    const stageTop = settings.stageBaseTop - (50 - distance) * settings.stageMultiplier; 
    document.getElementById('stage-obj').style.top = stageTop + "px";
  }

  function moveStage(dx, dy) {
    if (isGameOver || currentStep < 2) return;
    
    slideX += dx; slideY += dy;
    
    if (slideX > 100) slideX = 100;
    if (slideX < -100) slideX = -100;
    if (slideY > 100) slideY = 100;
    if (slideY < -100) slideY = -100;
    
    checkMagAvailability();
    updateVisuals();
  }
  
  function checkMagAvailability() {
    if (document.getElementById('eye-view').style.display !== 'flex') return;
    
    const focusDist = Math.abs(distance - targetDistance);
    const posDist = Math.abs(slideX) <= 50 && Math.abs(slideY) <= 50;
    const posStrict = Math.abs(slideX) <= 10 && Math.abs(slideY) <= 10; 
    
    const btn10x = document.getElementById('btn-to-10x');
    const btn40x = document.getElementById('btn-to-40x');

    if (currentStep === 2) {
      btn10x.classList.remove('hidden');
      if (focusDist <= 1) { 
         btn10x.disabled = false;
         btn10x.innerText = "🔍 【STEP 3】100倍(対物10x)に切り替える";
         btn10x.className = "op-btn highlight";
         
         if (posDist) { 
            msgBoard("⭕【OK】対象が見えました！紫のボタンを押して「100倍」に切り替えてください。");
            document.getElementById('status-board').className = 'success';
         } else { 
            msgBoard("⚠️【注意】ピントは合っていますが、対象が視野から外れています！矢印ボタンで探してください！");
            document.getElementById('status-board').className = 'warning';
         }
      } else {
         btn10x.disabled = true;
         btn10x.innerText = "🔒 100倍切替（ピントを合わせよ）";
         btn10x.className = "op-btn";
         
         if (distance < targetDistance) {
           msgBoard("【STEP 2】最初はぼんやり見えます。微動ねじを「遠ざける」方向に回し、はっきり見える位置を探せ。");
         } else {
           msgBoard("【STEP 2】遠ざけすぎました！ピントが合っていません。「近づける」方向にねじを回して戻してください。");
         }
         document.getElementById('status-board').className = '';
      }
    } else if (currentStep === 3) {
      if (currentMode === 1) { 
        btn40x.classList.add('hidden');
        if (focusDist === 0 && posDist) {
           msgBoard("⭕【完璧】100倍のピントが合いました！絞りを開いて明るさを調整し、試験終了を押してください。");
           document.getElementById('status-board').className = 'success';
        } else if (focusDist === 0) {
           msgBoard("⚠️【注意】ピントは合っていますが、対象が視野から外れています！矢印ボタンで探してください。");
           document.getElementById('status-board').className = 'warning';
        } else {
           msgBoard("⚠️【STEP 3】100倍になりピントが少しズレました。微動ねじでピントを合わせよ！(粗動は免停)");
           document.getElementById('status-board').className = 'alert';
        }
      } else { 
        btn40x.classList.remove('hidden');
        if (focusDist === 0) { 
           btn40x.disabled = false;
           btn40x.innerText = "🔍 【STEP 4】400倍(対物40x)に切り替える";
           btn40x.className = "op-btn danger highlight";
           
           if (posStrict) { 
              msgBoard("⭕【OK】100倍のピントが合いました！対象を「ど真ん中」にしてから、赤ボタンで400倍に切り替えよ！");
              document.getElementById('status-board').className = 'success';
           } else { 
              msgBoard("⚠️【注意】ピントは合いましたが、対象が『ど真ん中』にありません！このまま400倍にすると確実に見失うぞ！");
              document.getElementById('status-board').className = 'warning';
           }
        } else {
           btn40x.disabled = true;
           btn40x.innerText = "🔒 400倍切替（ピントを完璧に合わせよ）";
           btn40x.className = "op-btn";
           msgBoard("【STEP 3】100倍になりピントが少しズレました。微動ねじでピントを完璧に合わせよ！");
           document.getElementById('status-board').className = 'alert';
        }
      }
    }
  }

  function switchTo10x() {
    if (Math.abs(slideX) > 50 || Math.abs(slideY) > 50) {
      gameOver("❌【見失った！】対象を視野の中央付近に置かずに倍率を上げたため、視野外に消え去りました！"); return;
    }

    currentStep = 3;
    document.getElementById('btn-to-10x').classList.add('hidden');
    
    currentLensType = 1;
    const lensEl = document.getElementById('active-lens');
    lensEl.className = 'lens-type-1';
    lensEl.innerText = '10x';

    distance += (Math.random() < 0.5 ? 1 : -1); 
    
    checkMagAvailability();
    updateStagePosition();
    updateVisuals();
  }

  function switchTo40x() {
    if (Math.abs(slideX) > 10 || Math.abs(slideY) > 10) {
      gameOver("❌【見失った！】対象を『ど真ん中』に置かずに400倍にしたため、視野外に消え去りました！"); return;
    }

    currentStep = 4;
    document.getElementById('btn-to-40x').classList.add('hidden');
    
    currentLensType = 2;
    const lensEl = document.getElementById('active-lens');
    lensEl.className = 'lens-type-2';
    lensEl.innerText = '40x';

    distance += (Math.random() < 0.5 ? 1 : -1); 
    msgBoard("⚠️【STEP 4】最高倍率400倍！さらに暗くなります！「微動ねじ」だけでピントを合わせよ！粗動ねじは一発免停！");
    document.getElementById('status-board').className = 'alert';
    
    checkMagAvailability();
    updateStagePosition();
    updateVisuals();
  }

  function moveFocus(val, type) {
    if (isGameOver) return;
    const isEyeView = document.getElementById('eye-view').style.display === 'flex';

    if (isEyeView && val < 0 && type === 1) { 
        gameOver("❌【一発免停】覗きながら粗動ねじで近づけましたね？衝突事故です！"); return; 
    }
    
    if (currentStep >= 3 && type === 1) { 
        gameOver("❌【一発免停】高倍率で粗動ねじを回しましたね！？大きく動きすぎてピントを完全に見失う、またはレンズが激突する危険な操作です！"); return; 
    }

    distance += val;
    
    const currentLimits = DEVICE_SETTINGS[currentDevice].crashLimits;
    if (distance <= currentLimits[currentLensType]) { 
        distance = currentLimits[currentLensType]; 
        updateStagePosition(); updateVisuals(); 
        gameOver("❌【一発免停】ガシャン！レンズがプレパラートに衝突し破損しました！"); return; 
    }

    if (distance > 50) {
       distance = 50;
       msgBoard("⚠️【警告】遠ざけすぎです。これ以上回してもピントは合いません。近づけ直してください。");
       document.getElementById('status-board').className = 'alert';
       updateStagePosition();
       updateVisuals();
       return;
    }
    
    if (currentStep === 1 && distance <= 12) { 
      msgBoard("⭕【OK】極限まで接近しました！「接眼レンズを覗く」に切り替えてください。");
      document.getElementById('status-board').className = 'success';
      document.getElementById('btn-eye').disabled = false;
    } else if (currentStep === 1 && distance > 12) {
      msgBoard("⚠️【STEP 1】横から見て、粗動ねじでレンズを極限まで近づけよ！");
      document.getElementById('status-board').className = '';
      document.getElementById('btn-eye').disabled = true; 
    }

    if (currentStep === 4) {
      if (Math.abs(distance - targetDistance) === 0) {
         if (Math.abs(slideX) <= 10 && Math.abs(slideY) <= 10) {
             msgBoard("⭕【完璧】400倍のピントが合いました！絞りを開いて明るさを調整し、試験終了を押してください。");
             document.getElementById('status-board').className = 'success';
         } else {
             msgBoard("⚠️【注意】ピントは合っていますが、対象が視野から外れています！矢印で慎重に探せ！");
             document.getElementById('status-board').className = 'warning';
         }
      } else {
         msgBoard("⚠️【STEP 4】400倍は激シビアです！微動ねじだけで慎重にピントを探れ！");
         document.getElementById('status-board').className = 'alert';
      }
    }

    checkMagAvailability();
    updateStagePosition();
    updateVisuals();
  }

  function switchView(mode) {
    if (isGameOver) return;
    if (mode === 'eye') {
      if (currentStep === 1 && distance > 12) { alert("まだ遠すぎます！横から見て極限まで近づけて！"); return; }
      
      if (currentStep < 2) currentStep = 2; 
      
      document.getElementById('side-view').style.display = 'none';
      document.getElementById('eye-view').style.display = 'flex';
      document.getElementById('btn-eye').disabled = true;
      document.getElementById('btn-side').disabled = false;
      document.getElementById('btn-side').classList.remove('action');
      
      checkMagAvailability();
    } else {
      document.getElementById('side-view').style.display = 'flex';
      document.getElementById('eye-view').style.display = 'none';
      document.getElementById('btn-eye').disabled = false;
      document.getElementById('btn-side').disabled = true;
    }
  }

  function updateVisuals() {
    let scale = 1;
    if (currentStep === 3) scale = 2.5; 
    if (currentStep >= 4) scale = 8; 
    
    const slideGroup = document.getElementById('slide-group');
    if (slideGroup) {
      slideGroup.style.transform = `scale(${scale}) translate(${slideX}px, ${slideY}px)`;
      
      let blurMultiplier = 3;
      if (currentStep === 3) blurMultiplier = 6;
      if (currentStep >= 4) blurMultiplier = 15;
      
      let rawBlur = Math.abs(distance - targetDistance) * blurMultiplier;
      let blur = Math.min(rawBlur, 15); 
      
      const irisVal = parseInt(document.getElementById('iris').value, 10);
      let baseBrightness = 1.0;
      let irisFactor = 0;

      if (currentStep < 3) {
        baseBrightness = 0.8;
        irisFactor = (irisVal / 100) * 0.4;
      } else if (currentStep === 3) {
        baseBrightness = 0.4;
        irisFactor = (irisVal / 100) * 0.6;
      } else {
        baseBrightness = 0.1;
        irisFactor = (irisVal / 100) * 0.8;
      }
      
      const brightness = baseBrightness + irisFactor;
      slideGroup.style.filter = `blur(${blur}px) brightness(${brightness})`;
    }
  }

  function createConfetti() {
    const container = document.getElementById('confetti-container');
    const emojis = ['🎉', '✨', '🎊', '🏆', '⭐'];
    for (let i = 0; i < 35; i++) {
      let confetti = document.createElement('div');
      confetti.className = 'confetti';
      confetti.innerText = emojis[Math.floor(Math.random() * emojis.length)];
      confetti.style.left = Math.random() * 100 + '%';
      confetti.style.animationDuration = (Math.random() * 2 + 2) + 's';
      confetti.style.animationDelay = Math.random() * 2 + 's';
      container.appendChild(confetti);
    }
  }

  function gameOver(txt) {
    isGameOver = true;
    if(timerId) clearInterval(timerId);
    document.getElementById('status-board').innerText = txt;
    document.getElementById('status-board').className = 'alert';
    
    document.querySelectorAll('.op-btn').forEach(b => b.disabled = true);
    document.getElementById('iris').disabled = true;
    
    const judgeBtn = document.getElementById('btn-judge');
    if(judgeBtn) {
      judgeBtn.innerText = "トップへ戻る";
      judgeBtn.disabled = false;
      judgeBtn.className = "op-btn danger";
      judgeBtn.setAttribute("onclick", "location.reload()");
    }
    document.getElementById('view-area').style.borderColor = "#d32f2f";
  }

  function judge() {
    if (isGameOver) return;
    
    let requiredStep = currentMode === 1 ? 3 : 4;
    let targetMag = currentMode === 1 ? "100倍" : "400倍";
    
    if (currentStep < requiredStep) { alert(`❌ 不合格！まだ目標倍率「${targetMag}」での詳細な観察が終わっていません！`); return; }

    const iris = parseInt(document.getElementById('iris').value, 10);
    const focusOK = Math.abs(distance - targetDistance) === 0;
    
    const posOK = currentMode === 1 ? (Math.abs(slideX) <= 50 && Math.abs(slideY) <= 50) : (Math.abs(slideX) <= 10 && Math.abs(slideY) <= 10);
    const irisOK = currentMode === 1 ? (iris >= 40 && iris <= 90) : (iris >= 80); 
    
    if (focusOK && irisOK && posOK) {
      if(timerId) clearInterval(timerId);
      
      document.getElementById('simulator-screen').classList.add('hidden');
      document.getElementById('license-screen').classList.remove('hidden');
      
      createConfetti(); 
      
      const licenseCard = document.getElementById('license-card');
      const licenseRank = document.getElementById('license-rank');
      const licenseTime = document.getElementById('license-time');
      const crownIcon = document.getElementById('crown-icon');
      
      if(currentMode === 1) {
        licenseRank.innerText = "ブロンズ認定";
        licenseCard.className = "license-card card-bronze";
        licenseRank.style.color = "#a0a0a0";
        crownIcon.innerText = "🥉";
        document.getElementById('license-time-wrap').style.display = "none";
      } else if (currentMode === 2) {
        let timeUsed = 180 - timeLeft;
        licenseTime.innerText = timeUsed;
        document.getElementById('license-time-wrap').style.display = "block";
        
        if(timeUsed <= 60) {
          licenseRank.innerText = "特級 ゴールド認定";
          licenseCard.className = "license-card card-gold";
          licenseRank.style.color = "#fbc02d";
          crownIcon.innerText = "👑";
        } else {
          licenseRank.innerText = "シルバー認定";
          licenseCard.className = "license-card card-silver";
          licenseRank.style.color = "#9e9e9e";
          crownIcon.innerText = "🥈";
        }
      }
    } else {
      let advice = "❌ 不合格です。";
      if (!focusOK) advice += ` ${targetMag}でのピントが甘いです。`;
      if (!posOK) advice += " 対象が視野の中央にありません。";
      if (!irisOK) advice += ` ${targetMag}にしては明るさが不適切です。絞りを調整してください！`;
      alert(advice + " もう一度調整してください！");
    }
  }

</script>
</body>
</html>
