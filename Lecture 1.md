---


---

<h2 id="intrusive-container">Intrusive Container</h2>
<p>Хотим оптимально хранить движущихся юнитов</p>
<pre class=" language-cpp"><code class="prism  language-cpp">set<span class="token operator">&lt;</span>unit<span class="token operator">*</span><span class="token operator">&gt;</span> units<span class="token punctuation">;</span>
set<span class="token operator">&lt;</span>unit<span class="token operator">*</span><span class="token operator">&gt;</span> moving_units<span class="token punctuation">;</span>
</code></pre>
<p>Можно заменить <code>moving_units</code> на двухсвязанный список с <code>next</code> и  <code>prev</code> (<code>intrusive_list)</code></p>
<p><code>Units</code> можно связать так же</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">template</span><span class="token operator">&lt;</span><span class="token keyword">typename</span> Tag <span class="token operator">=</span> defaltH_tag<span class="token operator">&gt;</span>
<span class="token keyword">struct</span> node <span class="token punctuation">{</span>
	node <span class="token operator">*</span>next<span class="token punctuation">,</span> <span class="token operator">*</span>prev<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<pre class=" language-cpp"><code class="prism  language-cpp">unit<span class="token operator">*</span> <span class="token function">toUnit</span><span class="token punctuation">(</span>node<span class="token operator">*</span> p<span class="token punctuation">)</span> <span class="token punctuation">}</span>
	<span class="token keyword">return</span> <span class="token punctuation">(</span>unit<span class="token operator">*</span><span class="token punctuation">)</span> <span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token keyword">char</span><span class="token operator">*</span><span class="token punctuation">)</span>p <span class="token operator">-</span> <span class="token function">offsetof</span><span class="token punctuation">(</span>unit<span class="token punctuation">,</span> link<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span> 

<span class="token keyword">struct</span> unit <span class="token operator">:</span> node<span class="token operator">&lt;</span><span class="token keyword">struct</span> all_units_tag<span class="token operator">&gt;</span> <span class="token punctuation">{</span>
<span class="token punctuation">}</span>

intrusive_list<span class="token operator">&lt;</span>unit<span class="token punctuation">,</span> <span class="token keyword">struct</span> all_units_tag<span class="token operator">&gt;</span><span class="token punctuation">;</span>
</code></pre>

<table>
<thead>
<tr>
<th></th>
<th>intrusive</th>
<th>not-intrusive</th>
</tr>
</thead>
<tbody>
<tr>
<td>memory size</td>
<td>less? (+)</td>
<td>greater?</td>
</tr>
<tr>
<td>number of alloc</td>
<td>smaller (+)</td>
<td>bigger</td>
</tr>
<tr>
<td>require object modification</td>
<td><strong>YES</strong> (-)</td>
<td>No</td>
</tr>
<tr>
<td>containers</td>
<td>non-copyable (-)</td>
<td>copyable</td>
</tr>
<tr>
<td>container manages lifetime of objects</td>
<td><strong>NO (critical)</strong> (-)</td>
<td>Yes</td>
</tr>
<tr>
<td>exception safety guarantees</td>
<td>better (mostly nothrow) (+)</td>
<td>worse</td>
</tr>
<tr>
<td>value -&gt; iterator</td>
<td>allow (+)</td>
<td>disallow</td>
</tr>
</tbody>
</table>
