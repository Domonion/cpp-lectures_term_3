---


---

<h2 id="еще-про-многопоточность">Еще про многопоточность</h2>
<h3 id="promise--future">Promise + future</h3>
<pre class=" language-cpp"><code class="prism  language-cpp">promise<span class="token punctuation">.</span><span class="token function">set_value</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
promise<span class="token punctuation">.</span><span class="token function">set_exception</span><span class="token punctuation">(</span><span class="token punctuation">)</span>

future<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
<p><code>Promise</code> - отдается потоку, который считает<br>
<code>Future</code> - отдается потоку, которому требуется значение<br>
Эта пара позволяет считать что-то в одном потоке, а получать в другом без нагромождения <code>mutex</code> и т.д.</p>
<p>Часто неприменимо, например <code>.get()</code> в UI потоке зафризит UI поток, подобные штуки сильно ограничивают применимость.</p>
<h3 id="packaged_task-некая-обертка-над-promise">Packaged_task (некая обертка над promise)</h3>
<pre class=" language-cpp"><code class="prism  language-cpp">Позволяет сделать<span class="token operator">:</span>
T <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">+</span> promise<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span> <span class="token operator">-</span><span class="token operator">&gt;</span> <span class="token keyword">void</span> <span class="token function">g</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
<h3 id="stdasync">std::async</h3>
<pre class=" language-cpp"><code class="prism  language-cpp">Принимает T <span class="token function">f</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> возвращает future<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span>
</code></pre>
<p>Позволяет считать 2 способами: полностью асинхронно, либо лениво - можно не считать до вызова <code>get()</code></p>
<h3 id="еще-о-потоках">Еще о потоках</h3>
<p>В стандартной библиотеке завершить поток можно только внутри него<br>
В принципе ОС может вырубить поток просто так, но это по сути UB, потому что не вызываются деструкторы и вообще поток вырубается в произвольный момент</p>
<p>Но почему <code>cancellation</code> не включить в <code>std</code>? Дело в том, что <code>conditional variable</code> и <code>mutex</code> реализованы через интерфейс ОС, поэтому они будут от поддержки замедляться. Поэтому обычно <code>cancellation</code> реализуют из вне, но отсюда возникает проблема, что когда ждешь <code>conditional variable</code> или <code>mutex</code>, то не от<code>cancell</code>’ишься.</p>
<p><strong>Thread pool</strong><br>
Создавать все время потоки может быть не выгодно. Давайте тогда создадим какое-то количество потоков, удалять их не будем, а когда приходит новая задача будем отдавать ее в очередной из созданных потоков. Это концепция и называется <strong>thread pool</strong>. В <code>std</code> нет ничего такого.</p>
<p>Проблема в том, что потоки могут друг друга ждать, и это ожидание может быть в глубину в разы больше количества потоков, поэтому pool может встать в очередь просто.</p>
<p>Поэтому разные режимы async потенциально позволяют ему использовать полноценный <strong>thread pool</strong>.</p>
<p><strong>TLS (thread local storage)</strong><br>
Глобальная переменная, но своя для каждого потока</p>
<p><strong>STM (Software transactional  memory)</strong><br>
Мы хотим что бы какой-то код был выполнен как единое целое<br>
Если deadlock - откатить все и попробовать заново</p>
<p>Проблема - на практике все что реализовывали оказывалось медленным</p>
<p><strong>HTM(Hardware  transactional  memory)</strong><br>
Аппаратная поддержка того же самого<br>
Но тут много проблем с реализаций тоже (при неудаче транзакции надо много канселить, потом еще и <code>mutex</code> брать и в итоге все плохо), интел пытались но получилось не оч и не прижилось</p>

