# **17 – Transient Data Using Sessions**

## Ringkasan

Sessions digunakan untuk menyimpan data sementara yang tetap tersimpan di antara request, misalnya counter, shopping cart, atau flash message. Pyramid menyediakan session bawaan dengan SignedCookieSessionFactory.

## Analisis

Dengan menambahkan `session_factory` ke Configurator, Pyramid otomatis menyediakan `request.session`, sebuah dictionary yang bisa dipakai untuk menyimpan data non-permanen.

Pada contoh ini, setiap kali halaman dimuat, property `counter` mengecek apakah session memiliki nilai sebelumnya. Jika ada, nilainya ditambah; jika tidak, session diinisialisasi.

Session tetap tersimpan meski user berpindah URL atau me-reload halaman, dan bahkan setelah aplikasi restart—selama cookie masih ada. Pendekatan ini sangat cocok untuk fitur sederhana tanpa perlu database atau penyimpanan eksternal.

## Kode Utama

### **init** .py

<pre class="overflow-visible!" data-start="1036" data-end="1227"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>from</span><span> pyramid.session </span><span>import</span><span> SignedCookieSessionFactory

my_session_factory = SignedCookieSessionFactory(</span><span>'itsaseekreet'</span><span>)
config = Configurator(session_factory=my_session_factory)
</span></span></code></div></div></pre>

### views.py

<pre class="overflow-visible!" data-start="1242" data-end="1404"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>@property</span><span>
</span><span>def</span><span></span><span>counter</span><span>(</span><span>self</span><span>):
    session = self.request.session
    session[</span><span>'counter'</span><span>] = session.get(</span><span>'counter'</span><span>, </span><span>0</span><span>) + </span><span>1</span><span>
    </span><span>return</span><span> session[</span><span>'counter'</span><span>]
</span></span></code></div></div></pre>

### Template

<pre class="overflow-visible!" data-start="1419" data-end="1460"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-html"><span><span><p</span><span>>Count: ${view.counter}</span><span></p</span><span>>
</span></span></code></div></div></pre>

## Inti Pembelajaran

* Session diakses lewat `request.session` seperti dictionary.
* Data session bertahan antar request.
* Cocok untuk counter, login state, flash message, dan data sementara lainnya.
* Implementasi sederhana sudah cukup tanpa database.
