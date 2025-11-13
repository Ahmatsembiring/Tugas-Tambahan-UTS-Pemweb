# **04 – Easier Development with debugtoolbar**

## Ringkasan

Topik ini memperkenalkan  **pyramid_debugtoolbar** , sebuah add-on yang membantu debugging dengan menampilkan error, request info, dan introspeksi aplikasi langsung dari browser. Add-on ini diaktifkan melalui konfigurasi `.ini` dan dipasang menggunakan fitur **extras** di `setup.py`.

## Analisis

`pyramid_debugtoolbar` ditambahkan sebagai dependency khusus development menggunakan `extras_require`, lalu di-install dengan:

<pre class="overflow-visible!" data-start="598" data-end="629"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre!"><span><span>pip</span><span> install -e </span><span>".[dev]"</span><span>
</span></span></code></div></div></pre>

Konfigurasi add-on dilakukan lewat `pyramid.includes` di `development.ini`, sehingga toolbar aktif tanpa perubahan kode. Setelah dijalankan dengan `pserve`, toolbar muncul di sisi kanan browser dan memberikan detail error serta informasi request yang membantu debugging.

Pendekatan ini memperlihatkan kekuatan Pyramid: fitur bisa ditambahkan atau dimatikan cukup melalui konfigurasi `.ini`, bukan mengubah kode inti aplikasi.

## Kode Utama

### setup.py (menambah dev dependency)

<pre class="overflow-visible!" data-start="1117" data-end="1186"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>extras_require={
    </span><span>'dev'</span><span>: [</span><span>'pyramid_debugtoolbar'</span><span>],
}
</span></span></code></div></div></pre>

### development.ini (mengaktifkan add-on)

<pre class="overflow-visible!" data-start="1230" data-end="1284"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-ini"><span><span>pyramid.includes</span><span> =
    pyramid_debugtoolbar
</span></span></code></div></div></pre>

### Menjalankan aplikasi

<pre class="overflow-visible!" data-start="1311" data-end="1364"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-bash"><span><span>$VENV</span><span>/bin/pserve development.ini --reload
</span></span></code></div></div></pre>

## Inti Pembelajaran

* `pyramid_debugtoolbar` membantu debugging aplikasi secara visual.
* Add-on dapat diaktifkan melalui `.ini` menggunakan `pyramid.includes`.
* Dependency khusus development dikelola dengan  **extras_require** .
* Toolbar dapat dimatikan cukup dengan menghapusnya dari konfigurasi `.ini`.
