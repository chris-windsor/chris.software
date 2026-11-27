<script lang="ts">
	import gradientBg from '$lib/assets/gradient-bg.jpg';
	import githubLogo from '$lib/assets/github.svg';
	import linkedinLogo from '$lib/assets/linkedin.svg';

	const navItems = [
		{ name: 'GitHub', href: 'https://github.com/chris-windsor' },
		{ name: 'LinkedIn', href: 'https://linkedin.com/in/chris-windsor' },
		{ name: 'Email', href: 'mailto:hey@chris.software' }
	];

	import reaerolizeIcon from '$lib/assets/projects/reaerolize-icon.jpg';
	import reaerolizeHero from '$lib/assets/projects/reaerolize-hero.jpg';
	import windowseatIcon from '$lib/assets/projects/windowseat-icon.jpg';
	import windowseatHero from '$lib/assets/projects/windowseat-hero.jpg';
	import dinersaursIcon from '$lib/assets/projects/dinersaurs-icon.jpg';
	import dinersaursHero from '$lib/assets/projects/dinersaurs-hero.jpg';

	type Hero = { kind: 'image'; src: string; alt: string } | { kind: 'gradient' };

	type Project = {
		name: string;
		href: string;
		appStore?: string;
		icon?: string;
		hero: Hero;
		platforms: string;
		/** schema.org applicationCategory, for search results */
		category: string;
		description: string;
	};

	const projects: Project[] = [
		{
			name: 'reaerolize',
			href: 'https://reaerolize.com',
			appStore: 'https://apps.apple.com/us/app/reaerolize/id6830252554',
			icon: reaerolizeIcon,
			hero: {
				kind: 'image',
				src: reaerolizeHero,
				alt: 'reaerolize: live air traffic over New York'
			},
			platforms: 'iPhone · iPad · web',
			category: 'TravelApplication',
			description: 'aero data dashboard and tools for enthusiasts'
		},
		{
			name: 'windowseat.photos',
			href: 'https://windowseat.photos',
			appStore: 'https://apps.apple.com/us/app/windowseat-photos/id6838129154',
			icon: windowseatIcon,
			hero: {
				kind: 'image',
				src: windowseatHero,
				alt: 'windowseat.photos: photos from the window seat'
			},
			platforms: 'iPhone · iPad · web',
			category: 'SocialNetworkingApplication',
			description: 'social website to share pictures taken from airplanes'
		},
		{
			name: 'dinersaurs',
			href: 'https://dinersaurs.com',
			appStore: 'https://apps.apple.com/us/app/dinersaurs/id6838470978?platform=tv',
			icon: dinersaursIcon,
			hero: {
				kind: 'image',
				src: dinersaursHero,
				alt: "Dinersaurs: cook together, don't go extinct"
			},
			platforms: 'Apple TV',
			category: 'GameApplication',
			description: 'couch co-op cooking game for Apple TV, played with your phone'
		},
		{
			name: 'topo.tools',
			href: 'https://topo.tools',
			hero: { kind: 'gradient' },
			platforms: 'web',
			category: 'ReferenceApplication',
			description: 'toolset for researchers to query and scan maps and imagery simultaneously'
		}
	];

	const siteUrl = 'https://chris.software';
	const studio = { name: 'siliwafer', url: 'https://siliwafer.com' };
	const description =
		'Chris Windsor is a software engineer and the founder of siliwafer, building reaerolize, windowseat.photos, dinersaurs and topo.tools.';

	const structuredData = {
		'@context': 'https://schema.org',
		'@graph': [
			{
				'@type': 'Person',
				'@id': `${siteUrl}/#person`,
				name: 'Chris Windsor',
				url: siteUrl,
				jobTitle: 'Software Engineer',
				worksFor: { '@id': `${studio.url}/#organization` },
				founderOf: { '@id': `${studio.url}/#organization` },
				sameAs: navItems.filter((item) => item.href.startsWith('https://')).map((item) => item.href)
			},
			{
				'@type': 'Organization',
				'@id': `${studio.url}/#organization`,
				name: studio.name,
				url: studio.url,
				founder: { '@id': `${siteUrl}/#person` }
			},
			{
				'@type': 'WebSite',
				'@id': `${siteUrl}/#website`,
				url: siteUrl,
				name: 'Chris Windsor',
				author: { '@id': `${siteUrl}/#person` }
			},
			...projects.map((project) => ({
				'@type': project.appStore ? 'SoftwareApplication' : 'WebApplication',
				name: project.name,
				url: project.href,
				description: project.description,
				operatingSystem: project.platforms,
				applicationCategory: project.category,
				offers: { '@type': 'Offer', price: 0, priceCurrency: 'USD' },
				...(project.appStore ? { installUrl: project.appStore, sameAs: [project.appStore] } : {}),
				author: { '@id': `${siteUrl}/#person` },
				publisher: { '@id': `${studio.url}/#organization` }
			}))
		]
	};
	// Escape "<" so the JSON can't close the script tag early.
	const structuredDataTag = `<script type="application/ld+json">${JSON.stringify(
		structuredData
	).replace(/</g, '\\u003c')}</${'script'}>`;

	// Desktop: the bio column swaps to the contact panel once the projects are scrolled through,
	// or when the visitor clicks "contact" (for screens tall enough that the page barely scrolls).
	let isDesktop = $state(false);
	let scrolledToContact = $state(false);
	let contactRequested = $state(false);
	const showContact = $derived(isDesktop && (scrolledToContact || contactRequested));
	let projectList: HTMLElement | undefined = $state();

	function openContact(event: MouseEvent) {
		if (!isDesktop) return; // on phones the link just jumps to the form below the projects
		event.preventDefault();
		contactRequested = true;
	}

	function closeContact() {
		contactRequested = false;
		window.scrollTo({ top: 0, behavior: 'smooth' });
	}

	$effect(() => {
		const list = projectList;
		if (!list) return;
		// Matches Tailwind's lg breakpoint, where the two-column layout starts.
		const desktop = window.matchMedia('(min-width: 64rem)');
		let frame = 0;
		const update = () => {
			frame = 0;
			const lastCard = list.lastElementChild;
			const lastCardReached =
				!!lastCard && lastCard.getBoundingClientRect().top < window.innerHeight * 0.5;
			const atBottom =
				window.innerHeight + window.scrollY >= document.documentElement.scrollHeight - 4;
			// Ignore tiny scrolls so a page that barely scrolls doesn't flip back and forth.
			isDesktop = desktop.matches;
			scrolledToContact = window.scrollY > 120 && (lastCardReached || atBottom);
		};
		const schedule = () => {
			if (!frame) frame = requestAnimationFrame(update);
		};
		update();
		window.addEventListener('scroll', schedule, { passive: true });
		window.addEventListener('resize', schedule);
		desktop.addEventListener('change', schedule);
		return () => {
			cancelAnimationFrame(frame);
			window.removeEventListener('scroll', schedule);
			window.removeEventListener('resize', schedule);
			desktop.removeEventListener('change', schedule);
		};
	});

	const contactEmail = 'hey@chris.software';
	let contactName = $state('');
	let contactMessage = $state('');
	let contactError = $state('');

	function sendMessage(event: SubmitEvent) {
		event.preventDefault();
		if (!contactMessage.trim()) {
			contactError = 'write a message first';
			return;
		}
		contactError = '';
		const subject = contactName.trim()
			? `hello from ${contactName.trim()}`
			: 'hello from chris.software';
		const href = `mailto:${contactEmail}?subject=${encodeURIComponent(subject)}&body=${encodeURIComponent(contactMessage.trim())}`;
		window.location.href = href;
	}
</script>

<svelte:head>
	<title>Chris Windsor · software engineer</title>
	<meta name="description" content={description} />
	<link rel="canonical" href={`${siteUrl}/`} />
	<meta property="og:type" content="profile" />
	<meta property="og:site_name" content="chris.software" />
	<meta property="og:url" content={`${siteUrl}/`} />
	<meta property="og:title" content="Chris Windsor · software engineer" />
	<meta property="og:description" content={description} />
	<meta property="og:image" content={`${siteUrl}/og.png`} />
	<meta property="og:image:width" content="1200" />
	<meta property="og:image:height" content="630" />
	<meta property="og:image:alt" content="Chris Windsor, software engineer" />
	<meta name="twitter:card" content="summary_large_image" />
	<meta name="twitter:title" content="Chris Windsor · software engineer" />
	<meta name="twitter:description" content={description} />
	<meta name="twitter:image" content={`${siteUrl}/og.png`} />
	<!-- eslint-disable-next-line svelte/no-at-html-tags -- static JSON built above -->
	{@html structuredDataTag}
</svelte:head>

<div class="fixed top-0 left-0 h-full w-2 md:w-3">
	<img src={gradientBg} alt="" class="h-full w-full object-cover" />
</div>
<div class="ml-2 md:ml-3">
	<nav class="sticky top-0 z-50 border-b border-border bg-background/80 backdrop-blur-sm">
		<div
			class="mx-auto flex max-w-3xl items-center justify-between px-6 py-4 md:px-12 lg:max-w-6xl xl:max-w-7xl"
		>
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

	<main
		class="mx-auto max-w-3xl px-6 pt-12 pb-12 md:px-12 md:pt-20 lg:grid lg:max-w-6xl lg:grid-cols-[minmax(0,5fr)_minmax(0,7fr)] lg:gap-16 xl:max-w-7xl xl:grid-cols-[minmax(0,4fr)_minmax(0,8fr)]"
	>
		<aside class="lg:sticky lg:top-28 lg:self-start">
			<div class="lg:grid">
				<section
					class="motion-safe:transition motion-safe:duration-500 lg:[grid-area:1/1] {showContact
						? 'lg:pointer-events-none lg:-translate-y-2 lg:opacity-0'
						: ''}"
					aria-hidden={showContact ? 'true' : undefined}
				>
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
					<p class="mt-6 max-w-xl text-base leading-relaxed text-muted-foreground md:text-lg">
						i am currently building at
						<a
							href={studio.url}
							target="_blank"
							rel="noopener"
							class="text-foreground underline-offset-4 hover:underline"
							>siliwafer<span
								class="ml-0.5 inline-block text-xs text-muted-foreground"
								aria-hidden="true">↗</span
							></a
						>, my software studio in Lehi.
					</p>
					<a
						href="#contact"
						onclick={openContact}
						class="mt-8 inline-block font-mono text-sm text-foreground underline-offset-4 hover:underline"
					>
						contact →
					</a>
				</section>

				<section
					class="hidden motion-safe:transition motion-safe:duration-500 lg:block lg:[grid-area:1/1] {showContact
						? ''
						: 'pointer-events-none translate-y-2 opacity-0'}"
					inert={!showContact}
					aria-label="contact"
				>
					{@render contactPanel('desktop')}
					<button
						type="button"
						onclick={closeContact}
						class="mt-8 font-mono text-sm text-muted-foreground underline-offset-4 hover:text-foreground hover:underline"
					>
						← back
					</button>
				</section>
			</div>
		</aside>

		<div>
			<section class="mt-16 lg:mt-0" aria-labelledby="projects">
				<h2 id="projects" class="mb-4 font-mono text-xs text-muted-foreground">prized projects</h2>

				<ul
					bind:this={projectList}
					class="grid gap-5 sm:grid-cols-2 lg:grid-cols-1 lg:gap-8 xl:grid-cols-2 xl:gap-6"
				>
					{#each projects as project (project.name)}
						<li
							class="group relative flex flex-col overflow-hidden rounded-2xl border border-border bg-background transition duration-300 focus-within:ring-2 focus-within:ring-foreground/20 hover:-translate-y-0.5 hover:shadow-lg"
						>
							<div class="relative aspect-[40/21] overflow-hidden border-b border-border">
								{#if project.hero.kind === 'image'}
									<img
										src={project.hero.src}
										alt={project.hero.alt}
										loading="lazy"
										class="h-full w-full object-cover transition duration-500 group-hover:scale-[1.03]"
									/>
								{:else}
									<div class="relative h-full w-full">
										<img
											src={gradientBg}
											alt=""
											class="h-full w-full object-cover transition duration-500 group-hover:scale-[1.03]"
										/>
										<span
											class="absolute inset-0 grid place-items-center font-mono text-2xl font-medium text-white"
										>
											{project.name}
										</span>
									</div>
								{/if}
							</div>

							<div class="flex flex-1 gap-3 p-4">
								{#if project.icon}
									<img
										src={project.icon}
										alt=""
										class="h-10 w-10 flex-none rounded-[10px] ring-1 ring-black/5"
									/>
								{:else}
									<div
										class="relative h-10 w-10 flex-none overflow-hidden rounded-[10px] ring-1 ring-black/5"
										aria-hidden="true"
									>
										<img src={gradientBg} alt="" class="h-full w-full object-cover" />
										<span
											class="absolute inset-0 grid place-items-center font-mono text-xs font-medium text-white"
											>{project.name.slice(0, 2)}</span
										>
									</div>
								{/if}
								<div class="min-w-0">
									<a
										href={project.href}
										target="_blank"
										rel="noopener noreferrer"
										class="font-medium text-foreground underline-offset-4 group-hover:underline after:absolute after:inset-0 after:content-[''] focus-visible:outline-none"
									>
										{project.name}
									</a>
									<p class="mt-0.5 text-sm leading-relaxed text-muted-foreground">
										{project.description}
									</p>
									<div class="mt-2.5 flex flex-wrap items-center gap-1.5 font-mono text-[11px]">
										{#if project.appStore}
											<a
												href={project.appStore}
												target="_blank"
												rel="noopener noreferrer"
												class="relative z-10 rounded-full bg-foreground px-2.5 py-0.5 text-background transition-opacity hover:opacity-80"
											>
												app store ↗
											</a>
										{/if}
										<span
											class="rounded-full border border-border px-2.5 py-0.5 text-muted-foreground"
										>
											{project.platforms}
										</span>
									</div>
								</div>
							</div>
						</li>
					{/each}
				</ul>
			</section>

			<section id="contact" class="mt-16 scroll-mt-24 lg:hidden" aria-label="contact">
				{@render contactPanel('mobile')}
			</section>

			<footer class="mt-16 border-t border-border pt-6">
				<p class="font-mono text-xs text-muted-foreground">published 2026</p>
			</footer>
		</div>
	</main>
</div>

{#snippet aboutParagraph(content: string)}
	<p class="mt-6 max-w-xl text-base leading-relaxed text-muted-foreground md:text-lg">
		{content}
	</p>
{/snippet}

{#snippet contactPanel(id: string)}
	<h2 class="text-3xl font-medium tracking-tight text-foreground md:text-4xl">contact</h2>
	<p class="mt-3 max-w-md text-base leading-relaxed text-muted-foreground">
		have an idea, a question about one of the apps, or just want to talk planes? send me a note.
	</p>
	<form class="mt-6 grid max-w-md gap-3" onsubmit={sendMessage} novalidate>
		<label class="grid gap-1 font-mono text-xs text-muted-foreground" for={`${id}-name`}>
			name
			<input
				id={`${id}-name`}
				bind:value={contactName}
				autocomplete="name"
				class="rounded-lg border-border bg-background font-sans text-sm text-foreground focus:border-foreground focus:ring-0"
			/>
		</label>
		<label class="grid gap-1 font-mono text-xs text-muted-foreground" for={`${id}-message`}>
			message
			<textarea
				id={`${id}-message`}
				bind:value={contactMessage}
				oninput={() => (contactError = '')}
				rows="4"
				aria-invalid={contactError ? 'true' : undefined}
				aria-describedby={contactError ? `${id}-error` : undefined}
				class="rounded-lg border-border bg-background font-sans text-sm text-foreground focus:border-foreground focus:ring-0"
			></textarea>
		</label>
		{#if contactError}
			<p id={`${id}-error`} class="font-mono text-xs text-red-600">{contactError}</p>
		{/if}
		<div class="flex flex-wrap items-center gap-3">
			<button
				type="submit"
				class="rounded-full bg-foreground px-4 py-2 font-mono text-xs text-background transition-opacity hover:opacity-80"
			>
				send ↗
			</button>
			<span class="font-mono text-xs text-muted-foreground">
				opens your mail app · or write to
				<a
					href={`mailto:${contactEmail}`}
					class="text-foreground underline-offset-4 hover:underline">{contactEmail}</a
				>
			</span>
		</div>
	</form>
{/snippet}
