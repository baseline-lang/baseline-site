<svelte:head>
	<title>API Reference | Baseline</title>
	<meta name="description" content="Complete API reference for the Baseline programming language standard library." />
</svelte:head>

<nav class="on-this-page">
	<h4>Language</h4>
	<ul>
		<li><a href="#lang-core">Core</a></li>
		<li><a href="#lang-io">IO</a></li>
		<li><a href="#lang-async">Async</a></li>
		<li><a href="#lang-data">Data</a></li>
		<li><a href="#lang-error">Error Handling</a></li>
		<li><a href="#lang-other">Other</a></li>
	</ul>
	<h4>Server</h4>
	<ul>
		<li><a href="#srv-web">Web</a></li>
		<li><a href="#srv-db">Database</a></li>
		<li><a href="#srv-auth">Auth</a></li>
		<li><a href="#srv-validation">Validation</a></li>
		<li><a href="#srv-realtime">Realtime</a></li>
	</ul>
</nav>

<h1>API Reference</h1>
<p class="lead">
	Every module and function in the standard library.
	Language modules ship with the core prelude; server modules require <code>@prelude(server)</code>.
</p>

<section id="language-modules">
	<h2>Language Modules</h2>

	<h4 id="lang-core">Core</h4>

	<h3 id="list">List</h3>
	<p>List operations.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>length</code></td><td><code>fn length(List&lt;T&gt;) -&gt; Int</code></td><td>Number of elements</td></tr>
			<tr><td><code>head</code></td><td><code>fn head(List&lt;T&gt;) -&gt; Option&lt;T&gt;</code></td><td>First element</td></tr>
			<tr><td><code>tail</code></td><td><code>fn tail(List&lt;T&gt;) -&gt; List&lt;T&gt;</code></td><td>All except first</td></tr>
			<tr><td><code>get</code></td><td><code>fn get(List&lt;T&gt;, Int) -&gt; Option&lt;T&gt;</code></td><td>Element at index</td></tr>
			<tr><td><code>get_or</code></td><td><code>fn get_or(List&lt;T&gt;, Int, T) -&gt; T</code></td><td>Element at index with default</td></tr>
			<tr><td><code>set</code></td><td><code>fn set(List&lt;T&gt;, Int, T) -&gt; List&lt;T&gt;</code></td><td>Replace element at index</td></tr>
			<tr><td><code>slice</code></td><td><code>fn slice(List&lt;T&gt;, Int, Int) -&gt; List&lt;T&gt;</code></td><td>Sub-list (start, end exclusive)</td></tr>
			<tr><td><code>fill</code></td><td><code>fn fill(Int, T) -&gt; List&lt;T&gt;</code></td><td>Create list filled with value</td></tr>
			<tr><td><code>reverse</code></td><td><code>fn reverse(List&lt;T&gt;) -&gt; List&lt;T&gt;</code></td><td>Reverse order</td></tr>
			<tr><td><code>sort</code></td><td><code>fn sort(List&lt;T&gt;) -&gt; List&lt;T&gt;</code></td><td>Sort ascending</td></tr>
			<tr><td><code>concat</code></td><td><code>fn concat(List&lt;T&gt;, List&lt;T&gt;) -&gt; List&lt;T&gt;</code></td><td>Concatenate two lists</td></tr>
			<tr><td><code>contains</code></td><td><code>fn contains(List&lt;T&gt;, T) -&gt; Bool</code></td><td>Check membership</td></tr>
			<tr><td><code>map</code></td><td><code>fn map(List&lt;T&gt;, (T) -&gt; U) -&gt; List&lt;U&gt;</code></td><td>Apply function to each</td></tr>
			<tr><td><code>filter</code></td><td><code>fn filter(List&lt;T&gt;, (T) -&gt; Bool) -&gt; List&lt;T&gt;</code></td><td>Keep matching elements</td></tr>
			<tr><td><code>fold</code></td><td><code>fn fold(List&lt;T&gt;, U, (U, T) -&gt; U) -&gt; U</code></td><td>Reduce to single value</td></tr>
			<tr><td><code>find</code></td><td><code>fn find(List&lt;T&gt;, (T) -&gt; Bool) -&gt; Option&lt;T&gt;</code></td><td>First matching element</td></tr>
		</tbody>
	</table>

	<h3 id="string">String</h3>
	<p>String operations.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>length</code></td><td><code>fn length(String) -&gt; Int</code></td><td>Character count</td></tr>
			<tr><td><code>contains</code></td><td><code>fn contains(String, String) -&gt; Bool</code></td><td>Has substring?</td></tr>
			<tr><td><code>starts_with</code></td><td><code>fn starts_with(String, String) -&gt; Bool</code></td><td>Starts with prefix?</td></tr>
			<tr><td><code>ends_with</code></td><td><code>fn ends_with(String, String) -&gt; Bool</code></td><td>Ends with suffix?</td></tr>
			<tr><td><code>to_upper</code></td><td><code>fn to_upper(String) -&gt; String</code></td><td>Uppercase</td></tr>
			<tr><td><code>to_lower</code></td><td><code>fn to_lower(String) -&gt; String</code></td><td>Lowercase</td></tr>
			<tr><td><code>trim</code></td><td><code>fn trim(String) -&gt; String</code></td><td>Strip whitespace</td></tr>
			<tr><td><code>reverse</code></td><td><code>fn reverse(String) -&gt; String</code></td><td>Reverse</td></tr>
			<tr><td><code>split</code></td><td><code>fn split(String, String) -&gt; List&lt;String&gt;</code></td><td>Split by delimiter</td></tr>
			<tr><td><code>join</code></td><td><code>fn join(List&lt;String&gt;, String) -&gt; String</code></td><td>Join with separator</td></tr>
			<tr><td><code>slice</code></td><td><code>fn slice(String, Int, Int) -&gt; String</code></td><td>Substring (start, length)</td></tr>
			<tr><td><code>replace</code></td><td><code>fn replace(String, String, String) -&gt; String</code></td><td>Replace all occurrences</td></tr>
			<tr><td><code>replace_regex</code></td><td><code>fn replace_regex(String, String, String) -&gt; String</code></td><td>Regex replace</td></tr>
			<tr><td><code>matches</code></td><td><code>fn matches(String, String) -&gt; Bool</code></td><td>Regex test</td></tr>
			<tr><td><code>find_matches</code></td><td><code>fn find_matches(String, String) -&gt; List&lt;String&gt;</code></td><td>All regex matches</td></tr>
			<tr><td><code>chars</code></td><td><code>fn chars(String) -&gt; List&lt;String&gt;</code></td><td>Split into characters</td></tr>
			<tr><td><code>char_at</code></td><td><code>fn char_at(String, Int) -&gt; Option&lt;String&gt;</code></td><td>Character at index</td></tr>
			<tr><td><code>index_of</code></td><td><code>fn index_of(String, String) -&gt; Int</code></td><td>First occurrence index (-1 if none)</td></tr>
			<tr><td><code>to_int</code></td><td><code>fn to_int(String) -&gt; Int</code></td><td>Parse as integer</td></tr>
			<tr><td><code>from_char_code</code></td><td><code>fn from_char_code(Int) -&gt; String</code></td><td>String from Unicode code point</td></tr>
			<tr><td><code>char_code</code></td><td><code>fn char_code(String) -&gt; Int</code></td><td>Unicode code point of first char</td></tr>
		</tbody>
	</table>

	<h3 id="int">Int</h3>
	<p>Integer operations.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>to_string</code></td><td><code>fn to_string(Int) -&gt; String</code></td><td>Convert to string</td></tr>
			<tr><td><code>parse</code></td><td><code>fn parse(String) -&gt; Int</code></td><td>Parse string as int</td></tr>
			<tr><td><code>from_float</code></td><td><code>fn from_float(Float) -&gt; Int</code></td><td>Truncate float to int</td></tr>
		</tbody>
	</table>

	<h3 id="float">Float</h3>
	<p>Floating-point operations.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>from_int</code></td><td><code>fn from_int(Int) -&gt; Float</code></td><td>Convert int to float</td></tr>
			<tr><td><code>format</code></td><td><code>fn format(Float, Int) -&gt; String</code></td><td>Format with decimal places</td></tr>
		</tbody>
	</table>

	<h3 id="map">Map</h3>
	<p>Persistent key-value maps.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>empty</code></td><td><code>fn empty() -&gt; Map&lt;K, V&gt;</code></td><td>Create empty map</td></tr>
			<tr><td><code>insert</code></td><td><code>fn insert(Map&lt;K, V&gt;, K, V) -&gt; Map&lt;K, V&gt;</code></td><td>Insert key-value pair</td></tr>
			<tr><td><code>set</code></td><td><code>fn set(Map&lt;K, V&gt;, K, V) -&gt; Map&lt;K, V&gt;</code></td><td>Alias for insert</td></tr>
			<tr><td><code>get</code></td><td><code>fn get(Map&lt;K, V&gt;, K) -&gt; Option&lt;V&gt;</code></td><td>Look up key</td></tr>
			<tr><td><code>remove</code></td><td><code>fn remove(Map&lt;K, V&gt;, K) -&gt; Map&lt;K, V&gt;</code></td><td>Remove key</td></tr>
			<tr><td><code>contains</code></td><td><code>fn contains(Map&lt;K, V&gt;, K) -&gt; Bool</code></td><td>Check if key exists</td></tr>
			<tr><td><code>has</code></td><td><code>fn has(Map&lt;K, V&gt;, K) -&gt; Bool</code></td><td>Alias for contains</td></tr>
			<tr><td><code>keys</code></td><td><code>fn keys(Map&lt;K, V&gt;) -&gt; List&lt;K&gt;</code></td><td>All keys</td></tr>
			<tr><td><code>values</code></td><td><code>fn values(Map&lt;K, V&gt;) -&gt; List&lt;V&gt;</code></td><td>All values</td></tr>
			<tr><td><code>entries</code></td><td><code>fn entries(Map&lt;K, V&gt;) -&gt; List&lt;(K, V)&gt;</code></td><td>All key-value pairs</td></tr>
			<tr><td><code>len</code></td><td><code>fn len(Map&lt;K, V&gt;) -&gt; Int</code></td><td>Number of entries</td></tr>
			<tr><td><code>size</code></td><td><code>fn size(Map&lt;K, V&gt;) -&gt; Int</code></td><td>Alias for len</td></tr>
			<tr><td><code>from_list</code></td><td><code>fn from_list(List&lt;(K, V)&gt;) -&gt; Map&lt;K, V&gt;</code></td><td>Create from pairs</td></tr>
		</tbody>
	</table>

	<h3 id="set">Set</h3>
	<p>Persistent sets.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>empty</code></td><td><code>fn empty() -&gt; Set&lt;T&gt;</code></td><td>Create empty set</td></tr>
			<tr><td><code>insert</code></td><td><code>fn insert(Set&lt;T&gt;, T) -&gt; Set&lt;T&gt;</code></td><td>Add element</td></tr>
			<tr><td><code>remove</code></td><td><code>fn remove(Set&lt;T&gt;, T) -&gt; Set&lt;T&gt;</code></td><td>Remove element</td></tr>
			<tr><td><code>contains</code></td><td><code>fn contains(Set&lt;T&gt;, T) -&gt; Bool</code></td><td>Check membership</td></tr>
			<tr><td><code>union</code></td><td><code>fn union(Set&lt;T&gt;, Set&lt;T&gt;) -&gt; Set&lt;T&gt;</code></td><td>Set union</td></tr>
			<tr><td><code>intersection</code></td><td><code>fn intersection(Set&lt;T&gt;, Set&lt;T&gt;) -&gt; Set&lt;T&gt;</code></td><td>Set intersection</td></tr>
			<tr><td><code>len</code></td><td><code>fn len(Set&lt;T&gt;) -&gt; Int</code></td><td>Number of elements</td></tr>
			<tr><td><code>from_list</code></td><td><code>fn from_list(List&lt;T&gt;) -&gt; Set&lt;T&gt;</code></td><td>Create from list</td></tr>
		</tbody>
	</table>

	<h4 id="lang-io">IO</h4>

	<h3 id="console">Console</h3>
	<p>Terminal I/O.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>println!</code></td><td><code>fn println!(String) -&gt; Unit</code></td><td>Print with newline</td></tr>
			<tr><td><code>print!</code></td><td><code>fn print!(String) -&gt; Unit</code></td><td>Print without newline</td></tr>
			<tr><td><code>error!</code></td><td><code>fn error!(String) -&gt; Unit</code></td><td>Print to stderr</td></tr>
			<tr><td><code>read_line!</code></td><td><code>fn read_line!() -&gt; String</code></td><td>Read line from stdin</td></tr>
		</tbody>
	</table>

	<h3 id="fs">Fs</h3>
	<p>Filesystem operations.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>read!</code></td><td><code>fn read!(String) -&gt; String</code></td><td>Read file contents</td></tr>
			<tr><td><code>write!</code></td><td><code>fn write!(String, String) -&gt; Unit</code></td><td>Write string to file</td></tr>
			<tr><td><code>exists!</code></td><td><code>fn exists!(String) -&gt; Bool</code></td><td>Check if path exists</td></tr>
			<tr><td><code>read_file!</code></td><td><code>fn read_file!(String) -&gt; String</code></td><td>Alias for read!</td></tr>
			<tr><td><code>write_file!</code></td><td><code>fn write_file!(String, String) -&gt; Unit</code></td><td>Alias for write!</td></tr>
			<tr><td><code>list_dir!</code></td><td><code>fn list_dir!(String) -&gt; List&lt;String&gt;</code></td><td>List directory entries</td></tr>
		</tbody>
	</table>

	<h3 id="env">Env</h3>
	<p>Environment variables.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>get!</code></td><td><code>fn get!(String) -&gt; Option&lt;String&gt;</code></td><td>Read env var</td></tr>
			<tr><td><code>set!</code></td><td><code>fn set!(String, String) -&gt; Unit</code></td><td>Set env var</td></tr>
			<tr><td><code>args!</code></td><td><code>fn args!() -&gt; List&lt;String&gt;</code></td><td>Command-line arguments</td></tr>
		</tbody>
	</table>

	<h4 id="lang-async">Async</h4>

	<h3 id="async">Async</h3>
	<p>Run concurrent tasks with parallel execution, racing, timeouts, and delays.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>parallel!</code></td><td><code>fn parallel!(List&lt;() -&gt; T&gt;) -&gt; List&lt;T&gt;</code></td><td>Run thunks concurrently, collect all results</td></tr>
			<tr><td><code>race!</code></td><td><code>fn race!(List&lt;() -&gt; T&gt;) -&gt; T</code></td><td>Run thunks concurrently, return first result</td></tr>
			<tr><td><code>scatter_gather!</code></td><td><code>fn scatter_gather!(List&lt;() -&gt; T&gt;, (List&lt;T&gt;) -&gt; U) -&gt; U</code></td><td>Run thunks concurrently, apply gather function</td></tr>
			<tr><td><code>delay!</code></td><td><code>fn delay!(Int, () -&gt; T) -&gt; T</code></td><td>Wait milliseconds, then execute thunk</td></tr>
			<tr><td><code>interval!</code></td><td><code>fn interval!(Int, () -&gt; Unit) -&gt; Unit</code></td><td>Repeatedly execute thunk at interval</td></tr>
			<tr><td><code>timeout!</code></td><td><code>fn timeout!(Int, () -&gt; T) -&gt; Result&lt;T, String&gt;</code></td><td>Run thunk with timeout, Err on expiry</td></tr>
		</tbody>
	</table>

	<h3 id="channel">Channel</h3>
	<p>Send and receive messages between concurrent tasks.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>bounded</code></td><td><code>fn bounded(Int) -&gt; (Sender&lt;T&gt;, Receiver&lt;T&gt;)</code></td><td>Create bounded channel with capacity</td></tr>
			<tr><td><code>send!</code></td><td><code>fn send!(Sender&lt;T&gt;, T) -&gt; Unit</code></td><td>Send value into channel</td></tr>
			<tr><td><code>recv!</code></td><td><code>fn recv!(Receiver&lt;T&gt;) -&gt; Option&lt;T&gt;</code></td><td>Receive value, None if closed</td></tr>
			<tr><td><code>close!</code></td><td><code>fn close!(Sender&lt;T&gt;) -&gt; Unit</code></td><td>Close channel</td></tr>
		</tbody>
	</table>

	<h3 id="scope">Scope</h3>
	<p>Structured concurrency.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>spawn!</code></td><td><code>fn spawn!(Scope, () -&gt; T) -&gt; Cell&lt;T&gt;</code></td><td>Spawn task in scope</td></tr>
		</tbody>
	</table>

	<h4 id="lang-data">Data</h4>

	<h3 id="json">Json</h3>
	<p>JSON serialization.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>parse</code></td><td><code>fn parse(String) -&gt; T</code></td><td>Parse JSON string</td></tr>
			<tr><td><code>to_string</code></td><td><code>fn to_string(T) -&gt; String</code></td><td>Serialize to compact JSON</td></tr>
			<tr><td><code>to_string_pretty</code></td><td><code>fn to_string_pretty(T) -&gt; String</code></td><td>Serialize to pretty JSON</td></tr>
			<tr><td><code>to_camel_case</code></td><td><code>fn to_camel_case(T) -&gt; T</code></td><td>Convert keys to camelCase</td></tr>
			<tr><td><code>to_snake_case</code></td><td><code>fn to_snake_case(T) -&gt; T</code></td><td>Convert keys to snake_case</td></tr>
		</tbody>
	</table>

	<h3 id="crypto">Crypto</h3>
	<p>Cryptographic utilities.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>sha256</code></td><td><code>fn sha256(String) -&gt; String</code></td><td>SHA-256 hash</td></tr>
			<tr><td><code>hmac_sha256</code></td><td><code>fn hmac_sha256(String, String) -&gt; String</code></td><td>HMAC-SHA256 signature</td></tr>
			<tr><td><code>constant_time_eq</code></td><td><code>fn constant_time_eq(String, String) -&gt; Bool</code></td><td>Constant-time string comparison</td></tr>
		</tbody>
	</table>

	<h3 id="datetime">DateTime</h3>
	<p>Date and time operations.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>now!</code></td><td><code>fn now!() -&gt; Int</code></td><td>Current Unix timestamp</td></tr>
			<tr><td><code>parse</code></td><td><code>fn parse(String) -&gt; Result&lt;Int, String&gt;</code></td><td>Parse date string to timestamp</td></tr>
			<tr><td><code>to_string</code></td><td><code>fn to_string(Int) -&gt; String</code></td><td>Format timestamp as ISO 8601</td></tr>
			<tr><td><code>add</code></td><td><code>fn add(Int, Int) -&gt; Int</code></td><td>Add seconds to timestamp</td></tr>
			<tr><td><code>diff</code></td><td><code>fn diff(Int, Int) -&gt; Int</code></td><td>Difference in seconds</td></tr>
		</tbody>
	</table>

	<h3 id="math">Math</h3>
	<p>Mathematical functions.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>abs</code></td><td><code>fn abs(Int) -&gt; Int</code></td><td>Absolute value</td></tr>
			<tr><td><code>min</code></td><td><code>fn min(Int, Int) -&gt; Int</code></td><td>Smaller of two</td></tr>
			<tr><td><code>max</code></td><td><code>fn max(Int, Int) -&gt; Int</code></td><td>Larger of two</td></tr>
			<tr><td><code>clamp</code></td><td><code>fn clamp(Int, Int, Int) -&gt; Int</code></td><td>Constrain between min and max</td></tr>
			<tr><td><code>pow</code></td><td><code>fn pow(Int, Int) -&gt; Int</code></td><td>Exponentiation</td></tr>
			<tr><td><code>sqrt</code></td><td><code>fn sqrt(Float) -&gt; Float</code></td><td>Square root</td></tr>
			<tr><td><code>sin</code></td><td><code>fn sin(Float) -&gt; Float</code></td><td>Sine</td></tr>
			<tr><td><code>cos</code></td><td><code>fn cos(Float) -&gt; Float</code></td><td>Cosine</td></tr>
			<tr><td><code>atan2</code></td><td><code>fn atan2(Float, Float) -&gt; Float</code></td><td>Two-argument arctangent</td></tr>
			<tr><td><code>floor</code></td><td><code>fn floor(Float) -&gt; Float</code></td><td>Round down</td></tr>
			<tr><td><code>ceil</code></td><td><code>fn ceil(Float) -&gt; Float</code></td><td>Round up</td></tr>
		</tbody>
	</table>

	<h4 id="lang-error">Error Handling</h4>

	<h3 id="option">Option</h3>
	<p>Optional values.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>unwrap</code></td><td><code>fn unwrap(Option&lt;T&gt;) -&gt; T</code></td><td>Extract value or panic</td></tr>
			<tr><td><code>unwrap_or</code></td><td><code>fn unwrap_or(Option&lt;T&gt;, T) -&gt; T</code></td><td>Extract value or default</td></tr>
			<tr><td><code>is_some</code></td><td><code>fn is_some(Option&lt;T&gt;) -&gt; Bool</code></td><td>Has value?</td></tr>
			<tr><td><code>is_none</code></td><td><code>fn is_none(Option&lt;T&gt;) -&gt; Bool</code></td><td>Is None?</td></tr>
			<tr><td><code>ok_or</code></td><td><code>fn ok_or(Option&lt;T&gt;, E) -&gt; Result&lt;T, E&gt;</code></td><td>Convert to Result</td></tr>
			<tr><td><code>map</code></td><td><code>fn map(Option&lt;T&gt;, (T) -&gt; U) -&gt; Option&lt;U&gt;</code></td><td>Transform value</td></tr>
			<tr><td><code>flat_map</code></td><td><code>fn flat_map(Option&lt;T&gt;, (T) -&gt; Option&lt;U&gt;) -&gt; Option&lt;U&gt;</code></td><td>Chain optional operations</td></tr>
		</tbody>
	</table>

	<h3 id="result">Result</h3>
	<p>Error handling.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>unwrap</code></td><td><code>fn unwrap(Result&lt;T, E&gt;) -&gt; T</code></td><td>Extract Ok or panic</td></tr>
			<tr><td><code>unwrap_or</code></td><td><code>fn unwrap_or(Result&lt;T, E&gt;, T) -&gt; T</code></td><td>Extract Ok or default</td></tr>
			<tr><td><code>is_ok</code></td><td><code>fn is_ok(Result&lt;T, E&gt;) -&gt; Bool</code></td><td>Is Ok?</td></tr>
			<tr><td><code>is_err</code></td><td><code>fn is_err(Result&lt;T, E&gt;) -&gt; Bool</code></td><td>Is Err?</td></tr>
			<tr><td><code>ensure</code></td><td><code>fn ensure(Bool, E) -&gt; Result&lt;Unit, E&gt;</code></td><td>Assert condition</td></tr>
			<tr><td><code>map</code></td><td><code>fn map(Result&lt;T, E&gt;, (T) -&gt; U) -&gt; Result&lt;U, E&gt;</code></td><td>Transform Ok value</td></tr>
			<tr><td><code>and_then</code></td><td><code>fn and_then(Result&lt;T, E&gt;, (T) -&gt; Result&lt;U, E&gt;) -&gt; Result&lt;U, E&gt;</code></td><td>Chain Result operations</td></tr>
			<tr><td><code>map_err</code></td><td><code>fn map_err(Result&lt;T, E&gt;, (E) -&gt; F) -&gt; Result&lt;T, F&gt;</code></td><td>Transform Err value</td></tr>
			<tr><td><code>context</code></td><td><code>fn context(Result&lt;T, E&gt;, String) -&gt; Result&lt;T, {"{"}context: String, error: E{"}"}&gt;</code></td><td>Add error context</td></tr>
		</tbody>
	</table>

	<h4 id="lang-other">Other</h4>

	<h3 id="log">Log</h3>
	<p>Structured logging (ambient effect).</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>info!</code></td><td><code>fn info!(String) -&gt; Unit</code></td><td>Log at info level</td></tr>
			<tr><td><code>warn!</code></td><td><code>fn warn!(String) -&gt; Unit</code></td><td>Log at warning level</td></tr>
			<tr><td><code>error!</code></td><td><code>fn error!(String) -&gt; Unit</code></td><td>Log at error level</td></tr>
			<tr><td><code>debug!</code></td><td><code>fn debug!(String) -&gt; Unit</code></td><td>Log at debug level</td></tr>
		</tbody>
	</table>

	<h3 id="time">Time</h3>
	<p>Clock operations (ambient effect).</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>now!</code></td><td><code>fn now!() -&gt; Int</code></td><td>Current Unix timestamp in milliseconds</td></tr>
			<tr><td><code>sleep!</code></td><td><code>fn sleep!(Int) -&gt; Unit</code></td><td>Pause for milliseconds</td></tr>
		</tbody>
	</table>

	<h3 id="random">Random</h3>
	<p>Random number generation.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>int!</code></td><td><code>fn int!(Int, Int) -&gt; Int</code></td><td>Random int in range (inclusive)</td></tr>
			<tr><td><code>bool!</code></td><td><code>fn bool!() -&gt; Bool</code></td><td>Random boolean</td></tr>
			<tr><td><code>uuid!</code></td><td><code>fn uuid!() -&gt; String</code></td><td>Random UUID v4</td></tr>
			<tr><td><code>bytes!</code></td><td><code>fn bytes!(Int) -&gt; String</code></td><td>Random bytes as hex (max 1024)</td></tr>
		</tbody>
	</table>

	<h3 id="weak">Weak</h3>
	<p>Weak references.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>downgrade</code></td><td><code>fn downgrade(T) -&gt; Weak&lt;T&gt;</code></td><td>Create weak reference</td></tr>
			<tr><td><code>upgrade</code></td><td><code>fn upgrade(Weak&lt;T&gt;) -&gt; Option&lt;T&gt;</code></td><td>Try to upgrade to strong reference</td></tr>
		</tbody>
	</table>
</section>

<section id="server-modules">
	<h2>Server Modules</h2>

	<h4 id="srv-web">Web</h4>

	<h3 id="router">Router</h3>
	<p>HTTP routing.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>new</code></td><td><code>fn new() -&gt; Router</code></td><td>Create empty router</td></tr>
			<tr><td><code>get</code></td><td><code>fn get(Router, String, Handler) -&gt; Router</code></td><td>GET route</td></tr>
			<tr><td><code>post</code></td><td><code>fn post(Router, String, Handler) -&gt; Router</code></td><td>POST route</td></tr>
			<tr><td><code>put</code></td><td><code>fn put(Router, String, Handler) -&gt; Router</code></td><td>PUT route</td></tr>
			<tr><td><code>delete</code></td><td><code>fn delete(Router, String, Handler) -&gt; Router</code></td><td>DELETE route</td></tr>
			<tr><td><code>patch</code></td><td><code>fn patch(Router, String, Handler) -&gt; Router</code></td><td>PATCH route</td></tr>
			<tr><td><code>options</code></td><td><code>fn options(Router, String, Handler) -&gt; Router</code></td><td>OPTIONS route</td></tr>
			<tr><td><code>head</code></td><td><code>fn head(Router, String, Handler) -&gt; Router</code></td><td>HEAD route</td></tr>
			<tr><td><code>any</code></td><td><code>fn any(Router, String, Handler) -&gt; Router</code></td><td>All methods</td></tr>
			<tr><td><code>use</code></td><td><code>fn use(Router, Middleware) -&gt; Router</code></td><td>Add middleware</td></tr>
			<tr><td><code>group</code></td><td><code>fn group(Router, String, Router) -&gt; Router</code></td><td>Group under prefix</td></tr>
			<tr><td><code>resources</code></td><td><code>fn resources(Router, String, Record) -&gt; Router</code></td><td>RESTful resource routes</td></tr>
			<tr><td><code>state</code></td><td><code>fn state(Router, String, T) -&gt; Router</code></td><td>Attach shared state</td></tr>
			<tr><td><code>docs_json</code></td><td><code>fn docs_json(Router) -&gt; String</code></td><td>Generate OpenAPI spec</td></tr>
			<tr><td><code>ws</code></td><td><code>fn ws(Router, String, Handler) -&gt; Router</code></td><td>WebSocket handler</td></tr>
			<tr><td><code>routes</code></td><td><code>fn routes(Router) -&gt; List&lt;Record&gt;</code></td><td>Route table</td></tr>
		</tbody>
	</table>

	<h3 id="server">Server</h3>
	<p>HTTP server.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>listen!</code></td><td><code>fn listen!(Router, Int) -&gt; Unit</code></td><td>Start server on port</td></tr>
		</tbody>
	</table>

	<h3 id="request">Request</h3>
	<p>HTTP request object.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>header</code></td><td><code>fn header(Request, String) -&gt; Option&lt;String&gt;</code></td><td>Read header</td></tr>
			<tr><td><code>method</code></td><td><code>fn method(Request) -&gt; String</code></td><td>HTTP method</td></tr>
			<tr><td><code>body_json</code></td><td><code>fn body_json(Request) -&gt; Result&lt;T, HttpError&gt;</code></td><td>Parse body as JSON</td></tr>
			<tr><td><code>param</code></td><td><code>fn param(Request, String) -&gt; Result&lt;String, String&gt;</code></td><td>Path parameter</td></tr>
			<tr><td><code>param_int</code></td><td><code>fn param_int(Request, String) -&gt; Result&lt;Int, String&gt;</code></td><td>Path parameter as int</td></tr>
			<tr><td><code>query</code></td><td><code>fn query(Request, String) -&gt; Option&lt;String&gt;</code></td><td>Query parameter</td></tr>
			<tr><td><code>query_int</code></td><td><code>fn query_int(Request, String) -&gt; Result&lt;Int, String&gt;</code></td><td>Query parameter as int</td></tr>
			<tr><td><code>decode</code></td><td><code>fn decode(Request, String) -&gt; Result&lt;T, Response&gt;</code></td><td>Decode and validate body</td></tr>
			<tr><td><code>multipart</code></td><td><code>fn multipart(Request) -&gt; List&lt;Record&gt;</code></td><td>Parse multipart form data</td></tr>
		</tbody>
	</table>

	<h3 id="response">Response</h3>
	<p>HTTP response construction.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>ok</code></td><td><code>fn ok(String) -&gt; Response</code></td><td>200 with text</td></tr>
			<tr><td><code>json</code></td><td><code>fn json(T) -&gt; Response</code></td><td>200 with JSON</td></tr>
			<tr><td><code>created</code></td><td><code>fn created(T) -&gt; Response</code></td><td>201</td></tr>
			<tr><td><code>no_content</code></td><td><code>fn no_content() -&gt; Response</code></td><td>204</td></tr>
			<tr><td><code>bad_request</code></td><td><code>fn bad_request(String) -&gt; Response</code></td><td>400</td></tr>
			<tr><td><code>unauthorized</code></td><td><code>fn unauthorized(String) -&gt; Response</code></td><td>401</td></tr>
			<tr><td><code>forbidden</code></td><td><code>fn forbidden(String) -&gt; Response</code></td><td>403</td></tr>
			<tr><td><code>not_found</code></td><td><code>fn not_found(String) -&gt; Response</code></td><td>404</td></tr>
			<tr><td><code>method_not_allowed</code></td><td><code>fn method_not_allowed(String) -&gt; Response</code></td><td>405</td></tr>
			<tr><td><code>conflict</code></td><td><code>fn conflict(String) -&gt; Response</code></td><td>409</td></tr>
			<tr><td><code>unprocessable</code></td><td><code>fn unprocessable(String) -&gt; Response</code></td><td>422</td></tr>
			<tr><td><code>too_many_requests</code></td><td><code>fn too_many_requests(String) -&gt; Response</code></td><td>429</td></tr>
			<tr><td><code>error</code></td><td><code>fn error(String) -&gt; Response</code></td><td>500</td></tr>
			<tr><td><code>bad_gateway</code></td><td><code>fn bad_gateway(String) -&gt; Response</code></td><td>502</td></tr>
			<tr><td><code>service_unavailable</code></td><td><code>fn service_unavailable(String) -&gt; Response</code></td><td>503</td></tr>
			<tr><td><code>gateway_timeout</code></td><td><code>fn gateway_timeout(String) -&gt; Response</code></td><td>504</td></tr>
			<tr><td><code>status</code></td><td><code>fn status(Int, String) -&gt; Response</code></td><td>Custom status</td></tr>
			<tr><td><code>with_header</code></td><td><code>fn with_header(Response, String, String) -&gt; Response</code></td><td>Add header</td></tr>
			<tr><td><code>with_headers</code></td><td><code>fn with_headers(Response, List&lt;(String, String)&gt;) -&gt; Response</code></td><td>Add multiple headers</td></tr>
			<tr><td><code>set_cookie</code></td><td><code>fn set_cookie(Response, String, String) -&gt; Response</code></td><td>Set cookie</td></tr>
			<tr><td><code>redirect</code></td><td><code>fn redirect(String) -&gt; Response</code></td><td>302 redirect</td></tr>
			<tr><td><code>redirect_permanent</code></td><td><code>fn redirect_permanent(String) -&gt; Response</code></td><td>301 redirect</td></tr>
			<tr><td><code>redirect_temporary</code></td><td><code>fn redirect_temporary(String) -&gt; Response</code></td><td>307 redirect</td></tr>
		</tbody>
	</table>

	<h3 id="http">Http</h3>
	<p>HTTP client.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>get!</code></td><td><code>fn get!(String) -&gt; Response</code></td><td>GET request</td></tr>
			<tr><td><code>post!</code></td><td><code>fn post!(String, String) -&gt; Response</code></td><td>POST request with body</td></tr>
			<tr><td><code>put!</code></td><td><code>fn put!(String, String) -&gt; Response</code></td><td>PUT request with body</td></tr>
			<tr><td><code>delete!</code></td><td><code>fn delete!(String) -&gt; Response</code></td><td>DELETE request</td></tr>
			<tr><td><code>request!</code></td><td><code>fn request!(RequestConfig) -&gt; Response</code></td><td>Custom request</td></tr>
		</tbody>
	</table>

	<h4 id="srv-db">Database</h4>

	<h3 id="sqlite">Sqlite</h3>
	<p>SQLite database.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>connect!</code></td><td><code>fn connect!(String) -&gt; Unit</code></td><td>Open database file</td></tr>
			<tr><td><code>execute!</code></td><td><code>fn execute!(String, List&lt;String&gt;) -&gt; Int</code></td><td>Execute statement</td></tr>
			<tr><td><code>query!</code></td><td><code>fn query!(String, List&lt;String&gt;) -&gt; List&lt;Row&gt;</code></td><td>Run query</td></tr>
			<tr><td><code>query_one!</code></td><td><code>fn query_one!(String, List&lt;String&gt;) -&gt; Option&lt;Row&gt;</code></td><td>First row</td></tr>
			<tr><td><code>query_as!</code></td><td><code>fn query_as!(Decoder, String, List&lt;String&gt;) -&gt; List&lt;T&gt;</code></td><td>Query with decoder</td></tr>
			<tr><td><code>query_one_as!</code></td><td><code>fn query_one_as!(Decoder, String, List&lt;String&gt;) -&gt; Option&lt;T&gt;</code></td><td>First row with decoder</td></tr>
			<tr><td><code>query_map!</code></td><td><code>fn query_map!(String, List&lt;String&gt;) -&gt; List&lt;Row&gt;</code></td><td>Query for mapping</td></tr>
		</tbody>
	</table>

	<h3 id="postgres">Postgres</h3>
	<p>PostgreSQL database.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>connect!</code></td><td><code>fn connect!(String) -&gt; Unit</code></td><td>Open connection</td></tr>
			<tr><td><code>execute!</code></td><td><code>fn execute!(String, List&lt;String&gt;) -&gt; Int</code></td><td>Execute statement</td></tr>
			<tr><td><code>query!</code></td><td><code>fn query!(String, List&lt;String&gt;) -&gt; List&lt;Row&gt;</code></td><td>Run query</td></tr>
			<tr><td><code>query_one!</code></td><td><code>fn query_one!(String, List&lt;String&gt;) -&gt; Option&lt;Row&gt;</code></td><td>First row</td></tr>
			<tr><td><code>query_as!</code></td><td><code>fn query_as!(Decoder, String, List&lt;String&gt;) -&gt; List&lt;T&gt;</code></td><td>Query with decoder</td></tr>
			<tr><td><code>query_one_as!</code></td><td><code>fn query_one_as!(Decoder, String, List&lt;String&gt;) -&gt; Option&lt;T&gt;</code></td><td>First row with decoder</td></tr>
			<tr><td><code>query_map!</code></td><td><code>fn query_map!(String, List&lt;String&gt;) -&gt; List&lt;Row&gt;</code></td><td>Query for mapping</td></tr>
		</tbody>
	</table>

	<h3 id="mysql">Mysql</h3>
	<p>MySQL database.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>connect!</code></td><td><code>fn connect!(String) -&gt; Unit</code></td><td>Open connection</td></tr>
			<tr><td><code>execute!</code></td><td><code>fn execute!(String, List&lt;String&gt;) -&gt; Int</code></td><td>Execute statement</td></tr>
			<tr><td><code>query!</code></td><td><code>fn query!(String, List&lt;String&gt;) -&gt; List&lt;Row&gt;</code></td><td>Run query</td></tr>
			<tr><td><code>query_one!</code></td><td><code>fn query_one!(String, List&lt;String&gt;) -&gt; Option&lt;Row&gt;</code></td><td>First row</td></tr>
			<tr><td><code>query_as!</code></td><td><code>fn query_as!(Decoder, String, List&lt;String&gt;) -&gt; List&lt;T&gt;</code></td><td>Query with decoder</td></tr>
			<tr><td><code>query_one_as!</code></td><td><code>fn query_one_as!(Decoder, String, List&lt;String&gt;) -&gt; Option&lt;T&gt;</code></td><td>First row with decoder</td></tr>
			<tr><td><code>query_map!</code></td><td><code>fn query_map!(String, List&lt;String&gt;) -&gt; List&lt;Row&gt;</code></td><td>Query for mapping</td></tr>
		</tbody>
	</table>

	<h3 id="row">Row</h3>
	<p>Database row access.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>string</code></td><td><code>fn string(Row, String) -&gt; String</code></td><td>String column</td></tr>
			<tr><td><code>int</code></td><td><code>fn int(Row, String) -&gt; Int</code></td><td>Int column</td></tr>
			<tr><td><code>float</code></td><td><code>fn float(Row, String) -&gt; Float</code></td><td>Float column</td></tr>
			<tr><td><code>bool</code></td><td><code>fn bool(Row, String) -&gt; Bool</code></td><td>Bool column</td></tr>
			<tr><td><code>optional_string</code></td><td><code>fn optional_string(Row, String) -&gt; Option&lt;String&gt;</code></td><td>Nullable string</td></tr>
			<tr><td><code>optional_int</code></td><td><code>fn optional_int(Row, String) -&gt; Option&lt;Int&gt;</code></td><td>Nullable int</td></tr>
			<tr><td><code>decode</code></td><td><code>fn decode(Row, Decoder) -&gt; T</code></td><td>Decode row to record</td></tr>
		</tbody>
	</table>

	<h3 id="sql">Sql</h3>
	<p>Database migrations.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>migrate!</code></td><td><code>fn migrate!(String) -&gt; Result&lt;Int, String&gt;</code></td><td>Run migrations from directory</td></tr>
		</tbody>
	</table>

	<h4 id="srv-auth">Auth</h4>

	<h3 id="jwt">Jwt</h3>
	<p>JSON Web Tokens.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>sign</code></td><td><code>fn sign(Record, String) -&gt; String</code></td><td>Sign claims as JWT (HS256)</td></tr>
			<tr><td><code>sign_with</code></td><td><code>fn sign_with(Record, String, {"{"} expires_in: Int {"}"}) -&gt; String</code></td><td>Sign with options</td></tr>
			<tr><td><code>verify</code></td><td><code>fn verify(String, String) -&gt; Result&lt;Record, String&gt;</code></td><td>Verify and decode</td></tr>
			<tr><td><code>decode</code></td><td><code>fn decode(String) -&gt; Result&lt;Record, String&gt;</code></td><td>Decode without verifying</td></tr>
		</tbody>
	</table>

	<h3 id="session">Session</h3>
	<p>Session management.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>create!</code></td><td><code>fn create!(Record) -&gt; String</code></td><td>Create session, return ID</td></tr>
			<tr><td><code>get!</code></td><td><code>fn get!(String) -&gt; Option&lt;Record&gt;</code></td><td>Get session data</td></tr>
			<tr><td><code>set!</code></td><td><code>fn set!(String, String, T) -&gt; Unit</code></td><td>Update session field</td></tr>
			<tr><td><code>delete!</code></td><td><code>fn delete!(String) -&gt; Unit</code></td><td>Remove session</td></tr>
		</tbody>
	</table>

	<h3 id="middleware">Middleware</h3>
	<p>Request middleware utilities.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>extract_bearer</code></td><td><code>fn extract_bearer(Request) -&gt; Result&lt;String, HttpError&gt;</code></td><td>Extract Bearer token</td></tr>
			<tr><td><code>extract_basic</code></td><td><code>fn extract_basic(Request) -&gt; Result&lt;(String, String), HttpError&gt;</code></td><td>Extract Basic auth</td></tr>
			<tr><td><code>cors_config</code></td><td><code>fn cors_config(List&lt;String&gt;) -&gt; Record</code></td><td>CORS configuration</td></tr>
			<tr><td><code>rate_limit_config</code></td><td><code>fn rate_limit_config(Int, Int) -&gt; Record</code></td><td>Rate limiting config</td></tr>
		</tbody>
	</table>

	<h4 id="srv-validation">Validation</h4>

	<h3 id="validate">Validate</h3>
	<p>Request validation.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>required</code></td><td><code>fn required(T, String) -&gt; Result&lt;T, HttpError&gt;</code></td><td>Check not None</td></tr>
			<tr><td><code>string</code></td><td><code>fn string(T, String) -&gt; Result&lt;String, HttpError&gt;</code></td><td>Validate string</td></tr>
			<tr><td><code>int</code></td><td><code>fn int(T, String) -&gt; Result&lt;Int, HttpError&gt;</code></td><td>Validate int</td></tr>
			<tr><td><code>boolean</code></td><td><code>fn boolean(T, String) -&gt; Result&lt;Bool, HttpError&gt;</code></td><td>Validate bool</td></tr>
			<tr><td><code>min_length</code></td><td><code>fn min_length(String, Int, String) -&gt; Result&lt;String, HttpError&gt;</code></td><td>Min string length</td></tr>
			<tr><td><code>max_length</code></td><td><code>fn max_length(String, Int, String) -&gt; Result&lt;String, HttpError&gt;</code></td><td>Max string length</td></tr>
			<tr><td><code>min</code></td><td><code>fn min(Int, Int, String) -&gt; Result&lt;Int, HttpError&gt;</code></td><td>Min number</td></tr>
			<tr><td><code>max</code></td><td><code>fn max(Int, Int, String) -&gt; Result&lt;Int, HttpError&gt;</code></td><td>Max number</td></tr>
			<tr><td><code>one_of</code></td><td><code>fn one_of(String, List&lt;String&gt;, String) -&gt; Result&lt;String, HttpError&gt;</code></td><td>Allowed values</td></tr>
		</tbody>
	</table>

	<h3 id="httperror">HttpError</h3>
	<p>Typed HTTP errors.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>bad_request</code></td><td><code>fn bad_request(String) -&gt; HttpError</code></td><td>400</td></tr>
			<tr><td><code>unauthorized</code></td><td><code>fn unauthorized(String) -&gt; HttpError</code></td><td>401</td></tr>
			<tr><td><code>forbidden</code></td><td><code>fn forbidden(String) -&gt; HttpError</code></td><td>403</td></tr>
			<tr><td><code>not_found</code></td><td><code>fn not_found(String) -&gt; HttpError</code></td><td>404</td></tr>
			<tr><td><code>method_not_allowed</code></td><td><code>fn method_not_allowed(String) -&gt; HttpError</code></td><td>405</td></tr>
			<tr><td><code>conflict</code></td><td><code>fn conflict(String) -&gt; HttpError</code></td><td>409</td></tr>
			<tr><td><code>unprocessable</code></td><td><code>fn unprocessable(String) -&gt; HttpError</code></td><td>422</td></tr>
			<tr><td><code>too_many_requests</code></td><td><code>fn too_many_requests(String) -&gt; HttpError</code></td><td>429</td></tr>
			<tr><td><code>internal</code></td><td><code>fn internal(String) -&gt; HttpError</code></td><td>500</td></tr>
			<tr><td><code>bad_gateway</code></td><td><code>fn bad_gateway(String) -&gt; HttpError</code></td><td>502</td></tr>
			<tr><td><code>service_unavailable</code></td><td><code>fn service_unavailable(String) -&gt; HttpError</code></td><td>503</td></tr>
			<tr><td><code>gateway_timeout</code></td><td><code>fn gateway_timeout(String) -&gt; HttpError</code></td><td>504</td></tr>
			<tr><td><code>with_context</code></td><td><code>fn with_context(HttpError, String) -&gt; HttpError</code></td><td>Add context</td></tr>
			<tr><td><code>with_code</code></td><td><code>fn with_code(HttpError, String) -&gt; HttpError</code></td><td>Add error code</td></tr>
			<tr><td><code>to_response</code></td><td><code>fn to_response(HttpError) -&gt; Response</code></td><td>Convert to Response</td></tr>
		</tbody>
	</table>

	<h4 id="srv-realtime">Realtime</h4>

	<h3 id="ws">Ws</h3>
	<p>WebSocket communication.</p>
	<table>
		<thead><tr><th>Function</th><th>Signature</th><th>Description</th></tr></thead>
		<tbody>
			<tr><td><code>send!</code></td><td><code>fn send!(String) -&gt; Unit</code></td><td>Send message</td></tr>
			<tr><td><code>receive!</code></td><td><code>fn receive!() -&gt; Result&lt;String, String&gt;</code></td><td>Receive message</td></tr>
			<tr><td><code>close!</code></td><td><code>fn close!() -&gt; Unit</code></td><td>Close connection</td></tr>
		</tbody>
	</table>
</section>
