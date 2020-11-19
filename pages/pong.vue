<template>
    <div class="container mx-auto px-4" id="pongGame">
        <div class="scores">
            <h4><strong>Opponents: </strong>{{ scores.opponent }}</h4>
            <h4><strong>You: </strong>{{ scores.player }}</h4>
            <div class="pong">

            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            scores: {
                player: 0,
                opponent: 0
            }
        }
    },

    mounted() {
        var c = document.getElementById("pongGame");
        var ctx = c.getContext("2d");    
        this.context = ctx;
    },

    methods: {
        drawRectangle() {
            this.context.fillStyle = "Black";
            this.context.fillRect(100, 200, 50, 75)
        }
    }
}
</script>

<style>
    .pong {
        width: 600px;
        height: 400px
    }
</style>