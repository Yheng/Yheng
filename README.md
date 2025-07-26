# 

<div align="center">

# 🚀 Ariel Retes

**`Developer.class { skill_level: "Expert", caffeine_dependency: true }`**

<img src="https://readme-typing-svg.herokuapp.com?font=JetBrains+Mono&size=18&duration=3000&pause=800&color=F97316&center=true&vCenter=true&width=600&lines=console.log(%22Hello%2C+World!%22);while(coffee+%3E+0)+%7B+code();+%7D;git+commit+-m+%22Fixed+it+(hopefully)%22;npm+install+happiness" alt="Code Animation" />

</div>

### 🕹️ **Retro Gaming Break**
*"When debugging gets tough, the tough play Space Invaders"*

<div align="center">
  <details>
    <summary>🚀 <strong>Play Space Invaders</strong> 🚀</summary>
    <br>
    <div id="game-container">
      <canvas id="spaceInvaders" width="600" height="400" style="border: 2px solid #bd93f9; background: linear-gradient(180deg, #1a1a2e 0%, #16213e 50%, #0f3460 100%); border-radius: 8px;"></canvas>
      <br>
      <p style="margin: 10px 0; color: #ff79c6; font-family: monospace;">
        <strong>Controls:</strong> ← → Arrow Keys to Move | Spacebar to Shoot | ESC to Pause
      </p>
      <p style="margin: 5px 0; color: #50fa7b; font-family: monospace; font-size: 14px;">
        Score: <span id="score">0</span> | Lives: <span id="lives">3</span> | Level: <span id="level">1</span>
      </p>
    </div>
    
    <script>
      const canvas = document.getElementById('spaceInvaders');
      const ctx = canvas.getContext('2d');
      
      // Game state
      let gameState = {
        score: 0,
        lives: 3,
        level: 1,
        gameRunning: true,
        paused: false
      };
      
      // Player
      const player = {
        x: canvas.width / 2 - 25,
        y: canvas.height - 60,
        width: 50,
        height: 30,
        speed: 5,
        color: '#50fa7b'
      };
      
      // Arrays for game objects
      let bullets = [];
      let invaders = [];
      let invaderBullets = [];
      let particles = [];
      
      // Create invaders
      function createInvaders() {
        invaders = [];
        for (let row = 0; row < 5; row++) {
          for (let col = 0; col < 10; col++) {
            invaders.push({
              x: col * 55 + 50,
              y: row * 40 + 50,
              width: 35,
              height: 25,
              alive: true,
              color: row < 2 ? '#ff79c6' : row < 4 ? '#8be9fd' : '#ffb86c'
            });
          }
        }
      }
      
      // Create particle effect
      function createParticles(x, y, color) {
        for (let i = 0; i < 8; i++) {
          particles.push({
            x: x,
            y: y,
            vx: (Math.random() - 0.5) * 6,
            vy: (Math.random() - 0.5) * 6,
            life: 30,
            color: color
          });
        }
      }
      
      // Draw functions
      function drawPlayer() {
        ctx.fillStyle = player.color;
        ctx.fillRect(player.x, player.y, player.width, player.height);
        
        // Add glow effect
        ctx.shadowColor = player.color;
        ctx.shadowBlur = 10;
        ctx.fillRect(player.x + 5, player.y - 5, player.width - 10, 5);
        ctx.shadowBlur = 0;
      }
      
      function drawInvaders() {
        invaders.forEach(invader => {
          if (invader.alive) {
            ctx.fillStyle = invader.color;
            ctx.fillRect(invader.x, invader.y, invader.width, invader.height);
            
            // Add glow effect
            ctx.shadowColor = invader.color;
            ctx.shadowBlur = 5;
            ctx.fillRect(invader.x + 5, invader.y + 5, invader.width - 10, invader.height - 10);
            ctx.shadowBlur = 0;
          }
        });
      }
      
      function drawBullets() {
        ctx.fillStyle = '#f1fa8c';
        bullets.forEach(bullet => {
          ctx.fillRect(bullet.x, bullet.y, bullet.width, bullet.height);
        });
        
        ctx.fillStyle = '#ff5555';
        invaderBullets.forEach(bullet => {
          ctx.fillRect(bullet.x, bullet.y, bullet.width, bullet.height);
        });
      }
      
      function drawParticles() {
        particles.forEach(particle => {
          ctx.globalAlpha = particle.life / 30;
          ctx.fillStyle = particle.color;
          ctx.fillRect(particle.x, particle.y, 3, 3);
          ctx.globalAlpha = 1;
        });
      }
      
      // Update functions
      function updateBullets() {
        bullets = bullets.filter(bullet => {
          bullet.y -= bullet.speed;
          return bullet.y > 0;
        });
        
        invaderBullets = invaderBullets.filter(bullet => {
          bullet.y += bullet.speed;
          return bullet.y < canvas.height;
        });
      }
      
      function updateParticles() {
        particles = particles.filter(particle => {
          particle.x += particle.vx;
          particle.y += particle.vy;
          particle.life--;
          return particle.life > 0;
        });
      }
      
      function updateInvaders() {
        let moveDown = false;
        let leftMost = canvas.width;
        let rightMost = 0;
        
        invaders.forEach(invader => {
          if (invader.alive) {
            leftMost = Math.min(leftMost, invader.x);
            rightMost = Math.max(rightMost, invader.x + invader.width);
          }
        });
        
        if (leftMost <= 0 || rightMost >= canvas.width) {
          moveDown = true;
        }
        
        invaders.forEach(invader => {
          if (invader.alive) {
            if (moveDown) {
              invader.y += 20;
            } else {
              invader.x += leftMost <= 0 ? 1 : -1;
            }
          }
        });
      }
      
      // Collision detection
      function checkCollisions() {
        // Bullet vs invaders
        bullets.forEach((bullet, bulletIndex) => {
          invaders.forEach((invader, invaderIndex) => {
            if (invader.alive && 
                bullet.x < invader.x + invader.width &&
                bullet.x + bullet.width > invader.x &&
                bullet.y < invader.y + invader.height &&
                bullet.y + bullet.height > invader.y) {
              
              invader.alive = false;
              bullets.splice(bulletIndex, 1);
              gameState.score += 10;
              createParticles(invader.x + invader.width/2, invader.y + invader.height/2, invader.color);
            }
          });
        });
        
        // Invader bullets vs player
        invaderBullets.forEach((bullet, bulletIndex) => {
          if (bullet.x < player.x + player.width &&
              bullet.x + bullet.width > player.x &&
              bullet.y < player.y + player.height &&
              bullet.y + bullet.height > player.y) {
            
            invaderBullets.splice(bulletIndex, 1);
            gameState.lives--;
            createParticles(player.x + player.width/2, player.y + player.height/2, player.color);
            
            if (gameState.lives <= 0) {
              gameState.gameRunning = false;
            }
          }
        });
      }
      
      // Input handling
      const keys = {};
      
      document.addEventListener('keydown', (e) => {
        keys[e.key] = true;
        
        if (e.key === ' ') {
          e.preventDefault();
          if (bullets.length < 3) {
            bullets.push({
              x: player.x + player.width/2 - 2,
              y: player.y,
              width: 4,
              height: 10,
              speed: 7
            });
          }
        }
        
        if (e.key === 'Escape') {
          gameState.paused = !gameState.paused;
        }
      });
      
      document.addEventListener('keyup', (e) => {
        keys[e.key] = false;
      });
      
      // Game loop
      function gameLoop() {
        if (!gameState.gameRunning || gameState.paused) {
          ctx.fillStyle = 'rgba(0, 0, 0, 0.7)';
          ctx.fillRect(0, 0, canvas.width, canvas.height);
          ctx.fillStyle = '#ff79c6';
          ctx.font = '30px monospace';
          ctx.textAlign = 'center';
          ctx.fillText(gameState.gameRunning ? 'PAUSED' : 'GAME OVER', canvas.width/2, canvas.height/2);
          ctx.font = '16px monospace';
          ctx.fillText('Press ESC to ' + (gameState.gameRunning ? 'resume' : 'restart'), canvas.width/2, canvas.height/2 + 40);
          
          if (!gameState.gameRunning && keys['Escape']) {
            // Restart game
            gameState = { score: 0, lives: 3, level: 1, gameRunning: true, paused: false };
            bullets = [];
            invaderBullets = [];
            particles = [];
            createInvaders();
          }
          
          requestAnimationFrame(gameLoop);
          return;
        }
        
        // Clear canvas
        ctx.fillStyle = 'rgba(26, 26, 46, 0.1)';
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        
        // Handle input
        if (keys['ArrowLeft'] && player.x > 0) {
          player.x -= player.speed;
        }
        if (keys['ArrowRight'] && player.x < canvas.width - player.width) {
          player.x += player.speed;
        }
        
        // Update game objects
        updateBullets();
        updateInvaders();
        updateParticles();
        checkCollisions();
        
        // Invaders shoot randomly
        if (Math.random() < 0.02) {
          const aliveInvaders = invaders.filter(inv => inv.alive);
          if (aliveInvaders.length > 0) {
            const shooter = aliveInvaders[Math.floor(Math.random() * aliveInvaders.length)];
            invaderBullets.push({
              x: shooter.x + shooter.width/2 - 2,
              y: shooter.y + shooter.height,
              width: 4,
              height: 8,
              speed: 3
            });
          }
        }
        
        // Draw everything
        drawPlayer();
        drawInvaders();
        drawBullets();
        drawParticles();
        
        // Check win condition
        if (invaders.every(inv => !inv.alive)) {
          gameState.level++;
          createInvaders();
        }
        
        // Update UI
        document.getElementById('score').textContent = gameState.score;
        document.getElementById('lives').textContent = gameState.lives;
        document.getElementById('level').textContent = gameState.level;
        
        requestAnimationFrame(gameLoop);
      }
      
      // Initialize game
      createInvaders();
      gameLoop();
    </script>
  </details>
</div>

<br>

## 🤖 Hello, I'm Ariel

I'm a caffeine-powered developer who turns `undefined` into defined solutions. I believe the best debugging tool is still `console.log()`, and yes, I do count HTML as a programming language (fight me). 

```javascript
const ariel = {
    location: "localhost:3000",
    languages: ["JavaScript", "Python", "TypeScript", "Sarcasm"],
    hobbies: ["Coding", "Gaming", "Explaining why it works on my machine"],
    currentStatus: "Probably fixing someone else's code",
    motto: "There are only 10 types of people: those who understand binary and those who don't"
};
```

<div align="center">

### 🔧 **My Tech Stack** 
*"It's not a bug, it's a feature"*

</div>

<table align="center">
<tr>
<td align="center" width="33%">
<img src="https://media.giphy.com/media/ln7z2eWriiQAllfVcn/giphy.gif" width="50" height="50"/>
<br><strong>Frontend Wizardry</strong>
<br><sub>Making pixels dance since 2010</sub>
</td>
<td align="center" width="33%">
<img src="https://media.giphy.com/media/kdFc8fubgS31b8DsVu/giphy.gif" width="50" height="50"/>
<br><strong>Backend Sorcery</strong>
<br><sub>Where the real magic happens</sub>
</td>
<td align="center" width="33%">
<img src="https://media.giphy.com/media/du3J3cXyzhj75IOgvA/giphy.gif" width="50" height="50"/>
<br><strong>DevOps Alchemy</strong>
<br><sub>Docker containers go brrrr</sub>
</td>
</tr>
</table>

<br>

<div align="center">

### 🛠️ **Weapons of Choice**
*"No, I will not fix your printer"*

<img src="https://skillicons.dev/icons?i=js,ts,react,vue,nodejs,python,docker,aws,git,vscode" />

</div>

<div align="center">

### 📊 **GitHub Stats** 
*"git blame someone_else"*

<img height="180em" src="https://github-readme-stats.vercel.app/api?username=Yheng&show_icons=true&theme=dracula&hide_border=true"/>
<img height="180em" src="https://github-readme-stats.vercel.app/api/top-langs/?username=Yheng&layout=compact&theme=dracula&hide_border=true"/>

</div>

<div align="center">
  <img src="https://github-readme-streak-stats.herokuapp.com/?user=Yheng&theme=dracula&hide_border=true" />
</div>

### 🎮 **Achievement Unlocked**

<div align="center">
  <img src="https://github-profile-trophy.vercel.app/?username=Yheng&theme=dracula&no-frame=true&no-bg=false&margin-w=4" />
</div>

<br>

<div align="center">

### 🚀 **Current Quests**
*"It compiles, ship it!"*

</div>

<div align="center">
  <a href="https://github.com/Yheng/PillPulse">
    <img src="https://github-readme-stats.vercel.app/api/pin/?username=Yheng&repo=PillPulse&theme=dracula&hide_border=true" />
  </a>
</div>

<br>

<div align="center">

### 📈 **Commit History**
*"git commit -m 'fixed the thing'"*

<img src="https://github-readme-activity-graph.vercel.app/graph?username=Yheng&theme=dracula&bg_color=282a36&color=ff79c6&line=bd93f9&point=ffb86c&area_color=44475a&area=true&hide_border=true&custom_title=Code%20Contributions" />

</div>

<br>

---

<div align="center">

### 📡 **Contact Protocols**
*"Have you tried turning it off and on again?"*

<br>

<a href="mailto:yhengdesigns@gmail.com">
  <img src="https://img.shields.io/badge/Email-FF6B6B?style=for-the-badge&logo=gmail&logoColor=white" />
</a>
&nbsp;&nbsp;
<a href="https://www.linkedin.com/in/arielretes/">
  <img src="https://img.shields.io/badge/LinkedIn-4ECDC4?style=for-the-badge&logo=linkedin&logoColor=white" />
</a>
&nbsp;&nbsp;
<a href="https://buymeacoffee.com/arielretes">
  <img src="https://img.shields.io/badge/Buy%20Me%20A%20Coffee-FFDD44?style=for-the-badge&logo=buy-me-a-coffee&logoColor=black" />
</a>

<br><br>

### ☕ **Fuel My Code**
*"Powered by coffee and Stack Overflow"*

<a href="https://buymeacoffee.com/arielretes" target="_blank">
  <img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" height="50" width="180">
</a>

<br>

```bash
$ sudo apt install motivation
Reading package lists... Done
Building dependency tree... Done
The following packages will be installed:
  coffee caffeine energy focus
Do you want to continue? [Y/n] Y
```

</div>

<br>

<div align="center">
  <img src="https://komarev.com/ghpvc/?username=Yheng&color=ff79c6&style=flat-square&label=Profile+Views" />
  <img src="https://img.shields.io/github/followers/Yheng?label=Followers&style=flat-square&color=bd93f9" />
  <img src="https://img.shields.io/badge/Status-Probably%20Debugging-red?style=flat-square" />
</div>

<div align="center">

**`// END OF FILE - No bugs found (that we know of) 🐛`**

</div>
