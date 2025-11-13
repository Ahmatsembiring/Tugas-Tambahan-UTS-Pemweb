# **10 – Handling Web Requests and Responses**

## Ringkasan

Topik ini menunjukkan bagaimana Pyramid memproses **request** dan menghasilkan **response** menggunakan WebOb. View dapat membaca parameter dari request (`request.params`) dan mengatur response seperti konten, header, serta melakukan redirect.

## Analisis

Pyramid memakai **WebOb** sebagai pondasi request–response sehingga pengambilan data dari URL, query string, maupun informasi request menjadi sederhana. View `home` menunjukkan cara membuat **redirect** dengan mengembalikan `HTTPFound`, sedangkan view `plain` membaca parameter `name` dari request dan memasukkannya ke body response.

Response dibuat manual menggunakan `Response(content_type=..., body=...)`. Redirect, query parameter, dan URL penuh (`request.url`) semuanya diproses secara konsisten oleh WebOb.

Testing memastikan dua hal penting: redirect pada route `/`, serta response yang berubah sesuai ada/tidaknya parameter `name`.

## Kode Utama

### **init** .py

<pre class="overflow-visible!" data-start="1147" data-end="1226"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>config.add_route(</span><span>'home'</span><span>, </span><span>'/'</span><span>)
config.add_route(</span><span>'plain'</span><span>, </span><span>'/plain'</span><span>)
</span></span></code></div></div></pre>

### views.py

<pre class="overflow-visible!" data-start="1241" data-end="1569"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>@view_config(route_name='home'</span><span>)
</span><span>def</span><span></span><span>home</span><span>(</span><span>self</span><span>):
    </span><span>return</span><span> HTTPFound(location=</span><span>'/plain'</span><span>)

</span><span>@view_config(route_name='plain'</span><span>)
</span><span>def</span><span></span><span>plain</span><span>(</span><span>self</span><span>):
    name = self.request.params.get(</span><span>'name'</span><span>, </span><span>'No Name Provided'</span><span>)
    body = </span><span>f'URL {self.request.url}</span><span> with name: </span><span>{name}</span><span>'
    </span><span>return</span><span> Response(content_type=</span><span>'text/plain'</span><span>, body=body)
</span></span></code></div></div></pre>

### Unit Test (ringkas)

<pre class="overflow-visible!" data-start="1595" data-end="1745"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>request = testing.DummyRequest()
inst = TutorialViews(request)
response = inst.plain()
self.assertIn(</span><span>b'No Name Provided'</span><span>, response.body)
</span></span></code></div></div></pre>

## Inti Pembelajaran

* Pyramid menggunakan WebOb untuk request & response.
* Query parameter diakses melalui `request.params`.
* Redirect dibuat dengan `HTTPFound`.
* Response bisa dikustomisasi (content type, body, dll).
* Testing memverifikasi redirect dan handling query string.
