<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const trades = ref([])
const name = ref('')

const input_content = ref('')
const input_leverage = ref(null)
const input_entry = ref('')
const input_takeprofit = ref('')
const input_stoploss = ref('')
const input_liqprice = ref('')

const trades_asc = computed(() => trades.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(trades, (newVal) => {
	localStorage.setItem('trades', JSON.stringify(newVal))
}, {
	deep: true
})

const addTrade = () => {
	if (input_content.value.trim() === '' || input_leverage.value === null ||
   input_entry.value === null || input_takeprofit.value == null || 
   input_stoploss === null ||
   input_liqprice === null ) {
		return
	}

	trades.value.push({
		content: input_content.value,
		leverage: input_leverage.value,
    entry: input_entry.value,
    takeprofit: input_takeprofit.value,
    stoploss: input_stoploss.value,
    liqprice: input_liqprice.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}

const removeTodo = (todo) => {
	trades.value = trades.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	trades.value = JSON.parse(localStorage.getItem('trades')) || []
})
</script>

<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				Today is the day, <input type="text" id="name" placeholder="Name here" v-model="name">
			</h2>
		</section>

		<section class="create-todo">
			<h2>My Crypto Plan</h2>

			<form id="new-todo-form" @submit.prevent="addTrade">
				<h3>What's next on your trade list?</h3>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="$$$"
					v-model="input_content" />
				
				<h4>Allowed leverage</h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="leverage" 
							id="leverage1" 
							value="10x"
							v-model="input_leverage" />
						<span class="bubble 10x"></span>
						<div>10x</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="leverage" 
							id="leverage2" 
							value="20x"
							v-model="input_leverage" />
						<span class="bubble 20x"></span>
						<div>20x</div>
					</label>

          <input 
					type="text" 
					name="entry" 
					id="entry" 
					placeholder="Entry price"
					v-model="input_entry" />

          <input 
					type="text" 
					name="takeprofit" 
					id="takeprofit" 
					placeholder="Take profit price"
					v-model="input_takeprofit" />
          <input 
					type="text" 
					name="stoploss" 
					id="stoploss" 
					placeholder="Stop loss price"
					v-model="input_stoploss" />

          <input 
					type="text" 
					name="liqprice" 
					id="liqprice" 
					placeholder="liq. price"
					v-model="input_liqprice" />

				</div>

				<input type="submit" value="Add the trade" />
			</form>
		</section>

		<section class="todo-list">
			<h3> Trade History</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in trades_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.leverage == '10x' 
								? '10x' 
								: '20x'
						}`"></span>
					</label>

					<div class="todo-content">

            $
						<input class="tablediv" type="text" v-model="todo.content" />
            EP
            <input class="tablediv" type="text" v-model="todo.entry" />
            TP
            <input class="tablediv" type="text" v-model="todo.takeprofit" />
            SL
            <input class="tablediv" type="text" v-model="todo.stoploss" />
            L.P
            <input class="tablediv" type="text" v-model="todo.liqprice" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Delete</button>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>