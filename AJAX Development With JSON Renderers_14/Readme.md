# **14 – AJAX Development With JSON Renderers**

## Ringkasan

Pyramid menyediakan JSON renderer yang memungkinkan sebuah view mengembalikan data dalam format JSON. Ini berguna untuk AJAX, API kecil, atau UI dinamis. Satu view dapat melayani HTML dan JSON sekaligus dengan menambahkan beberapa decorator.

## Analisis

JSON renderer bekerja dengan mengambil dictionary Python dari view, lalu mengubahnya menjadi JSON lengkap dengan `Content-Type: application/json`.

Pada contoh ini, view `hello` memiliki dua route: `/howdy` (HTML) dan `/howdy.json` (JSON). Dengan menambahkan decorator `renderer='json'` pada route JSON, Pyramid otomatis memilih renderer yang benar tanpa perlu menulis ulang view.

Pendekatan ini memudahkan pembuatan endpoint AJAX hanya dengan menambah route dan renderer, tanpa memecah logika view.

## Kode Utama

### **init** .py

<pre class="overflow-visible!" data-start="1004" data-end="1063"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>config.add_route(</span><span>'hello_json'</span><span>, </span><span>'/howdy.json'</span><span>)
</span></span></code></div></div></pre>

### views.py

<pre class="overflow-visible!" data-start="1078" data-end="1230"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>@view_config(route_name='hello'</span><span>)
</span><span>@view_config(route_name='hello_json'</span><span>, renderer=</span><span>'json'</span><span>)
</span><span>def</span><span></span><span>hello</span><span>(</span><span>self</span><span>):
    </span><span>return</span><span> {</span><span>'name'</span><span>: </span><span>'Hello View'</span><span>}
</span></span></code></div></div></pre>

### Functional Test

<pre class="overflow-visible!" data-start="1252" data-end="1421"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>res = self.testapp.get(</span><span>'/howdy.json'</span><span>, status=</span><span>200</span><span>)
self.assertIn(</span><span>b'{"name": "Hello View"}'</span><span>, res.body)
self.assertEqual(res.content_type, </span><span>'application/json'</span><span>)
</span></span></code></div></div></pre>

## Inti Pembelajaran

* Pyramid dapat merender JSON menggunakan renderer bawaan.
* Satu view bisa melayani dua format sekaligus (HTML & JSON).
* JSON sangat cocok untuk AJAX dan API modern.
* Renderer menentukan bagaimana data akhir dikirim ke klien.
