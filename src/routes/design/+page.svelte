<svelte:head>
	<title>Design | Baseline</title>
	<meta name="description" content="Baseline's language design: refinement types, algebraic effects, machine-actionable diagnostics, and current limitations." />
</svelte:head>

<nav class="on-this-page">
	<h4>On this page</h4>
	<ul>
		<li><a href="#correctness">Refinement Types</a></li>
		<li><a href="#refinements-effects">Refinements + Effects</a></li>
		<li><a href="#diagnostics">Machine-Actionable Diagnostics</a></li>
		<li><a href="#landscape">Comparison</a></li>
		<li><a href="#performance">Performance</a></li>
		<li><a href="#limitations">Current Limitations</a></li>
		<li><a href="#roadmap">Roadmap</a></li>
	</ul>
</nav>

<h1>Language Design</h1>
<p class="lead">
	Baseline combines refinement types, algebraic effects, and machine-actionable
	diagnostics. This page explains the design decisions, how they interact, and
	where the language stands today.
</p>

<section id="correctness">
	<h2>Refinement Types</h2>
	<p>
		Baseline's type system sits between two extremes:
	</p>
	<ul>
		<li>
			<strong>TypeScript-level types</strong> are advisory.
			<code>type Port = number</code> compiles but doesn't prevent
			<code>listen(-1)</code>. Developers bolt on Zod, branded types, and
			runtime assertions to enforce constraints the type system ignores.
		</li>
		<li>
			<strong>Theorem provers</strong> (Idris, Agda, Lean) enforce arbitrary
			invariants but require expertise in dependent types and proof tactics.
			Most teams can't afford the learning curve.
		</li>
	</ul>
	<p>
		Refinement types check real constraints in a familiar syntax:
	</p>
	<pre><code>{@html `<span class="kw">type</span> <span class="type">Port</span> <span class="op">=</span> <span class="type">Int</span> <span class="kw">where</span> <span class="num">1</span> <span class="op">&lt;=</span> <span class="fn">self</span> <span class="op">&lt;=</span> <span class="num">65535</span>
<span class="kw">type</span> <span class="type">Percentage</span> <span class="op">=</span> <span class="type">Int</span> <span class="kw">where</span> <span class="fn">self</span> <span class="op">&gt;=</span> <span class="num">0</span> <span class="op">&amp;&amp;</span> <span class="fn">self</span> <span class="op">&lt;=</span> <span class="num">100</span>

<span class="kw">fn</span> <span class="fn">listen!</span><span class="punct">(</span><span class="fn">port</span><span class="op">:</span> <span class="type">Port</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="effect">{Net}</span> <span class="type">Server</span> <span class="op">=</span>
  <span class="comment">// port is guaranteed 1..65535. No validation code needed.</span>
  ...`}</code></pre>
	<p>
		The compiler verifies at every call site that the value satisfies the
		constraint. Pass a literal <code>0</code> and you get a compile error.
		Pass a variable and the compiler checks whether the constraint can be
		proven from context. This is refinement checking, not runtime validation.
	</p>
	<p>
		Compare the same constraint in TypeScript:
	</p>
	<pre><code>{@html `<span class="comment">// TypeScript: the type is a lie</span>
<span class="kw">type</span> <span class="type">Port</span> <span class="op">=</span> <span class="type">number</span><span class="punct">;</span>

<span class="kw">function</span> <span class="fn">listen</span><span class="punct">(</span><span class="fn">port</span><span class="op">:</span> <span class="type">Port</span><span class="punct">)</span> <span class="punct">{</span>
  <span class="kw">if</span> <span class="punct">(</span><span class="fn">port</span> <span class="op">&lt;</span> <span class="num">1</span> <span class="op">||</span> <span class="fn">port</span> <span class="op">&gt;</span> <span class="num">65535</span><span class="punct">)</span> <span class="kw">throw</span> <span class="kw">new</span> <span class="type">Error</span><span class="punct">(</span><span class="str">"invalid port"</span><span class="punct">);</span>
  <span class="comment">// ...</span>
<span class="punct">}</span>`}</code></pre>
	<p>
		The TypeScript version compiles with <code>listen(-1)</code>. The
		Baseline version does not.
	</p>
	<p>
		A function that receives <code>Port</code> never needs to check the range.
		A function that receives <code>Percentage</code> never needs to clamp.
		The constraint is encoded once in the type definition and enforced
		everywhere by the compiler.
	</p>
</section>

<section id="refinements-effects">
	<h2>Refinements + Effects Together</h2>
	<p>
		Two verification systems, each useful alone, become significantly more
		useful together.
	</p>
	<p>
		<strong>Refinements</strong> check data invariants: is this port in range?
		Is this string non-empty? <strong>Effects</strong> check behavior invariants:
		does this function touch the filesystem? Does it make network requests?
	</p>
	<p>
		Together, they answer a question no other practical language can answer at
		compile time: <em>is this function honest about what it accepts AND what
		it does?</em>
	</p>
	<pre><code>{@html `<span class="kw">fn</span> <span class="fn">agent_task!</span><span class="punct">(</span><span class="fn">input</span><span class="op">:</span> <span class="type">Input</span><span class="punct">)</span> <span class="op">-&gt;</span> <span class="effect">{Log, Db.read}</span> <span class="type">Output</span> <span class="op">=</span>
  <span class="type">Log</span><span class="punct">.</span><span class="fn">info!</span><span class="punct">(</span><span class="str">"Processing \${input}"</span><span class="punct">)</span>
  <span class="kw">let</span> <span class="fn">data</span> <span class="op">=</span> <span class="type">Db</span><span class="punct">.</span><span class="fn">query!</span><span class="punct">(</span><span class="str">"SELECT * FROM items"</span><span class="punct">)</span>
  <span class="comment">// Fs.delete!("important.txt")  COMPILE ERROR: Fs not in effect set</span>
  <span class="fn">transform</span><span class="punct">(</span><span class="fn">data</span><span class="punct">)</span>`}</code></pre>
	<p>
		This is compile-time capability enforcement. The untrusted code never runs
		because the compiler rejects it before execution. Compare this to runtime
		sandboxing, where the code executes and the sandbox intercepts the call.
	</p>

	<h3>Who else has what</h3>
	<table>
		<thead>
			<tr><th>Language</th><th>Refinements</th><th>Effects</th><th>Both</th></tr>
		</thead>
		<tbody>
			<tr>
				<td>Liquid Haskell</td>
				<td>SMT-backed (powerful)</td>
				<td>No</td>
				<td>No</td>
			</tr>
			<tr>
				<td>Koka</td>
				<td>No</td>
				<td>Row-polymorphic (gold standard)</td>
				<td>No</td>
			</tr>
			<tr>
				<td>OCaml 5</td>
				<td>No</td>
				<td>Effect handlers</td>
				<td>No</td>
			</tr>
			<tr>
				<td>TypeScript</td>
				<td>No (Zod is runtime)</td>
				<td>No</td>
				<td>No</td>
			</tr>
			<tr>
				<td>Baseline</td>
				<td>Integer intervals (growing)</td>
				<td>Declared + transitively checked</td>
				<td><strong>Yes</strong></td>
			</tr>
		</tbody>
	</table>
	<p>
		Liquid Haskell's refinements are more expressive. Koka's effect system
		is more mature. Neither combines both, and neither ships as a standalone
		language with a server framework.
	</p>
</section>

<section id="diagnostics">
	<h2>Machine-Actionable Diagnostics</h2>
	<p>
		Every language has error messages. Baseline's are structured data.
	</p>
	<pre><code>{@html `$ blc check app.bl --json`}</code></pre>
	<pre><code>{@html `{
  <span class="str">"code"</span>: <span class="str">"CAP_001"</span>,
  <span class="str">"message"</span>: <span class="str">"Unauthorized Side Effect: 'Http.get!'"</span>,
  <span class="str">"context"</span>: <span class="str">"Function 'process!' declares effects {Log}, but calls 'Http.get!' which requires {Http}."</span>,
  <span class="str">"suggestions"</span>: [{
    <span class="str">"strategy"</span>: <span class="str">"escalate_capability"</span>,
    <span class="str">"patch"</span>: {
      <span class="str">"original_text"</span>: <span class="str">"fn process!(data: String) -> {Log} String"</span>,
      <span class="str">"replacement_text"</span>: <span class="str">"fn process!(data: String) -> {Log, Http} String"</span>
    }
  }]
}`}</code></pre>
	<p>
		This is not a human-readable error message with a suggestion. It is a
		machine-actionable patch with a named strategy
		(<code>escalate_capability</code>), the exact text to find, and the exact
		replacement. An LLM receives this, applies the patch, resubmits. One-shot fix.
	</p>

	<h3>Three verification layers, one format</h3>
	<p>
		Every diagnostic follows the same structure across all three checkers:
	</p>
	<table>
		<thead>
			<tr><th>Layer</th><th>Code prefix</th><th>Example</th></tr>
		</thead>
		<tbody>
			<tr><td>Types</td><td><code>TYP_xxx</code></td><td>Type mismatch, undefined variable, missing field</td></tr>
			<tr><td>Effects</td><td><code>CAP_xxx</code></td><td>Unauthorized side effect, missing declaration</td></tr>
			<tr><td>Refinements</td><td><code>REF_xxx</code></td><td>Constraint violation, unprovable refinement</td></tr>
		</tbody>
	</table>
	<p>
		Each includes error codes, source locations, context, and (where applicable)
		patch suggestions with confidence scores.
	</p>

	<h3>The generate-check-fix loop</h3>
	<p>
		The compiler becomes a collaboration partner, not an obstacle. An LLM
		generating Baseline code enters a feedback loop:
	</p>
	<ol>
		<li>Generate code from a type signature</li>
		<li>Run <code>blc check --json</code></li>
		<li>Parse structured errors</li>
		<li>Apply suggested patches</li>
		<li>Repeat until clean</li>
	</ol>
	<p>
		The compiler guides the model to correctness through structured feedback.
		LLMs don't need millions of Baseline programs to generate correct code
		if the compiler teaches them through actionable diagnostics.
	</p>
</section>

<section id="landscape">
	<h2>Comparison</h2>
	<p>
		How Baseline compares to established languages on each axis.
	</p>

	<h3>vs. TypeScript</h3>
	<p>
		TypeScript's type system is structurally sound in many cases but
		fundamentally advisory. <code>any</code> exists. Type assertions exist.
		Runtime and compile-time types can disagree. TypeScript has npm
		(2M+ packages) and massive LLM training data. Baseline has 38 stdlib
		modules and near-zero training data. TypeScript is the pragmatic choice
		for most teams today. Baseline's value is for teams that need compile-time
		correctness guarantees TypeScript cannot provide.
	</p>

	<h3>vs. Rust</h3>
	<p>
		Rust has ownership, borrow checking, and fearless concurrency. Its type
		system prevents memory bugs and data races. Baseline has refinements and
		effects, which Rust lacks. Rust's error messages are the gold standard
		for human readability but are not structured for machine consumption.
		Rust is a better choice for systems programming. Baseline targets
		application-level code where data invariants and effect tracking matter
		more than memory safety.
	</p>

	<h3>vs. OCaml 5</h3>
	<p>
		OCaml 5 added effect handlers. It has a mature type system, great
		performance, and a real ecosystem (opam). Baseline adds refinement types
		and structured diagnostics on top of effects. OCaml's effect handlers
		are lower-level (untyped in the initial implementation). Baseline's are
		declared in function signatures and checked transitively. OCaml is the
		more mature choice. Baseline's advantage is the combination of refinements
		+ effects + diagnostics.
	</p>

	<h3>vs. Koka</h3>
	<p>
		Koka is the gold standard for algebraic effects with row polymorphism
		and the Perceus reference counting system. Baseline's effect system is
		simpler (declared sets, not row-polymorphic). Koka lacks refinement types
		and structured machine-actionable diagnostics. Koka is a research language.
		Baseline targets practical use with a server framework and JIT compiler.
	</p>
</section>

<section id="performance">
	<h2>Performance</h2>
	<p>
		Baseline compiles to native code via Cranelift JIT. All times are
		median of 3 runs on Apple Silicon (arm64).
	</p>

	<h3>Hanabi Suite</h3>
	<table>
		<thead>
			<tr><th>Benchmark</th><th>C -O2</th><th>Baseline</th><th>Node.js</th><th>Python</th></tr>
		</thead>
		<tbody>
			<tr><td>nbody (5M)</td><td>0.21s</td><td>0.94s</td><td>0.53s</td><td>21.06s</td></tr>
			<tr><td>binarytrees (18)</td><td>1.07s</td><td>4.63s</td><td>0.69s</td><td>5.09s</td></tr>
			<tr><td>fasta (2.5M)</td><td>0.29s</td><td>0.68s</td><td>0.003s</td><td>4.50s</td></tr>
			<tr><td>fannkuch (10)</td><td>0.006s</td><td>2.46s</td><td>1.97s</td><td>3.85s</td></tr>
			<tr><td>spectral-norm (500)</td><td>0.004s</td><td>0.72s</td><td>0.07s</td><td>1.31s</td></tr>
		</tbody>
	</table>

	<h3>CPU Micro-Benchmarks</h3>
	<table>
		<thead>
			<tr><th>Benchmark</th><th>Rust</th><th>Go</th><th>Baseline</th><th>OCaml</th><th>Node.js</th><th>Python</th></tr>
		</thead>
		<tbody>
			<tr><td>tak</td><td>0.098s</td><td>0.098s</td><td>0.084s</td><td>0.125s</td><td>0.353s</td><td>3.164s</td></tr>
			<tr><td>fib (35)</td><td>0.044s</td><td>0.046s</td><td>0.047s</td><td>0.050s</td><td>0.146s</td><td>1.016s</td></tr>
			<tr><td>divsum</td><td>0.060s</td><td>0.057s</td><td>0.059s</td><td>0.062s</td><td>0.142s</td><td>1.474s</td></tr>
			<tr><td>primes</td><td>0.020s</td><td>0.018s</td><td>0.023s</td><td>0.009s</td><td>0.085s</td><td>0.424s</td></tr>
			<tr><td>mergesort</td><td>0.011s</td><td>0.015s</td><td>0.021s</td><td>0.009s</td><td>0.075s</td><td>0.048s</td></tr>
			<tr><td>mapbuild</td><td>0.014s</td><td>0.016s</td><td>0.027s</td><td>0.009s</td><td>0.080s</td><td>0.049s</td></tr>
			<tr><td>treemap</td><td>0.119s</td><td>0.028s</td><td>0.320s</td><td>0.009s</td><td>0.107s</td><td>0.226s</td></tr>
		</tbody>
	</table>
</section>

<section id="limitations">
	<h2>Current Limitations</h2>
	<p>
		As of v0.3, these are the areas where Baseline is incomplete.
	</p>
	<ul>
		<li>
			<strong>Refinement types are integer intervals only.</strong>
			<code>Int where self &gt; 0</code> works. String refinements
			(regex patterns, length constraints) are not yet supported.
		</li>
		<li>
			<strong>No concurrency.</strong> There is no async/await, no
			task spawning, no parallelism. Structured concurrency is on
			the <a href="#roadmap">roadmap</a>.
		</li>
		<li>
			<strong>Small standard library.</strong> 38 modules covering
			core operations, HTTP, database, and JSON. You will encounter
			gaps. There is no package registry.
		</li>
		<li>
			<strong>No embedding API.</strong> Baseline runs as a standalone
			compiler and runtime. There is no C API or way to embed it in
			a host application.
		</li>
	</ul>
</section>

<section id="roadmap">
	<h2>Roadmap</h2>
	<p>
		Planned work beyond v0.3. Designs are subject to change.
	</p>
	<ul>
		<li>
			<strong>Concurrency.</strong> Structured concurrency modeled through
			the effect system: async/await, task spawning, supervision, and
			capability-based fiber sandboxing.
		</li>
		<li>
			<strong>Rust interop.</strong> FFI layer for calling Rust libraries
			from Baseline and embedding Baseline in Rust applications.
		</li>
		<li>
			<strong>Memory management.</strong> Perceus reuse analysis,
			ownership-based RC elimination, and arena allocation for
			request-scoped data.
		</li>
		<li>
			<strong>Numeric performance.</strong> Monomorphization, unboxed
			arrays, effect erasure, and SIMD emission for compute-heavy
			workloads.
		</li>
		<li>
			<strong>String and compound refinements.</strong> Regex patterns,
			length constraints, and refinements on non-integer types.
		</li>
		<li>
			<strong>Agent tooling.</strong> Agent convergence benchmarks,
			<code>blc init</code> scaffolding, and Context7 distribution
			for LLM context retrieval.
		</li>
		<li>
			<strong>Extended standard library.</strong> CLI framework, UUID
			generation, checked arithmetic, async I/O, and broader
			ecosystem coverage.
		</li>
	</ul>
</section>

<hr />

<p>
	See the <a href="/changelog">changelog</a> for what shipped and when.
</p>
<p>
	<a href="/guide" class="btn btn-primary">Get started</a>
	<a href="/learn" class="btn" style="margin-left:0.5rem">Learn the language</a>
</p>
