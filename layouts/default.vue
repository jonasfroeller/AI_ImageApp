<template>
    <header class="sticky top-0 left-0 p-4">
        <h1 class="text-6xl">Text to Image</h1>
    </header>
    <main class="flex flex-col items-center justify-center min-h-screen p-8">
        <slot></slot>
    </main>
    <footer class="p-4">
        <a href="https://github.com/jonasfroeller/AI_ImageApp" class="hover:underline"><UIcon dynamic name="grommet-icons:github" /> Github</a>
    </footer>
</template>
