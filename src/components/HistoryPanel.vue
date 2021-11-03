<template>
	<ul class="history-panel">
		<transition-group name="fade-slide-right">
			<li v-if="!history.length">There’s no history yet.</li>
			<li v-for="log in history" :key="log">{{ log }}</li>
		</transition-group>
	</ul>
</template>

<script setup>
defineProps({
	history: Array,
})
</script>

<style scoped lang="scss">
.history-panel {
	list-style: none;
	line-height: 1.75;
	position: absolute;
	top: 0;
	left: 20%;
	width: 80%;
	height: 60%;
	overflow-y: auto;
	padding: 1rem;
	font-size: 1.25rem;
	color: var(--gray-200);
	background-color: var(--gray-900);
	transition: left 0.32s ease, z-index 0s linear;
	z-index: -1;

	&.is-visible {
		left: 100%;
		z-index: 1;
		transition: left 0.32s ease, z-index 0s linear 0.32s;
	}
}
</style>
