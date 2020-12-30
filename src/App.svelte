<script>
	import Github from './Github.svelte';
	import { setContext, onMount } from 'svelte';

	import Navbar from './Navbar.svelte';
	import ExpenseList from "./ExpenseList.svelte";
	import Totals from "./Totals.svelte";
	import ExpenseForm from "./ExpenseForm.svelte";
	import Modal from "./Modal.svelte";

	//import expensesData from "./expenses";
	let expenses = [];
	//let expenses = [...expensesData];
	let setName = "";
	let setAmount = null;
	let setId = null;

	let isFormOpen = false;

	$: isEditing = setId ? true : false;
	$: total = expenses.reduce((acc, curr) => {
			return (acc += curr.amount)
		},0)


	function showForm() {
		isFormOpen = true;
	}

	function hideForm() {
		isFormOpen = false;
		setId = null;
		setName = '';
		setAmount = null;
	}

	function removeExpense(id) {
		expenses = expenses.filter(item => item.id != id);
		setLocalStorage();
	}

	function clearExpenses () {
		expenses=[];
		setLocalStorage();
	}

	function addExpense({ name, amount }){
		let expense = { id: Math.random() * Date.now(), name, amount};
		expenses = [expense, ...expenses];
		setLocalStorage();
		
	}

	function setModifiedExpense (id) {
		let expense = expenses.find(item => item.id === id);
		setId = expense.id;
		setName = expense.name;
		setAmount = expense.amount;
		showForm();
	}

	function editExpense ({ name, amount }) {
		expenses = expenses.map(item => {
			return item.id === setId ? {...item, name, amount } : { ...item };
			setId = null;
			setAmount = null;
			setName = "";
			setLocalStorage();
		});
	}
	//context
	setContext("remove", removeExpense);
	setContext("modify", setModifiedExpense);
	//local storage
	function setLocalStorage(){
		localStorage.setItem("expenses", JSON.stringify(expenses));
	}
	onMount(()=>{
		expenses = localStorage.getItem("expenses")
		? JSON.parse(localStorage.getItem("expenses"))
		: [];
	})
</script>


<Navbar {showForm}/>
<main class="content">
	
	{#if isFormOpen}
	<Modal>
		<ExpenseForm bind:name={setName} bind:amount={setAmount} {addExpense} {editExpense} {isEditing} {hideForm}/>
	</Modal>
		
	{/if}
	<Totals title="total expenses" total={total} />
	<ExpenseList expenses={expenses}/>
	<button type="button" class="btn btn-primary btn-block" on:click={clearExpenses}>
		clear expenses
	</button>
</main>

