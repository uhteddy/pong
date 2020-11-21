<template>
    <div class="container mx-auto px-4">
        <div class="centerContain">
            <canvas id="pongGame"></canvas>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            rectX: 0,
            FPS: 50,
            contextHeight: 0,
            contextWeight: 0,
            paddles: {
                user: {
                    x: 0,
                    y: 10,
                    width: 10,
                    height: 200,
                    score: 0
                },

                computer: {
                    x: 0,
                    y: 0,
                    width: 10,
                    height: 100,
                    score: 0,
                    level: 0.005
                }
            },

            net: {
                x: 0,
                y: 0,
                width: 2,
                height: 10
            },

            ball: {
                x: 0,
                y: 0,
                radius: 5,
                speed: 0.5,
                velocityX: 0.2,
                velocityY: 0.5
            }
        }
    },

    mounted() {
        var c = document.getElementById("pongGame");
        var ctx = c.getContext("2d");    
        this.context = ctx;
        this.canvas = c;

        ctx.moveTo(20.5, 0);
        ctx.lineTo(20.5, 50);
        ctx.scale(1,1)
        this.contextWidth = 600;
        this.contextHeight = 400;

        const heightFactor = c.height / 3
        // User
        this.paddles.user.height = heightFactor
        this.paddles.user.y = c.height/2 - this.paddles.user.height / 2
        this.net.x = c.width/2;

        //Computer
        this.paddles.computer.x = c.width - this.paddles.computer.width;
        this.paddles.computer.height = heightFactor
        this.paddles.computer.y = c.height/2 - this.paddles.computer.height / 2

        // Ball
        this.ball.x = c.width/2;
        this.ball.y = c.height/2;

        c.addEventListener('mousemove', this.movePaddle);

        setInterval(this.game, this.FPS/1000);
    },

    methods: {
        // General Rendering
        render() {
            // Background
            this.drawRect(0, 0, 600, 400, "black");

            //Score
            this.drawText(this.paddles.user.score, this.canvas.width/4, this.canvas.height/5, "white")
            this.drawText(this.paddles.computer.score, 3 * this.canvas.width/4, this.canvas.height/5, "white")
            this.drawNet();

            // Paddles
            this.drawRect(this.paddles.user.x, this.paddles.user.y, this.paddles.user.width, this.paddles.user.height, "yellow")
            this.drawRect(this.paddles.computer.x, this.paddles.computer.y, this.paddles.computer.width, this.paddles.computer.height, "white");

            // Ball
            this.drawCircle(this.ball.x, this.ball.y, this.ball.radius, 'red')
        },

        update() {
            this.ball.x += this.ball.velocityX;
            this.ball.y += this.ball.velocityY;

            // Collisions on X Axis
            if (this.ball.y + this.ball.radius > this.canvas.height || this.ball.y + this.ball.radius < this.ball.radius*2) {
                this.ball.velocityY = - this.ball.velocityY;
            };

            let player = (this.ball.x < this.canvas.width / 2) ? this.paddles.user : this.paddles.computer
            
            // Collisions Player
            if (this.collison(this.ball, player)) {
                let collidePoint = this.ball.y - (player.y + player.height / 2);
                collidePoint = collidePoint / (player.height / 2);
                let angleRad = collidePoint * (Math.PI/4)

                let direction = (this.ball.x < this.canvas.width / 2)? 1 : -1;

                this.ball.velocityX = direction * this.ball.speed * Math.cos(angleRad);
                this.ball.velocityY = direction * this.ball.speed * Math.sin(angleRad);

                this.ball.speed += 0.05;
            };

            // Collisions on Y Axis
            if (this.ball.x - this.ball.radius < 0) {
                this.paddles.computer.score++;
                this.resetBall();
            } else if (this.ball.x + this.ball.radius > this.canvas.width) {
                this.paddles.user.score++;
                this.paddles.computer.level = this.paddles.computer.level + 0.001;
                this.resetBall();     
            }

            this.paddles.computer.y += (this.ball.y - (this.paddles.computer.y + this.paddles.computer.height/3)) * this.paddles.computer.level
        },

        resetBall() {
            this.ball.x = this.canvas.width/2;
            this.ball.y = this.canvas.height/2; 
            this.ball.speed = 0.5;
            this.ball.velocityX = -this.ball.velocityX 
        },

        game() {
            this.update(); // Updates data about collisions, players, balls, etc
            this.render(); // Renders those changes.
        },

        collison(ball, player) {
            player.top = player.y;
            player.bottom = player.y + player.height;
            player.left = player.x;
            player.right = player.x + player.width;

            ball.top = ball.y - ball.radius;
            ball.bottom = ball.y + ball.radius;
            ball.left = ball.x - ball.radius;
            ball.right = ball.x + ball.radius;

            return ball.right > player.left && ball.top < player.bottom && ball.left < player.right && ball.bottom > player.top; 
        },

        drawNet() {
            for (let i = 0; i <= this.contextHeight; i+=15) {
                this.drawRect(this.net.x, this.net.y + i, this.net.width, this.net.height, 'white')
            }
        },

        // Utilities
        drawRect(x, y, w, h, color) {
            this.context.fillStyle = color;
            this.context.fillRect(x, y, w, h);
        },
        
        movePaddle(evt) {
            let rect = this.canvas.getBoundingClientRect();
            this.paddles.user.y = evt.clientY - rect.top - this.paddles.user.height / 3 
        },

        drawCircle(x, y, r, color) {
            this.context.fillStyle = color;
            this.context.beginPath();
            this.context.arc(x, y, r, 0, Math.PI*2, false);
            this.context.closePath();
            this.context.fill();     
        },

        drawText(text, x, y, color) {
            this.context.fillStyle = color;
            this.context.font = "30px fantasy"
            this.context.fillText(text, x, y)
        },
    }
}
</script>

<style>
    #pongGame {
        width: 1200px;
        height: 650px
    }

    .centerContain {
        margin: 0 auto;
        min-height: 100vh;
        display: flex;
        justify-content: center;
        align-items: center;
        text-align: center;
    }

    body {
        background-color: #212121;
    }
</style>