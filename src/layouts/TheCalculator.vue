<template>
	<section class="calculator">
		<button type="button" @click="toggleHistoryVisibility" class="button history">
			<i class="fas fa-history" />
		</button>
		<p class="current-operation">{{ currOperation }}</p>
		<div class="display">{{ currOperand || 0 }}</div>
		<button class="button helper" id="button-delete" @click="clear">C</button>
		<button class="button helper" id="button-percent" @click="percent">%</button>
		<button class="button helper" id="button-clear-entry" @click="clearEntry">⌫</button>
		<button class="button operator" id="button-divide" @click="divide">÷</button>
		<button class="button" id="button-seven" @click="attachToCurrOperand(7)">7</button>
		<button class="button" id="button-eight" @click="attachToCurrOperand(8)">8</button>
		<button class="button" id="button-nine" @click="attachToCurrOperand(9)">9</button>
		<button class="button operator" id="button-multiply" @click="multiply">×</button>
		<button class="button" id="button-four" @click="attachToCurrOperand(4)">4</button>
		<button class="button" id="button-five" @click="attachToCurrOperand(5)">5</button>
		<button class="button" id="button-six" @click="attachToCurrOperand(6)">6</button>
		<button class="button operator" id="button-subtract" @click="subtract">-</button>
		<button class="button" id="button-one" @click="attachToCurrOperand(1)">1</button>
		<button class="button" id="button-two" @click="attachToCurrOperand(2)">2</button>
		<button class="button" id="button-three" @click="attachToCurrOperand(3)">3</button>
		<button class="button operator" id="button-add" @click="add">+</button>
		<button class="button" id="button-one" @click="negate">±</button>
		<button class="button" id="button-zero" @click="attachToCurrOperand(0)">0</button>
		<button class="button" id="button-period" @click="attachToCurrOperand('.')">.</button>
		<button class="button operator" id="button-equal" @click="equal">=</button>
		<HistoryPanel :history="history" :class="{ 'is-visible': isHistoryVisible }" />
	</section>
</template>

<script setup>
import HistoryPanel from '@/components/HistoryPanel.vue'
import { ref, reactive } from 'vue'

const history = reactive( [] )
const isHistoryVisible = ref( false )
const currOperation = ref( '' )
const prevOperand = ref( null )
const currOperand = ref( '' )
let currOperator = null
let hasActiveOperator = false

const toggleHistoryVisibility = () => {
	isHistoryVisible.value = !isHistoryVisible.value
}

const clear = () => currOperand.value = ''

const clearEntry = () => currOperand.value = currOperand.value.slice( 0, -1 )

const negate = () => {
	currOperand.value = currOperand.value.charAt( 0 ) !== '-' ? `-${ currOperand.value }` : currOperand.value.slice( 1 )
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
	if ( currOperation.value && currOperation.value[ currOperation.value.length - 1 ] === '=' ) {
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
		history.push( currOperation.value + operate[ currOperator ]( prevOperand.value, currOperand.value ) )
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

window.addEventListener( 'keydown', event => {
	switch ( event.key ) {
		case '0':
			document.getElementById( 'button-zero' ).click()
			break
		case '1':
			document.getElementById( 'button-one' ).click()
			break
		case '2':
			document.getElementById( 'button-two' ).click()
			break
		case '3':
			document.getElementById( 'button-three' ).click()
			break
		case '4':
			document.getElementById( 'button-four' ).click()
			break
		case '5':
			document.getElementById( 'button-five' ).click()
			break
		case '6':
			document.getElementById( 'button-six' ).click()
			break
		case '7':
			document.getElementById( 'button-seven' ).click()
			break
		case '8':
			document.getElementById( 'button-eight' ).click()
			break
		case '9':
			document.getElementById( 'button-nine' ).click()
			break
		case '.':
			document.getElementById( 'button-period' ).click()
			break
		case '+':
			document.getElementById( 'button-add' ).click()
			break
		case '-':
			document.getElementById( 'button-subtract' ).click()
			break
		case '*':
			document.getElementById( 'button-multiply' ).click()
			break
		case '/':
			// Prevent Quick Find shortcut in Firefox.
			event.preventDefault()
			document.getElementById( 'button-divide' ).click()
			break
		case '%':
			document.getElementById( 'button-percent' ).click()
			break
		case 'Enter':
			document.getElementById( 'button-equal' ).click()
			break
		case 'Backspace':
			document.getElementById( 'button-clear-entry' ).click()
			break
		case 'Delete':
			document.getElementById( 'button-delete' ).click()
			break
	}
})
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
