<template>
	<section class="calculator">
		<button type="button" @click="toggleHistoryVisibility" class="button history">
			<i class="fas fa-history" />
		</button>
		<p class="current-operation">{{ currOperation }}</p>
		<div class="display">{{ currOperand || 0 }}</div>
		<button type="button" @click="clear" class="button helper">C</button>
		<button type="button" @click="percent" class="button helper">%</button>
		<button type="button" @click="backwardDelete" class="button helper">⌫</button>
		<button type="button" @click="divide" class="button operator">÷</button>
		<button type="button" @click="attachToCurrOperand(7)" class="button">7</button>
		<button type="button" @click="attachToCurrOperand(8)" class="button">8</button>
		<button type="button" @click="attachToCurrOperand(9)" class="button">9</button>
		<button type="button" @click="multiply" class="button operator">×</button>
		<button type="button" @click="attachToCurrOperand(4)" class="button">4</button>
		<button type="button" @click="attachToCurrOperand(5)" class="button">5</button>
		<button type="button" @click="attachToCurrOperand(6)" class="button">6</button>
		<button type="button" @click="subtract" class="button operator">-</button>
		<button type="button" @click="attachToCurrOperand(1)" class="button">1</button>
		<button type="button" @click="attachToCurrOperand(2)" class="button">2</button>
		<button type="button" @click="attachToCurrOperand(3)" class="button">3</button>
		<button type="button" @click="add" class="button operator">+</button>
		<button type="button" @click="invert" class="button">±</button>
		<button type="button" @click="attachToCurrOperand(0)" class="button">0</button>
		<button type="button" @click="attachToCurrOperand('.')" class="button">.</button>
		<button type="button" @click="equal" class="button operator">=</button>
		<HistoryPanel :history="history" :class="{ 'is-visible': isHistoryVisible }" />
	</section>
</template>

<script>
import HistoryPanel from '@/components/HistoryPanel.vue'
import { ref, reactive } from 'vue'

export default {
	name: 'TheCalculator',
	components: {
		HistoryPanel,
	},
	setup() {
		const history = reactive( [] )
		const isHistoryVisible = ref( false )
		const currOperation = ref( '' )
		const prevOperand = ref( null )
		const currOperand = ref( '' )
		let currOperator = null
		let hasActiveOperator = false

		const toggleHistoryVisibility = () => {
			console.log( 'Working' )
			isHistoryVisible.value = !isHistoryVisible.value
		}

		const clear = () => currOperand.value = ''

		const backwardDelete = () => currOperand.value = currOperand.value.slice( 0, -1 )

		const invert = () => {
			currOperand.value =
				currOperand.value.charAt( 0 ) !== '-'
					? `-${ currOperand.value }`
					: currOperand.value.slice( 1 )
		}

		const operate = {
			'+': ( a, b ) => `${ +a + +b }`,
			'-': ( a, b ) => `${ +a - +b }`,
			'×': ( a, b ) => `${ +a * +b }`,
			'÷': ( a, b ) => `${ +a / +b }`,
			'%': a => `${ +a / 100 }`,
		}

		const attachToCurrOperation = character => {
			// If an operation has finished, reset the current operation.
			if (
				currOperation.value &&
				currOperation.value[ currOperation.value.length - 1 ] === '='
			) {
				currOperation.value = ''
			}

			if (
				currOperation.value === '' &&
				( character === '+' || character === '-' || character === '×' || character === '÷' )
			) {
				currOperation.value += currOperand.value + character
			} else {
				currOperation.value += character
			}
		}

		const attachToCurrOperand = character => {
			// Clean up if this is the first insertion after an operator.
			if ( hasActiveOperator ) {
				clear()
				hasActiveOperator = false
			}
			// Prevent multiple periods.
			if ( character === '.' && currOperand.value.indexOf( '.' ) !== -1 ) return
			// Prevent multiple zeros at the beginning.
			if ( character === 0 && currOperand.value === 0 ) return
			// Prevent absence of zero on initial period.
			if ( character === '.' && currOperand.value === '' ) {
				attachToCurrOperation( 0 )
				currOperand.value = 0
			}
			currOperand.value = `${ currOperand.value }${ character }`
			attachToCurrOperation( character )
		}

		const setPrevOperand = () => {
			prevOperand.value = currOperand.value
		}

		const setCurrOperand = () => {
			if ( currOperator === '%' ) {
				currOperand.value = operate[ currOperator ]( currOperand.value )
			} else {
				currOperand.value = operate[ currOperator ]( prevOperand.value, currOperand.value )
			}
		}

		const attachToHistory = () => {
			if ( currOperator === '%' ) {
				history.push( currOperation.value + operate[ currOperator ]( currOperand.value ) )
			} else {
				history.push(
					currOperation.value +
						operate[ currOperator ]( prevOperand.value, currOperand.value ),
				)
			}
		}

		const add = () => {
			currOperator = '+'
			hasActiveOperator = true
			attachToCurrOperation( '+' )
			setPrevOperand()
		}

		const subtract = () => {
			currOperator = '-'
			hasActiveOperator = true
			attachToCurrOperation( '-' )
			setPrevOperand()
		}

		const multiply = () => {
			currOperator = '×'
			hasActiveOperator = true
			attachToCurrOperation( '×' )
			setPrevOperand()
		}

		const divide = () => {
			currOperator = '÷'
			hasActiveOperator = true
			attachToCurrOperation( '÷' )
			setPrevOperand()
		}

		const percent = () => {
			currOperator = '%'
			// Will allow for a new operation to start if a digit is pressed instead of an operator.
			hasActiveOperator = true
			attachToCurrOperation( '%=' )
			attachToHistory()
			setCurrOperand()
			prevOperand.value = null
		}

		const equal = () => {
			attachToCurrOperation( '=' )
			attachToHistory()
			setCurrOperand()
			prevOperand.value = null
			// Will allow for a new operation to start if a digit is pressed instead of an operator.
			hasActiveOperator = true
		}

		return {
			history,
			isHistoryVisible,
			toggleHistoryVisibility,
			currOperation,
			prevOperand,
			currOperand,
			clear,
			percent,
			backwardDelete,
			invert,
			attachToCurrOperand,
			add,
			subtract,
			multiply,
			divide,
			equal,
		}
	},
}
</script>

<style scoped lang="scss">
.calculator {
	font-size: 1.5rem;
	color: var(--white);
	background-color: var(--gray-950);
	width: 22rem;
	max-width: 100%;
	padding: 1rem;
	margin: 0 auto;
	display: grid;
	grid-template-columns: repeat(4, 1fr);
	grid-gap: 0.25rem;
	position: relative;
	overflow: visible;
}

.current-operation {
	grid-column: 1/5;
	color: var(--gray-400);
	text-align: right;
	padding: 0 1rem;
	min-height: 1.75rem;
}

.display {
	font-size: 3rem;
	text-align: right;
	padding: 0.5rem 1rem 1.5rem;
	grid-column: 1/5;
}

.button {
	line-height: 1;
	padding: 1em 0;
	border: none;
	background-color: var(--gray-900);
	transition: background-color 0.32s ease, outline 0.32s ease;
	outline: 2px solid transparent;
	cursor: pointer;

	&:focus {
		outline: 2px solid var(--white);
	}

	&:hover {
		background-color: var(--gray-800);
	}
}

.history {
	grid-column: 1/2;
	background-color: transparent;
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
