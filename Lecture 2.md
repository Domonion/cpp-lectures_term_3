---


---

<h2 id="multi-index-containers">Multi-index containers</h2>
<p><strong>bimap</strong> - пары (a, b), по элементу можно найти второй<br>
<strong>lru-cache</strong> - мапа с ограниченным числом элементов (самое старое удаляет)</p>
<p>Почему например просто 2 мапы плохо? Одни и те же данные хранятся и в самом объекте, и как ключи мапы, если мы добавили в одну мапу, а при добавлении во вторую произошло исключение, то объект будет лишь в 1 мапе</p>
<p>Есть годная штука, которой даешь объект и говоришь, какие индексы хочешь<br>
<strong>boost::multi_index</strong> - контейнер с определенным количеством индексов</p>
<h2 id="heterogeneous-lookup-гетерогенный-поиск">Heterogeneous lookup (гетерогенный поиск)</h2>
<p>Позволяет искать (в контейнере?)  не только по искомому типу (н-р вместо <code>string</code> передавать <code>const char*</code>)</p>
<p>С помощью него же в массиве &lt;ключ, значение&gt; искать только по ключу (например в мапу надобавляли элементов, а потом только ищем)</p>
<p>Есть в <code>std::lowel_bound</code>, <code>std::set</code>, <code>std:::map</code></p>
<h2 id="shared_pointer">Shared_pointer</h2>
<p>Много указателей - объект один<br>
Все аккуратно удаляется</p>
<pre class=" language-cpp"><code class="prism  language-cpp">shared_ptr<span class="token operator">&lt;</span>my_type<span class="token operator">&gt;</span> <span class="token function">p</span><span class="token punctuation">(</span><span class="token keyword">new</span> my_type<span class="token punctuation">)</span><span class="token punctuation">;</span>
shared_ptr<span class="token operator">&lt;</span>my_type<span class="token operator">&gt;</span> q <span class="token operator">=</span> p<span class="token punctuation">;</span>
<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
</code></pre>
<p>Нельзя хранить счетчик в самом поинтере - будет у каждого свой<br>
Выход - храним отдельный <code>shared_counter</code> и на него ссылаемся</p>
<pre class=" language-cpp"><code class="prism \ language-cpp"><span class="token keyword">template</span><span class="token operator">&lt;</span><span class="token keyword">typename</span> T<span class="token operator">&gt;</span>
<span class="token keyword">struct</span> shared_ptr <span class="token punctuation">{</span>
	T<span class="token operator">*</span> obj<span class="token punctuation">;</span>
	size_t<span class="token operator">*</span> count<span class="token punctuation">;</span>

	<span class="token keyword">template</span><span class="token operator">&lt;</span><span class="token keyword">typename</span> D<span class="token operator">&gt;</span>
	<span class="token function">shared_ptr</span> <span class="token punctuation">(</span>T<span class="token operator">*</span> ptr<span class="token punctuation">,</span> D deleter<span class="token punctuation">)</span> <span class="token operator">:</span> <span class="token function">obj</span><span class="token punctuation">(</span>ptr<span class="token punctuation">)</span><span class="token punctuation">,</span> 
						<span class="token keyword">try</span> <span class="token punctuation">{</span>
			 				count <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token function">size_t</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
						<span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
							<span class="token function">deleter</span><span class="token punctuation">(</span>ptr<span class="token punctuation">)</span><span class="token punctuation">;</span>
							<span class="token keyword">throw</span><span class="token punctuation">;</span>
						<span class="token punctuation">}</span>
	<span class="token punctuation">{</span><span class="token punctuation">}</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>

my_type <span class="token operator">*</span>t <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token function">my_type</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
shared_ptr<span class="token operator">&lt;</span>my_type<span class="token operator">&gt;</span> <span class="token function">p</span><span class="token punctuation">(</span>t<span class="token punctuation">)</span><span class="token punctuation">;</span>
shared_ptr<span class="token operator">&lt;</span>my_type<span class="token operator">&gt;</span> <span class="token function">q</span><span class="token punctuation">(</span>t<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// Очень плохо - 2 разных счетчика</span>
</code></pre>
<p><code>deleter</code> нужен, если мы хотим н-р вернуть объект в пулл, а не удалить его, когда все указатели удалятся</p>
<pre class=" language-cpp"><code class="prism  language-cpp">
<span class="token keyword">struct</span> wheel <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>

<span class="token keyword">struct</span> car <span class="token punctuation">{</span>
	array<span class="token operator">&lt;</span>weel<span class="token punctuation">,</span> <span class="token number">4</span><span class="token operator">&gt;</span> wheels<span class="token punctuation">;</span> <span class="token comment">//Беда</span>
	array<span class="token operator">&lt;</span>shared_ptr<span class="token operator">&lt;</span>wheel<span class="token operator">&gt;</span><span class="token punctuation">,</span> <span class="token number">4</span><span class="token operator">&gt;</span> wheels<span class="token punctuation">;</span> <span class="token comment">//Норм?</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Хотим <code>shared_ptr</code> на wheel</p>
<p>Что плохого может быть? Если мы откуда-то ссылаемся на объект, то это не значит что он должен жить. Н-р удалили машину, а колесо не знает что она удалена и продолжает жить</p>
<p><strong>Aliasing constructor</strong><br>
Выход - сделать общий счетчик у <code>car</code> и у <code>wheel</code>, а само <code>wheel</code> хранить внутри <code>car</code></p>
<pre class=" language-cpp"><code class="prism  language-cpp">shared_ptr<span class="token operator">&lt;</span>car<span class="token operator">&gt;</span> p<span class="token punctuation">;</span>
shared_ptr<span class="token operator">&lt;</span>wheel<span class="token operator">&gt;</span> <span class="token function">q</span><span class="token punctuation">(</span>p<span class="token punctuation">,</span> <span class="token operator">&amp;</span>p<span class="token operator">-</span><span class="token operator">&gt;</span>wheels<span class="token punctuation">[</span><span class="token number">2</span><span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>Идиоматическое использование <code>shared_ptr</code></p>
<pre class=" language-cpp"><code class="prism  language-cpp">shared_ptr<span class="token operator">&lt;</span>my_type<span class="token operator">&gt;</span> q <span class="token operator">=</span> make_shared<span class="token operator">&lt;</span>my_type<span class="token operator">&gt;</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">)</span>
</code></pre>
<p>Такое использование не делает 2 аллокации (на <code>counter</code> и <code>obj</code>), а сделает один общий объект -&gt; 1 аллокация</p>
<h2 id="weak_pointer">Weak_pointer</h2>
<p>Допустим хотим хранить кэш картинок</p>
<pre class=" language-cpp"><code class="prism  language-cpp">map<span class="token operator">&lt;</span>string<span class="token punctuation">,</span> shared_ptr<span class="token operator">&lt;</span>image<span class="token operator">&gt;&gt;</span> cache<span class="token punctuation">;</span>
</code></pre>
<p>Проблема - все что было загружено, не будет удалено пока существует кэш, так как <code>shared_ptr</code> держит объект</p>
<p>Хранит указатель на <code>shared_counter</code><br>
Это позволяет удалить объект до того, как будут удалены все <code>weak_ptr</code> (хранится еще и <code>weak_count</code>)  (<strong>не работает при make_shared, ибо 1 аллокация, при нем будет вызван деструктор, но не будет освобождена память</strong>)</p>
<pre class=" language-cpp"><code class="prism  language-cpp">map<span class="token operator">&lt;</span>string<span class="token punctuation">,</span> weak_pointer<span class="token operator">&lt;</span>image<span class="token operator">&gt;&gt;</span> cache<span class="token punctuation">;</span>
</code></pre>
<p>Позволит выгрузить из кэша картинку, с <code>shared_ptr</code> картинки будут в кэше до его удаления</p>
<pre class=" language-cpp"><code class="prism  language-cpp">weak_ptr<span class="token operator">&lt;</span>mytype<span class="token operator">&gt;</span> w<span class="token punctuation">;</span>
w<span class="token punctuation">.</span><span class="token function">lock</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token comment">//Возвращает либо поинтер, либо 0 если объект уже удален</span>
</code></pre>
<p>Для многопоточности важно, что <code>.lock()</code> это одна операция, что она не разделена на 2</p>
<h2 id="smart-pointers--наследование">Smart pointers + наследование</h2>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">struct</span> B <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token keyword">struct</span> D <span class="token operator">:</span> B <span class="token punctuation">{</span><span class="token punctuation">}</span>

shared_prt<span class="token operator">&lt;</span>B<span class="token operator">&gt;</span> b<span class="token punctuation">;</span>
shared_prt<span class="token operator">&lt;</span>D<span class="token operator">&gt;</span> d <span class="token operator">=</span> static_pointer_cast<span class="token operator">&lt;</span>D<span class="token operator">&gt;</span><span class="token punctuation">(</span>b<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token punctuation">(</span><span class="token operator">?</span><span class="token punctuation">)</span>
</code></pre>
<p><strong>Для shared_ptr</strong></p>

