# **13 – CSS/JS/Images Files With Static Assets**

## Ringkasan

Topik ini membahas cara menambahkan **static assets** seperti CSS, JS, dan gambar ke aplikasi Pyramid. Static assets dipetakan ke URL melalui `config.add_static_view`, dan template dapat menghasilkan URL-nya menggunakan `request.static_url()`.

## Analisis

Dengan `config.add_static_view(name='static', path='tutorial:static')`, Pyramid membuat direktori `tutorial/static` dapat diakses lewat URL `/static/`. Template kemudian memanggil `request.static_url()` untuk menghasilkan URL yang konsisten terhadap konfigurasi, sehingga link static tetap valid meskipun struktur proyek berubah.

Testing menambahkan pengecekan bahwa file CSS benar-benar dapat diakses. Pendekatan ini membuat pengelolaan aset menjadi fleksibel dan mengikuti standar web modern.

## Kode Utama

### **init** .py

<pre class="overflow-visible!" data-start="988" data-end="1063"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>config.add_static_view(name=</span><span>'static'</span><span>, path=</span><span>'tutorial:static'</span><span>)
</span></span></code></div></div></pre>

### Template (home.pt)

<pre class="overflow-visible!" data-start="1088" data-end="1186"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-html"><span><span><link</span><span></span><span>rel</span><span>=</span><span>"stylesheet"</span><span>
      </span><span>href</span><span>=</span><span>"${request.static_url('tutorial:static/app.css')}"</span><span>/>
</span></span></code></div></div></pre>

### app.css

<pre class="overflow-visible!" data-start="1200" data-end="1265"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-css"><span><span>body</span><span> {
    </span><span>margin</span><span>: </span><span>2em</span><span>;
    </span><span>font-family</span><span>: sans-serif;
}
</span></span></code></div></div></pre>

### Functional Test

<pre class="overflow-visible!" data-start="1287" data-end="1387"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>res = self.testapp.get(</span><span>'/static/app.css'</span><span>, status=</span><span>200</span><span>)
self.assertIn(</span><span>b'body'</span><span>, res.body)
</span></span></code></div></div></pre>

## Inti Pembelajaran

* `add_static_view` mempublikasikan direktori file statis ke URL.
* `request.static_url()` menghasilkan URL aman & fleksibel untuk assets.
* Static assets seperti CSS kini dapat digunakan di template.
* Functional test memastikan asset benar-benar tersedia.
