# **07 – Basic Web Handling With Views**

## Ringkasan

Topik ini memisahkan kode view ke file khusus `views.py`, lalu menggunakan decorator `@view_config` agar konfigurasi view lebih deklaratif. Pyramid kemudian memindai (scan) modul views untuk mendaftarkan view otomatis.

## Analisis

Dengan memindahkan view ke modul terpisah, struktur aplikasi menjadi lebih rapi dan mudah dipelihara. Fungsi `config.scan('.views')` membuat Pyramid membaca semua view yang memiliki decorator `@view_config`, sehingga kita tidak perlu lagi menambahkan view secara manual dengan `config.add_view`.

Dua view baru (`home` dan `hello`) menunjukkan bagaimana satu route dapat diarahkan ke view tertentu dan bagaimana URL, nama route, dan nama fungsi view dapat berbeda. Testing diperbarui untuk mencakup kedua view, baik unit test maupun functional test.Kode Utama

### **init** .py

<pre class="overflow-visible!" data-start="1019" data-end="1248"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>def</span><span></span><span>main</span><span>(</span><span>global_config, **settings</span><span>):
    config = Configurator(settings=settings)
    config.add_route(</span><span>'home'</span><span>, </span><span>'/'</span><span>)
    config.add_route(</span><span>'hello'</span><span>, </span><span>'/howdy'</span><span>)
    config.scan(</span><span>'.views'</span><span>)
    </span><span>return</span><span> config.make_wsgi_app()
</span></span></code></div></div></pre>

### views.py

<pre class="overflow-visible!" data-start="1263" data-end="1515"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>@view_config(route_name='home'</span><span>)
</span><span>def</span><span></span><span>home</span><span>(</span><span>request</span><span>):
    </span><span>return</span><span> Response(</span><span>'<body>Visit <a href="/howdy">hello</a></body>'</span><span>)

</span><span>@view_config(route_name='hello'</span><span>)
</span><span>def</span><span></span><span>hello</span><span>(</span><span>request</span><span>):
    </span><span>return</span><span> Response(</span><span>'<body>Go back <a href="/">home</a></body>'</span><span>)
</span></span></code></div></div></pre>

### Functional test (ringkas)

<pre class="overflow-visible!" data-start="1547" data-end="1634"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>res = self.testapp.get(</span><span>'/'</span><span>, status=</span><span>200</span><span>)
self.assertIn(</span><span>b'Visit'</span><span>, res.body)
</span></span></code></div></div></pre>

## Inti Pembelajaran

* View dipisah dari startup code untuk struktur lebih bersih.
* `@view_config` membuat konfigurasi view lebih deklaratif.
* `config.scan()` membaca modul view otomatis.
* URL, nama route, dan nama fungsi view dapat berbeda.
