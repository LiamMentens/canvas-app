<script setup>
const initFractal = () => {

    const canvas = document.querySelector('#canvas')
    const ctx = canvas.getContext('2d')

    canvas.width = window.innerWidth * 0.8
    canvas.height = window.innerHeight * 0.8

    //canvas settings
    ctx.fillStyle = 'blue'
    ctx.lineCap = 'round'
    ctx.shadowColor = 'rgba(0,0,0,1)'
    ctx.shadowOffsetX = 10
    ctx.shadowOffsetY = 5
    ctx.shadowBlur = 10

    //effect settings
    let size = canvas.width < canvas.height ? canvas.width * 0.3 : canvas.height * 0.3
    const maxLevel = 4
    const branches = 2

    let sides = 5
    let scale = 0.5
    let spread = 0.6

    //hue saturation lightness
    let color = 'hsl(' + Math.random() * 360 + ',100%, 50%)'
    let lineWidth = Math.floor(Math.random() * 20 + 10)

    //controls
    const slider_spread = document.querySelector('#spread')
    const label_spread = document.querySelector('[for="spread"]')
    const slider_sides = document.querySelector('#sides')
    const label_slides = document.querySelector('[for="sides"]')
    slider_spread.addEventListener('change', (e) => {
        spread = e.target.value
        updateSliders()
        drawFractal()

    })
    slider_sides.addEventListener('change', (e) => {
        sides = e.target.value
        updateSliders()
        drawFractal()

    })

    const drawBranch = (level) => {
        if (level > maxLevel) return
        //restore resets to last saved state
        ctx.beginPath()

        //start coordinates at center of canvas
        ctx.moveTo(0, 0)

        //end coordinates
        ctx.lineTo(size, 0)
        ctx.stroke()

        for (let i = 0; i < branches; i++) {
            ctx.save()

            ctx.translate(size - (size / branches) * i, 0)
            ctx.scale(scale, scale)

            ctx.save()
            ctx.rotate(spread)
            drawBranch(level + 1)
            ctx.restore()

            ctx.save()
            ctx.rotate(-spread)
            drawBranch(level + 1)
            ctx.restore()

            ctx.restore()
        }
    }
    const drawFractal = () => {
        ctx.clearRect(0, 0, canvas.width, canvas.height)
        ctx.save()
        ctx.strokeStyle = color
        ctx.lineWidth = Math.floor(Math.random() * 20 + 10)
        ctx.translate(canvas.width / 2, canvas.height / 2)
        ctx.scale(1, 1)
        ctx.rotate(0)
        for (let i = 0; i < sides; i++) {
            ctx.rotate((Math.PI * 2) / sides)
            drawBranch(0)
        }
        ctx.restore()
    }
    const randomizeFractal = () => {
        sides = Math.floor(Math.random() * 7 + 2)
        scale = Math.random() * 0.2 + 0.4
        spread = Math.random() * 2.9 + 0.1
        color = 'hsl(' + Math.random() * 360 + ',100%, 50%)'
        lineWidth = Math.floor(Math.random() * 20 + 10)
    }

    const generateRandomFractal = () => {
        randomizeFractal()
        updateSliders()
        drawFractal()
    }

    const updateSliders = () => {
        slider_spread.value = spread
        label_spread.innerText = 'Spread: ' + Number(spread).toFixed(2)
        slider_sides.value = sides
        label_slides.innerText = 'Sides: ' + Number(sides)

    }

    drawFractal()

    const clearFractals = () => {
        ctx.clearRect(0, 0, canvas.width, canvas.height)
    }

    return {
        generateRandomFractal,
        clearFractals
    }
}

onBeforeUnmount(() => {
    if (process.client) {
        initFractal().clearFractals()
    }
})

onMounted(() => {
    initFractal()

})


</script>
<template>
    <div class="h-screen grid place-items-center relative text-white">
        <div class="relative container  text-black">
            <div class="border-2 border-white p-3 flex gap-3 items-center ">
                <button @click="initFractal().generateRandomFractal()">Randomize</button>
                <label for="spread">Slider</label>
                <input class="w-auto" id="spread" type="range" min="0.1" max="3.1" step="0.02" value="1">
                <label for="sides">Slider</label>
                <input class="w-auto" id="sides" type="range" min="2" max="10" step="1" value="5">
            </div>
            <canvas id="canvas" class="bg-black"></canvas>

        </div>
    </div>
</template>
