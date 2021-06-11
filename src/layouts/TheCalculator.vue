<template>
	<section class="calculator">
		<ul class="history">
			<li v-for="log in history" :key="log">{{ log }}</li>
		</ul>
		<div class="display">{{ currDisplay || 0 }}</div>
		<button type="button" @click="clear" class="button helper">C</button>
		<button type="button" @click="percent" class="button helper">%</button>
		<button type="button" @click="backwardDelete" class="button helper">⌫</button>
		<button type="button" @click="divide" class="button operator">÷</button>
		<button type="button" @click="attach(7)" class="button">7</button>
		<button type="button" @click="attach(8)" class="button">8</button>
		<button type="button" @click="attach(9)" class="button">9</button>
		<button type="button" @click="multiply" class="button operator">×</button>
		<button type="button" @click="attach(4)" class="button">4</button>
		<button type="button" @click="attach(5)" class="button">5</button>
		<button type="button" @click="attach(6)" class="button">6</button>
		<button type="button" @click="subtract" class="button operator">-</button>
		<button type="button" @click="attach(1)" class="button">1</button>
		<button type="button" @click="attach(2)" class="button">2</button>
		<button type="button" @click="attach(3)" class="button">3</button>
		<button type="button" @click="add" class="button operator">+</button>
		<button type="button" @click="invert" class="button">±</button>
		<button type="button" @click="attach(0)" class="button">0</button>
		<button type="button" @click="attach('.')" class="button">.</button>
		<button type="button" @click="equal" class="button operator">=</button>
	</section>
</template>

<script>
import { ref, reactive } from 'vue'

export default {
	name: 'TheCalculator',
	setup() {
		const history = reactive( [] )
		const prevDisplay = ref( null )
		const currDisplay = ref( '' )
		let operator = null
		let hasActiveOperator = false

		const clear = () => currDisplay.value = ''

		const percent = () => currDisplay.value = +currDisplay.value / 1000

		const backwardDelete = () => currDisplay.value = currDisplay.value.slice( 0, -1 )

		const invert = () => {
			currDisplay.value =
				currDisplay.value.charAt( 0 ) !== '-'
					? `-${ currDisplay.value }`
					: currDisplay.value.slice( 1 )
		}

		const attach = character => {
			// Prevent multiple periods.
			if ( character === '.' && currDisplay.value.indexOf( '.' ) !== -1 ) return
			// Prevent multiple zeros at the beginning.
			if ( character === 0 && currDisplay.value === 0 ) return
			// Prevent absence of zero on initial period.
			if ( character === '.' && currDisplay.value === '' ) currDisplay.value = 0
			if ( hasActiveOperator ) {
				clear()
				hasActiveOperator = false
			}
			currDisplay.value = `${ currDisplay.value }${ character }`
		}

		const setPreviousDisplay = () => {
			prevDisplay.value = currDisplay.value
			hasActiveOperator = true
		}

		const add = () => {
			operator = 'addition'
			setPreviousDisplay()
		}

		const subtract = () => {
			operator = 'subtraction'
			setPreviousDisplay()
		}

		const multiply = () => {
			operator = 'multiplication'
			setPreviousDisplay()
		}

		const divide = () => {
			operator = 'division'
			setPreviousDisplay()
		}

		const log = {
			'+': ( a, b ) => `${ a } + ${ b } = ${ +a + +b }`,
			'-': ( a, b ) => `${ a } + ${ b } = ${ +a - +b }`,
			'*': ( a, b ) => `${ a } × ${ b } = ${ +a * +b }`,
			'/': ( a, b ) => `${ a } ÷ ${ b } = ${ +a / +b }`,
		}

		const equal = () => {
			switch ( operator ) {
				case 'addition':
					history.push( log[ '+' ]( prevDisplay.value, currDisplay.value ) )
					currDisplay.value = `${ +prevDisplay.value + +currDisplay.value }`
					break
				case 'subtraction':
					history.push( log[ '-' ]( prevDisplay.value, currDisplay.value ) )
					currDisplay.value = `${ +prevDisplay.value - +currDisplay.value }`
					break
				case 'multiplication':
					history.push( log[ '*' ]( prevDisplay.value, currDisplay.value ) )
					currDisplay.value = `${ +prevDisplay.value * +currDisplay.value }`
					break
				case 'division':
					history.push( log[ '/' ]( prevDisplay.value, currDisplay.value ) )
					currDisplay.value = `${ +prevDisplay.value / +currDisplay.value }`
					break
				default:
					break
			}
			prevDisplay.value = null
			// Will allow for a new operation to start
			// if a digit is pressed instead of an operator.
			hasActiveOperator = true
		}

		return {
			history,
			prevDisplay,
			currDisplay,
			clear,
			percent,
			backwardDelete,
			invert,
			attach,
			add,
			subtract,
			multiply,
			divide,
			equal,
		}
	},
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.calculator {
	font-size: 1.5rem;
	color: var(--white);
	width: 22rem;
	max-width: 100%;
	padding: 1rem;
	margin: 0 auto;
	display: grid;
	grid-template-columns: repeat(4, 1fr);
	grid-gap: 0.25rem;
	background-color: var(--gray-950);
}

.history {
	grid-column: 1/5;
	list-style: none;
}

.display {
	font-size: 3rem;
	text-align: right;
	padding: 1.5rem;
	grid-column: 1/5;
}

.button {
	line-height: 1;
	padding: 1em 0;
	border: none;
	background-color: var(--gray-900);
	transition: background-color 0.32s ease, outline 0.32s ease;
	outline: 2px solid transparent;

	&:focus {
		outline: 2px solid var(--white);
	}

	&:hover {
		background-color: var(--gray-800);
	}
}

.operator {
	color: var(--purple-500);

	&:focus {
		outline-color: var(--purple-500);
	}
}

.helper {
	color: var(--teal-500);

	&:focus {
		outline-color: var(--teal-500);
	}
}
</style>
