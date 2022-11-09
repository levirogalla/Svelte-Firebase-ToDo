<script>
    import AddTodo from '../lib/components/AddTodo.svelte';
    import ManageToDo from '../lib/components/ManageToDo.svelte';
    import UserStore from '../lib/stores/UserStore';
</script>

<div class="main">
    {#if $UserStore}
        <AddTodo />
        <ManageToDo />
    {:else}
        <p>Please sign up or login</p>
    {/if}
</div>

<style>
    h1 {
        text-align: center;
        padding: 10px;
    }
    .main {
        width: 500px;
        max-width: 80%;
        margin: auto;
        margin-top: 20px;
    }
</style>
