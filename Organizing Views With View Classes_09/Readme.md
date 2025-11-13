# **09 – Organizing Views With View Classes**

## Ringkasan

Topik ini mengubah view dari fungsi biasa menjadi  **metode pada sebuah class** . Pendekatan ini memudahkan pengelompokan view yang saling berkaitan dan memungkinkan penggunaan konfigurasi bersama menggunakan `@view_defaults`.

## Analisis

Dengan mengubah view menjadi class, dua view (`home` dan `hello`) kini menjadi metode dalam `TutorialViews`. Decorator `@view_defaults(renderer='home.pt')` mengatur default renderer untuk seluruh method di class, sehingga tidak perlu menambahkan `renderer=` di setiap view.

Testing juga perlu menyesuaikan: sebelum memanggil metode view, kita harus membuat instance view class dengan `request` sebagai parameter konstruktor. Pendekatan ini memperlihatkan bagaimana view yang saling terkait bisa berbagi state dan konfigurasi dengan lebih mudah.

## Kode Utama

### views.py

<pre class="overflow-visible!" data-start="1019" data-end="1343"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>@view_defaults(renderer='home.pt'</span><span>)
</span><span>class</span><span></span><span>TutorialViews</span><span>:
    </span><span>def</span><span></span><span>__init__</span><span>(</span><span>self, request</span><span>):
        self.request = request

</span><span>    @view_config(route_name='home'</span><span>)
    </span><span>def</span><span></span><span>home</span><span>(</span><span>self</span><span>):
        </span><span>return</span><span> {</span><span>'name'</span><span>: </span><span>'Home View'</span><span>}

</span><span>    @view_config(route_name='hello'</span><span>)
    </span><span>def</span><span></span><span>hello</span><span>(</span><span>self</span><span>):
        </span><span>return</span><span> {</span><span>'name'</span><span>: </span><span>'Hello View'</span><span>}
</span></span></code></div></div></pre>

### Unit Test (ringkas)

<pre class="overflow-visible!" data-start="1369" data-end="1516"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>request = testing.DummyRequest()
inst = TutorialViews(request)
response = inst.home()
self.assertEqual(</span><span>'Home View'</span><span>, response[</span><span>'name'</span><span>])
</span></span></code></div></div></pre>

### Functional Test

<pre class="overflow-visible!" data-start="1538" data-end="1632"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>res = self.testapp.get(</span><span>'/'</span><span>, status=</span><span>200</span><span>)
self.assertIn(</span><span>b'Hi Home View'</span><span>, res.body)
</span></span></code></div></div></pre>

## Inti Pembelajaran

* View class memudahkan pengelompokan view yang saling berhubungan.
* `@view_defaults` menyederhanakan konfigurasi berulang.
* Testing harus membuat instance dari view class sebelum memanggil method.
* Struktur ini ideal untuk aplikasi yang memiliki banyak operasi terkait satu resource.
