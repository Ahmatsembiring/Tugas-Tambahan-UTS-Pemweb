# **06 – Functional Testing with WebTest**

## Ringkasan

WebTest digunakan untuk melakukan  **functional testing** , yaitu pengujian penuh dari request hingga response seperti benar-benar mengakses web. Tidak seperti unit test, WebTest mensimulasikan HTTP request terhadap WSGI app tanpa menjalankan server, sehingga cepat namun tetap akurat.

## Analisis

Dengan menambahkan `webtest` ke `extras_require`, kita bisa membuat test yang memeriksa isi HTML sebenarnya dari aplikasi. WebTest membuat instance `TestApp` dari fungsi `main()` sehingga test dapat melakukan request seperti browser sungguhan.

Functional test melengkapi unit test karena memastikan seluruh stack—routing, view, response, dan HTML—berfungsi dengan benar. Hal ini membantu mendeteksi error yang tidak terlihat di level unit test.

## Kode Utama

### setup.py (menambah webtest)

<pre class="overflow-visible!" data-start="995" data-end="1084"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>dev_requires = [
    </span><span>'pyramid_debugtoolbar'</span><span>,
    </span><span>'pytest'</span><span>,
    </span><span>'webtest'</span><span>,
]
</span></span></code></div></div></pre>

### functional test

<pre class="overflow-visible!" data-start="1106" data-end="1443"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>from</span><span> webtest </span><span>import</span><span> TestApp
</span><span>from</span><span> tutorial </span><span>import</span><span> main

</span><span>class</span><span></span><span>TutorialFunctionalTests</span><span>(unittest.TestCase):
    </span><span>def</span><span></span><span>setUp</span><span>(</span><span>self</span><span>):
        app = main({})
        self.testapp = TestApp(app)

    </span><span>def</span><span></span><span>test_hello_world</span><span>(</span><span>self</span><span>):
        res = self.testapp.get(</span><span>'/'</span><span>, status=</span><span>200</span><span>)
        self.assertIn(</span><span>b'<h1>Hello World!</h1>'</span><span>, res.body)
</span></span></code></div></div></pre>

### Menjalankan test

<pre class="overflow-visible!" data-start="1466" data-end="1515"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-bash"><span><span>$VENV</span><span>/bin/pytest tutorial/tests.py -q
</span></span></code></div></div></pre>

## Inti Pembelajaran

* WebTest memungkinkan pengujian end-to-end tanpa server.
* `TestApp` mengirim request seperti browser sungguhan.
* Functional test memvalidasi HTML, routing, dan response.
* `b''` digunakan karena body response berupa  **bytes** , bukan string.
