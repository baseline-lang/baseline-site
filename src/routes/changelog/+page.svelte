<svelte:head>
	<title>Changelog | Baseline</title>
	<meta name="description" content="What's new in each release of the Baseline compiler." />
</svelte:head>

<h1>Changelog</h1>
<p class="lead">
	What's new in each release of the Baseline compiler.
</p>

<section id="v0.3.3">
	<h2>v0.3.3 <span class="dim small">March 9, 2026</span></h2>
	<p>
		New native List and String operations, a refinement checker fix, and benchmark validation
		proving Baseline beats Python on all seven Hanabi benchmarks.
	</p>

	<h3>Standard Library</h3>
	<ul>
		<li><code>List.count_flips</code> -- pancake flip loop executed entirely in Rust with in-place mutation</li>
		<li><code>List.rotate_left</code>, <code>List.reverse_prefix</code>, <code>List.swap</code> -- permutation primitives with CoW owning variants</li>
		<li><code>List.push</code> -- single-element append with copy-on-write</li>
		<li><code>List.bisect</code> -- linear scan for cumulative probability threshold</li>
		<li><code>String.cyclic_substring</code> -- extract chars with wrap-around, fast ASCII byte path</li>
		<li><code>String.random_fasta_line</code> -- PRNG loop with cumulative lookup and string building in one native call</li>
	</ul>

	<h3>Bug Fixes</h3>
	<ul>
		<li>Fixed refinement checker parsing of string equality with <code>||</code> operator</li>
		<li>Refined <code>String</code> <code>++</code> operator support</li>
	</ul>

	<h3>Benchmarks</h3>
	<ul>
		<li>Baseline now <strong>beats Python on all 7 Hanabi benchmarks</strong> (nbody, binarytrees, fasta, fannkuch-redux, spectral-norm, pidigits, mandelbrot)</li>
		<li>Added optimized benchmark programs and multi-language comparison harness</li>
	</ul>
</section>

<hr />

<section id="v0.3.2">
	<h2>v0.3.2 <span class="dim small">March 9, 2026</span></h2>
	<p>
		Production-ready server runtime, scalar replacement of aggregates, and mutable bindings.
		The HTTP server gains middleware, graceful shutdown, rate limiting, compression, and CORS --
		while the JIT compiler delivers major performance wins through SRA and scalar RC elision.
	</p>

	<h3>Server</h3>
	<ul>
		<li>JIT-compiled <strong>middleware chain</strong> with <code>next(req)</code> continuation</li>
		<li>Graceful shutdown on SIGINT/SIGTERM with 30s connection drain</li>
		<li>Per-IP <strong>rate limiting</strong> (token bucket, 100 req/s) with 429 responses</li>
		<li>Gzip <strong>response compression</strong> for bodies over 1KB</li>
		<li>Built-in <strong>CORS preflight</strong> handling for OPTIONS requests</li>
		<li>Request header (8KB) and body (1MB) size limits with 413 responses</li>
		<li>Structured request logging with request ID tracking</li>
		<li>Built-in <code>/__health</code> endpoint</li>
		<li>Keep-alive timeout (30s) and server workers via <code>SO_REUSEPORT</code></li>
		<li>Panic recovery with <code>catch_unwind</code> around handler dispatch</li>
	</ul>

	<h3>JIT Compiler</h3>
	<ul>
		<li><strong>Scalar replacement of aggregates</strong> (SRA) -- record parameters decomposed into individual registers, eliminating heap allocation in tight loops</li>
		<li>SRA let-floating exposes inlined records to optimization (<strong>6x nbody speedup</strong>)</li>
		<li><strong>Scalar RC elision</strong> -- skip incref/decref for Int, Float, Bool, Unit values</li>
		<li>Multi-value returns for all-scalar records (one register per field)</li>
		<li>Mutable field assignment compiles to direct SRA variable update (zero allocation)</li>
		<li>Tail recursion modulo constructor (TRMC) rewrites recursive constructors into iterative loops</li>
		<li>Typed drop-reuse codegen for enum, tuple, record, and struct values</li>
		<li>Typed <code>JitError</code> variants and <code>can_jit_reason()</code> diagnostics</li>
		<li>Profiling counters via <code>BLC_JIT_COUNTERS</code> and IR dump via <code>BASELINE_DUMP_IR</code></li>
	</ul>

	<h3>Language</h3>
	<ul>
		<li><code>let mut</code> bindings with assignment statements for mutable local variables</li>
		<li>Mutable record field assignment (<code>obj.field = val</code>)</li>
	</ul>

	<h3>Tooling</h3>
	<ul>
		<li>Moved to <code>baseline-lang</code> GitHub organization</li>
		<li>Heap benchmark gate and nbody reference outputs in CI</li>
	</ul>
</section>

<hr />

<section id="v0.3.0">
	<h2>v0.3.0 <span class="dim small">March 5, 2026</span></h2>
	<p>
		JIT-only execution, fiber-based effect handlers, and a major stdlib expansion.
		The tree-walk interpreter has been removed -- Cranelift JIT is now the default and only runtime.
	</p>

	<h3>JIT Compiler</h3>
	<ul>
		<li>Cranelift JIT is now the <strong>default and only runtime</strong> -- the bytecode VM interpreter has been removed</li>
		<li>Self-tail-call optimization (recursive calls compile to loops)</li>
		<li>Base-case speculation inlines guard checks at call sites</li>
		<li>Clone-on-write enum field updates bypass <code>Arc::get_mut</code> via safe raw pointer mutation</li>
		<li>Owning dispatch for native CoW functions</li>
		<li>Evidence passing transform for tail-resumptive effects</li>
		<li>Float arithmetic (<code>+</code>, <code>-</code>, <code>*</code>, <code>/</code>) in native code</li>
		<li>List pattern matching in JIT</li>
		<li>Error propagation replaces panics with <code>catch_unwind</code></li>
	</ul>

	<h3>Performance</h3>
	<ul>
		<li>Indexed field access -- field indices resolved at compile time, O(1) Vec lookup at runtime</li>
		<li>Aggressive function inlining (lightweight functions up to 120 IR nodes)</li>
		<li>Tuple-let fusion eliminates allocation from inlined multi-return</li>
		<li>Non-atomic RC mode for single-threaded execution</li>
		<li>Checked integer overflow replaces silent wrapping arithmetic</li>
		<li>Persistent map operations optimized</li>
		<li>Unboxed scalar fast path restored for non-Int scalar functions</li>
	</ul>

	<h3>Effects &amp; Handlers</h3>
	<ul>
		<li>Fiber-based effect handlers with Perceus reuse analysis</li>
		<li>One-shot continuation semantics codified</li>
		<li>JIT effect conformance tests</li>
		<li>Arithmetic on refined types</li>
	</ul>

	<h3>Standard Library</h3>
	<ul>
		<li><code>Math.sqrt</code>, <code>Math.sin</code>, <code>Math.cos</code>, <code>Math.atan2</code>, <code>Math.floor</code>, <code>Math.ceil</code></li>
		<li><code>Float.from_int</code>, <code>Float.format</code>, <code>Int.from_float</code></li>
		<li><code>Map.map</code>, <code>Map.filter</code>, <code>Map.fold</code>, <code>Map.entries</code></li>
		<li><code>List.set</code>, <code>List.slice</code>, <code>List.fill</code>, <code>List.get_or</code></li>
		<li>Implicit first-arg piping</li>
		<li>17 new API modules, 135 new functions total</li>
	</ul>

	<h3>Tooling</h3>
	<ul>
		<li>GitHub Actions release workflow with Homebrew-compatible tarballs</li>
		<li>Conformance regression gates in CI</li>
		<li>Package dependency resolution</li>
		<li>Async LSP improvements</li>
		<li>Language tour for new developers</li>
		<li>Error message catalog with all 56 diagnostic codes</li>
	</ul>
</section>

<hr />

<section id="v0.2.0">
	<h2>v0.2.0 <span class="dim small">February 17, 2026</span></h2>
	<p>
		Web framework, database connectors, trait system, and advanced pattern matching.
		This release turns Baseline into a practical language for building web services.
	</p>

	<h3>Web Framework</h3>
	<ul>
		<li><code>Router</code> module with route definitions, <code>Router.resources</code>, and <code>Router.state</code></li>
		<li><code>Request</code> module with typed parameter extraction</li>
		<li><code>Response</code> helpers with auto-serialization</li>
		<li><code>Middleware</code> module for request/response pipelines</li>
		<li><code>Server</code> runtime with router state injection and auto-docs</li>
		<li><code>HttpError</code> expanded to 12 variants with JSON:API error format</li>
		<li>Structured error pipelines, multipart parsing, JWT auth, in-memory sessions</li>
		<li>WebSocket support and schema-driven validation</li>
	</ul>

	<h3>Database</h3>
	<ul>
		<li>Generic SQL backend with <code>Sqlite</code>, <code>Postgres</code>, and <code>Mysql</code> connectors</li>
		<li>Typed database rows with <code>SqlValue</code>, <code>Row</code>, and typed accessors</li>
		<li><code>query_map!</code> HOF and <code>query_one!</code> for all backends</li>
		<li>Parameterized queries and migration runner</li>
	</ul>

	<h3>Type System</h3>
	<ul>
		<li>Trait/interface system with compile-time dictionary-passing dispatch</li>
		<li>Record destructuring and open records with row polymorphism</li>
		<li>Trait bounds with supertrait support</li>
	</ul>

	<h3>Pattern Matching</h3>
	<ul>
		<li>Guard patterns (<code>match x {'{'} n if n &gt; 0 =&gt; ... {'}'}</code>)</li>
		<li>Or-patterns (<code>match x {'{'} 1 | 2 | 3 =&gt; ... {'}'}</code>)</li>
		<li>List patterns (<code>match xs {'{'} [head, ..tail] =&gt; ... {'}'}</code>)</li>
		<li>Enhanced record patterns with partial matching and literal fields</li>
	</ul>

	<h3>Standard Library</h3>
	<ul>
		<li><code>Crypto</code>, <code>DateTime</code>, <code>Regex</code> modules</li>
		<li><code>Result.map_err</code> and <code>Result.context</code> for error conversions</li>
		<li>Semantic logger with timestamps, structured fields, and JSON output</li>
		<li>Unified Log API (removed <code>_with!</code> variants)</li>
		<li><code>Validate</code> module</li>
	</ul>

	<h3>Tooling</h3>
	<ul>
		<li>MCP server with 5 tools for AI agent integration</li>
		<li>Website redesign with API reference, guides, and search</li>
		<li><code>llms.txt</code> and <code>llms-full.txt</code> for AI agent context</li>
		<li><code>blc docs</code> CLI search and expanded API documentation</li>
		<li>Rosetta Stone and Common Agent Mistakes in <code>llms.txt</code></li>
	</ul>
</section>

<hr />

<section id="v0.1.0">
	<h2>v0.1.0 <span class="dim small">February 2, 2026</span></h2>
	<p>
		Initial release. A working compiler with type checking, effect inference,
		refinement types, and an interpreter runtime.
	</p>

	<h3>Language</h3>
	<ul>
		<li>Core types: <code>Int</code>, <code>String</code>, <code>Boolean</code>, <code>Float</code>, <code>List&lt;T&gt;</code></li>
		<li>Sum types with <code>Option&lt;T&gt;</code> and <code>Result&lt;T, E&gt;</code></li>
		<li>Closures, string interpolation, tuple evaluation</li>
		<li>Pattern matching with <code>match</code> expressions</li>
		<li>Pipe operator (<code>|&gt;</code>), range expressions (<code>1..10</code>)</li>
		<li>Module system with <code>@prelude</code> levels</li>
	</ul>

	<h3>Type System</h3>
	<ul>
		<li>Hindley-Milner-style inference for built-in generics</li>
		<li>Effect inference with transitive checking via call graph</li>
		<li>Refinement types with integer interval constraints (<code>type Port = Int where self &gt; 0</code>)</li>
		<li>Built-in effects: <code>Console</code>, <code>Http</code>, <code>Fs</code>, <code>Log</code>, <code>Time</code>, <code>Random</code>, <code>Env</code></li>
	</ul>

	<h3>Standard Library</h3>
	<ul>
		<li><code>Math</code> module with arithmetic and comparison functions</li>
		<li><code>String</code> module with manipulation and search</li>
		<li><code>List</code> module with <code>map</code>, <code>filter</code>, <code>fold</code>, <code>find</code>, <code>head</code>, <code>tail</code></li>
		<li><code>Option</code> and <code>Result</code> modules with constructors and combinators</li>
	</ul>

	<h3>Tooling</h3>
	<ul>
		<li>Tree-sitter parser (<code>tree-sitter-baseline</code>)</li>
		<li><code>blc check</code> with JSON diagnostic output</li>
		<li>Zed editor extension with syntax highlighting</li>
		<li>Property-based tests and fuzzing targets</li>
		<li>Conformance test suite (45 test files, 12 categories)</li>
	</ul>
</section>
