# Super-Mangonio-Bros
Juego de mangonio parodia Super Mario Bros
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Super Mangonio Bros</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body { background: black; color: white; font-family: monospace; }
    canvas { display: block; margin: 0 auto; background: #5c94fc; image-rendering: pixelated; }
    #overlay, #startScreen, #pauseScreen {
      position: absolute;
      top: 0; left: 0;
      width: 100vw; height: 100vh;
      display: flex; align-items: center; justify-content: center;
      background: rgba(0,0,0,0.8);
      z-index: 2;
      color: white;
      font-size: 2rem;
      text-align: center;
    }
    #pauseScreen { display: none; }
  </style>
</head>
<body>
  
    <div id="startScreen">
      <div>
        <h1>Super Mangonio Bros</h1>
        <p>Presiona <strong>Q</strong> para comenzar</p>
        <h2>Controles:</h2>
        <ul style="text-align:left; margin-top:10px;">
          <li><strong>A</strong>: Mover a la izquierda</li>
          <li><strong>D</strong>: Mover a la derecha</li>
          <li><strong>ESPACIO</strong>: Saltar</li>
          <li><strong>Q</strong>: Iniciar juego</li>
          <li><strong>P</strong>: Pausar juego</li>
        </ul>
      </div>
    </div>
    
  <div id="pauseScreen">PAUSA - Presiona P para continuar</div>
  <canvas id="gameCanvas" width="640" height="360"></canvas>
  <audio id="bgm" loop>
    <source src="music.mp3" type="audio/mpeg">
  </audio>
  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");
    const startScreen = document.getElementById("startScreen");
    const pauseScreen = document.getElementById("pauseScreen");
    const music = document.getElementById("bgm");

    const keys = {};
    let started = false, paused = false;

    document.addEventListener("keydown", e => {
      keys[e.key.toLowerCase()] = true;

      if (e.key.toLowerCase() === "q" && !started) {
        startScreen.style.display = "none";
        music.play();
        started = true;
        loop();
      }
      if (e.key.toLowerCase() === "p" && started) {
        paused = !paused;
        pauseScreen.style.display = paused ? "flex" : "none";
        if (!paused) loop();
      }
    });

    document.addEventListener("keyup", e => {
      keys[e.key.toLowerCase()] = false;
    });

    const playerImg = new Image();
    playerImg.src = "mango8bit.png";
    const mushroomImg = new Image();
    mushroomImg.src = "mushroom.png";
    const fireFlowerImg = new Image();
    fireFlowerImg.src = "fireflower.png";

    const player = {
      x: 50, y: 260, width: 32, height: 32,
      vy: 0, gravity: 0.8, jumpForce: -12,
      grounded: false, speed: 3, big: false, fire: false
    };

    const mushrooms = [{ x: 300, y: 280 }];
    const flowers = [{ x: 500, y: 280 }];

    function update() {
      if (!started || paused) return;

      if (keys["a"]) player.x -= player.speed;
      if (keys["d"]) player.x += player.speed;
      if (keys[" "] && player.grounded) {
        player.vy = player.jumpForce;
        player.grounded = false;
      }

      player.vy += player.gravity;
      player.y += player.vy;

      if (player.y + player.height >= canvas.height) {
        player.y = canvas.height - player.height;
        player.vy = 0;
        player.grounded = true;
      }

      // Champiñón
      for (let i = 0; i < mushrooms.length; i++) {
        const m = mushrooms[i];
        if (Math.abs(player.x - m.x) < 30 && Math.abs(player.y - m.y) < 30) {
          player.big = true;
          player.width = 48;
          player.height = 48;
          mushrooms.splice(i, 1);
        }
      }

      // Flor de fuego
      for (let i = 0; i < flowers.length; i++) {
        const f = flowers[i];
        if (Math.abs(player.x - f.x) < 30 && Math.abs(player.y - f.y) < 30) {
          player.fire = true;
          flowers.splice(i, 1);
        }
      }
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      ctx.fillStyle = "#5c94fc";
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      // Suelo
      ctx.fillStyle = "#228B22";
      ctx.fillRect(0, canvas.height - 30, canvas.width, 30);

      // Champiñones
      for (let m of mushrooms) {
        ctx.drawImage(mushroomImg, m.x, m.y, 32, 32);
      }

      // Flores
      for (let f of flowers) {
        ctx.drawImage(fireFlowerImg, f.x, f.y, 32, 32);
      }

      // Jugador
      ctx.drawImage(playerImg, player.x, player.y, player.width, player.height);
    }

    function loop() {
      if (!paused) {
        update();
        draw();
        requestAnimationFrame(loop);
      }
    }
  </script>
</body>
</html>
