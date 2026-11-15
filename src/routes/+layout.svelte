<script lang="ts">
	import './layout.css';
	import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();
</script>

<svelte:head>
	<title>Chris Windsor · software engineer</title>
	<link rel="icon" type="image/svg+xml" href={favicon} />
</svelte:head>
{@render children()}
