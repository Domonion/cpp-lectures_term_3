---


---

<h3 id="немного-об-устройстве-интерфейса-файловой-системы">Немного об устройстве интерфейса файловой системы</h3>
<p>В самой директории хранится информация о названии файла и ссылка на другую ячейку памяти с основной информацией (<strong>inode</strong>)</p>
<p><strong>Hardlink</strong> — ссылка на одну <strong>inode</strong><br>
<strong>Symlink</strong>  — хранится просто путь на другой файл</p>
<p>Справедливо не для всех, например <strong>FAT</strong> хранит все в самой директории (поэтому он не поддерживает hardlinkи)</p>
<p>Файловые дескрипторы (<strong>FD</strong>) ссылаются на <strong>inode</strong>. Следовательно если меняются данные — изменения видны. Если файл удалить/переименовать - <strong>FD</strong> об этом даже не узнает</p>
<p>Появляется хак - если файл создается долго, можно просто создать <strong>inode</strong>, записать файл, а потом просто привязать ее к какой-то директории</p>
<p>В винде используются <strong>file lockes</strong>, в линухе есть что-то похожее, но не используется. Блокировать файл, чтобы не было работы с данными, которые уже некорректны. Но тогда вываливаются ошибки и что лучше - хз.</p>
<p>Можно подписываться на изменения файлов, но нужно понимать, что подписываетесь вы на <strong>inode</strong>. Поэтому не стоит путать изменения файла(данных) или изменения в каталоге.</p>
<p>Системный вызов <strong>stat</strong> позволяет вернуть что-то похожее на <strong>inode</strong>. По нему можно посчитать <strong>ref-count</strong> и как-то разобраться с <strong>hardlinks</strong></p>

