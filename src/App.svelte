<script>
  import { setContext, onMount } from "svelte";
  import {slide} from 'svelte/transition'
  // components
  import Navbar from "./components/Navbar.svelte";
  import ExpensesList from "./components/ExpensesList.svelte";
  import Totals from "./components/Totals.svelte";
  import ExpenseForm from "./components/ExpenseForm.svelte";
  import Modal from "./components/Modal.svelte";

  //data

  // import expensesData from "./expenses";

  // variables

  let expenses = [];

  //set editing variables

  let setName = "";
  let setAmount = null;
  let setId = null;

  //toggle form variables

  let isFormOpen = false;

  // reactive
  $: isEditing = setId ? true : false;
  $: total = expenses.reduce((acc, curr) => {
    return (acc += curr.amount);
  }, 0);

  // functions

  function showForm() {
    isFormOpen = true;
  }

  function hideForm() {
    isFormOpen = false;
    setName = "";
    setAmount = null;
    setId = null;
  }

  function removeExpense(id) {
    expenses = expenses.filter((item) => item.id !== id);
    setLocalStorage();
  }

  function clearAllExpenses() {
    expenses = [];
    setLocalStorage();
  }

  function addExpense({ name, amount }) {
    let expense = { id: Math.random() * Date.now(), name, amount };
    expenses = [expense, ...expenses];
    setLocalStorage();
  }

  function setModifiedExpense(id) {
    let expense = expenses.find((item) => item.id === id);
    setId = expense.id;
    setName = expense.name;
    setAmount = expense.amount;
    showForm();
  }

  function editExpense({ name, amount }) {
    expenses = expenses.map((item) => {
      return item.id === setId ? { ...item, name, amount } : { ...item };
    });
    setId = null;
    setAmount = null;
    setName = "";
    setLocalStorage();
  }
  //context

  setContext("remove", removeExpense);
  setContext("modify", setModifiedExpense);

  // local storage

  function setLocalStorage() {
    localStorage.setItem("expenses", JSON.stringify(expenses));
  }

  onMount(() => {
    expenses = localStorage.getItem("expenses")
      ? JSON.parse(localStorage.getItem("expenses"))
      : [];
  });

  // afterUpdate(() => {
  //   setLocalStorage();
  // });
</script>

<Navbar {showForm} />
<main class="content">
  {#if isFormOpen}
    <Modal >
      <ExpenseForm
        {addExpense}
        name={setName}
        amount={setAmount}
        {isEditing}
        {editExpense}
        {hideForm}
      />
    </Modal>
  {/if}
  <Totals title="total expenses" {total} />
  <ExpensesList {expenses} />
  <button
    type="button"
    class="btn btn-primary btn-block"
    on:click={clearAllExpenses}
  transition:slide>
    Clear All Expenses
  </button>
</main>
