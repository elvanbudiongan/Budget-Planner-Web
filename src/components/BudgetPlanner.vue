<script lang="ts">
import { defineComponent, ref, computed } from "vue";

interface Expense {
  name: string;
  amount: number;
  percentage: number;
}

export default defineComponent({
  name: "BudgetPlanner",
  setup() {
    // State variables
    const total = ref<number>(0);
    const balance = ref<number>(0);
    const expenses = ref<Expense[]>([]);
    const newExpense = ref<Partial<Expense>>({
      name: "",
      amount: 0,
    });

    // Update the balance when total or expenses change
    const updateBalance = () => {
      const totalExpenses = expenses.value.reduce((sum, expense) => sum + expense.amount, 0);
      balance.value = total.value - totalExpenses;
    };

    // Computed property to format the balance with two decimal places
    const formattedBalance = computed(() => balance.value.toFixed(2));

    // Check if the user can add the expense based on the remaining balance
    const canAddExpense = computed(() => {
      return newExpense.value.amount && newExpense.value.amount <= balance.value;
    });

    // Add an expense and update balance
    const addExpense = () => {
      if (canAddExpense.value && newExpense.value.name && newExpense.value.amount) {
        const percentage = (newExpense.value.amount / total.value) * 100;
        expenses.value.push({
          name: newExpense.value.name,
          amount: newExpense.value.amount,
          percentage: parseFloat(percentage.toFixed(2)),
        });
        updateBalance();
        // Reset the form fields
        newExpense.value.name = "";
        newExpense.value.amount = 0;
      }
    };

    // Remove an expense from the list
    const removeExpense = (index: number) => {
      expenses.value.splice(index, 1);
      updateBalance();
    };

    return {
      total,
      balance,
      expenses,
      newExpense,
      formattedBalance,
      canAddExpense,
      addExpense,
      removeExpense,
      updateBalance,
    };
  },
});
</script>


<template>
  <div class="budget-planner">
    <h1>Budget Planner</h1>

    <!-- Input for total budget -->
    <div>
      <label for="total">Total Budget:</label>
      <input
        v-model.number="total"
        type="number"
        id="total"
        placeholder="Enter total budget"
        @input="updateBalance"
      />
    </div>

    <!-- Display balance -->
    <h3>Balance: {{ formattedBalance }}</h3>

    <!-- Add Expense Form -->
    <form @submit.prevent="addExpense">
      <div>
        <label for="expenseName">Expense Name:</label>
        <input v-model="newExpense.name" type="text" id="expenseName" placeholder="Expense name" required />
      </div>
      <div>
        <label for="expenseAmount">Amount:</label>
        <input v-model.number="newExpense.amount" type="number" id="expenseAmount" placeholder="Amount" required />
      </div>
      <button type="submit" :disabled="!canAddExpense">Add Expense</button>

    </form>

    <!-- Display List of Expenses -->
    <h3>Expenses:</h3>
    <ul>
      <li v-for="(expense, index) in expenses" :key="index">
        {{ expense.name }} - ${{ expense.amount }} ({{ expense.percentage }}%)
        <button @click="removeExpense(index)">Remove</button>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.budget-planner {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background-color: none;
  border-radius: 8px;
  border: 1px solid green;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
form {
  margin-bottom: 20px;
}
.error {
  color: red;
}
ul {
  list-style: none;
  padding: 0;
}
ul li {
  margin-bottom: 10px;
}
button {
  margin-left: 10px;
}
</style>
