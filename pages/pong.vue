<template>
    <div class="container mx-auto px-4">
        <div class="scores">
            <h4><strong>Opponents: </strong>{{ paddles.computer.score }}</h4>
            <h4><strong>You: </strong>{{ paddles.user.score }}</h4>
            <div class="pong">

            </div>
        </div>

        <canvas id="pongGame"></canvas>
    </div>
</template>

<script>
export default {
    data() {
        return {
            rectX: 0,
            renderSpeed: 1000,
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
                    score: 0
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
                radius: 10
            }
        }
    },

    mounted() {
        var c = document.getElementById("pongGame");
        var ctx = c.getContext("2d");    
        this.context = ctx;
        this.canvas = c;

        this.contextWidth = c.width;
        this.contextHeight = c.height;

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

        setInterval(this.render, this.renderSpeed);
    },

    methods: {
        // General Rendering
        render() {
            this.drawRect(0, 0, 600, 400, "black"); // Creates Background
            this.drawRect(this.rectX, 100, 100, 100, "red");

            this.drawNet();

            // Paddles
            this.drawRect(this.paddles.user.x, this.paddles.user.y, this.paddles.user.width, this.paddles.user.height, "white")
            this.drawRect(this.paddles.computer.x, this.paddles.computer.y, this.paddles.computer.width, this.paddles.computer.height, "white");

            // Ball
            this.drawCircle(this.ball.x, this.ball.y, this.ball.radius, 'white')
            this.rectX = this.rectX + 100;

            //Score
            this.drawText(this.paddles.user.score, this.canvas.width/4, this.canvas.height/5, "white")
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

        drawCircle(x, y, r, color) {
            this.context.fillStyle = color;
            this.context.beginPath();
            this.context.arc(x, y, r, 0, Math.PI*2, false);
            this.context.closePath();
            this.context.fill();     
        },

        drawText(text, x, y, color) {
            this.context.fillStyle = color;
            this.context.font = "75px fantasy"
            this.context.fillText(text, x, y)
        },
    }
}
</script>

<style>
    #pongGame {
        width: 800px;
        height: 550px
    }
</style>