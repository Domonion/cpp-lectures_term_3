---


---

<h2 id="ranges">Ranges</h2>
<pre class=" language-cpp"><code class="prism  language-cpp">std<span class="token operator">::</span>transform
std<span class="token operator">::</span>copy_if
</code></pre>
<p>Вместо того чтобы делать цикл который делай не пойми что можно запилить набор функциональных штук выше, которые выполнят нашу задачу</p>
<p>Удобно их собирать в некоторые “контейнеры” — <strong>view</strong>. Самый внешний такой вью называется <strong>Ranges v3</strong> (оно же библиотека)</p>
<p>Насколько понял, оно считает это все постепенно, а не все сразу (намек на корутины?), но можно сказать посчитай все сразу.</p>
<h3 id="push-and-pull-strategies">Push and pull strategies</h3>
<p>Пусть у нас есть такая последовательность вызовов <code>h(g(f(...)))</code></p>
<p><strong>Pull model</strong> (это вьюхи)<br>
Стратегия следующая: внешний цикл хочет прочитать данные у <code>h</code>,  <code>h</code> в свою очередь спрашивает их у <code>g</code>,  а потом с этими данными что-то делает. <code>g</code> же спрашивает их у <code>f</code> и т. д.</p>
<p><strong>Push model</strong> (а это уже не вьюха, это что-то другое)<br>
Другая стратегия: Мы говорим <code>f</code> “запиши нам данные”, но <code>f</code> ничего не хранит и предает их в <code>g</code>, <code>g</code> что-то тоже ничего не хранит и передает их в <code>h</code> и т. д.</p>
<p>Если мы хотим дерево, то если <code>g</code> зависит от двух <code>f</code>, то <code>push</code> нереализуема, а если <code>g</code> разбивается на две <code>h</code>, то неосуществима <code>pull</code>.</p>
<p><strong>Например:</strong><br>
<code>cin</code> работает по <code>pull</code> модели<br>
<code>cout</code> работает по <code>push</code> модели</p>
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

