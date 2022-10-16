<script setup>
//Perlin or Simplex noise
let canvas = ref(null)
let ctx = null
let flowField = null
let flowFieldAnimation = null

const initCanvas = () => {
	ctx = canvas.value.getContext('2d')
	canvas.value.width = window.innerWidth
	canvas.value.height = window.innerHeight
	flowField = new FlowFieldEffect(ctx, canvas.value.width, canvas.value.height)
	flowField.animate(0)

	onResize()
	onMouseMove()

}

const onResize = () => {

	window.onresize = () => {

		cancelAnimationFrame(flowFieldAnimation)
		canvas.value.width = window.innerWidth
		canvas.value.height = window.innerHeight

		flowField = new FlowFieldEffect(ctx, canvas.value.width, canvas.value.height)

		flowField.animate(0)
	}
}

const mouse = {
	x: 0,
	y: 0
}

const onMouseMove = () => {
	onmousemove = (e) => {
		mouse.x = e.x
		mouse.y = e.y
	}
}


class FlowFieldEffect {
	#ctx
	#width
	#height
	constructor(ctx, width, height) {
		this.#ctx = ctx
		this.#ctx.strokeStyle = 'white'
		this.#ctx.lineWidth = 1
		this.#width = width
		this.#height = height

		this.lastTime = 0
		this.interval = 1000 / 60
		this.timer = 0
		this.cellSize = 15
		this.gradient
		this.#createGradient()
		this.#ctx.strokeStyle = this.gradient
		this.radius = 5
		this.vr = 0.03
	}
	#createGradient() {
		this.gradient = this.#ctx.createLinearGradient(0, 0, this.#width, this.#height)
		this.gradient.addColorStop("0.1", "#ff5c33")
		this.gradient.addColorStop("0.2", "#ff66b3")
		this.gradient.addColorStop("0.4", "#ccccff")
		this.gradient.addColorStop("0.6", "#b3ffff")
		this.gradient.addColorStop("0.8", "#80ff80")
		this.gradient.addColorStop("0.9", "#ffff33")
	}
	#drawLine(angle, x, y) {
		let positionX = x
		let positionY = y
		let dx = mouse.x - positionX
		let dy = mouse.y - positionY
		let distance = dx * dx + dy * dy
		if (distance > 600_000) distance = 600_000
		else if (distance < 5_000) distance = 50_000

		let length = distance / 10_000
		this.#ctx.beginPath()
		this.#ctx.moveTo(x, y)
		this.#ctx.lineTo(x + Math.cos(angle) * length, y + Math.sin(angle) * length)
		this.#ctx.stroke()
	}
	animate(timeStamp) {
		// difference current and previous frame in ms
		let deltaTime = timeStamp - this.lastTime
		this.lastTime = timeStamp
		if (this.timer > this.interval) {

			this.#ctx.clearRect(0, 0, this.#width, this.#height)
			this.radius += this.vr
			if (this.radius > 5 || this.radius < -5) this.vr *= -1

			for (let y = 0; y < this.#height; y += this.cellSize) {
				for (let x = 0; x < this.#width; x += this.cellSize) {
					const angle = (Math.cos(x * 0.005) + Math.sin(y * 0.005)) * this.radius
					this.#drawLine(angle, x, y)
				}
			}

			this.timer = 0
		}
		else { this.timer += deltaTime }

		flowFieldAnimation = requestAnimationFrame(this.animate.bind(this))
	}
}
onBeforeUnmount(() => {
	ctx.clearRect(0, 0, canvas.value.width, canvas.value.height);
})

onMounted(() => {
	initCanvas()

})
</script>
<template>
	<section class="h-screen relative  text-white">
		<canvas ref="canvas" class="bg-black"></canvas>
		<div class="absolute flex items-center  gap-4 top-0 left-0 z-100 ">
			<button @click="initCanvas()">Initialize</button>
			<p>Works on resize</p>



		</div>
	</section>
</template>