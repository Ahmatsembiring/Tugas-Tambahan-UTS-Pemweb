# **16 – Collecting Application Info With Logging**

## Ringkasan

Pyramid menggunakan sistem logging standar Python. Dengan konfigurasi default pada `development.ini`, aplikasi dapat mencatat aktivitas, pesan debug, dan error. Logging sangat membantu untuk memantau perilaku aplikasi saat development maupun produksi.

## Analisis

Pyramid tidak membuat sistem logging sendiri—ia memanfaatkan modul `logging` milik Python.

Dengan membuat logger melalui `logging.getLogger(__name__)`, setiap view dapat mencatat aktivitas penting. File `development.ini` kemudian menentukan level log, handler, serta format output.

Ketika view dijalankan, pesan log akan muncul di console dan dapat dilihat juga melalui debug toolbar. Pendekatan ini memastikan pengembang dapat mendeteksi masalah atau memahami alur aplikasi dengan lebih mudah.

## Kode Utama

### views.py

<pre class="overflow-visible!" data-start="995" data-end="1169"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>import</span><span> logging
log = logging.getLogger(__name__)

</span><span>@view_config(route_name='home'</span><span>)
</span><span>def</span><span></span><span>home</span><span>(</span><span>self</span><span>):
    log.debug(</span><span>'In home view'</span><span>)
    </span><span>return</span><span> {</span><span>'name'</span><span>: </span><span>'Home View'</span><span>}
</span></span></code></div></div></pre>

### development.ini

<pre class="overflow-visible!" data-start="1191" data-end="1297"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-ini"><span><span>[loggers]</span><span>
</span><span>keys</span><span> = root, tutorial

</span><span>[logger_tutorial]</span><span>
</span><span>level</span><span> = DEBUG
</span><span>handlers</span><span> =
</span><span>qualname</span><span> = tutorial
</span></span></code></div></div></pre>

## Inti Pembelajaran

* Pyramid memakai logging standar Python.
* Logger didefinisikan dengan `getLogger(__name__)`.
* Pesan log muncul di console & debug toolbar.
* Level logging diatur melalui file `.ini`, mudah diubah tanpa menyentuh kode.
