<script setup>
    import Header from './components/Header.vue';
    import Balance from './components/Balance.vue';
    import IncomeExpenses from './components/IncomeExpenses.vue';
    import TransactionList from './components/TransactionList.vue';
    import AddTransaction from './components/AddTransaction.vue';

    import { useToast } from 'vue-toastification';

    import { ref, computed, onMounted } from 'vue';

    const toast = useToast();

    const transactions = ref([]);

    onMounted(() => {
        const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

        if (savedTransactions)
        {
            transactions.value = savedTransactions;
        }
    });

    // Get total:
    const total = computed(() => {
        return transactions.value.reduce((accumulator, transaction) => {
            return accumulator + transaction.amount;
        }, 0);
    });

    // Get income:
    const income = computed(() => {
        return transactions.value
        .filter((transaction) => transaction.amount > 0)
        .reduce((accumulator, transaction) => {
            return accumulator + transaction.amount;
        }, 0)
        .toFixed(2);
    });

    // Get expenses:
    const expenses = computed(() => {
        return transactions.value
        .filter((transaction) => transaction.amount < 0)
        .reduce((accumulator, transaction) => {
            return accumulator + transaction.amount;
        }, 0)
        .toFixed(2);
    });

    // Add transaction:
    const handleTransactionSubmitted = (transactionData) => {
        transactions.value.push({
            id: generateUniqueId(),
            text: transactionData.text,
            amount: transactionData.amount
        });

        saveTransactionsToLocalStorage();

        toast.success('Transaction added succesfully!')
    };

    // Generate unique ID:
    const generateUniqueId = () => {
        return Math.floor(Math.random() * 1000000);
    };

    // Delete transaction:
    const handleTransactionDeleted = (id) => {
        transactions.value = transactions.value.filter((transaction) => transaction.id !== id);

        saveTransactionsToLocalStorage();

        toast.success('Transaction removed successfully!');
    ;}

    // Save transaction to local storage:
    const saveTransactionsToLocalStorage = () => {
        localStorage.setItem('transactions', JSON.stringify(transactions.value));
    }

    /*
    export default
    {
        components: 
        {
            Header,
            Balance,
            IncomeExpenses,
            TransactionList,
            AddTransaction,
        }
    }
    */
</script>

<template>
    <Header></Header>

    <div class="container">
        <Balance :total="+total">

        </Balance>

        <IncomeExpenses :income="+income" :expenses="+expenses">

        </IncomeExpenses>

        <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted">

        </TransactionList>

        <AddTransaction @transactionSubmitted="handleTransactionSubmitted">

        </AddTransaction>
    </div>
</template>