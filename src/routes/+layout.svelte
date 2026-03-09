<script lang="ts">
	import '../app.css';
	import { page } from '$app/state';

	let { children } = $props();

	function isActive(href: string): boolean {
		if (href === '/') return page.url.pathname === '/';
		return page.url.pathname.startsWith(href);
	}
</script>

<header>
	<div class="container">
		<a href="/" class="wordmark">Baseline</a>
		<nav>
			<a href="/" class:active={isActive('/')}>Home</a>
			<a href="/guide" class:active={isActive('/guide')}>Quickstart</a>
			<a href="/learn" class:active={isActive('/learn')}>Learn</a>
			<a href="/api" class:active={isActive('/api')}>API</a>
			<a href="/changelog" class:active={isActive('/changelog')}>Changelog</a>
		</nav>
	</div>
</header>

<main class="container">
	{@render children()}
</main>

<footer>
	<div class="container" style="display:flex;justify-content:space-between;align-items:center">
		<span>&copy; Dawson Design Ltd.</span>
		<span>
			<a href="https://github.com/baseline-lang/baseline">GitHub</a>
			<a href="/llms.txt">llms.txt</a>
		</span>
	</div>
</footer>
