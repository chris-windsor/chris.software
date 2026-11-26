<script lang="ts">
	import gradientBg from '$lib/assets/gradient-bg.jpg';
	import githubLogo from '$lib/assets/github.svg';
	import linkedinLogo from '$lib/assets/linkedin.svg';

	const navItems = [
		{ name: 'GitHub', href: 'https://github.com/chris-windsor' },
		{ name: 'LinkedIn', href: 'https://linkedin.com/in/chris-windsor' },
		{ name: 'Email', href: 'mailto:hey@chris.software' }
	];

	type Project = {
		name: string;
		href: string;
		appStore?: string;
		description: string;
	};

	const projects: Project[] = [
		{
			name: 'reaerolize',
			href: 'https://reaerolize.com',
			appStore: 'https://apps.apple.com/us/app/reaerolize/id6830252554',
			description: 'aero data dashboard and tools for enthusiasts'
		},
		{
			name: 'windowseat.photos',
			href: 'https://windowseat.photos',
			appStore: 'https://apps.apple.com/us/app/windowseat-photos/id6838129154',
			description: 'social website to share pictures taken from airplanes'
		},
		{
			name: 'dinersaurs',
			href: 'https://dinersaurs.com',
			appStore: 'https://apps.apple.com/us/app/dinersaurs/id6838470978?platform=tv',
			description: 'couch co-op cooking game for Apple TV, played with your phone'
		},
		{
			name: 'topo.tools',
			href: 'https://topo.tools',
			description: 'toolset for researchers to query and scan maps and imagery simultaneously'
		}
	];
</script>

<div class="fixed top-0 left-0 h-full w-2 md:w-3">
	<img src={gradientBg} alt="" class="h-full w-full object-cover" />
</div>
<div class="ml-2 md:ml-3">
	<nav class="sticky top-0 z-50 border-b border-border bg-background/80 backdrop-blur-sm">
		<div class="mx-auto flex max-w-3xl items-center justify-between px-6 py-4 md:px-12">
			<a href="https://chris.software" class="font-mono text-sm text-foreground">chris.software</a>
			<div class="flex items-center gap-6">
				{#each navItems as item (item.name)}
					<a
						href={item.href}
						target="_blank"
						rel="noopener noreferrer"
						class="flex h-5 w-5 items-center justify-center opacity-70 transition-opacity hover:opacity-100"
						aria-label={item.name}
					>
						{#if item.name === 'GitHub'}
							<img src={githubLogo} alt="GitHub logo" />
						{:else if item.name === 'LinkedIn'}
							<img src={linkedinLogo} alt="LinkedIn logo" />
						{:else if item.name === 'Email'}
							<svg
								xmlns="http://www.w3.org/2000/svg"
								width="20"
								height="20"
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

	<main class="mx-auto max-w-3xl px-6 pt-8 pb-4 md:px-12">
		<section>
			<h1 class="text-4xl font-medium tracking-tight text-balance text-foreground md:text-5xl">
				Chris Windsor
			</h1>
			<p class="mt-2 font-mono text-sm text-muted-foreground">software engineer</p>
			{@render aboutParagraph(`i started tinkering with computers at age 5
				and started programming at age 9 on a Raspberry Pi. i have spent tens of thousands of
				hours since then building whatever my heart desired.`)}
			{@render aboutParagraph(
				`my dream is to use the full potential of my skills in combination with aviation or performance cars.`
			)}
		</section>

		<section class="mt-8" aria-labelledby="projects">
			<h2 id="projects" class="pb-2 font-mono text-xs text-muted-foreground">prized projects</h2>

			<ul>
				{#each projects as project (project.name)}
					<li class="border-t border-border py-2.5">
						<div class="min-w-0">
							<div class="flex items-baseline gap-3">
								<a
									href={project.href}
									target="_blank"
									rel="noopener noreferrer"
									class="font-medium text-foreground underline-offset-4 hover:underline"
								>
									{project.name}<span
										class="ml-1 inline-block text-xs text-muted-foreground"
										aria-hidden="true">↗</span
									>
								</a>
								{#if project.appStore}
									<a
										href={project.appStore}
										target="_blank"
										rel="noopener noreferrer"
										class="font-mono text-xs text-muted-foreground underline-offset-4 hover:text-foreground hover:underline"
									>
										app store ↗
									</a>
								{/if}
							</div>
							<p class="mt-1 text-sm leading-relaxed text-muted-foreground">
								{project.description}
							</p>
						</div>
					</li>
				{/each}
			</ul>
		</section>

		<footer class="mt-5 border-t border-border pt-3">
			<p class="font-mono text-xs text-muted-foreground">published 2026</p>
		</footer>
	</main>
</div>

{#snippet aboutParagraph(content: string)}
	<p class="mt-4 max-w-xl text-base leading-relaxed text-muted-foreground md:text-lg">
		{content}
	</p>
{/snippet}
