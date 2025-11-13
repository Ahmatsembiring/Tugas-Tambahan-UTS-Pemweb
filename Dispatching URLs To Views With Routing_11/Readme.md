
# **11 – Dispatching URLs to Views With Routing**

## Ringkasan

Topik ini menunjukkan bagaimana Pyramid mengekstrak bagian URL menggunakan **replacement pattern** seperti `{first}` dan `{last}`. Data yang ditangkap dari URL akan dimasukkan ke `request.matchdict` dan bisa digunakan langsung di view.

## Analisis

Route `/howdy/{first}/{last}` memberitahu Pyramid bahwa URL memiliki dua variabel dinamis. Ketika URL diakses, misalnya `/howdy/Jane/Doe`, Pyramid otomatis menempatkan `"Jane"` dan `"Doe"` ke dalam `request.matchdict`.

View kemudian mengakses nilai tersebut dan mengembalikannya sebagai data template. Pendekatan ini membuat routing lebih fleksibel dan cocok untuk URL yang memerlukan parameter seperti ID, username, atau kategori.

Testing memastikan baik unit maupun functional test dapat menangkap nilai dari URL dengan benar.

## Kode Utama

### **init** .py

<pre class="overflow-visible!" data-start="1023" data-end="1086"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>config.add_route(</span><span>'home'</span><span>, </span><span>'/howdy/{first}/{last}'</span><span>)
</span></span></code></div></div></pre>

### views.py

<pre class="overflow-visible!" data-start="1101" data-end="1230"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>first = self.request.matchdict[</span><span>'first'</span><span>]
last = self.request.matchdict[</span><span>'last'</span><span>]
</span><span>return</span><span> {</span><span>'first'</span><span>: first, </span><span>'last'</span><span>: last}
</span></span></code></div></div></pre>

### Template (home.pt)

<pre class="overflow-visible!" data-start="1255" data-end="1304"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-html"><span><span><p</span><span>>First: ${first}, Last: ${last}</span><span></p</span><span>>
</span></span></code></div></div></pre>

### Functional Test

<pre class="overflow-visible!" data-start="1326" data-end="1426"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>res = self.testapp.get(</span><span>'/howdy/Jane/Doe'</span><span>, status=</span><span>200</span><span>)
self.assertIn(</span><span>b'Jane'</span><span>, res.body)
</span></span></code></div></div></pre>

## Inti Pembelajaran

* Route dapat memiliki parameter dinamis melalui `{}`.
* Nilai parameter tersedia di `request.matchdict`.
* View dapat menggunakan data dari URL untuk logika atau template.
* Testing memverifikasi bahwa URL parsing bekerja sesuai harapan.
