<script lang="ts">
	import { OutlineFormField } from 'ui/form-field';
	import { login } from 'auth';
	import type { FormEventHandler } from 'svelte/elements';

	export let redirectUrl = '/';

	let invalid = false;

	const handleLogin = () => {
		window.location.href = redirectUrl;
	};

	const onSubmit: FormEventHandler<HTMLFormElement> = (event) => {
		const formData = new FormData(event.currentTarget);
		const username = formData.get('username')?.toString() ?? '';
		const password = formData.get('password')?.toString() ?? '';

		login(username, password)
			.then(handleLogin)
			.catch(() => (invalid = true));
	};
</script>

<article class="card card-padding">
	<h1>Sign in</h1>

	<hr class="my-2" />

	<form method="POST" class="mb-6 grid gap-4" on:submit|preventDefault={onSubmit}>
		<OutlineFormField>
			<svelte:fragment slot="label">Username</svelte:fragment>
			<input type="text" name="username" required />
		</OutlineFormField>

		<OutlineFormField>
			<svelte:fragment slot="label">Password</svelte:fragment>
			<input type="password" name="password" required />
		</OutlineFormField>

		{#if invalid}
			<p class="text-warning">Username or password invalid.</p>
		{/if}

		<button type="submit" class="button raised primary w-full">Sign in</button>
	</form>

	<footer>
		<p>
			Don't have an account?
			<a href="/auth/register" class="link">Register</a>
		</p>
	</footer>
</article>
