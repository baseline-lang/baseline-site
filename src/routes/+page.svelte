<svelte:head>
	<title>Baseline</title>
	<meta name="description" content="A fast, safe functional language that is easy to learn but hard to break." />
</svelte:head>

<h1>Baseline</h1>
<p class="lead">
	A fast, safe functional language that's easy to learn and hard to break,
	whether you write the code or use an AI agent.
</p>

<ul>
	<li><strong>Easy to learn:</strong> Small, consistent syntax reads the same everywhere.</li>
	<li><strong>Fast and lightweight:</strong> Compiles to native code with low memory use.</li>
	<li><strong>Safe by default:</strong> The compiler checks types, effects, and errors before running.</li>
</ul>

<pre><code>{@html `<span class="kw">fn</span> <span class="fn">fetch_user!</span><span class="punct">(</span><span class="fn">id</span><span class="op">:</span> <span class="type">Int</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="effect">{Http, Console}</span> <span class="type">Result&lt;String, String&gt;</span> <span class="op">=</span>
  <span class="type">Console</span><span class="punct">.</span><span class="fn">print!</span><span class="punct">(</span><span class="str">"Fetching user \${id}"</span><span class="punct">)</span>
  <span class="kw">let</span> <span class="fn">response</span> <span class="op">=</span> <span class="type">Http</span><span class="punct">.</span><span class="fn">get!</span><span class="punct">(</span><span class="str">"/users/\${id}"</span><span class="punct">)</span><span class="op">?</span>
  <span class="kw">match</span> <span class="fn">response</span><span class="punct">.</span><span class="fn">status</span>
    <span class="num">200</span> <span class="op">-&gt;</span> <span class="type">Ok</span><span class="punct">(</span><span class="fn">response</span><span class="punct">.</span><span class="fn">body</span><span class="punct">)</span>
    <span class="num">404</span> <span class="op">-&gt;</span> <span class="type">Err</span><span class="punct">(</span><span class="str">"User not found"</span><span class="punct">)</span>
    <span class="fn">code</span> <span class="op">-&gt;</span> <span class="type">Err</span><span class="punct">(</span><span class="str">"HTTP \${code}"</span><span class="punct">)</span>`}</code></pre>

<p>You can read this function's entire contract from the first line:</p>
<ul>
	<li>The <code>!</code> suffix on function names means they have side effects, and <code>{"{Http, Console}"}</code> declares exactly which ones. This function can talk to the network and print output, nothing else.</li>
	<li>It returns <code>Result&lt;String, String&gt;</code> instead of a bare <code>String</code>, so the caller knows this function can fail and they must handle the happy path and error cases.</li>
	<li>The <code>?</code> after <code>Http.get!</code> passes errors up to the caller automatically, so you don't write error-handling boilerplate in every function.</li>
</ul>
<p class="muted small" style="max-width: none">Remove <code>{"{Http}"}</code> from the declaration and the compiler rejects the program.</p>

<section id="ai">
	<h2>Designed for AI Agents</h2>
	<p>
		AI agents generate code fast but make subtle mistakes: invalid states,
		hidden side effects, unchecked errors. Most type systems either catch
		too little to help or require formal proofs that agents can't produce.
		Baseline finds a middle ground: types that check real constraints,
		like rejecting a port number of -1 at compile time, without needing
		formal verification expertise.
	</p>
	<p>
		Many languages let you do the same thing in several ways. They hide
		side effects in functions that look safe and rely on runtime checks
		that can be missed. That's manageable when people review every line,
		but it breaks down when agents generate code at scale.
	</p>
	<p>
		Baseline uses one syntax for each concept, so agents don't waste tokens
		choosing between equivalent alternatives. Effects appear in every
		function signature, making dependencies obvious. Constraints are checked
		before the code runs. The compiler gives structured JSON diagnostics
		that agents can parse and fix in a single pass.
	</p>
	<p class="small muted"><a href="/design">Read the full design &rarr;</a></p>

	<h3 id="types">Types as Specs</h3>
	<p>
		Refinement types let you state a constraint once. The compiler proves
		it at every call site. No runtime validation code to forget.
	</p>
	<pre><code>{@html `<span class="kw">type</span> <span class="type">Port</span> <span class="op">=</span> <span class="type">Int</span> <span class="kw">where</span> <span class="fn">self</span> <span class="op">&gt;=</span> <span class="num">1</span> <span class="op">&amp;&amp;</span> <span class="fn">self</span> <span class="op">&lt;=</span> <span class="num">65535</span>
<span class="kw">type</span> <span class="type">Percentage</span> <span class="op">=</span> <span class="type">Int</span> <span class="kw">where</span> <span class="fn">self</span> <span class="op">&gt;=</span> <span class="num">0</span> <span class="op">&amp;&amp;</span> <span class="fn">self</span> <span class="op">&lt;=</span> <span class="num">100</span>
<span class="kw">type</span> <span class="type">PositiveInt</span> <span class="op">=</span> <span class="type">Int</span> <span class="kw">where</span> <span class="fn">self</span> <span class="op">&gt;</span> <span class="num">0</span>

<span class="kw">fn</span> <span class="fn">listen</span><span class="punct">(</span><span class="fn">port</span><span class="op">:</span> <span class="type">Port</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">Unit</span> <span class="op">=</span>
  <span class="comment">// port is guaranteed 1..65535. No validation needed.</span>
  ...`}</code></pre>
	<p>
		No <code>null</code>, no exceptions, no <code>undefined</code>. You model
		your domain with algebraic types and the compiler holds you to it.
	</p>
	<pre><code>{@html `<span class="kw">type</span> <span class="type">Connection</span> <span class="op">=</span>
  <span class="op">|</span> <span class="type">Disconnected</span>
  <span class="op">|</span> <span class="type">Connected</span><span class="punct">(</span><span class="type">Socket</span><span class="punct">)</span>
  <span class="op">|</span> <span class="type">Error</span><span class="punct">(</span><span class="type">String</span><span class="punct">)</span>

<span class="kw">fn</span> <span class="fn">status</span><span class="punct">(</span><span class="fn">conn</span><span class="op">:</span> <span class="type">Connection</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">String</span> <span class="op">=</span>
  <span class="kw">match</span> <span class="fn">conn</span>
    <span class="type">Disconnected</span>      <span class="op">-&gt;</span> <span class="str">"offline"</span>
    <span class="type">Connected</span><span class="punct">(</span><span class="fn">_</span><span class="punct">)</span>     <span class="op">-&gt;</span> <span class="str">"online"</span>
    <span class="type">Error</span><span class="punct">(</span><span class="fn">msg</span><span class="punct">)</span>       <span class="op">-&gt;</span> <span class="str">"error: \${msg}"</span>`}</code></pre>
	<p class="small muted"><a href="/learn#refinements">More on types &rarr;</a></p>

	<h3 id="effects">Effects as Permissions</h3>
	<p>
		Side effects go in the type signature. If a function doesn't declare
		<code>{"{Fs}"}</code>, it can't touch the filesystem. No <code>{"{Http}"}</code>
		means no network requests. The compiler enforces this, not a linter.
	</p>
	<pre><code>{@html `<span class="comment">// Pure: no effects, no surprises</span>
<span class="kw">fn</span> <span class="fn">add</span><span class="punct">(</span><span class="fn">a</span><span class="op">:</span> <span class="type">Int</span><span class="punct">,</span> <span class="fn">b</span><span class="op">:</span> <span class="type">Int</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="type">Int</span> <span class="op">=</span> <span class="fn">a</span> <span class="op">+</span> <span class="fn">b</span>

<span class="comment">// Effectful: declares exactly what it does</span>
<span class="kw">fn</span> <span class="fn">save!</span><span class="punct">(</span><span class="fn">data</span><span class="op">:</span> <span class="type">String</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="effect">{Fs}</span> <span class="type">Unit</span> <span class="op">=</span>
  <span class="type">Fs</span><span class="punct">.</span><span class="fn">write!</span><span class="punct">(</span><span class="str">"out.txt"</span><span class="punct">,</span> <span class="fn">data</span><span class="punct">)</span>`}</code></pre>
	<p>Built-in effects:</p>
	<table>
		<tbody>
			<tr><td><code>Console</code></td><td>Terminal I/O</td><td><code>Http</code></td><td>Network requests</td></tr>
			<tr><td><code>Fs</code></td><td>Filesystem</td><td><code>Random</code></td><td>Random numbers</td></tr>
			<tr><td><code>Env</code></td><td>Environment vars</td><td><code>Sqlite</code></td><td>Database</td></tr>
			<tr><td><code>Log</code></td><td>Logging (ambient)</td><td><code>Time</code></td><td>Clock (ambient)</td></tr>
		</tbody>
	</table>
	<p>
		Want to sandbox AI-generated code? Grant <code>{"{Console}"}</code> but
		withhold <code>{"{Fs}"}</code> and <code>{"{Http}"}</code>. The type
		system is the sandbox.
	</p>
	<p class="small muted"><a href="/learn#effects">More on effects &rarr;</a></p>

	<h3 id="syntax">One Way to Do Each Thing</h3>
	<p>
		There's one syntax per operation. Not a style guide, the grammar itself.
		One way to chain, one way to handle errors, one way to call functions.
	</p>
	<table>
		<thead>
			<tr><th>Concept</th><th>Baseline</th><th>Not supported</th></tr>
		</thead>
		<tbody>
			<tr><td>Chaining</td><td><code>x |&gt; f |&gt; g</code></td><td>method chaining, composition</td></tr>
			<tr><td>Errors</td><td><code>Result&lt;T, E&gt;</code> + <code>?</code></td><td>try/catch, exceptions</td></tr>
			<tr><td>Optionals</td><td><code>Option&lt;T&gt;</code></td><td>null, undefined, nil</td></tr>
			<tr><td>Calls</td><td><code>Module.fn(value)</code></td><td>value.method()</td></tr>
			<tr><td>Negation</td><td><code>not x</code></td><td><code>!x</code></td></tr>
			<tr><td>Concat</td><td><code>"$&#123;a&#125;$&#123;b&#125;"</code></td><td><code>a + b</code></td></tr>
		</tbody>
	</table>

	<h3 id="diagnostics">Machine-Readable Diagnostics</h3>
	<p>
		The compiler outputs structured JSON: source locations, error codes,
		and fix suggestions with confidence scores. AI agents don't need to
		scrape error messages, they get machine-readable diagnostics.
	</p>
	<pre><code>{@html `$ blc check app.bl --json
{
  <span class="str">"status"</span>: <span class="str">"failure"</span>,
  <span class="str">"diagnostics"</span>: [{
    <span class="str">"code"</span>: <span class="str">"TYP_002"</span>,
    <span class="str">"message"</span>: <span class="str">"Undefined variable \`nme\`"</span>,
    <span class="str">"suggestions"</span>: [{
      <span class="str">"description"</span>: <span class="str">"Did you mean \`name\`?"</span>,
      <span class="str">"confidence"</span>: <span class="num">0.8</span>
    }]
  }]
}`}</code></pre>
	<p>
		The agent loop: load <code>llms.txt</code>, generate code,
		run <code>blc check --json</code>, apply fixes, repeat. Since there's
		only one way to write each construct, the agent doesn't waste tokens
		choosing between equivalent alternatives.
	</p>
</section>

<p>
	<a href="/guide" class="btn btn-primary">Get started</a>
	<a href="https://github.com/baseline-lang/baseline" class="btn" style="margin-left:0.5rem">GitHub</a>
</p>
