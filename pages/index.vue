<template>
    <div class="container mx-auto mt-20">
        <h1 class="text-3xl">Canvas playground</h1>
        <p>Playground for canvas experiments</p>
        <p>Warning: experimental, shit breaks :)</p>
        <p>Tutorials: <a target="_blank" href="https://www.youtube.com/c/Frankslaboratory">Franks Laboratory</a></p>
        <div class="grid gap-3 my-4">
            <NuxtLink to="/fractals">Fractals</NuxtLink>
            <NuxtLink to="/pixels/physics">Pixels physics</NuxtLink>
            <NuxtLink to="/pixels/rain">Pixel rain</NuxtLink>
            <NuxtLink to="/vector-field">Vector field</NuxtLink>
        </div>
    </div>
</template>