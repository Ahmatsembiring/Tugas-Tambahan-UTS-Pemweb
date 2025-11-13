# **05 – Unit Tests and pytest**

## Ringkasan

Topik ini mengenalkan cara melakukan **unit testing** pada aplikasi Pyramid menggunakan `pytest`. Tujuannya memastikan fungsi view berjalan seperti yang diharapkan tanpa harus terus membuka browser. Testing dilakukan menggunakan bantuan modul `pyramid.testing`.

## Analisis

`pytest` ditambahkan sebagai dependency development melalui `extras_require`, lalu dijalankan dengan `pip install -e ".[dev]"`. Unit test dibuat dengan membuat  **dummy request** , memanggil fungsi view, dan mengecek hasilnya.

Pengujian dilakukan di level kecil (unit) untuk memastikan fungsi bekerja konsisten. Import view dilakukan *di dalam* method test untuk mencegah efek samping import yang dapat mengganggu isolasi test.

## Kode Utama

### setup.py (menambah pytest)

<pre class="overflow-visible!" data-start="950" data-end="1024"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>dev_requires = [
    </span><span>'pyramid_debugtoolbar'</span><span>,
    </span><span>'pytest'</span><span>,
]
</span></span></code></div></div></pre>

### tests.py (unit test sederhana)

<pre class="overflow-visible!" data-start="1061" data-end="1481"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>import</span><span> unittest
</span><span>from</span><span> pyramid </span><span>import</span><span> testing

</span><span>class</span><span></span><span>TutorialViewTests</span><span>(unittest.TestCase):
    </span><span>def</span><span></span><span>setUp</span><span>(</span><span>self</span><span>):
        self.config = testing.setUp()

    </span><span>def</span><span></span><span>tearDown</span><span>(</span><span>self</span><span>):
        testing.tearDown()

    </span><span>def</span><span></span><span>test_hello_world</span><span>(</span><span>self</span><span>):
        </span><span>from</span><span> tutorial </span><span>import</span><span> hello_world
        request = testing.DummyRequest()
        response = hello_world(request)
        self.assertEqual(response.status_code, </span><span>200</span><span>)
</span></span></code></div></div></pre>

### Menjalankan test

<pre class="overflow-visible!" data-start="1504" data-end="1553"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-bash"><span><span>$VENV</span><span>/bin/pytest tutorial/tests.py -q
</span></span></code></div></div></pre>

## Inti Pembelajaran

* `pytest` memudahkan pengujian fungsi Pyramid.
* Dummy request dipakai untuk mensimulasikan request nyata.
* Import view berada di dalam test untuk menjaga isolasi.
* Testing memastikan kode stabil tanpa membuka browser.
