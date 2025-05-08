<script>
	import App from '$lib/App.svelte';
	import TechShould from './page-components/TechShould.svelte';
	import WhatWeAre from './page-components/WhatWeAre.svelte';
	import Footer from './page-components/Footer.svelte';
	import Opportunities from './page-components/Opportunities.svelte';
</script>

<header class="mobile-header px-8 relative z-[292929292]">
	<div class="px-2">
		<a href="/"><img src="/logos/logo.png" alt="LSG Logo" class="logo" /></a>

		<button id="hamburger-btn">
			<img src="/svgs/hamburger.svg" alt="menu" />
		</button>
	</div>

	<section
		id="mobile-menu"
		class="w-[20rem] max-w-full bg-[#353A40] fixed bottom-0 right-0 h-[calc(100dvh_-_70px)] translate-x-full z-50 transform transition-transform duration-300 ease-in-out p-8 flex flex-col gap-5 text-right"
	>
		<button id="close-btn" class="absolute top-8 left-8">
			<img src="/svgs/close-icon.svg" class="h-5 w-5" alt="close" />
		</button>
		<a class="w-full p-1 hover:bg-slate-700" href="/">Home</a>
		<a class="w-full p-1 hover:bg-slate-700" href="./about.html">Who We Are</a>
		<a class="w-full p-1 hover:bg-slate-700" href="./whatwedo.html">What We Do</a>
		<a
			class="w-full p-1 hover:bg-slate-700"
			href="https://careers.lsg.ng/"
			rel="noopener noreferrer">Join the Team</a
		>
		<a class="w-full p-1 hover:bg-slate-700" href="./contactUs.html">Contact Us</a>
	</section>
</header>

<section id="hero-section" class="hero pointer-events-none bg-black h-[100vh] grid">
	<header class="hero-nav z-10">
		<a href="../../new.html"
			><img src="/logos/LSG LOGO PLATINUM.png" alt="LSG Logo" class="h-20 object-contain w-32" />
		</a>
		<nav class="text-lg text-white hidden py-4 lg:flex gap-12 justify-end">
			<a href="./index.html" class="nav-current">Home</a>
			<a href="./about.html">Who We Are</a>
			<a href="./whatwedo.html">What We Do</a>
			<a href="https://careers.lsg.ng/" rel="noopener noreferrer">Join The Team</a>
			<a href="./contactUs.html">Contact Us</a>
		</nav>
	</header>

	<!-- <h1 class="title text-8xl text-white z-10 pointer-events-none">Technology Beyond</h1> -->
</section>

<div class="absolute inset-0 z-0 bg-black"><App /></div>

<TechShould />

<WhatWeAre />

<Opportunities />

<Footer />

<style lang="postcss">
	.hero-nav {
		position: absolute;
		width: calc(100vw - 15.5px);

		display: grid;
		grid-template-columns: 100px 1fr;

		padding: 16px 64px;
	}

	.title {
		align-content: center;
		padding: 64px;

		max-width: 20ch;
		user-select: none;
	}

	.mobile-header {
		display: none;
	}

	.mobile-header > div {
		justify-content: space-between;
		align-items: center;
		display: flex;
	}
	.mobile-header .logo {
		height: 1.625rem;
	}

	@media (width <= 1200px) {
		.mobile-header {
			color: #fff;
			z-index: 100;
			background: #fff;
			width: 100%;
			padding-top: 1rem;
			padding-bottom: 1rem;
			display: block;
			position: absolute;
			top: 0;
			left: 0;
		}
	}
</style>
