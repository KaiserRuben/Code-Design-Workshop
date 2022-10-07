<script>
    import {onMount} from "svelte";

    import Modal, { getModal } from '$lib/components/modal.svelte';

    let todos = [
        {title: 'Code+Design Camp', text: "Geniale Millionenidee entwickeln"},
        {title: 'Frankreich', text: "Parisurlaub mit der Familie: 12.00 Abfahrt in Frankfurt"},
        {title: 'Müll rausbringen', text: "Restmüll rausbringen, extrem wichtig, sonst isst ihn die Katze."}
    ];
    let neuesTodo = {title: '', text: ''};

    function addTodo() {
        todos = [...todos, neuesTodo];
        neuesTodo = {title: '', text: ''};
        getModal('hinzufügen').close();
    }
    function deleteTodo(index) {
        todos.splice(index, 1)
        todos = [...todos];
    }
    onMount(() => {
        console.log("Hello World");
    });
</script>
<svelte:head>
    <title>#ToDo</title>
</svelte:head>

<div class="container">
    <div class="title-container">
        <h1 class="title"><span class="accent">#Todo</span> - deine ToDos</h1>
    </div>
    {#each todos as todo, i}
        <div class="todo-container">
            <div class="header-delete-container"><h2 class="todo-title"> {todo.title}</h2> <img src="delete.svg" alt="delete" on:click={() => deleteTodo(i)} class="delete-button"></div>
            <p class="todo-content">{todo.text}</p>
        </div>
    {/each}
    <button class="hinzufügen" on:click={() => getModal("hinzufügen").open()}>hinzufügen</button>
</div>

<Modal id="hinzufügen">
    <div class="logInModal">
        <h2>Füge ein ToDo hinzu</h2>
        <form on:submit|preventDefault={addTodo}>
            <input type="text" placeholder="Titel" bind:value={neuesTodo.title} />
            <input type="text" placeholder="Text" bind:value={neuesTodo.text} />
            <button type="submit" style="transition: 0s">Hinzufügen</button>
        </form>
    </div>
</Modal>

<style>
    .container {
        width: 100%;
        height: 100vh;
        display: flex;
        justify-content: center;
        align-items: center;
        flex-direction: column;
    }

    .title {
        font-weight: 800;
    }

    .todo-container {
        background: #2b2b2b;
        padding: 30px 20px;
        margin: 20px;
        border-radius: 20px;
        width: 600px;
    }

    .todo-title {
        margin-top: 0;
        margin-bottom: 0;
        font-size: 1.5em;
        font-weight: 400;
    }

    .todo-content {
        margin-bottom: 0;
        font-size: 1em;
        font-weight: 200;
    }
    .header-delete-container{
        display: flex;
        justify-content: space-between;
        align-items: flex-start;
    }
    .delete-button{
        height: 30px;
    }
    .delete-button:hover{
        cursor: pointer;
    }

    .accent {
        color: #85F35E;
    }
</style>