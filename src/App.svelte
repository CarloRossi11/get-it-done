<svelte:head>
	<title>Get It Done</title>
</svelte:head>

<script>
	import Card   from './components/Card.svelte'
	import Modal  from './components/Modal.svelte'
	import Header from './components/Header.svelte'
	import Footer from './components/Footer.svelte'
	import Button from './components/Button.svelte'
	import Loader from './components/Loader.svelte'
	import { fade, slide } from 'svelte/transition'

	var script = document.createElement( "script" );
    script.src='https://sdk.userbase.com/2/userbase.js';
	document.head.appendChild( script );
	
	// Init
    let userObject = null;
    const userbase = window.userbase;
    let authPromise = userbase.init({appId: 'ac7ec592-3d3f-4bde-9d83-b41fccb89f6d'})
		.then(({user}) => userObject = user)
	
	// Auth
    let username, password;
    const signIn = () => authPromise = userbase.signIn({username, password}).then(user => userObject = user);
    const signUp = () => authPromise = userbase.signUp({username, password}).then(user => userObject = user);
    const signOut = () => authPromise = userbase.signOut().then(() => userObject = null)
	
	// DB
	let todoPromise, todos = [], newTodo = '';
    const databaseName = 'todos';
    function changeHandler(items) {todos = items}
    $: if (userObject) todoPromise = userbase.openDatabase({databaseName, changeHandler})
	
	// DB Actions
	function addTodo() {
        todoPromise = userbase.insertItem({databaseName, item: newTodo})
        newTodo = '';
    }
    function deleteTodo(itemId) {
        todoPromise = userbase.deleteItem({databaseName, itemId});
    }
    function updateTodo(index) {
        const {item, itemId} = todos[index];
        todoPromise = userbase.updateItem({databaseName, itemId, item});
	}
	
	// Modal
	let showModal = false
	const toggleModal = () => {
	  showModal = !showModal
	}
</script>

<body>
<Modal {showModal} on:click={toggleModal}>Welcome to Get It Done, where  Users can sign up and log in for full CRUD capabilites on their todo lists. Built by Carlo Berardelli in December 2020 using <a href="https://svelte.dev/">Svelte</a> for the client side, and <a href="https://userbase.com/">Userbase</a> for the DB & authentication. This todo app is built to demonstrate many of the Svelte features and functionalities. These features include: slot, bind, transitions, #if, #each, #await, $:, and a pretty cool loading animation. <br> <a href="https://github.com/CarloRossi11/get-it-done">GitHub</a> <br><a href="https://portfolio-alpha-orpin.vercel.app/">Carlo's Portfolio</a>  </Modal>
<Header/>
	{#await authPromise}
	<div in:fade out:slide><Loader/></div>
	{:then _}
		{#if !userObject}
		<form in:fade >
			<label for="username">Username</label>
			<input id="username" type="text" bind:value={username}><br>
			<label for="password">Password</label>
			<input id="password" type="password" bind:value={password}><br>
			<div class="cluster">
				<Button on:click={signIn} >Sign in</Button>
				<Button on:click={signUp} >Sign up</Button>
			</div>
		</form>
		<div class="cluster"><Button type='secondary' on:click={toggleModal}>About</Button></div>
		{:else}
			<h1 in:slide>Welcome, {userObject.username}</h1>
			{#await todoPromise}{:then _}
			<label in:slide for="new-todo">Here are your todos</label><br/>
			<input in:slide id="new-todo" type="text" bind:value={newTodo}>
			<Button on:click={addTodo}>Add todo</Button><br>
			{#each todos as {item, itemId}, index}
			<Card>
				<input in:slide type="text" bind:value={todos[index].item} on:blur={() => updateTodo(index)}>
				<Button on:click={() => deleteTodo(itemId)}>X</Button><br>
			</Card>
			{/each}
			<div class="cluster"><Button inverse='true' on:click={signOut}>Log Out</Button></div>
			{:catch error} Error! {error} {/await}
		{/if}
	{:catch error} Error! {error} {/await}
</body>
<Footer/>

<style>
	body{
		text-align: center;
		color: #0e2f56;
	}
	form{
		padding-top: 30px;
	}
	.cluster{
		margin: 15px ;
	}
	h1{
		color: #d91b42;
	}
</style>