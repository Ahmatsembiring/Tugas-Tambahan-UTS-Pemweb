# **12 – Templating With Jinja2**

# Ringkasan

Topik ini menunjukkan bahwa Pyramid mendukung berbagai template engine. Di sini digunakan **Jinja2** melalui add-on  **pyramid_jinja2** , sehingga view dapat merender file `.jinja2` sebagai HTML.

## Analisis

Untuk memakai Jinja2, cukup menambahkan `pyramid_jinja2` sebagai dependency lalu memanggil `config.include('pyramid_jinja2')`. Add-on ini mendaftarkan renderer baru yang otomatis mengenali template dengan ekstensi `.jinja2`.

View class tetap sama; hanya `renderer`-nya yang berubah. Template Jinja2 menggunakan sintaks `{{ variable }}`, mirip dengan template engine populer lain, sehingga mudah dipakai. Langkah ini menunjukkan fleksibilitas Pyramid dalam memilih template system tanpa mengubah struktur view.

## Kode Utama

### setup.py

<pre class="overflow-visible!" data-start="947" data-end="1059"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>install_requires=[
    </span><span>'pyramid'</span><span>,
    </span><span>'pyramid_chameleon'</span><span>,
    </span><span>'pyramid_jinja2'</span><span>,
    </span><span>'waitress'</span><span>,
]
</span></span></code></div></div></pre>

### **init** .py

<pre class="overflow-visible!" data-start="1077" data-end="1189"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>config.include(</span><span>'pyramid_jinja2'</span><span>)
config.add_route(</span><span>'home'</span><span>, </span><span>'/'</span><span>)
config.add_route(</span><span>'hello'</span><span>, </span><span>'/howdy'</span><span>)
</span></span></code></div></div></pre>

### views.py

<pre class="overflow-visible!" data-start="1204" data-end="1370"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>@view_defaults(renderer='home.jinja2'</span><span>)
</span><span>class</span><span></span><span>TutorialViews</span><span>:
</span><span>    @view_config(route_name='home'</span><span>)
    </span><span>def</span><span></span><span>home</span><span>(</span><span>self</span><span>):
        </span><span>return</span><span> {</span><span>'name'</span><span>: </span><span>'Home View'</span><span>}
</span></span></code></div></div></pre>

### Template (home.jinja2)

<pre class="overflow-visible!" data-start="1399" data-end="1433"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-html"><span><span><h1</span><span>>Hi {{ name }}</span><span></h1</span><span>>
</span></span></code></div></div></pre>

## Inti Pembelajaran

* Pyramid mendukung berbagai template engine, termasuk Jinja2.
* Add-on cukup diaktifkan dengan `config.include()`.
* Renderer menentukan file template mana yang dipakai.
* View tetap fokus pada data, bukan HTML.
