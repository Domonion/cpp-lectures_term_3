---


---

<h2 id="stackless-coroutines">Stackless coroutines</h2>
<p>Основаны на том, что компилятор за нас будет переписывать код</p>
<pre class=" language-cpp"><code class="prism  language-cpp">generator<span class="token operator">&lt;</span><span class="token keyword">int</span><span class="token operator">&gt;</span> <span class="token function">tenInts</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token keyword">int</span> i <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span> i <span class="token operator">&lt;</span> <span class="token number">10</span><span class="token punctuation">;</span> <span class="token operator">++</span>i<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		cout <span class="token operator">&lt;&lt;</span> <span class="token string">"Next: "</span> <span class="token operator">&lt;&lt;</span> i<span class="token punctuation">;</span>
		co_yield i<span class="token punctuation">;</span> <span class="token comment">// Точка паузы + return</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	genetaror<span class="token operator">&lt;</span><span class="token keyword">int</span><span class="token operator">&gt;</span> g <span class="token operator">=</span> <span class="token function">tenInts</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token keyword">int</span> v <span class="token operator">:</span> g<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// Do something</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Позволяет упростить асинхронные операции</p>
<pre class=" language-cpp"><code class="prism  language-cpp">future<span class="token operator">&lt;</span>path<span class="token operator">&gt;</span> <span class="token function">cacheUrl</span><span class="token punctuation">(</span>string url<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	cout <span class="token operator">&lt;&lt;</span> <span class="token string">"Downloading url"</span><span class="token punctuation">;</span>
	string text <span class="token operator">=</span> co_await <span class="token function">downloadingAsync</span><span class="token punctuation">(</span>url<span class="token punctuation">)</span><span class="token punctuation">;</span>
	
	cout <span class="token operator">&lt;&lt;</span> <span class="token string">"Saving in cache"</span><span class="token punctuation">;</span>
	path p <span class="token operator">=</span> <span class="token function">randomFileName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	
	co_await <span class="token function">saveInCacheAsync</span><span class="token punctuation">(</span>p<span class="token punctuation">,</span> text<span class="token punctuation">)</span><span class="token punctuation">;</span>

	co_return p<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Как корутина переписывается компилятором</p>
<pre class=" language-cpp"><code class="prism  language-cpp">ret_type <span class="token function">answer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">struct</span> frame_type <span class="token punctuation">{</span>
		generator<span class="token operator">&lt;</span><span class="token keyword">int</span><span class="token operator">&gt;</span><span class="token operator">::</span>promise_type promise<span class="token punctuation">;</span>
		<span class="token comment">//local</span>
		<span class="token comment">//resume point</span>
	<span class="token punctuation">}</span>
	frame_type <span class="token operator">*</span>f <span class="token operator">=</span> <span class="token function">create_frame</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">auto</span> <span class="token operator">&amp;</span>p <span class="token operator">=</span> 
	<span class="token keyword">new</span><span class="token punctuation">(</span>ret_address<span class="token punctuation">)</span> <span class="token function">ret_tupe</span><span class="token punctuation">(</span>p<span class="token punctuation">.</span><span class="token function">get_return_object</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	co_await p<span class="token punctuation">.</span><span class="token function">initial_suspend</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">try</span> <span class="token punctuation">{</span>
		p<span class="token punctuation">.</span><span class="token function">return_value</span><span class="token punctuation">(</span><span class="token number">42</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>P.s</strong><br>
Эту херню так просто не поймешь<br>
<em>Гор Нишанов, C++ Coroutines – a negative overhead abstraction</em> - вот что посмотреть надо</p>

