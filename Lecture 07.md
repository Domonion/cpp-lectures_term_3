---


---

<h3 id="signals">Signals</h3>
<p>Signals aka listeners aka observers<br>
Не путой с<br>
Unix signals</p>
<p><code>slot</code> - подписанный на событие</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">struct</span> signal <span class="token punctuation">{</span>
	typesef function <span class="token operator">&lt;</span><span class="token keyword">void</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token operator">&gt;</span> slot<span class="token punctuation">;</span>
	<span class="token keyword">void</span> <span class="token function">connect</span><span class="token punctuation">(</span>slot s<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		slots<span class="token punctuation">.</span><span class="token function">push_back</span><span class="token punctuation">(</span><span class="token function">move</span><span class="token punctuation">(</span>s<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>	
	<span class="token punctuation">}</span>
	
	<span class="token keyword">void</span> <span class="token keyword">operator</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">const</span> <span class="token punctuation">{</span>
		<span class="token keyword">for</span><span class="token punctuation">(</span><span class="token operator">&amp;&amp;</span><span class="token keyword">auto</span> s<span class="token operator">:</span> slots<span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token function">s</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span>

<span class="token keyword">private</span><span class="token operator">:</span>
 	vector<span class="token operator">&lt;</span>slot<span class="token operator">&gt;</span> slots<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Проблемы:<br>
1. Если <code>s</code> бросает исключение. Пробрасывать его выше не стоит, так как кто вызывает сигнал, обычно не представляет что за ошибка вообще.<br>
2. Как отписываться? <code>function</code> не умеет сравниваться. Можно возвращать <code>id</code>'шник из <code>connect</code> и удалять по нему.  Логично использовать двусвязный список.</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">struct</span> signal <span class="token punctuation">{</span>
	typesef function <span class="token operator">&lt;</span><span class="token keyword">void</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token operator">&gt;</span> slot<span class="token punctuation">;</span>
	connection <span class="token function">connect</span><span class="token punctuation">(</span>slot s<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		slots<span class="token punctuation">.</span><span class="token function">push_back</span><span class="token punctuation">(</span><span class="token function">move</span><span class="token punctuation">(</span>s<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>	
		<span class="token keyword">return</span> <span class="token function">connection</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">,</span> <span class="token operator">--</span>slots<span class="token punctuation">.</span><span class="token function">end</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
	
	<span class="token keyword">void</span> <span class="token keyword">operator</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">const</span> <span class="token punctuation">{</span>
		<span class="token keyword">for</span><span class="token punctuation">(</span><span class="token operator">&amp;&amp;</span><span class="token keyword">auto</span> s<span class="token operator">:</span> slots<span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token keyword">try</span> <span class="token punctuation">{</span> <span class="token function">s</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
			<span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token function">log</span><span class="token punctuation">(</span><span class="token string">'...'</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span>

<span class="token keyword">private</span><span class="token operator">:</span>
 	list<span class="token operator">&lt;</span>slot<span class="token operator">&gt;</span> slots<span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">struct</span> signal<span class="token operator">::</span>connection <span class="token punctuation">{</span>
	<span class="token function">connection</span><span class="token punctuation">(</span>signal<span class="token operator">*</span> sig<span class="token punctuation">,</span> list<span class="token operator">&lt;</span>slot<span class="token operator">&gt;</span><span class="token operator">::</span>iterator it<span class="token punctuation">)</span> <span class="token operator">:</span> <span class="token function">sig</span><span class="token punctuation">(</span>sig<span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token function">it</span><span class="token punctuation">(</span>it<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>
	<span class="token function">disconnect</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		sig <span class="token operator">-</span><span class="token operator">&gt;</span> slots<span class="token punctuation">.</span><span class="token function">erase</span><span class="token punctuation">(</span>it<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token keyword">private</span><span class="token operator">:</span>
	signal<span class="token operator">*</span> sig<span class="token punctuation">;</span>
	list<span class="token operator">&lt;</span>slot<span class="token operator">&gt;</span><span class="token operator">::</span>iterator it<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Но вот большая беда:</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">void</span> <span class="token function">on_timeout</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
	conn<span class="token punctuation">.</span><span class="token function">disconnect</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Мы будем удалять элемент из листа во время прохода по листу<br>
Хранить указатель на следующий не сильно поможет, так как один обработчик может удалять другие<br>
Можно попробовать копировать</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">struct</span> signal <span class="token punctuation">{</span>
	<span class="token operator">/</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token operator">/</span>
	<span class="token keyword">void</span> <span class="token keyword">operator</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">const</span> <span class="token punctuation">{</span>
		vector<span class="token operator">&lt;</span>slot<span class="token operator">&gt;</span> <span class="token function">copy</span><span class="token punctuation">(</span>slots<span class="token punctuation">.</span><span class="token function">begin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> slots<span class="token punctuation">.</span><span class="token function">end</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">for</span><span class="token punctuation">(</span><span class="token operator">&amp;&amp;</span><span class="token keyword">auto</span> s<span class="token operator">:</span> copy<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token operator">/</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token operator">/</span> <span class="token punctuation">}</span>
	<span class="token punctuation">}</span>
	<span class="token operator">/</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token operator">/</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Новая проблема: если <code>A</code> удалил <code>B</code>, то <code>B</code> все равно вызовется. В такой реализации нет команды <code>отписаться</code>, есть команда <code>отпишись через какое-то время</code></p>
<p>Попробуем пофиксить:</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">struct</span> signal <span class="token punctuation">{</span>
	<span class="token operator">/</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token operator">/</span>
	<span class="token keyword">void</span> <span class="token keyword">operator</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">const</span> <span class="token punctuation">{</span>
		<span class="token keyword">for</span><span class="token punctuation">(</span><span class="token operator">&amp;&amp;</span><span class="token keyword">auto</span> s<span class="token operator">:</span> slots<span class="token punctuation">)</span> <span class="token punctuation">{</span> 
			<span class="token keyword">if</span><span class="token punctuation">(</span>s<span class="token punctuation">.</span>first<span class="token punctuation">)</span> <span class="token punctuation">{</span>
				s<span class="token punctuation">.</span><span class="token function">second</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token punctuation">}</span>
		<span class="token punctuation">}</span>
		slots<span class="token punctuation">.</span><span class="token function">erase</span><span class="token punctuation">(</span><span class="token function">remove_if</span><span class="token punctuation">(</span>slots<span class="token punctuation">.</span><span class="token function">begin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> 
							   slots<span class="token punctuation">.</span><span class="token function">end</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> 
							   <span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">(</span>pair<span class="token operator">&lt;</span><span class="token keyword">bool</span><span class="token punctuation">,</span> slot<span class="token operator">&gt;</span> <span class="token keyword">const</span> <span class="token operator">&amp;</span>p<span class="token punctuation">)</span> <span class="token punctuation">{</span>
							   	  <span class="token keyword">return</span> <span class="token operator">!</span>p<span class="token punctuation">.</span>first<span class="token punctuation">;</span>
							   <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">,</span> 
					 slots<span class="token punctuation">.</span><span class="token function">end</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token keyword">private</span><span class="token operator">:</span>
	list<span class="token operator">&lt;</span>pair<span class="token operator">&lt;</span><span class="token keyword">bool</span><span class="token punctuation">,</span> slot<span class="token operator">&gt;&gt;</span> slots<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Опа, проблема (callstack):</p>
<pre class=" language-cpp"><code class="prism  language-cpp">signal<span class="token operator">::</span><span class="token function">op</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token operator">|</span>_ A
<span class="token operator">|</span>  <span class="token operator">|</span>_ signal<span class="token operator">::</span><span class="token function">op</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token operator">|</span>	 <span class="token operator">|</span>_ B
<span class="token operator">|</span>	 <span class="token operator">|</span>	<span class="token operator">|</span>_ <span class="token function">disconnect</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token operator">|</span>	 <span class="token operator">|</span>_ cleanup
<span class="token operator">|</span>_ <span class="token operator">++</span>i<span class="token punctuation">;</span>  <span class="token operator">-</span> разъименование удаленного итератора
</code></pre>
<p>Фиксим еще раз, учитывая рекурсивные вызовы</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">struct</span> signal <span class="token punctuation">{</span>
	<span class="token operator">/</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token operator">/</span>
	<span class="token keyword">void</span> <span class="token keyword">operator</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">const</span> <span class="token punctuation">{</span>
		<span class="token operator">++</span>rec_counter<span class="token punctuation">;</span>
		<span class="token keyword">for</span><span class="token punctuation">(</span><span class="token operator">&amp;&amp;</span><span class="token keyword">auto</span> s<span class="token operator">:</span> slots<span class="token punctuation">)</span> <span class="token punctuation">{</span> 
			<span class="token keyword">if</span><span class="token punctuation">(</span>s<span class="token punctuation">.</span>first<span class="token punctuation">)</span> <span class="token punctuation">{</span>
				s<span class="token punctuation">.</span><span class="token function">second</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token punctuation">}</span>
		<span class="token punctuation">}</span>
		<span class="token operator">--</span>rec_counter<span class="token punctuation">;</span>
		<span class="token keyword">if</span><span class="token punctuation">(</span>rec_counter <span class="token operator">==</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token keyword">for</span><span class="token punctuation">(</span><span class="token keyword">auto</span> i <span class="token operator">=</span> slots<span class="token punctuation">.</span><span class="token function">begin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> i <span class="token operator">!=</span> slots<span class="token punctuation">.</span><span class="token function">end</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
				<span class="token keyword">if</span><span class="token punctuation">(</span>i<span class="token operator">-</span><span class="token operator">&gt;</span>first<span class="token punctuation">)</span> 
					<span class="token operator">++</span>i<span class="token punctuation">;</span>
				<span class="token keyword">else</span> 
					i <span class="token operator">=</span> slots<span class="token punctuation">.</span><span class="token function">erase</span><span class="token punctuation">(</span>i<span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token punctuation">}</span>
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span>

<span class="token keyword">private</span><span class="token operator">:</span>
	size_t rec_counter<span class="token punctuation">;</span>
	list<span class="token operator">&lt;</span>pair<span class="token operator">&lt;</span><span class="token keyword">bool</span><span class="token punctuation">,</span> slot<span class="token operator">&gt;&gt;</span> slots<span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">struct</span> signal<span class="token operator">::</span>connection <span class="token punctuation">{</span>
	<span class="token operator">/</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token operator">/</span>
	<span class="token function">disconnect</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">if</span> <span class="token punctuation">(</span>sig<span class="token operator">-</span><span class="token operator">&gt;</span>rec_counter <span class="token operator">==</span> <span class="token number">0</span><span class="token punctuation">)</span>
			sig <span class="token operator">-</span><span class="token operator">&gt;</span> slots<span class="token punctuation">.</span><span class="token function">erase</span><span class="token punctuation">(</span>it<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">else</span>
			it<span class="token operator">-</span><span class="token operator">&gt;</span>first <span class="token operator">=</span> <span class="token boolean">false</span><span class="token punctuation">;</span> 
	<span class="token punctuation">}</span>

<span class="token keyword">private</span><span class="token operator">:</span>
	signal<span class="token operator">*</span> sig<span class="token punctuation">;</span>
	list<span class="token operator">&lt;</span>slot<span class="token operator">&gt;</span><span class="token operator">::</span>iterator it<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Все выше - проблема <strong>reentrancy</strong>.  Если мы рекурсивно (прямо или косвенно) вызываем функцию работы с одними и теми же данными, сложно предсказать работу программы.</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">log</span><span class="token punctuation">(</span><span class="token string">'...'</span><span class="token punctuation">)</span>
<span class="token function">assert</span><span class="token punctuation">(</span>flag<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// Даже тут assert может упасть при удаленных вызовах в log</span>

<span class="token keyword">void</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	flag <span class="token operator">=</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>

