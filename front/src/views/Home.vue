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
        let colors = ["#ff0000", "#1cff01"]; //цвет мячей

        let Ball = function () {
            this.x = Math.floor(Math.random() * (474 - 26 + 1)) + 26;
            this.y = Math.floor(Math.random() * (374 - 26 + 1)) + 26;
            this.xSpeed = -2;
            this.ySpeed = 3;
        }; //границы появления и скорость движения

        let ballDesign = function (x, y, radius, fillBallDesign, i) {
            ctx.beginPath();
            ctx.arc(x, y, 25, 0, Math.PI * 2, true);
            if (fillBallDesign) {
                ctx.fillStyle = colors[i];
                ctx.fill();
            } else {
                ctx.stroke();
            }
        }; //дизайн мяча

        Ball.prototype.draw = function (i) {
            ballDesign(this.x, this.y, 3, true, i);
        }; //отрисовка

        Ball.prototype.move = function () {
            this.x += this.xSpeed;
            this.y += this.ySpeed;
        }; //движение

        Ball.prototype.checkCollision = function () {
            if (this.x < 25 || this.x > width - 25) {
                this.xSpeed = -this.xSpeed;
            }
            if (this.y < 25 || this.y > height - 25) {
                this.ySpeed = -this.ySpeed;
            }
        }; //столкновение с границами

        let canvas = document.getElementById("canvas"); //возвращает ссылку на элемент по его идентификатору
        let ctx = canvas.getContext("2d"); //двухмерная среда рисования
        let width = canvas.width; //ширина
        let height = canvas.height; //высота
        let ballsCount = 2; //количество мячей
        let balls = []; //массив мячей

        for (let i = 0; i < ballsCount; i++) {
            let ball = new Ball()
            balls.push(ball);
        }

        setInterval(function () {
            ctx.clearRect(0, 0, width, height);
            for (let i = 0; i < ballsCount; ++i) {
                balls[i].draw(i);
                balls[i].move();
                balls[i].checkCollision();
            }
            ctx.strokeRect(0, 0, width, height);
        }, 30); // вызывает функцию регулярно, повторяя вызов через определённый интервал времени (отрисовка, движение, столкновение)

        let getPixelColour = e => {
            let x = e.offsetX || e.originalEvent.layerX || e.layerX;
            let y = e.offsetY || e.originalEvent.layerY || e.layerY;
            let pixel = ctx.getImageData(x, y, 1, 1);

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
        canvas.addEventListener('click', getPixelColour);
    }; //получение цвета пикселя по клику и запись в логи результата

    data_time() {
        var date = new Date().toLocaleString();
        return date;
    } // дата и время
}
</script>
