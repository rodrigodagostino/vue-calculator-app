<template>
	<header class="site-header">
		<div class="container">
			<h1>Vue Calculator App</h1>
		</div>
	</header>
	<main class="site-main">
		<div class="container">
			<TheCalculator />
		</div>
	</main>
</template>

<script>
import TheCalculator from './layouts/TheCalculator.vue'

export default {
	name: 'App',
	components: {
		TheCalculator,
	},
}
</script>

<style lang="scss">
/**
 * Variables
 */
:root {
	--font-main: 'Rubik', Avenir, Helvetica, Arial, sans-serif;
	--color-main--lightest: #c7d2fe;
	--color-main--lighter: #a5b4fc;
	--color-main--light: #818cf8;
	--color-main: #6366f1;
	--color-main--dark: #4f46e5;
	--color-main--darker: #4338ca;
	--color-main--darkest: #3730a3;

	--teal-050: #f0fdfa;
	--teal-100: #ccfbf1;
	--teal-200: #99f6e4;
	--teal-300: #5eead4;
	--teal-400: #2dd4bf;
	--teal-500: #14b8a6;
	--teal-600: #0d9488;
	--teal-700: #0f766e;
	--teal-800: #115e59;
	--teal-900: #134e4a;

	--purple-050: #faf5ff;
	--purple-100: #f3e8ff;
	--purple-200: #e9d5ff;
	--purple-300: #d8b4fe;
	--purple-400: #c084fc;
	--purple-500: #a855f7;
	--purple-600: #9333ea;
	--purple-700: #7e22ce;
	--purple-800: #6b21a8;
	--purple-900: #581c87;

	--white: #f3f3f5;
	--gray-050: #ededf0;
	--gray-100: #e1e1e6;
	--gray-150: #d2d2da;
	--gray-200: #c3c3ce;
	--gray-300: #a4a6b5;
	--gray-400: #86889d;
	--gray-500: #686a84;
	--gray-600: #585a70;
	--gray-650: #515266;
	--gray-700: #494a5c;
	--gray-750: #414253;
	--gray-800: #393a49;
	--gray-850: #31323f;
	--gray-900: #2a2a35;
	--gray-950: #22222b;
	--black: #15151a;
}

/**
 * Reset styles.
 */
html {
	box-sizing: border-box;
	height: 100%;
}

*,
*::before,
*::after {
	/* Inherit box-sizing to make it easier to change the property for components that leverage other behavior; see http://css-tricks.com/inheriting-box-sizing-probably-slightly-better-best-practice/ */
	box-sizing: inherit;
}

/* Reset margins and paddings on most elements */
body,
h1,
h2,
h3,
h4,
h5,
h6,
ul,
ol,
li,
p,
pre,
blockquote,
figure,
hr {
	margin: 0;
	padding: 0;
}

a {
	color: var(--white);
	transition: outline 0.32s ease;
	outline: 1px dotted transparent;
}

a:focus {
	outline: 1px dotted var(--white);
}

/* Reset forms and buttons */
input,
textarea,
select,
button {
	color: inherit;
	font: inherit;
	letter-spacing: inherit;
}

input,
textarea,
button {
	border: 1px solid gray;
	min-width: 0;
}

button {
	border-radius: 0;
	padding: 0.75em 1em;
	background-color: transparent;
}

button * {
	pointer-events: none;
}

/* Easy responsive for media elements */
embed,
iframe,
img,
object,
video {
	display: block;
	max-width: 100%;
}

/* Useful table styles */
table {
	table-layout: fixed;
	width: 100%;
}

/**
 * Base styles.
 */
body {
	font-family: var(--font-main);
	color: var(--gray-800);
	background-color: var(--white);
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	height: 100%;
	display: flex;
	justify-content: center;
}

#app {
	width: 100%;
	height: 100%;
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
}

.container {
	display: flex;
	max-width: 52rem;
	margin: 0 auto;
	padding: 1rem;
	position: relative;
}

.site-header {
	h1 {
		text-align: center;
	}
}

.site-main {}

.fade-slide-up-enter-active {
	animation: fade-slide-up 0.32s ease;
}

.fade-slide-up-leave-active {
	animation: fade-slide-up 0.32s ease reverse;
}

@keyframes fade-slide-up {
	from {
		opacity: 0;
		transform: translate3d(0, 2rem, 0);
	}
	to {
		opacity: 1;
		transform: translate3d(0, 0, 0);
	}
}

.fade-slide-right-enter-active {
	animation: fade-slide-right 0.32s ease;
}

.fade-slide-right-leave-active {
	animation: fade-slide-right 0.32s ease reverse;
}

@keyframes fade-slide-right {
	from {
		opacity: 0;
		transform: translate3d(-2rem, 0, 0);
	}
	to {
		opacity: 1;
		transform: translate3d(0, 0, 0);
	}
}

.fade-enter-active {
	animation: fade 0.32s ease;
}

.fade-leave-active {
	animation: fade 0.32s ease reverse;
}

@keyframes fade {
	from {
		opacity: 0;
	}
	to {
		opacity: 1;
	}
}
</style>
