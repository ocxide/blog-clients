<script lang="ts">
	import type { ReplyCreate } from 'backend/replies';
	import type { User } from 'auth';
	import ReactBar from '@/components/bars/ReactBar.svelte';
	import ReplyForm from '@/components/replies/components/ReplyForm.svelte';
	import { createEventDispatcher } from 'svelte';

	const enum LooseLoad {
		None,
		Hidden,
		Shown
	}

	const dispatch = createEventDispatcher<{
		repliesCreation: ReplyCreate;
	}>();

	export let user: User | null = null;

	let toggled = LooseLoad.None;

	let isReplying = false;

	export let hasReplies = false;

	const handleSubmit = ({ detail: event }: CustomEvent<ReplyCreate>) => {
		const canContinue = dispatch('repliesCreation', event, { cancelable: true });
		if (!canContinue) return;
		hasReplies = true;
		isReplying = false;
		toggled = LooseLoad.Shown;
	};

	const handleRepliesToggle = ({ detail: checked }: CustomEvent<boolean>) => {
		toggled = checked ? LooseLoad.Shown : LooseLoad.Hidden;
	};
</script>

<ReactBar
	{hasReplies}
	repliesChecked={toggled === LooseLoad.Shown}
	canAnswer={user !== null}
	on:repliesToggle={handleRepliesToggle}
	on:replyClick={() => (isReplying = true)}
/>

<div class="ml-10">
	{#if user && isReplying}
		<ReplyForm on:cancel={() => (isReplying = false)} on:submit={handleSubmit} />
	{/if}

	<ul class:hidden={toggled !== LooseLoad.Shown}>
		<slot name="repliesCreation" />

		{#if toggled !== LooseLoad.None}
			<slot name="replies" />
		{/if}
	</ul>
</div>
