<script>
	import { goto } from '$app/navigation';
	import { tabs } from '$lib/utils/tabs';
	import { onMount } from 'svelte';

	let outOfDate = false;

	onMount(async () => {
		const res = await fetch('/api/checkVersion', {compress: true})
		const needUpdate = await res.json();
		outOfDate = needUpdate;
	})

	const year = new Date().getFullYear();

	let el, elNeedUpdate, innerWidth, footerHeight, footerHeightNeedUpdate;

	const resize = (w) => {
		const top = el?.getBoundingClientRect() ? el?.getBoundingClientRect().top  : 0;
		const bottom = el?.getBoundingClientRect() ? el?.getBoundingClientRect().bottom  : 0;
		const topNeedUpdate = elNeedUpdate?.getBoundingClientRect() ? elNeedUpdate?.getBoundingClientRect().top  : 0;
		const bottomNeedUpdate = elNeedUpdate?.getBoundingClientRect() ? elNeedUpdate?.getBoundingClientRect().bottom  : 0;

		footerHeight = bottom - top;
		footerHeightNeedUpdate = bottomNeedUpdate - topNeedUpdate;
	}

    $: resize(innerWidth);
</script>

<svelte:window bind:innerWidth={innerWidth} />

<style>
	footer {
		background-color: var(--f8f8f8);
		width: 100%;
        display: block;
        position: absolute;
        bottom: 0;
		z-index: 1;
		border-top: 1px solid #920505;
		padding: 30px 0 60px;
		text-align: center;
		color: #777;
	}

	#navigation {
		margin: 0 0 2em;
	}

	#navigation ul {
		margin: 0;
		padding: 0;
	}

	#navigation ul li {
		list-style-type: none;
		display: inline;
	}

	#navigation li:not(:first-child):before {
		content: " | ";
	}

	.navLink {
		display: inline-block;
		cursor: pointer;
		padding: 6px 10px;
	}

	.navLink:hover {
		color: #920505;
	}

	.updateNotice {
		color: var(--g999);
		font-style: italic;
		font-size: 0.8em;
		margin-top: 0;
	}

	.invisible {
		visibility: hidden;
		pointer-events: none;
	}
</style>

<div class="footerSpacer" style="height: {outOfDate ? footerHeightNeedUpdate : footerHeight}px;" />

<!-- footer with update notice -->
<footer class="{outOfDate ? '' : 'invisible'}" bind:this={elNeedUpdate}>
	<p class="updateNotice">There is an update available for your League Page. <a href="https://github.com/nmelhado/league-page/blob/master/TRAINING_WHEELS.md#iv-updates">Follow the Update Instructions</a> to get all of the newest features!</p>
	<div id="navigation">
		<ul>
			{#each tabs as tab}
				{#if !tab.nest}
					<li><div class="navLink" on:click={() => goto(tab.dest)}>{tab.label}</div></li>
				{:else}
					{#each tab.children as child}
						<li><div class="navLink" on:click={() => goto(child.dest)}>{child.label}</div></li>
					{/each}
				{/if}
			{/each}
		</ul>
	</div>
	<!-- PLEASE DO NOT REMOVE THE COPYRIGHT -->
	<span class="copyright">&copy; 2021 - {year} <a href="https://github.com/nmelhado/league-page">League Page</a></span>
	<br />
</footer>

<footer class="{!outOfDate ? '' : 'invisible'}" bind:this={el}>
	<div id="navigation">
		<ul>
			{#each tabs as tab}
				{#if !tab.nest}
					<li><div class="navLink" on:click={() => goto(tab.dest)}>{tab.label}</div></li>
				{:else}
					{#each tab.children as child}
						<li><div class="navLink" on:click={() => goto(child.dest)}>{child.label}</div></li>
					{/each}
				{/if}
			{/each}
		</ul>
	</div>
	<!-- PLEASE DO NOT REMOVE THE COPYRIGHT -->
	<span class="copyright">&copy; 2021 - {year} <a href="https://github.com/nmelhado/league-page">League Page</a></span>
	<br />
</footer>
