# <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flappy Bird</title>
    <style>
        body { margin: 0; padding: 0; display: flex; justify-content: center; align-items: center; height: 100vh; background-color: #70c5ce; }
        canvas { border: 2px solid #000; background-color: #70c5ce; }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="320" height="480"></canvas>
    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        let bird = { x: 50, y: 150, width: 20, height: 20, velocity: 0, gravity: 0.5, lift: -8 };
        let pipes = [];
        let score = 0;
        let isGameOver = false;

        const pipeWidth = 50, pipeGap = 150, pipeSpeed = 2;
        const birdSpeed = 2;

        // Game loop
        function gameLoop() {
            if (isGameOver) {
                ctx.fillStyle = 'black';
                ctx.font = '30px Arial';
                ctx.fillText('Game Over', 80, canvas.height / 2 - 20);
                ctx.fillText('Press any key to Restart', 40, canvas.height / 2 + 20);
                return;
            }

            // Clear canvas
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Bird mechanics
            bird.velocity += bird.gravity;
            bird.y += bird.velocity;

            // Check collisions with floor and ceiling
            if (bird.y + bird.height > canvas.height || bird.y < 0) {
                isGameOver = true;
            }

            // Pipe generation
            if (pipes.length === 0 || pipes[pipes.length - 1].x <= canvas.width - 200) {
                const height = Math.floor(Math.random() * (canvas.height - pipeGap));
                pipes.push({ x: canvas.width, y: height });
            }

            // Update and render pipes
            pipes.forEach((pipe, index) => {
                pipe.x -= pipeSpeed;
                if (pipe.x + pipeWidth < 0) pipes.splice(index, 1);

                // Draw pipes
                ctx.fillStyle = '#228B22';
                ctx.fillRect(pipe.x, 0, pipeWidth, pipe.y);
                ctx.fillRect(pipe.x, pipe.y + pipeGap, pipeWidth, canvas.height - pipe.y - pipeGap);

                // Collision detection with pipes
                if (bird.x + bird.width > pipe.x && bird.x < pipe.x + pipeWidth &&
                    (bird.y < pipe.y || bird.y + bird.height > pipe.y + pipeGap)) {
                    isGameOver = true;
                }
            });

            // Draw bird
            ctx.fillStyle = 'yellow';
            ctx.fillRect(bird.x, bird.y, bird.width, bird.height);

            // Draw score
            ctx.fillStyle = '#000';
            ctx.font = '20px Arial';
            ctx.fillText('Score: ' + score, 10, 30);

            // Score increment
            if (pipes[0] && pipes[0].x + pipeWidth < bird.x) {
                pipes.shift();
                score++;
            }

            // Request next frame
            requestAnimationFrame(gameLoop);
        }

        // Control bird with keyboard (space or any key)
        document.addEventListener('keydown', () => {
            if (isGameOver) {
                // Reset game if game over
                bird.y = 150;
                bird.velocity = 0;
                pipes = [];
                score = 0;
                isGameOver = false;
                gameLoop();
            } else {
                bird.velocity = bird.lift;
            }
        });

        // Start the game loop
        gameLoop();
    </script>
</body>
</html>
