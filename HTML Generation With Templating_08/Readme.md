# **08 – HTML Generation With Templating**

## Ringkasan

Topik ini mengenalkan cara menghasilkan HTML menggunakan  **template** , bukan lagi menulis HTML langsung di dalam Python. Pyramid mendukung berbagai template engine, dan pada langkah ini digunakan  **pyramid_chameleon** .

## Analisis

Dengan menambahkan `pyramid_chameleon`, view tidak lagi membuat `Response` manual. Setiap view cukup mengembalikan  **data dictionary** , lalu renderer (`renderer='home.pt'`) menghubungkannya dengan template `.pt`.

Pendekatan ini membuat kode lebih bersih: view fokus pada logika & data, sementara template menangani tampilan. Testing juga menjadi lebih sederhana karena cukup memeriksa data, dan functional test memastikan HTML yang dirender benar.

## Kode Utama

### setup.py

<pre class="overflow-visible!" data-start="919" data-end="1009"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>install_requires=[
    </span><span>'pyramid'</span><span>,
    </span><span>'pyramid_chameleon'</span><span>,
    </span><span>'waitress'</span><span>,
]
</span></span></code></div></div></pre>

### **init** .py

<pre class="overflow-visible!" data-start="1027" data-end="1296"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>def</span><span></span><span>main</span><span>(</span><span>global_config, **settings</span><span>):
    config = Configurator(settings=settings)
    config.include(</span><span>'pyramid_chameleon'</span><span>)
    config.add_route(</span><span>'home'</span><span>, </span><span>'/'</span><span>)
    config.add_route(</span><span>'hello'</span><span>, </span><span>'/howdy'</span><span>)
    config.scan(</span><span>'.views'</span><span>)
    </span><span>return</span><span> config.make_wsgi_app()
</span></span></code></div></div></pre>

### views.py

<pre class="overflow-visible!" data-start="1311" data-end="1536"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>@view_config(route_name='home'</span><span>, renderer=</span><span>'home.pt'</span><span>)
</span><span>def</span><span></span><span>home</span><span>(</span><span>request</span><span>):
    </span><span>return</span><span> {</span><span>'name'</span><span>: </span><span>'Home View'</span><span>}

</span><span>@view_config(route_name='hello'</span><span>, renderer=</span><span>'home.pt'</span><span>)
</span><span>def</span><span></span><span>hello</span><span>(</span><span>request</span><span>):
    </span><span>return</span><span> {</span><span>'name'</span><span>: </span><span>'Hello View'</span><span>}
</span></span></code></div></div></pre>

### Template (home.pt)

<pre class="overflow-visible!" data-start="1561" data-end="1592"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-html"><span><span><h1</span><span>>Hi ${name}</span><span></h1</span><span>>
</span></span></code></div></div></pre>

## Inti Pembelajaran

* Template engine memisahkan **logika view** dan  **HTML** .
* `renderer='...'` membuat Pyramid merender template otomatis.
* View kini hanya mengembalikan data (dictionary).
* Testing lebih mudah karena fokus pada output data dan HTML hasil render.
