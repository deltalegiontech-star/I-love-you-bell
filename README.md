<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>I love you Mih - Matrix Rosa</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&display=swap');

    html, body {
      margin: 0; padding: 0;
      width: 100%; height: 100%;
      background: black;
      overflow: hidden;
      font-family: 'Share Tech Mono', monospace;
      cursor: crosshair;
      user-select: none;
      position: relative;
      color: #ff69b4;
    }
    canvas {
      position: fixed;
      top: 0; left: 0;
      width: 100vw; height: 100vh;
      pointer-events: none;
      z-index: 1;
      display: block;
      background: transparent;
    }
    #love-text {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      font-size: clamp(2.5rem, 8vw, 6rem);
      color: #ff69b4;
      text-align: center;
      white-space: nowrap;
      user-select: none;
      z-index: 10;
      pointer-events: none;
      text-shadow:
        0 0 10px #ff69b4,
        0 0 20px #ff1493,
        0 0 30px #ff69b4,
        0 0 40px #ff1493,
        0 0 50px #ff69b4,
        0 0 60px #ff1493,
        0 0 70px #ff69b4,
        0 0 80px #ff1493;
      animation: pulseGlow 3s ease-in-out infinite;
      transition: transform 0.3s ease, text-shadow 0.3s ease; /* Adicionando transições suaves */
    }
    @keyframes pulseGlow {
      0%, 100% {
        transform: translate(-50%, -50%) scale(1);
      }
      50% {
        transform: translate(-50%, -50%) scale(1.1);
        text-shadow:
          0 0 25px #ff69b4,
          0 0 40px #ff1493,
          0 0 55px #ff69b4,
          0 0 70px #ff1493,
          0 0 85px #ff69b4,
          0 0 100px #ff1493;
      }
    }
  </style>
</head>
<body>
  <canvas id="matrix"></canvas>
  <canvas id="explosion"></canvas>
  <div id="love-text">I love you Mih</div>

<script>
  const matrixCanvas = document.getElementById('matrix');
  const matrixCtx = matrixCanvas.getContext('2d');
  const explosionCanvas = document.getElementById('explosion');
  const explosionCtx = explosionCanvas.getContext('2d');

  let width, height;
  function resize() {
    width = window.innerWidth;
    height = window.innerHeight;
    matrixCanvas.width = width;
    matrixCanvas.height = height;
    explosionCanvas.width = width;
    explosionCanvas.height = height;
  }
  window.addEventListener('resize', resize);
  resize();

  // MATRIX CONFIG
  const fontSize = 20;
  const columns = Math.floor(width / fontSize);
  const drops = new Array(columns).fill(0).map(() => Math.random() * height / fontSize);
  const phrase = 'eu te amo';
  const letters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789❤'.split('');

  function drawMatrix() {
    matrixCtx.fillStyle = 'rgba(0, 0, 0, 0.1)';
    matrixCtx.fillRect(0, 0, width, height);
    matrixCtx.font = fontSize + 'px Share Tech Mono';

    for (let i = 0; i < columns; i++) {
      let char;
      let highlight = false;

      if (Math.random() < 0.08) {
        const pos = Math.floor(drops[i]) % phrase.length;
        char = phrase[pos];
        highlight = true;
      } else {
        char = letters[Math.floor(Math.random() * letters.length)];
      }

      const x = i * fontSize;
      const y = drops[i] * fontSize;

      if (highlight) {
        matrixCtx.fillStyle = '#ff69b4';
        matrixCtx.shadowColor = '#ff1493';
        matrixCtx.shadowBlur = 12;
        matrixCtx.font = (fontSize + 6) + 'px Share Tech Mono';
      } else {
        matrixCtx.fillStyle = '#ff69b4';
        matrixCtx.shadowBlur = 0;
        matrixCtx.font = fontSize + 'px Share Tech Mono';
      }

      matrixCtx.fillText(char, x, y);

      drops[i] += 0.75;
      if (drops[i] * fontSize > height && Math.random() > 0.975) {
        drops[i] = 0;
      }
    }
  }

  // EXPLOSÕES
  class Particle {
    constructor(x, y, text, isHeart=false) {
      this.x = x;
      this.y = y;
      this.text = text;
      this.isHeart = isHeart;
      this.fontSize = isHeart ? (Math.random() * 12 + 8) : (Math.random() * 22 + 18);
      this.life = 140;
      this.opacity = 1;
      this.smokeOpacity = 0.3;
      this.smokeRadius = this.fontSize * 1.5;

      const angle = Math.random() * 2 * Math.PI;
      const speedBase = isHeart ? (Math.random() * 1.5 + 0.5) : (Math.random() * 3 + 1.5);
      this.vx = Math.cos(angle) * speedBase;
      this.vy = Math.sin(angle) * speedBase;
      this.ax = this.vx * 0.03;
      this.ay = this.vy * 0.03 + 0.01;
    }
    update() {
      this.vx += this.ax;
      this.vy += this.ay;
      this.x += this.vx;
      this.y += this.vy;
      this.life--;
      this.opacity = this.life / 140;
      this.smokeOpacity -= 0.006;
      if(this.smokeOpacity < 0) this.smokeOpacity = 0;
    }
    draw(ctx) {
      ctx.save();
      ctx.globalAlpha = this.opacity * (this.isHeart ? 0.3 : 1);
      ctx.fillStyle = '#ff69b4';
      ctx.font = `${this.fontSize}px Share Tech Mono`;
      ctx.textAlign = 'center';
      ctx.textBaseline = 'middle';
      ctx.shadowColor = '#ff69b4';
      ctx.shadowBlur = this.isHeart ? 8 : 18;
      ctx.fillText(this.text, this.x, this.y);
      ctx.restore();

      if (!this.isHeart && this.smokeOpacity > 0) {
        ctx.save();
        ctx.beginPath();
        ctx.strokeStyle = `rgba(255,105,180,${this.smokeOpacity})`;
        ctx.lineWidth = 3;
        ctx.shadowColor = `rgba(255,105,180,${this.smokeOpacity})`;
        ctx.shadowBlur = 25;
        ctx.arc(this.x, this.y, this.smokeRadius, 0, 2 * Math.PI);
        ctx.stroke();
        ctx.restore();
      }
    }
  }

  let particles = [];

  function createExplosion(x, y) {
    const phrase = 'eu te amo';
    const spacing = 28;
    const startX = x - (phrase.length / 2) * spacing;

    for(let i = 0; i < phrase.length; i++) {
      const px = startX + i * spacing + (Math.random() * 10 - 5);
      const py = y + (Math.random() * 10 - 5);
      particles.push(new Particle(px, py, phrase[i], false));
    }

    for(let i = 0; i < 15; i++) {
      const px = x + (Math.random() - 0.5) * 140;
      const py = y + (Math.random() - 0.5) * 140;
      particles.push(new Particle(px, py, '❤', true));
    }
  }

  function animate() {
    drawMatrix();

    explosionCtx.clearRect(0, 0, width, height);
    for(let i = particles.length - 1; i >= 0; i--) {
      const p = particles[i];
      p.update();
      p.draw(explosionCtx);
      if(p.life <= 0) particles.splice(i, 1);
    }

    requestAnimationFrame(animate);
  }

  animate();

  window.addEventListener('click', e => {
    createExplosion(e.clientX, e.clientY);
  });
</script>
</body>
</html>
