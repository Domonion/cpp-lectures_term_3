---


---

<h3 id="map-reduce-filter">Map, reduce, filter</h3>
<p>Отлично распараллеливается и некоторые задачи сводятся к этим</p>
<p>Используйте</p>
<pre class=" language-cpp"><code class="prism  language-cpp">	std<span class="token operator">::</span>transform <span class="token operator">-</span> map
	std<span class="token operator">::</span>copy_if <span class="token operator">/</span> remove_if <span class="token operator">-</span> filter
</code></pre>
<h3 id="во-что-может-упереться-распараллеливание">Во что может упереться распараллеливание?</h3>
<ol>
<li>Memory bund-width (пропускная способность памяти, на серверных процах может быть неверно)</li>
<li>Caches (кэши проца)</li>
<li>(Hyper-threading) execution units</li>
<li>Power usage (проц считает -&gt; нагревается)</li>
<li>Sharing (если запустили на разных ядрах, то у каждых отдельных ядер фактически свой <code>mutex</code> на кэш, из-за чего обращение к переменной, которая в кэше другого ядра происходит по времени как обращение к просто памяти)</li>
<li>False sharing (как <strong>sharing</strong>, но когда обращение не к одному объекту, а к объектам в одной кэш линии (процессоры синхронизируют кэш линии))</li>
</ol>
<h3 id="альтернативы-потокам">Альтернативы потокам</h3>
<p>Если все что вам нужно - это ждать, то может быть потоки не лучшее решение</p>
<p><strong>Message loop</strong><br>
Все события помещаются в некую “очередь” и из нее в бесконечном цикле достаются события и выполняются (так работает большинство UI программ)</p>
<p><strong>Coroutines</strong><br>
Он же <strong>fiber</strong><br>
Работает как потоки, но метается из одного “чувака” в другого (проще хранить состояние, при этом оно фактически ничего не параллелит)</p>

