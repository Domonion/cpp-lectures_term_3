---


---

<h2 id="многопоточность">Многопоточность</h2>
<h3 id="виды-параллелизации">Виды параллелизации</h3>
<ol>
<li><strong>ILP</strong> - распараллеливание команд на 1 процессоре (всякие скедулеры)</li>
<li><strong>SIMD</strong> - например есть регистры по размеру как 4 int.</li>
<li><strong>Hyper-threading</strong></li>
<li><strong>Multi core threading</strong></li>
</ol>
<p>Создаем поток</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token macro property">#<span class="token directive keyword">include</span> <span class="token string">&lt;thread&gt;</span></span>

<span class="token keyword">int</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	std<span class="token operator">::</span>thread <span class="token function">t</span><span class="token punctuation">(</span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token punctuation">{</span>
		std<span class="token operator">::</span>cout <span class="token operator">&lt;&lt;</span> <span class="token string">"Hello"</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	t<span class="token punctuation">.</span><span class="token function">join</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//Дождись пока поток закончится</span>
	t<span class="token punctuation">.</span><span class="token function">detach</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//Отвяжись от вызывающего потока (нужен редко)</span>
<span class="token punctuation">}</span>
</code></pre>
<h3 id="race-condition">Race condition</h3>
<p>Осторожно, дальше псевдокод</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">int</span> accounts<span class="token punctuation">[</span><span class="token number">10000</span><span class="token punctuation">]</span>

<span class="token keyword">void</span> <span class="token function">transfer</span><span class="token punctuation">(</span>size_t from<span class="token punctuation">,</span> size_t to<span class="token punctuation">,</span> <span class="token keyword">int</span> amount<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">if</span> <span class="token punctuation">(</span>accounts<span class="token punctuation">[</span>from<span class="token punctuation">]</span> <span class="token operator">&lt;</span> amount<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">throw</span> <span class="token function">runtime_error</span><span class="token punctuation">(</span><span class="token string">"in sufficient funds"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
	accounts<span class="token punctuation">[</span>from<span class="token punctuation">]</span> <span class="token operator">-</span><span class="token operator">=</span> amount<span class="token punctuation">;</span>
	accounts<span class="token punctuation">[</span>to<span class="token punctuation">]</span> <span class="token operator">+</span><span class="token operator">=</span> amount<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>Race condition</strong> - если результат работы нашей программы зависят от того в каком порядке выполняются потоки и в каком порядке скедулятся их команды, то программа некорректна</p>
<p>В многопоточном случае 2 потока могут проверить, что денег достаточно, и оба вычесть, а потом уйти в минус.<br>
Ну и как заставить это работать? Например <code>mutex</code></p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">int</span> accounts<span class="token punctuation">[</span><span class="token number">10000</span><span class="token punctuation">]</span>
std<span class="token operator">::</span>mutex m<span class="token punctuation">;</span>

<span class="token keyword">void</span> <span class="token function">transfer</span><span class="token punctuation">(</span>size_t from<span class="token punctuation">,</span> size_t to<span class="token punctuation">,</span> <span class="token keyword">int</span> amount<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	m<span class="token punctuation">.</span><span class="token function">lock</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//Если залочен - ждет, когда разлочен - лочит</span>
	<span class="token operator">-</span><span class="token comment">//-</span>
	m<span class="token punctuation">.</span><span class="token function">unlock</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//Разлочивает</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Заметим что если полетит исключение, то останется залоченным навсегда</p>
<p><strong>Спинлок</strong> - реализация <code>mutex</code> через просто <code>while</code><br>
Жрем цпу, ниче полезного не делаем, но быстро, потому что “уснуть” и “проснуться” не бесплатные операции, потому может быть полезен когда <code>lock()</code> применяется на очень маленький участок</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">int</span> accounts<span class="token punctuation">[</span><span class="token number">10000</span><span class="token punctuation">]</span>
std<span class="token operator">::</span>mutex m<span class="token punctuation">;</span>

<span class="token keyword">void</span> <span class="token function">transfer</span><span class="token punctuation">(</span>size_t from<span class="token punctuation">,</span> size_t to<span class="token punctuation">,</span> <span class="token keyword">int</span> amount<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	std<span class="token operator">::</span>lock_guard<span class="token operator">&lt;</span>std<span class="token operator">::</span>mutex<span class="token operator">&gt;</span> <span class="token function">lg</span><span class="token punctuation">(</span>m<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//Unlock в деструкторе</span>
	<span class="token operator">-</span><span class="token comment">//-</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Операция называется <strong>атомарной</strong>, если мы не можем видеть ее промежуточных шагов (не может быть частично выполнена, а частично не выполнена)</p>
<p>Н-р (не факт что верно)</p>
<pre class=" language-cpp"><code class="prism  language-cpp">std<span class="token operator">::</span>atomic<span class="token operator">&lt;</span><span class="token keyword">bool</span><span class="token operator">&gt;</span> locked<span class="token punctuation">;</span>
locked<span class="token punctuation">.</span><span class="token function">load</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//Атомарно получить</span>
locked<span class="token punctuation">.</span><span class="token function">store</span><span class="token punctuation">(</span>smth<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//Атомарно заменить</span>
locked<span class="token punctuation">.</span><span class="token function">exchange</span><span class="token punctuation">(</span>smth<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">//Атомарно заменить и вернуть преж</span>
locked<span class="token punctuation">.</span><span class="token function">compare_exchange</span><span class="token punctuation">(</span>v1<span class="token punctuation">,</span> v2<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//Если locked не равно v1 - заменяет v1 на locked, если равно заменяет locked на v2. Возращает равно ли locked v1</span>
</code></pre>
<p>Атомик гарантирует, что все что было до load выполнится до load, все что после - после</p>
<p>Но что-то у нас теперь не многопоточный код</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">void</span> <span class="token function">transfer</span><span class="token punctuation">(</span>size_t from<span class="token punctuation">,</span> size_t to<span class="token punctuation">,</span> <span class="token keyword">int</span> amount<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	std<span class="token operator">::</span>lock_guard<span class="token operator">&lt;</span>std<span class="token operator">::</span>mutex<span class="token operator">&gt;</span> <span class="token function">lg</span><span class="token punctuation">(</span>m<span class="token punctuation">[</span>from<span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	std<span class="token operator">::</span>lock_guard<span class="token operator">&lt;</span>std<span class="token operator">::</span>mutex<span class="token operator">&gt;</span> <span class="token function">lg</span><span class="token punctuation">(</span>m<span class="token punctuation">[</span>to<span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token operator">-</span><span class="token comment">//-</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Может быть deadlock (<code>A -&gt; B</code>, <code>B -&gt; A</code>) - бесконечное ожидание ресурсов потоками, которые сами жти ресурсы залочили</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">void</span> <span class="token function">transfer</span><span class="token punctuation">(</span>size_t from<span class="token punctuation">,</span> size_t to<span class="token punctuation">,</span> <span class="token keyword">int</span> amount<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	std<span class="token operator">::</span>lock_guard<span class="token operator">&lt;</span>std<span class="token operator">::</span>mutex<span class="token operator">&gt;</span> <span class="token function">lg</span><span class="token punctuation">(</span>m<span class="token punctuation">[</span><span class="token function">min</span><span class="token punctuation">(</span>from<span class="token punctuation">.</span> to<span class="token punctuation">)</span><span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	std<span class="token operator">::</span>lock_guard<span class="token operator">&lt;</span>std<span class="token operator">::</span>mutex<span class="token operator">&gt;</span> <span class="token function">lg</span><span class="token punctuation">(</span>m<span class="token punctuation">[</span><span class="token function">max</span><span class="token punctuation">(</span>from<span class="token punctuation">,</span> to<span class="token punctuation">)</span><span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token operator">-</span><span class="token comment">//-</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Уже не может - нет циклов</p>
<p>Количество поток ограничено только памятью, каждый поток требует стек (по умолчанию 4мб)</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">struct</span> queue <span class="token punctuation">{</span>
	<span class="token keyword">void</span> <span class="token function">push</span><span class="token punctuation">(</span>T val<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		lock_guard<span class="token operator">&lt;</span>mutex<span class="token operator">&gt;</span> <span class="token function">lg</span><span class="token punctuation">(</span>m<span class="token punctuation">)</span><span class="token punctuation">;</span>
		q<span class="token punctuation">.</span><span class="token function">push_back</span><span class="token punctuation">(</span><span class="token function">move</span><span class="token punctuation">(</span>val<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">bool</span> <span class="token function">empty</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">const</span> <span class="token punctuation">{</span>
		lock_guard<span class="token operator">&lt;</span>mutex<span class="token operator">&gt;</span> <span class="token function">lg</span><span class="token punctuation">(</span>m<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> q<span class="token punctuation">.</span><span class="token function">empty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	T <span class="token function">pop</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		lock_guard<span class="token operator">&lt;</span>mutex<span class="token operator">&gt;</span> <span class="token function">lg</span><span class="token punctuation">(</span>m<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token function">assert</span><span class="token punctuation">(</span><span class="token operator">!</span>q<span class="token punctuation">.</span><span class="token function">empty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		T res <span class="token operator">=</span> <span class="token function">move</span><span class="token punctuation">(</span>q<span class="token punctuation">.</span><span class="token function">front</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		q<span class="token punctuation">.</span><span class="token function">pop_front</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> res<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
	
	<span class="token keyword">mutable</span> mutex m<span class="token punctuation">;</span>
	deque<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span> q<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Казалось бы - каждая функция многопоточная, в чем проблема? Если 2 потока делают <code>if(!empty()) pop()</code>, то оба могут попытаться удалить один и тот же элемент<br>
Следовательно нужно изменить интерфейс</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">struct</span> queue <span class="token punctuation">{</span>
	<span class="token operator">-</span><span class="token comment">//-</span>

	<span class="token keyword">void</span> <span class="token function">push</span><span class="token punctuation">(</span>T val<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		lock_guard<span class="token operator">&lt;</span>mutex<span class="token operator">&gt;</span> <span class="token function">lg</span><span class="token punctuation">(</span>m<span class="token punctuation">)</span><span class="token punctuation">;</span>
		q<span class="token punctuation">.</span><span class="token function">push_back</span><span class="token punctuation">(</span><span class="token function">move</span><span class="token punctuation">(</span>val<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		non_empty<span class="token punctuation">.</span><span class="token function">notify_all</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>	

	T <span class="token function">pop</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		unique_lock<span class="token operator">&lt;</span>mutex<span class="token operator">&gt;</span> <span class="token function">lock</span><span class="token punctuation">(</span>m<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">if</span> <span class="token punctuation">(</span>q<span class="token punctuation">.</span><span class="token function">empty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			non_empty<span class="token punctuation">.</span><span class="token function">wait</span><span class="token punctuation">(</span>lock<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//Атомарно анлочит и начинает ждать, когда придет сигнал снова залочит</span>
		<span class="token punctuation">}</span>
		<span class="token operator">-</span><span class="token comment">//-</span>
	<span class="token punctuation">}</span>

	condition_variable <span class="token function">non_empty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">mutable</span> mutex m<span class="token punctuation">;</span>
	deque<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span> q<span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Condition variable:</p>
<ol>
<li><code>wait(unique_lock)</code> - залочиться и ждать</li>
<li><code>notify_one()</code> - разлочить кого-то одного</li>
<li><code>notify_all()</code> - разлочить всех</li>
</ol>
<p>Печально только что мы будем всех, но только одному удается продолжить выполнение, остальные опять засыпают. Для этого и есть <code>notify_one()</code></p>

