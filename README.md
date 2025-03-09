<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Reproductor de Video Profesional 2025</title>
  <style>
    /* Estilos generales */
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
      background-color: #000;
      color: #fff;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
    }

    /* Contenedor del reproductor */
    .video-container {
      width: 90%;
      max-width: 1200px;
      background-color: #111;
      border-radius: 10px;
      overflow: hidden;
      box-shadow: 0 0 20px rgba(0, 0, 0, 0.5);
    }

    /* Video */
    video {
      width: 100%;
      display: block;
    }

    /* Controles del reproductor */
    .controls {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 10px;
      background-color: #222;
    }

    .controls button {
      background: none;
      border: none;
      color: #fff;
      font-size: 16px;
      cursor: pointer;
      margin: 0 5px;
    }

    .controls button:hover {
      color: #1e90ff;
    }

    .controls input[type="range"] {
      flex-grow: 1;
      margin: 0 10px;
    }

    /* Barra de progreso */
    .progress-bar {
      width: 100%;
      height: 5px;
      background-color: #444;
      cursor: pointer;
      position: relative;
    }

    .progress {
      height: 100%;
      background-color: #1e90ff;
      width: 0%;
    }

    .thumbnail {
      position: absolute;
      bottom: 10px;
      left: 0;
      width: 160px;
      height: 90px;
      background-size: cover;
      display: none;
    }

    /* Selector de calidad */
    .quality-selector {
      margin-left: 10px;
    }

    /* Responsive */
    @media (max-width: 600px) {
      .controls button {
        font-size: 14px;
      }
    }
  </style>
</head>
<body>

  <!-- Reproductor de Video -->
  <div class="video-container">
    <!-- Video -->
    <video id="videoPlayer" controls>
      <!-- Usa un archivo HLS o DASH para streaming adaptativo -->
      <source src="https://test-streams.mux.dev/x36xhzz/x36xhzz.m3u8" type="application/x-mpegURL">
      Tu navegador no soporta la reproducción de video.
    </video>

    <!-- Controles personalizados -->
    <div class="controls">
      <button id="playPauseBtn">▶️</button>
      <button id="muteBtn">🔊</button>
      <input type="range" id="volumeSlider" min="0" max="1" step="0.1" value="1">
      <span id="currentTime">00:00</span> / <span id="duration">00:00</span>
      <select id="qualitySelector" class="quality-selector">
        <option value="auto">Auto</option>
      </select>
      <button id="speedBtn">1x</button>
      <button id="subtitleBtn">Subtítulos</button>
      <button id="theaterBtn">🎭</button>
      <button id="fullscreenBtn">📺</button>
    </div>

    <!-- Barra de progreso -->
    <div class="progress-bar" id="progressBar">
      <div class="progress" id="progress"></div>
      <div class="thumbnail" id="thumbnail"></div>
    </div>
  </div>

  <!-- Bibliotecas para HLS y DASH -->
  <script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
  <script src="https://cdn.dashjs.org/latest/dash.all.min.js"></script>
  <script>
    // Elementos del DOM
    const videoPlayer = document.getElementById('videoPlayer');
    const playPauseBtn = document.getElementById('playPauseBtn');
    const muteBtn = document.getElementById('muteBtn');
    const volumeSlider = document.getElementById('volumeSlider');
    const currentTime = document.getElementById('currentTime');
    const duration = document.getElementById('duration');
    const progressBar = document.getElementById('progressBar');
    const progress = document.getElementById('progress');
    const qualitySelector = document.getElementById('qualitySelector');
    const speedBtn = document.getElementById('speedBtn');
    const subtitleBtn = document.getElementById('subtitleBtn');
    const theaterBtn = document.getElementById('theaterBtn');
    const fullscreenBtn = document.getElementById('fullscreenBtn');
    const thumbnail = document.getElementById('thumbnail');

    let hls; // Para manejar HLS
    let dashPlayer; // Para manejar DASH

    // Función para cargar el video
    function loadVideo(src, type) {
      if (type === 'hls') {
        if (Hls.isSupported()) {
          hls = new Hls();
          hls.loadSource(src);
          hls.attachMedia(videoPlayer);

          // Actualizar el selector de calidad cuando se carguen los niveles
          hls.on(Hls.Events.MANIFEST_PARSED, () => {
            hls.levels.forEach((level, index) => {
              const option = document.createElement('option');
              option.value = index;
              option.text = `${level.height}p`;
              qualitySelector.appendChild(option);
            });
          });

          // Cambiar la calidad del video cuando el usuario seleccione una opción
          qualitySelector.addEventListener('change', () => {
            if (qualitySelector.value === 'auto') {
              hls.currentLevel = -1; // Auto
            } else {
              hls.currentLevel = parseInt(qualitySelector.value);
            }
          });
        } else if (videoPlayer.canPlayType('application/vnd.apple.mpegurl')) {
          // Soporte nativo para HLS en Safari
          videoPlayer.src = src;
        }
      } else if (type === 'dash') {
        dashPlayer = dashjs.MediaPlayer().create();
        dashPlayer.initialize(videoPlayer, src, true);
      }
    }

    // Cargar un video de prueba (HLS)
    loadVideo('https://cdn-xqrgj99p8krmzj4e.orbitcache.com:443/engine/hls2/01/12835/ja21txl55ayk_,n,.urlset/index-v1-a1.m3u8?t=hgEDRzfRKPy9c_YXaGMDTf1d8J1zIlqBwpSmn_uBQNw&s=1741417682&e=14400&f=64176789&node=5LpKIhsv95HV1Q3x7jrfRXldwp3y8CT5PdX8aM588gQ=&i=0.1&sp=2500&asn=28469&q=n', 'hls');

    // Reproducir/Pausar
    playPauseBtn.addEventListener('click', () => {
      if (videoPlayer.paused) {
        videoPlayer.play();
        playPauseBtn.textContent = '⏸️';
      } else {
        videoPlayer.pause();
        playPauseBtn.textContent = '▶️';
      }
    });

    // Silenciar/Desilenciar
    muteBtn.addEventListener('click', () => {
      videoPlayer.muted = !videoPlayer.muted;
      muteBtn.textContent = videoPlayer.muted ? '🔇' : '🔊';
    });

    // Control de volumen
    volumeSlider.addEventListener('input', () => {
      videoPlayer.volume = volumeSlider.value;
    });

    // Actualizar tiempo y duración
    videoPlayer.addEventListener('timeupdate', () => {
      // Tiempo actual
      const minutes = Math.floor(videoPlayer.currentTime / 60);
      const seconds = Math.floor(videoPlayer.currentTime % 60);
      currentTime.textContent = `${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;

      // Duración
      const totalMinutes = Math.floor(videoPlayer.duration / 60);
      const totalSeconds = Math.floor(videoPlayer.duration % 60);
      duration.textContent = `${String(totalMinutes).padStart(2, '0')}:${String(totalSeconds).padStart(2, '0')}`;

      // Barra de progreso
      const progressPercent = (videoPlayer.currentTime / videoPlayer.duration) * 100;
      progress.style.width = `${progressPercent}%`;
    });

    // Saltar a un punto del video
    progressBar.addEventListener('click', (e) => {
      const rect = progressBar.getBoundingClientRect();
      const clickPosition = (e.clientX - rect.left) / rect.width;
      videoPlayer.currentTime = clickPosition * videoPlayer.duration;
    });

    // Pantalla completa
    fullscreenBtn.addEventListener('click', () => {
      if (!document.fullscreenElement) {
        videoPlayer.requestFullscreen();
      } else {
        document.exitFullscreen();
      }
    });

    // Velocidad de reproducción
    const speeds = [0.5, 1, 1.5, 2];
    let currentSpeedIndex = 1;
    speedBtn.addEventListener('click', () => {
      currentSpeedIndex = (currentSpeedIndex + 1) % speeds.length;
      videoPlayer.playbackRate = speeds[currentSpeedIndex];
      speedBtn.textContent = `${speeds[currentSpeedIndex]}x`;
    });

    // Subtítulos
    subtitleBtn.addEventListener('click', () => {
      const track = videoPlayer.textTracks[0];
      track.mode = track.mode === 'showing' ? 'hidden' : 'showing';
    });

    // Modo teatro
    theaterBtn.addEventListener('click', () => {
      videoPlayer.classList.toggle('theater-mode');
    });

    // Miniaturas en la barra de progreso
    progressBar.addEventListener('mousemove', (e) => {
      const rect = progressBar.getBoundingClientRect();
      const clickPosition = (e.clientX - rect.left) / rect.width;
      const time = clickPosition * videoPlayer.duration;
      thumbnail.style.display = 'block';
      thumbnail.style.left = `${e.clientX - rect.left}px`;
      thumbnail.style.backgroundImage = `url(https://example.com/thumbnail?time=${time})`; // Cambia la URL por la de tu servicio de miniaturas
    });

    progressBar.addEventListener('mouseleave', () => {
      thumbnail.style.display = 'none';
    });

    // Atajos de teclado
    document.addEventListener('keydown', (e) => {
      switch (e.key) {
        case ' ':
          e.preventDefault();
          if (videoPlayer.paused) {
            videoPlayer.play();
            playPauseBtn.textContent = '⏸️';
          } else {
            videoPlayer.pause();
            playPauseBtn.textContent = '▶️';
          }
          break;
        case 'm':
          videoPlayer.muted = !videoPlayer.muted;
          muteBtn.textContent = videoPlayer.muted ? '🔇' : '🔊';
          break;
        case 'f':
          if (!document.fullscreenElement) {
            videoPlayer.requestFullscreen();
          } else {
            document.exitFullscreen();
          }
          break;
        case 't':
          videoPlayer.classList.toggle('theater-mode');
          break;
        case 'ArrowRight':
          videoPlayer.currentTime += 5;
          break;
        case 'ArrowLeft':
          videoPlayer.currentTime -= 5;
          break;
        case 'ArrowUp':
          videoPlayer.volume = Math.min(videoPlayer.volume + 0.1, 1);
          volumeSlider.value = videoPlayer.volume;
          break;
        case 'ArrowDown':
          videoPlayer.volume = Math.max(videoPlayer.volume - 0.1, 0);
          volumeSlider.value = videoPlayer.volume;
          break;
      }
    });
  </script>
</body>
</html>