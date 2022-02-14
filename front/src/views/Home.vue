<template>
<div class="home" id="home">
    <h1>Угроза/защита</h1>
    <canvas id="canvas" width="500" height="400"></canvas><br>
</div>
</template>

<style lang="scss">
.canvas {
    border: 1px solid black;
}
</style>

<script lang="ts">
import {
    Component,
    Vue
} from "vue-property-decorator";

@Component({
    components: {},
})

export default class Home extends Vue {

    mounted() {
        let colors = ["#ff0000", "#1cff01"];

        let Ball = function () {
            this.x = Math.floor(Math.random() * (474 - 26 + 1)) + 26;
            this.y = Math.floor(Math.random() * (374 - 26 + 1)) + 26;
            this.xSpeed = -2;
            this.ySpeed = 3;
        };

        let circle = function (x, y, radius, fillCircle, i) {
            ctx.beginPath();
            ctx.arc(x, y, 25, 0, Math.PI * 2, true);
            if (fillCircle) {
                ctx.fillStyle = colors[i];
                ctx.fill();
            } else {
                ctx.stroke();
            }
        };

        Ball.prototype.draw = function (i) {
            circle(this.x, this.y, 3, true, i);
        };

        Ball.prototype.move = function () {
            this.x += this.xSpeed;
            this.y += this.ySpeed;
        };

        Ball.prototype.checkCollision = function () {
            if (this.x < 25 || this.x > width - 25) {
                this.xSpeed = -this.xSpeed;
            }
            if (this.y < 25 || this.y > height - 25) {
                this.ySpeed = -this.ySpeed;
            }
        };

        let canvas = document.getElementById("canvas");
        let ctx = canvas.getContext("2d");
        let width = canvas.width;
        let height = canvas.height;
        let ballsCount = 2;
        let balls = [];

        for (let i = 0; i < ballsCount; i++) {
            let ball = new Ball()
            balls.push(ball);
        }
        setInterval(function () {
            ctx.clearRect(0, 0, width, height);
            for (let i = 0; i < 2; ++i) {
                balls[i].draw(i);
                balls[i].move();
                balls[i].checkCollision();
            }
            ctx.strokeRect(0, 0, width, height);
        }, 30);

        let getpixelcolour = e => {
            let x = e.offsetX || e.originalEvent.layerX || e.layerX,
                y = e.offsetY || e.originalEvent.layerY || e.layerY,
                pixel = ctx.getImageData(x, y, 1, 1);

            if (pixel.data != "0,0,0,0") {
                if (pixel.data == '255,0,0,255') {
                    console.log("Дата и время: " + this.data_time() + " - координаты: " + x + ", " + y + " - угроза.");
                } else {
                    console.log("Дата и время: " + this.data_time() + " - координаты: " + x + ", " + y + " - защита.");
                }
            } else {
                return;
            }
        }
        canvas.addEventListener('click', getpixelcolour);
    };
    data_time() {
        var date = new Date().toLocaleString();
        return date;
    }

}
</script>
