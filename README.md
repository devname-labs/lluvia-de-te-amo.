# lluvia-de-te-amo.<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Lluvia de TE AMO - Grabación</title>
  <link href="https://fonts.googleapis.com/css2?family=Great+Vibes&display=swap" rel="stylesheet">
  <style>
    body {
      margin: 0;
      overflow: hidden;
      background-color: black;
      font-family: 'Great Vibes', cursive;
    }
    canvas {
      display: block;
    }
    #recordBtn {
      position: absolute;
      top: 20px;
      left: 20px;
      padding: 10px 20px;
      font-size: 16px;
      z-index: 10;
      background: #ff69b4;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <button id="recordBtn">🎥 Grabar 5 seg</button>
  <canvas id="canvas"></canvas>

  <script>
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');

    const phrase = "TE AMO ";
    const fontSize = 26;
    let columns, drops = [], speeds = [];

    function initializeDrops() {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
      columns = Math.floor(canvas.width / fontSize);
      drops = Array(columns).fill(1);
      speeds = Array(columns).fill().map(() => Math.random() * 2 + 1);
    }

    function draw() {
      ctx.fillStyle = "rgba(0, 0, 0, 0.1)";
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      ctx.fillStyle = "#ff69b4";
      ctx.font = fontSize + "px 'Great Vibes'";

      for (let i = 0; i < drops.length; i++) {
        ctx.globalAlpha = Math.random() * 0.5 + 0.5;
        ctx.fillText(phrase, i * fontSize, drops[i] * fontSize);
        if (drops[i] * fontSize > canvas.height && Math.random() > 0.975) {
          drops[i] = 0;
        }
        drops[i] += speeds[i];
      }

      ctx.globalAlpha = 1.0;
    }

    // Animación continua
    window.addEventListener('resize', initializeDrops);
    initializeDrops();
    setInterval(draw, 100);

    // Grabación
    const recordBtn = document.getElementById('recordBtn');
    recordBtn.addEventListener('click', () => {
      const stream = canvas.captureStream(30); // 30 fps
      const mediaRecorder = new MediaRecorder(stream);
      const chunks = [];

      mediaRecorder.ondataavailable = e => chunks.push(e.data);
      mediaRecorder.onstop = () => {
        const blob = new Blob(chunks, { type: 'video/webm' });
        const url = URL.createObjectURL(blob);

        const a = document.createElement('a');
        a.href = url;
        a.download = 'lluvia-te-amo.webm';
        a.click();
      };

      mediaRecorder.start();
      recordBtn.disabled = true;
      recordBtn.textContent = "Grabando...";

      setTimeout(() => {
        mediaRecorder.stop();
        recordBtn.disabled = false;
        recordBtn.textContent = "🎥 Grabar 5 seg";
      }, 5000); // Graba 5 segundos
    });
  </script>
</body>
</html>
