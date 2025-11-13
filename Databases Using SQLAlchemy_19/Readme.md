# **19 – Databases Using SQLAlchemy**

## Ringkasan

Langkah ini menghubungkan aplikasi Pyramid dengan database **SQLite** menggunakan  **SQLAlchemy ORM** . Data wiki yang sebelumnya disimpan di dictionary kini disimpan di tabel `wikipages`. Transaksi dikelola otomatis dengan bantuan `pyramid_tm` dan `zope.sqlalchemy`.

## 📌= Analisis

SQLAlchemy digunakan untuk memetakan tabel ke class Python (`Page`), sementara `DBSession` menjadi pintu utama untuk query, insert, dan update.

Konfigurasi koneksi database diletakkan di `development.ini` lewat `sqlalchemy.url`, sehingga lokasi DB dapat diubah tanpa mengutak-atik kode.

`pyramid_tm` dan `zope.sqlalchemy` menghubungkan transaksi HTTP request dengan transaksi database: jika view sukses, transaksi di- *commit* ; jika error, otomatis di- *rollback* .

Sebuah console script `initialize_tutorial_db` dibuat melalui `console_scripts` di `setup.py` untuk inisialisasi database dan insert data awal (`Root` page).

## Kode Utama

### setup.py (dependensi & console script)

<pre class="overflow-visible!" data-start="1113" data-end="1460"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>install_requires = [
    </span><span>'deform'</span><span>,
    </span><span>'pyramid'</span><span>,
    </span><span>'pyramid_chameleon'</span><span>,
    </span><span>'pyramid_tm'</span><span>,
    </span><span>'sqlalchemy'</span><span>,
    </span><span>'waitress'</span><span>,
    </span><span>'zope.sqlalchemy'</span><span>,
]

entry_points = {
    </span><span>'paste.app_factory'</span><span>: [
        </span><span>'main = tutorial:main'</span><span>,
    ],
    </span><span>'console_scripts'</span><span>: [
        </span><span>'initialize_tutorial_db = tutorial.initialize_db:main'</span><span>,
    ],
}
</span></span></code></div></div></pre>

### models.py (ORM & session)

<pre class="overflow-visible!" data-start="1492" data-end="1751"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>DBSession = scoped_session(sessionmaker())
register(DBSession)
Base = declarative_base()

</span><span>class</span><span></span><span>Page</span><span>(</span><span>Base</span><span>):
    __tablename__ = </span><span>'wikipages'</span><span>
    uid = Column(Integer, primary_key=</span><span>True</span><span>)
    title = Column(Text, unique=</span><span>True</span><span>)
    body = Column(Text)
</span></span></code></div></div></pre>

### **init** .py (binding engine)

<pre class="overflow-visible!" data-start="1786" data-end="1913"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>engine = engine_from_config(settings, </span><span>'sqlalchemy.'</span><span>)
DBSession.configure(bind=engine)
Base.metadata.bind = engine
</span></span></code></div></div></pre>

### initialize_db.py (inisialisasi DB)

<pre class="overflow-visible!" data-start="1954" data-end="2188"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>engine = engine_from_config(settings, </span><span>'sqlalchemy.'</span><span>)
DBSession.configure(bind=engine)
Base.metadata.create_all(engine)
</span><span>with</span><span> transaction.manager:
    model = Page(title=</span><span>'Root'</span><span>, body=</span><span>'<p>Root</p>'</span><span>)
    DBSession.add(model)
</span></span></code></div></div></pre>

### Contoh di views.py

<pre class="overflow-visible!" data-start="2213" data-end="2384"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>pages = DBSession.query(Page).order_by(Page.title)

page = DBSession.query(Page).filter_by(uid=uid).one()
DBSession.add(Page(title=new_title, body=new_body))
</span></span></code></div></div></pre>

## Inti Pembelajaran

* SQLAlchemy mengelola model, query, dan mapping ke tabel database.
* `pyramid_tm` + `zope.sqlalchemy` mengatur transaksi otomatis per request.
* Konfigurasi DB diletakkan di `.ini`, bukan di hardcode.
* Console script mempermudah inisialisasi database dan seed data awal.
