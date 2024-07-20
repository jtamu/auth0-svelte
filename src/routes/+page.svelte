<script lang="ts">
	import { Auth0Client, createAuth0Client, User } from '@auth0/auth0-spa-js';
	import { onMount } from 'svelte';

	let auth0: Auth0Client;
	let user: User | undefined;

	onMount(async () => {
		const response = await fetch('/auth_config.json');
		const config = await response.json();

		auth0 = await createAuth0Client({
			domain: config.domain,
			clientId: config.clientId,
			authorizationParams: {
				redirect_uri: 'http://localhost:5173'
			}
		});
	});

	async function login() {
		await auth0.loginWithPopup();
		user = await auth0.getUser();
	}

	async function logout() {
		auth0.logout();
	}
</script>

<h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>

{#if !user}
	<button id="btn-login" on:click={login}>Log in</button>
{:else}
	<img src={user.picture} alt={user.name} />
	<p>{user.name}</p>
	<p>{user.email}</p>
	<button id="btn-logout" on:click={logout}>Log out</button>
{/if}
