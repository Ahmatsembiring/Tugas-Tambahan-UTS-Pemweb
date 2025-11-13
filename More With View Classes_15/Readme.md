# **15 – More With View Classes**

## Ringkasan

Pyramid memungkinkan satu kelas untuk berisi banyak view yang saling terkait. Dengan view classes, kita dapat berbagi konfigurasi, state, dan helper antar method, serta membedakan view berdasarkan *request method* atau parameter tertentu.

## Analisis

View class mempermudah pengelompokan beberapa operasi pada URL yang sama, seperti GET, POST edit, dan POST delete.

Dengan `@view_defaults`, konfigurasi umum dapat ditetapkan sekali di level kelas. Tiap metode view dapat menambahkan aturan sendiri, misalnya `request_method='POST'` atau `request_param='form.delete'`.

Kelas juga dapat menyimpan state (`self.request`) dan properti (`full_name`) yang secara otomatis tersedia di template melalui objek `view`.

Pendekatan ini menghasilkan struktur aplikasi yang lebih rapi, fleksibel, dan mudah diperluas.

## Kode Utama

### views.py

<pre class="overflow-visible!" data-start="1030" data-end="1882"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>@view_defaults(route_name='hello'</span><span>)
</span><span>class</span><span></span><span>TutorialViews</span><span>:
    </span><span>def</span><span></span><span>__init__</span><span>(</span><span>self, request</span><span>):
        self.request = request
        self.view_name = </span><span>'TutorialViews'</span><span>

</span><span>    @property</span><span>
    </span><span>def</span><span></span><span>full_name</span><span>(</span><span>self</span><span>):
        </span><span>return</span><span> self.request.matchdict[</span><span>'first'</span><span>] + </span><span>' '</span><span> + self.request.matchdict[</span><span>'last'</span><span>]

</span><span>    @view_config(route_name='home'</span><span>, renderer=</span><span>'home.pt'</span><span>)
    </span><span>def</span><span></span><span>home</span><span>(</span><span>self</span><span>):
        </span><span>return</span><span> {</span><span>'page_title'</span><span>: </span><span>'Home View'</span><span>}

</span><span>    @view_config(renderer='hello.pt'</span><span>)
    </span><span>def</span><span></span><span>hello</span><span>(</span><span>self</span><span>):
        </span><span>return</span><span> {</span><span>'page_title'</span><span>: </span><span>'Hello View'</span><span>}

</span><span>    @view_config(request_method='POST'</span><span>, renderer=</span><span>'edit.pt'</span><span>)
    </span><span>def</span><span></span><span>edit</span><span>(</span><span>self</span><span>):
        </span><span>return</span><span> {</span><span>'page_title'</span><span>: </span><span>'Edit View'</span><span>, </span><span>'new_name'</span><span>: self.request.params[</span><span>'new_name'</span><span>]}

</span><span>    @view_config(request_method='POST'</span><span>, request_param=</span><span>'form.delete'</span><span>, renderer=</span><span>'delete.pt'</span><span>)
    </span><span>def</span><span></span><span>delete</span><span>(</span><span>self</span><span>):
        </span><span>return</span><span> {</span><span>'page_title'</span><span>: </span><span>'Delete View'</span><span>}
</span></span></code></div></div></pre>

### Template (contoh)

<pre class="overflow-visible!" data-start="1906" data-end="1994"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-html"><span><span><h1</span><span>>${view.view_name} - ${page_title}</span><span></h1</span><span>>
</span><span><p</span><span>>Welcome, ${view.full_name}</span><span></p</span><span>>
</span></span></code></div></div></pre>

## Inti Pembelajaran

* View dapat dikelompokkan dalam satu class.
* `@view_defaults` menyederhanakan konfigurasi bersama.
* View dibedakan berdasarkan *request method* dan  *form parameter* .
* Class bisa berbagi state, helper, dan property ke semua template.
* `request.route_url()` menghasilkan URL dinamis yang lebih aman dibanding hardcode.
