<script setup lang="ts">
import { onMounted, reactive } from 'vue';
import debounce from 'lodash.debounce';

interface State {
  canvas: object,
  ctx: object,
  stars: Array<object>,
  windowWidth: number,
  windowHeight: number,
  speed: number,
  center: object,
  directions: Array<string>,
  craycray: Boolean,
}

let state: State = reactive({
    canvas: {},
    ctx: {},
    stars: [],
    speed: 25,
    windowWidth: 0,
    windowHeight: 0,
    center: { x: 0, y: 0},
    craycray: false,
})

function Star(x, y) {
    this.x = x;
    this.y = y;
    this.size = 0.25;
    
    // random direction with -90° angle compensation, conv. to rad
    this.angle = (Math.floor(Math.random() * 360) - 90) / 180 * Math.PI;

    this.travel = () => {
        this.x += state.speed * Math.cos(this.angle);
        this.y += state.speed * Math.sin(this.angle);
        
        state.ctx.beginPath();
        state.ctx.fillStyle = "white";
        state.ctx.arc(this.x, this.y, this.size+=0.015, 0, Math.PI*2, true); 
        state.ctx.closePath();
        state.ctx.fill();
    };
    
}

function getRandom() {
    return Math.floor(Math.random() * 50);
}

function setSizes() {
    console.log('I was called')
    state.windowWidth = window.innerWidth;
    state.windowHeight = window.innerHeight;
    
    state.center.x = state.windowWidth / 2;
    state.center.y = state.windowHeight / 2;

    state.canvas = document.getElementById('hs-canvas');
    state.canvas.width = state.windowWidth;
    state.canvas.height = state.windowHeight;
}

function animateStars() {
    // clear frame
    if (!state.craycray) {
        state.ctx.clearRect(0, 0, state.windowWidth, state.windowHeight);
    }

    state.stars.push(new Star(state.center.x, state.center.y));
    state.stars.forEach((star) => star.travel());
    window.requestAnimationFrame(animateStars);
}

onMounted(() => {
    setSizes();
    window.addEventListener('resize', debounce(setSizes, 250));

    state.ctx = state.canvas.getContext('2d');
    
    window.requestAnimationFrame(animateStars);
})
</script>

<template>
    <canvas id="hs-canvas"></canvas>
</template>