# **18 – Forms and Validation with Deform**

## Ringkasan

Pyramid tidak membawa library form bawaan, sehingga Anda bebas memilih. Pada langkah ini digunakan **Deform + Colander** untuk membuat form otomatis berbasis schema, lengkap dengan validasi, widget, dan error handling.

## Analisis

Deform bekerja dengan konsep  **schema-driven forms** . Developer mendefinisikan struktur input menggunakan Colander (`MappingSchema`, `SchemaNode`), lalu Deform otomatis membuat form HTML dan melakukan validasi POST data.

Setiap view menggunakan pendekatan  **self-posting form** : URL yang sama menangani GET (tampilkan form) dan POST (validasi & proses). Jika validasi gagal, form ditampilkan lagi dengan error; jika berhasil, data disimpan dan user diarahkan ke halaman baru menggunakan `HTTPFound`.

Deform juga memiliki registry resource untuk memuat CSS & JS widget secara otomatis. Static assets dari Deform dipublikasikan melalui `add_static_view`, sehingga widget rich text dapat berjalan tanpa konfigurasi manual.

## Kode Utama

### Schema dengan Colander

<pre class="overflow-visible!" data-start="1230" data-end="1445"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>class</span><span></span><span>WikiPage</span><span>(colander.MappingSchema):
    title = colander.SchemaNode(colander.String())
    body = colander.SchemaNode(
        colander.String(),
        widget=deform.widget.RichTextWidget()
    )
</span></span></code></div></div></pre>

### Membuat form di view class

<pre class="overflow-visible!" data-start="1478" data-end="1598"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>@property</span><span>
</span><span>def</span><span></span><span>wiki_form</span><span>(</span><span>self</span><span>):
    schema = WikiPage()
    </span><span>return</span><span> deform.Form(schema, buttons=(</span><span>'submit'</span><span>,))
</span></span></code></div></div></pre>

### Proses submit (add/edit)

<pre class="overflow-visible!" data-start="1629" data-end="1805"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>controls = self.request.POST.items()
</span><span>try</span><span>:
    appstruct = self.wiki_form.validate(controls)
</span><span>except</span><span> deform.ValidationFailure </span><span>as</span><span> e:
    </span><span>return</span><span></span><span>dict</span><span>(form=e.render())
</span></span></code></div></div></pre>

### Static assets Deform

<pre class="overflow-visible!" data-start="1832" data-end="1903"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>config.add_static_view(</span><span>'deform_static'</span><span>, </span><span>'deform:static/'</span><span>)
</span></span></code></div></div></pre>

## Inti Pembelajaran

* Deform membuat form otomatis berdasarkan schema Colander.
* Validasi POST dilakukan dengan `form.validate()`.
* Self-posting pattern: GET tampilkan form, POST proses form.
* Widget Deform membutuhkan CSS & JS dari static assets.
* Redirect (`HTTPFound`) dilakukan setelah proses sukses.
