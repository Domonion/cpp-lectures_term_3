---


---

<h2 id="uniform-initialization">Uniform initialization</h2>
<p>Инициализатор агрегатов</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">int</span> a<span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token keyword">struct</span> point <span class="token punctuation">{</span>
	<span class="token keyword">float</span> x<span class="token punctuation">,</span> y<span class="token punctuation">;</span>
<span class="token punctuation">}</span>

point p <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<p>Не работает, если есть хотя бы 1 конструктор<br>
До <code>c++11</code> вектор (например) нельзя было инициализировать <code>{...}</code><br>
В <code>c++11</code> появился конструктор</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token function">vector</span><span class="token punctuation">(</span>std<span class="token operator">::</span>initializer_list<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p><code>{...}</code> не имеют типа, они не являются <code>std::initializer_list&lt;T&gt;</code>, элементы <code>{...}</code> могут быть вообще разного типа<br>
Просто компилятор наблюдая <code>{...}</code> в коде вызывает конструктор от <code>std::initializer_list&lt;T&gt;</code></p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">void</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token keyword">int</span><span class="token punctuation">)</span>
<span class="token operator">&amp;</span>f <span class="token comment">//type = void (*)(int)</span>

<span class="token keyword">void</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token keyword">int</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">void</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token keyword">float</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token operator">&amp;</span>f <span class="token comment">//не имеет никакого типа</span>
</code></pre>
<p><code>auto</code> в таком случае не будет работать, но для IL он будет работать, если все его аргументы имеют один тип (просто в <code>auto</code> костыль стоит)</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token function">f</span><span class="token punctuation">(</span>vectot<span class="token operator">&lt;</span><span class="token keyword">int</span><span class="token operator">&gt;</span> <span class="token punctuation">{</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">new</span> vector<span class="token operator">&lt;</span><span class="token keyword">int</span><span class="token operator">&gt;</span> <span class="token punctuation">{</span><span class="token number">1</span><span class="token punctuation">,</span><span class="token number">2</span><span class="token punctuation">,</span><span class="token number">3</span><span class="token punctuation">}</span><span class="token punctuation">;</span> <span class="token comment">//Создали и сразу проинициализировали </span>
</code></pre>
<h3 id="немного-об-инициализации">Немного об инициализации</h3>
<p>Инициализация работает не так радужно, как кажется</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">new</span> <span class="token keyword">int</span><span class="token punctuation">;</span> <span class="token comment">//Не инициализированно</span>
<span class="token keyword">new</span> <span class="token keyword">int</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//Инициализированно 0</span>

<span class="token keyword">struct</span> point <span class="token punctuation">{</span>
	<span class="token keyword">float</span> x<span class="token punctuation">,</span> y<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
<span class="token keyword">new</span> point<span class="token punctuation">;</span> <span class="token comment">//Не инициализированно</span>
<span class="token keyword">new</span> <span class="token function">point</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//Инициализированно 0</span>
</code></pre>
<p>Виды инициализации</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">default</span> initialization
	<span class="token keyword">int</span> a<span class="token punctuation">;</span>
	<span class="token keyword">new</span> <span class="token keyword">int</span><span class="token punctuation">;</span>
	
value initialization
	<span class="token function">f</span><span class="token punctuation">(</span><span class="token keyword">int</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">new</span> <span class="token keyword">int</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	mytype<span class="token operator">::</span><span class="token function">mytype</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">:</span> <span class="token function">x</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>

copy initialization
	<span class="token keyword">int</span> a <span class="token operator">=</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

direct initialization
	mytype <span class="token function">a</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token function">f</span><span class="token punctuation">(</span><span class="token function">mytype</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span> <span class="token punctuation">,</span><span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">new</span> <span class="token function">mytype</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> <span class="token operator">:</span> <span class="token function">x</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>И вот в <code>c++11</code> захотели сделать единый синтаксис для инициализации</p>
<pre class=" language-cpp"><code class="prism  language-cpp">list initialization
	copy list initialization
		mypyte x <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
    direct list	initialization
	    mytype x<span class="token punctuation">{</span><span class="token number">1</span><span class="token punctuation">,</span><span class="token number">2</span><span class="token punctuation">,</span><span class="token number">3</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<p>При этом <code>f(int{}), new int{}</code> и т.д. это <code>value initialization</code>, т.е разница идет в <code>0</code> аргументов и <strong>не</strong> <code>0</code></p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">auto</span> x <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">}</span><span class="token punctuation">;</span> <span class="token comment">//В initializer_list&lt;T&gt; (т.е должны иметь 1 тип, иначе ошибка)</span>
<span class="token keyword">auto</span> y<span class="token punctuation">{</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">}</span><span class="token punctuation">;</span> <span class="token comment">//Если 1 аргумент - в этот тип, иначе ошибка </span>
</code></pre>
<h3 id="generalized-attributes">Generalized Attributes</h3>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">int</span> a <span class="token keyword">alignas</span><span class="token punctuation">(</span><span class="token number">16</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//Выравнивание (последние 4 бита нули)</span>

<span class="token operator">--</span>forceinline <span class="token keyword">void</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span> <span class="token comment">//Microsoft</span>
<span class="token keyword">void</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token function">__attribute__</span> <span class="token punctuation">(</span><span class="token punctuation">(</span>always_inline<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span> <span class="token comment">//GCC </span>
</code></pre>
<p>Атрибуты - советы, можно проигнорить этот совет и ничего не сломается<br>
Unused, noreturn, duplicated</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">void</span> <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">[</span><span class="token punctuation">[</span>gnu<span class="token operator">::</span>always_inline<span class="token punctuation">]</span><span class="token punctuation">]</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>  <span class="token comment">//c++11 атрибут </span>
</code></pre>

