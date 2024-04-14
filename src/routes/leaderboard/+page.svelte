<script lang="ts">
	import supabase from '$lib/supabase';
	import Glass from '$lib/components/Glass.svelte';
	import Section from '$lib/components/Section.svelte';
	import { onMount } from 'svelte';
	import { base } from '$app/paths';
	import Loading from '$lib/components/Loading.svelte';

	// Github bot server caches a local list of the leaderboard.
	// Frontend pings that server for the leaderboard.
	// The frontend sorts the list, not the server.
	// REMEMBER TO CACHE JSON
	let teams = true;
	let loading = true;

	async function loadTeams() {
		loading = true;
		const { data, error } = await supabase.from('leaderboard').select('name, points');
		if (error) console.log('Error Fetching Teams:', error.message);
		if (data.length) leaderboard = data.sort((a, b) => a.points - b.points); // Sort by points
		loading = false;
	}

	async function loadIdeas() {
		loading = true;
		const { data, error } = await supabase.from('ideas').select('title, votes, url');
		if (error) console.log('Error Fetching Ideas:', error.message);
		if (data.length) leaderboard = data.sort((a, b) => a.votes - b.votes); // Sort by votes
		loading = false;
	}

	onMount(async () => {
		loadTeams();
		resize();
	});

	let arr = [1, 0, 2];
	let leaderboard: any = [];
	function resize() {
		arr = innerWidth > 768 ? [1, 0, 2] : [0, 1, 2];
	}
	async function toggle(e: Event) {
		teams = e.target.getAttribute('data-create') == 'false';
		if (!teams) {
			loadIdeas();
		} else {
			loadTeams();
		}
	}
</script>

<svelte:window on:resize={resize} />

<Loading {loading}>
	<img
		src="{base}/assets/Asset 2.png"
		class="gradient absolute left-1/2 top-1/2 z-[-100] hidden h-screen translate-x-[-50%] translate-y-[-50%] md:block"
		alt="Gradient"
	/>
	<Section>
		<div class="m-auto max-w-3xl space-y-8">
			<div class="flex flex-col space-y-4 md:flex-row md:space-x-4 md:space-y-0">
				{#each arr as i}
					<Glass class="!px-6 !pb-5 !pt-10 {i == 0 ? 'md:-translate-y-6' : ''}">
						<div
							class="flex h-full w-full flex-row-reverse items-center justify-between space-x-4 text-center text-foreground md:flex-col md:space-x-0 md:space-y-4"
						>
							<h4>{leaderboard[i].name}</h4>
							<div
								class="podium flex aspect-square h-full max-h-32 flex-col justify-center rounded-[35px] max-md:before:!m-0 md:w-full md:max-w-32"
								id="podium{i + 1 + ''}"
							>
								<h4 class="text-5xl">{i + 1}</h4>
							</div>
						</div>
					</Glass>
				{/each}
				<style lang="postcss">
					.podium {
						background: #000;
						background-clip: padding-box;
						border: solid 8px transparent;

						position: relative;
						box-sizing: border-box;

						&:before {
							content: '';
							position: absolute;
							top: 0;
							right: 0;
							bottom: 0;
							left: 0;
							z-index: -1;
							margin: -8px;
							border-radius: inherit;
							background: