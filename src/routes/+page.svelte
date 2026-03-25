<script lang="ts">
	import gradientBg from '$lib/assets/gradient-bg.jpg';
	import githubLogo from '$lib/assets/github.svg';
	import linkedinLogo from '$lib/assets/linkedin.svg';

	const navItems = [
		{ name: 'GitHub', href: 'https://github.com/chris-windsor' },
		{ name: 'LinkedIn', href: 'https://linkedin.com/in/chris-windsor' },
		{ name: 'Email', href: 'mailto:hey@chris.software' }
	];
</script>

<div class="fixed top-0 left-0 h-full w-2 md:w-3">
	<img src={gradientBg} alt="" class="h-full w-full object-cover" />
</div>
<div class="ml-2 md:ml-3">
	<nav class="sticky top-0 z-50 border-b border-border bg-background/80 backdrop-blur-sm">
		<div class="mx-auto flex max-w-3xl items-center justify-between px-6 py-4 md:px-12">
			<a
				href="https://chris.software"
				class="hover:text-accent-foreground font-mono text-sm text-foreground transition-colors"
			>
				chris.software
			</a>
			<div class="flex items-center gap-6">
				{#each navItems as item}
					<a
						href={item.href}
						target="_blank"
						rel="noopener noreferrer"
						class="flex h-6 w-6 items-center justify-center"
						aria-label={item.name}
					>
						{#if item.name === 'GitHub'}
							<img src={githubLogo} alt="GitHub logo" />
						{:else if item.name === 'LinkedIn'}
							<img src={linkedinLogo} alt="LinkedIn logo" />
						{:else if item.name === 'Email'}
							<svg
								xmlns="http://www.w3.org/2000/svg"
								width="24"
								height="24"
								viewBox="0 0 24 24"
								fill="none"
								stroke="currentColor"
								stroke-width="2"
								stroke-linecap="round"
								stroke-linejoin="round"
								><rect width="20" height="16" x="2" y="4" rx="2" /><path
									d="m22 7-8.97 5.7a1.94 1.94 0 0 1-2.06 0L2 7"
								/></svg
							>
						{/if}
					</a>
				{/each}
			</div>
		</div>
	</nav>
	<div class="mx-auto max-w-3xl px-6 py-12 md:px-12 md:py-24">
		<section class="pt-8 md:pt-16">
			<h1 class="text-4xl font-medium tracking-tight text-balance text-foreground md:text-5xl">
				Chris Windsor
			</h1>
			<p class="mt-2 text-lg text-muted-foreground md:text-xl">Software Engineer</p>
			{@render aboutParagraph(`i started tinkering with computers at age 5
				and started programming at age 9 on a Raspberry Pi. i have spent tens of thousands of
				hours since then building whatever my heart desired.`)}
			{@render aboutParagraph(
				`my dream is to use the full potential of my skills in combination with aviation or performance cars.`
			)}
		</section>
		<footer class="mt-24 border-t border-border pt-8 pb-12">
			<p class="text-sm text-muted-foreground">published 2026</p>
		</footer>
	</div>
</div>

{#snippet aboutParagraph(content: string)}
	<p class="mt-6 max-w-xl text-base leading-relaxed text-muted-foreground md:text-lg">
		{content}
	</p>
{/snippet}
