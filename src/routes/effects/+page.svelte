<svelte:head>
	<title>Effects | Baseline</title>
	<meta name="description" content="How Baseline's effect system tracks side effects in the type system." />
</svelte:head>

<h1>Effects</h1>
<p class="lead">
	Side effects are declared in the type signature. The compiler enforces it.
</p>

<section id="what">
	<h2>What Are Effects?</h2>
	<p>
		In most languages, any function can quietly read files, hit the network,
		or write to a database. You find out by reading the source, the docs, or
		the stack trace after something breaks.
	</p>
	<p>
		In Baseline, the <code>!</code> suffix marks a function as effectful, and
		the curly braces say which effects it uses:
	</p>
	<pre><code>{@html `<span class="kw">fn</span> <span class="fn">greet!</span><span class="punct">(</span><span class="fn">name</span><span class="op">:</span> <span class="type">String</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="effect">{Console}</span> <span class="type">Unit</span> <span class="op">=</span>
  <span class="type">Console</span><span class="punct">.</span><span class="fn">print!</span><span class="punct">(</span><span class="str">"Hello, \${name}!"</span><span class="punct">)</span>`}</code></pre>
	<p>
		Try calling <code>Http.get!</code> from here and the compiler rejects
		it — <code>{"{Http}"}</code> isn't in the effect set.
	</p>
</section>

<section id="pure">
	<h2>Pure vs Effectful</h2>
	<p>
		Functions without <code>!</code> are pure. They can't call anything effectful.
		The compiler guarantees this.
	</p>
	<pre><code>{@html `<span class="comment">-- Pure: no effects, can be called from anywhere</span>
<span class="kw">fn</span> <span class="fn">add</span><span class="punct">(</span><span class="fn">a</span><span class="op">:</span> <span class="type">Int</span><span class="punct">,</span> <span class="fn">b</span><span class="op">:</span> <span class="type">Int</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">Int</span> <span class="op">=</span> <span class="fn">a</span> <span class="op">+</span> <span class="fn">b</span>

<span class="comment">-- Effectful: requires {Console}</span>
<span class="kw">fn</span> <span class="fn">log_add!</span><span class="punct">(</span><span class="fn">a</span><span class="op">:</span> <span class="type">Int</span><span class="punct">,</span> <span class="fn">b</span><span class="op">:</span> <span class="type">Int</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="effect">{Console}</span> <span class="type">Int</span> <span class="op">=</span>
  <span class="type">Console</span><span class="punct">.</span><span class="fn">print!</span><span class="punct">(</span><span class="str">"\${a} + \${b}"</span><span class="punct">)</span>
  <span class="fn">a</span> <span class="op">+</span> <span class="fn">b</span>

<span class="comment">-- Multiple effects: must declare all of them</span>
<span class="kw">fn</span> <span class="fn">fetch_and_log!</span><span class="punct">(</span><span class="fn">url</span><span class="op">:</span> <span class="type">String</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="effect">{Http, Console}</span> <span class="type">String</span> <span class="op">=</span>
  <span class="kw">let</span> <span class="fn">body</span> <span class="op">=</span> <span class="type">Http</span><span class="punct">.</span><span class="fn">get!</span><span class="punct">(</span><span class="fn">url</span><span class="punct">)</span>
  <span class="type">Console</span><span class="punct">.</span><span class="fn">print!</span><span class="punct">(</span><span class="fn">body</span><span class="punct">)</span>
  <span class="fn">body</span>`}</code></pre>
</section>

<section id="builtins">
	<h2>Built-in Effects</h2>
	<table>
		<thead>
			<tr><th>Effect</th><th>Capability</th><th>Notes</th></tr>
		</thead>
		<tbody>
			<tr><td><code>Console</code></td><td>Terminal I/O</td><td>print, read</td></tr>
			<tr><td><code>Http</code></td><td>Network requests and servers</td><td></td></tr>
			<tr><td><code>Fs</code></td><td>Filesystem read/write</td><td></td></tr>
			<tr><td><code>Random</code></td><td>Random number generation</td><td>Must declare</td></tr>
			<tr><td><code>Env</code></td><td>Environment variables</td><td></td></tr>
			<tr><td><code>Sqlite</code></td><td>Sqlite database</td><td>Requires server prelude</td></tr>
			<tr><td><code>Postgres</code></td><td>Postgres database</td><td>Requires server prelude</td></tr>
			<tr><td><code>Mysql</code></td><td>Mysql database</td><td>Requires server prelude</td></tr>
			<tr><td><code>Log</code></td><td>Structured logging</td><td>Ambient (no declaration needed)</td></tr>
			<tr><td><code>Time</code></td><td>Clock and timing</td><td>Ambient (no declaration needed)</td></tr>
		</tbody>
	</table>
	<p>
		<code>Log</code> and <code>Time</code> are <strong>ambient</strong> — you don't
		need to declare them. Everything else must appear in the effect set.
	</p>
</section>

<section id="inference">
	<h2>Effect Inference</h2>
	<p>
		If you leave off the effect annotation, the compiler infers it from the
		function body. Only explicit annotations get checked against.
	</p>
	<pre><code>{@html `<span class="comment">-- No annotation: effects inferred as {Console}</span>
<span class="kw">fn</span> <span class="fn">helper!</span><span class="punct">(</span><span class="fn">msg</span><span class="op">:</span> <span class="type">String</span><span class="punct">)</span> <span class="op">=</span>
  <span class="type">Console</span><span class="punct">.</span><span class="fn">print!</span><span class="punct">(</span><span class="fn">msg</span><span class="punct">)</span>

<span class="comment">-- Explicit annotation: compiler checks it</span>
<span class="kw">fn</span> <span class="fn">main!</span><span class="punct">()</span> <span class="op">-&gt;</span> <span class="effect">{Console}</span> <span class="type">Unit</span> <span class="op">=</span>
  <span class="fn">helper!</span><span class="punct">(</span><span class="str">"hello"</span><span class="punct">)</span>`}</code></pre>
	<p>
		In practice: skip annotations on internal helpers, add them at public
		boundaries.
	</p>
</section>

<section id="transitive">
	<h2>Transitive Effects</h2>
	<p>
		Effects bubble up. If <code>foo!</code> calls <code>bar!</code>, then
		<code>foo</code> must declare everything <code>bar</code> uses too.
	</p>
	<pre><code>{@html `<span class="kw">fn</span> <span class="fn">bar!</span><span class="punct">()</span> <span class="op">-&gt;</span> <span class="effect">{Http}</span> <span class="type">String</span> <span class="op">=</span>
  <span class="type">Http</span><span class="punct">.</span><span class="fn">get!</span><span class="punct">(</span><span class="str">"/data"</span><span class="punct">)</span>

<span class="comment">-- Must include {Http} because bar! requires it</span>
<span class="kw">fn</span> <span class="fn">foo!</span><span class="punct">()</span> <span class="op">-&gt;</span> <span class="effect">{Http, Console}</span> <span class="type">Unit</span> <span class="op">=</span>
  <span class="kw">let</span> <span class="fn">data</span> <span class="op">=</span> <span class="fn">bar!</span><span class="punct">()</span>
  <span class="type">Console</span><span class="punct">.</span><span class="fn">print!</span><span class="punct">(</span><span class="fn">data</span><span class="punct">)</span>`}</code></pre>
</section>

<section id="sandboxing">
	<h2>AI Agent Sandboxing</h2>
	<p>
		You can use effects to sandbox AI-generated code. Grant
		<code>{"{Console}"}</code> for output but withhold <code>{"{Fs}"}</code>
		and <code>{"{Http}"}</code>. If the generated code tries to use an
		effect you didn't grant, the compiler rejects it before anything runs.
	</p>
</section>
