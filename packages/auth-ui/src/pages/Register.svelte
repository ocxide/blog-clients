<script lang="ts">
	import { OutlineFormField } from 'ui/form-field';
	import { register } from 'auth';
	import type { FormEventHandler } from 'svelte/elements';

	let invalid = false;
	let password = '';

	const onSubmit: FormEventHandler<HTMLFormElement> = (event) => {
		const formData = new FormData(event.currentTarget);

		const username = formData.get('username')?.toString() ?? '';
		const password = formData.get('password')?.toString() ?? '';
		const name = formData.get('name')?.toString() ?? '';
		const email = formData.get('email')?.toString() ?? '';

		register({
			username,
			password,
			name,
			email
		})
			.then(() => {
				window.location.href = '/';
			})
			.catch(() => (invalid = true));
	};

	let repeatInput: HTMLInputElement;

	function checkRepeatInput() {
		repeatInput.setCustomValidity(password === repeatInput.value ? '' : 'Passwords do not match.');
	}
</script>

	<article class="card card-padding">
		<h1>Register</h1>

		<hr class="my-2" />

		<form method="POST" class="grid gap-4 mb-6" on:submit|preventDefault={onSubmit}>
			<div class="grid gap-4 grid-cols-2">
				<OutlineFormField>
					<svelte:fragment slot="label">Username</svelte:fragment>
					<input type="text" name="username" required />
				</OutlineFormField>

				<OutlineFormField>
					<svelte:fragment slot="label">Name</svelte:fragment>
					<input type="text" name="name" />
				</OutlineFormField>
			</div>

			<OutlineFormField>
				<svelte:fragment slot="label">Email</svelte:fragment>
				<input type="email" name="email" />
			</OutlineFormField>

			<div class="grid gap-4 md:grid-cols-2">
				<OutlineFormField>
					<svelte:fragment slot="label">Password</svelte:fragment>
					<input
						type="password"
						name="password"
						required
						bind:value={password}
						on:input={() => checkRepeatInput()}
					/>
				</OutlineFormField>

				<OutlineFormField>
					<svelte:fragment slot="label">Repeat password</svelte:fragment>
					<input
						type="password"
						name="rpassword"
						required
						on:input={() => checkRepeatInput()}
						bind:this={repeatInput}
					/>
				</OutlineFormField>
			</div>

			{#if invalid}
				<p class="text-warning">Username or password invalid.</p>
			{/if}

			<button type="submit" class="button raised primary w-full">Register</button>
		</form>

		<footer>
			<p class="text-center">
				Already have an account?
				<a href="/auth/sign-in" class="link">sign in</a>
			</p>
		</footer>
	</article>
