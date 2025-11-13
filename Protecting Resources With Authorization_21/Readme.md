# **21 – Protecting Resources With Authorization**

## Ringkasan

Langkah ini menambahkan **authorization** di atas sistem login sebelumnya.

Tujuannya: hanya user tertentu (misalnya  **editor** ) yang boleh mengakses view tertentu (misalnya `/howdy`).

Hal ini dilakukan dengan:

* **Root factory** yang menyediakan **ACL (Access Control List)**
* **SecurityPolicy** yang bisa menghitung **effective principals** (siapa & grup apa yang dimiliki user)
* **Permission predicate** di view (`permission='edit'`)
* **Forbidden view** yang mengarahkan user tak berizin ke halaman login

---

## 🧩 Konsep Utama

1. **Authentication vs Authorization**
   * *Authentication* → “siapa kamu?” (login).
   * *Authorization* → “kamu boleh ngapain?” (hak akses / permission).
   * Di sini, kita melanjutkan dari step authentication dan menambahkan  **permission-based access** .
2. **Principal**
   * Bukan cuma user ID (`'editor'`), tapi juga:
     * `Everyone` → semua orang
     * `Authenticated` → semua user yang sudah login
     * grup seperti `'group:editors'`
   * Authorization dicek berdasarkan **principal** ini.
3. **ACL (Access Control List)**
   * Ditempel di objek konteks (di sini: `Root`).
   * Bentuknya list aturan: `(Allow/Deny, principal, permission)`.
   * Contoh: `(Allow, 'group:editors', 'edit')` → grup editor boleh `edit`.
4. **Root Factory**
   * Fungsi / class yang memberi **root context** untuk setiap request.
   * Root inilah yang memegang `__acl__` → tempat Pyramid membaca aturan security.
5. **Forbidden View**
   * Jika user tidak punya permission, Pyramid melempar  **HTTPForbidden** .
   * `@forbidden_view_config` menentukan view mana yang dipakai untuk menangani kondisi ini (misal: tampilkan form login).

---

## 🧱 Kode & Alur Penting

### 1. Root Factory di  **init** .py

<pre class="overflow-visible!" data-start="1897" data-end="2532"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>from</span><span> pyramid.config </span><span>import</span><span> Configurator
</span><span>from</span><span> .security </span><span>import</span><span> SecurityPolicy

</span><span>def</span><span></span><span>main</span><span>(</span><span>global_config, **settings</span><span>):
    config = Configurator(
        settings=settings,
        root_factory=</span><span>'.resources.Root'</span><span></span><span># ← root context dengan ACL</span><span>
    )
    config.include(</span><span>'pyramid_chameleon'</span><span>)

    config.set_security_policy(
        SecurityPolicy(
            secret=settings[</span><span>'tutorial.secret'</span><span>],
        ),
    )

    config.add_route(</span><span>'home'</span><span>, </span><span>'/'</span><span>)
    config.add_route(</span><span>'hello'</span><span>, </span><span>'/howdy'</span><span>)
    config.add_route(</span><span>'login'</span><span>, </span><span>'/login'</span><span>)
    config.add_route(</span><span>'logout'</span><span>, </span><span>'/logout'</span><span>)
    config.scan(</span><span>'.views'</span><span>)
    </span><span>return</span><span> config.make_wsgi_app()
</span></span></code></div></div></pre>

* `root_factory='.resources.Root'` → setiap request akan memakai `Root` sebagai konteks awal.

---

### 2. ACL di resources.py

<pre class="overflow-visible!" data-start="2662" data-end="2884"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>from</span><span> pyramid.authorization </span><span>import</span><span> Allow, Everyone

</span><span>class</span><span></span><span>Root</span><span>:
    __acl__ = [
        (Allow, Everyone, </span><span>'view'</span><span>),
        (Allow, </span><span>'group:editors'</span><span>, </span><span>'edit'</span><span>),
    ]

    </span><span>def</span><span></span><span>__init__</span><span>(</span><span>self, request</span><span>):
        </span><span>pass</span><span>
</span></span></code></div></div></pre>

* Semua orang (`Everyone`) boleh permission `'view'`.
* Grup `'group:editors'` boleh permission `'edit'`.
* Nantinya view bisa bilang: “saya butuh permission `'edit'`” → Pyramid akan cek ke ACL ini.

---

### 3. Update SecurityPolicy (security.py)

<pre class="overflow-visible!" data-start="3135" data-end="3426"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>from</span><span> pyramid.authentication </span><span>import</span><span> AuthTktCookieHelper
</span><span>from</span><span> pyramid.authorization </span><span>import</span><span> (
    ACLHelper,
    Authenticated,
    Everyone,
)

USERS = {
    </span><span>'editor'</span><span>: hash_password(</span><span>'editor'</span><span>),
    </span><span>'viewer'</span><span>: hash_password(</span><span>'viewer'</span><span>),
}
GROUPS = {
    </span><span>'editor'</span><span>: [</span><span>'group:editors'</span><span>],
}
</span></span></code></div></div></pre>

* `USERS` → menyimpan user dan password hash.
* `GROUPS` → mapping user → daftar grup (di sini hanya `editor` yang jadi `group:editors`).

#### Menambahkan permits & effective_principals

<pre class="overflow-visible!" data-start="3618" data-end="4325"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>class</span><span></span><span>SecurityPolicy</span><span>:
    </span><span>def</span><span></span><span>__init__</span><span>(</span><span>self, secret</span><span>):
        self.authtkt = AuthTktCookieHelper(secret=secret)
        self.acl = ACLHelper()

    </span><span># ... identity(), authenticated_userid(), remember(), forget() tetap sama ...</span><span>

    </span><span>def</span><span></span><span>permits</span><span>(</span><span>self, request, context, permission</span><span>):
        principals = self.effective_principals(request)
        </span><span>return</span><span> self.acl.permits(context, principals, permission)

    </span><span>def</span><span></span><span>effective_principals</span><span>(</span><span>self, request</span><span>):
        principals = [Everyone]
        userid = self.authenticated_userid(request)
        </span><span>if</span><span> userid </span><span>is</span><span></span><span>not</span><span></span><span>None</span><span>:
            principals += [Authenticated, </span><span>'u:'</span><span> + userid]
            principals += GROUPS.get(userid, [])
        </span><span>return</span><span> principals
</span></span></code></div></div></pre>

* `effective_principals()`:
  * Jika belum login → hanya `Everyone`.
  * Jika sudah login (mis. `editor`) → `[Everyone, Authenticated, 'u:editor', 'group:editors']`.
* `permits()`:
  * Dipanggil Pyramid untuk menjawab: “apakah principal-principal ini boleh melakukan permission `X` pada context `Y`?”
  * Menggunakan `ACLHelper` untuk membaca `__acl__` dari `Root` (atau resource lain).

---

### 4. Proteksi View dengan Permission

<pre class="overflow-visible!" data-start="4760" data-end="5250"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>from</span><span> pyramid.view </span><span>import</span><span> (
    view_config,
    view_defaults,
    forbidden_view_config,
)

</span><span>@view_defaults(renderer='home.pt'</span><span>)
</span><span>class</span><span></span><span>TutorialViews</span><span>:
    </span><span>def</span><span></span><span>__init__</span><span>(</span><span>self, request</span><span>):
        self.request = request
        self.logged_in = request.authenticated_userid

</span><span>    @view_config(route_name='home'</span><span>)
    </span><span>def</span><span></span><span>home</span><span>(</span><span>self</span><span>):
        </span><span>return</span><span> {</span><span>'name'</span><span>: </span><span>'Home View'</span><span>}

</span><span>    @view_config(route_name='hello'</span><span>, permission=</span><span>'edit'</span><span>)
    </span><span>def</span><span></span><span>hello</span><span>(</span><span>self</span><span>):
        </span><span>return</span><span> {</span><span>'name'</span><span>: </span><span>'Hello View'</span><span>}
</span></span></code></div></div></pre>

* `home` → bebas diakses (tidak butuh permission).
* `hello` → butuh permission `'edit'`.
* Hasilnya:
  * Kalau user **belum login** atau bukan editor → Pyramid akan menganggap akses ini  **Forbidden** .
  * Jika editor (anggota `group:editors`) → boleh akses.

---

### 5. Forbidden View → Pakai Halaman Login

<pre class="overflow-visible!" data-start="5566" data-end="5761"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>    @view_config(route_name='login'</span><span>, renderer=</span><span>'login.pt'</span><span>)
</span><span>    @forbidden_view_config(renderer='login.pt'</span><span>)
    </span><span>def</span><span></span><span>login</span><span>(</span><span>self</span><span>):
        </span><span># ... isi sama seperti step authentication ...</span><span>
</span></span></code></div></div></pre>

* `@view_config(route_name='login', ...)` → view normal untuk `/login`.
* `@forbidden_view_config(renderer='login.pt')` → jika terjadi `HTTPForbidden`, Pyramid akan memakai view ini.
* Alurnya jadi:
  * User akses `/howdy` → butuh permission `edit`.
  * User belum login → Pyramid cek ACL → tidak punya `group:editors` → Forbidden.
  * Forbidden → diarahkan ke view `login` dengan template `login.pt`.
  * Setelah login sukses, user dikembalikan ke `came_from` (URL awal).

---

### 6. Login & Logout (sama seperti step 20)

Login tetap:

<pre class="overflow-visible!" data-start="6302" data-end="6657"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>if</span><span></span><span>'form.submitted'</span><span></span><span>in</span><span> request.params:
    login = request.params[</span><span>'login'</span><span>]
    password = request.params[</span><span>'password'</span><span>]
    hashed_pw = USERS.get(login)
    </span><span>if</span><span> hashed_pw </span><span>and</span><span> check_password(password, hashed_pw):
        headers = remember(request, login)
        </span><span>return</span><span> HTTPFound(location=came_from, headers=headers)
    message = </span><span>'Failed login'</span><span>
</span></span></code></div></div></pre>

Logout tetap:

<pre class="overflow-visible!" data-start="6674" data-end="6884"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>@view_config(route_name='logout'</span><span>)
</span><span>def</span><span></span><span>logout</span><span>(</span><span>self</span><span>):
    request = self.request
    headers = forget(request)
    url = request.route_url(</span><span>'home'</span><span>)
    </span><span>return</span><span> HTTPFound(location=url, headers=headers)
</span></span></code></div></div></pre>

---

## 🔍 Alur Kerja Authorization Singkat

1. User buka `/howdy`
2. View `hello` punya `permission='edit'`
3. Pyramid:
   * ambil **context** (Root) → lihat `__acl__`
   * hitung `effective_principals` dari user (mis. `[Everyone, Authenticated, 'u:editor', 'group:editors']`)
   * cek apakah salah satu principal diberi permission `'edit'` di ACL.
4. Jika **boleh** → jalankan view `hello`.
5. Jika **tidak boleh** → raise Forbidden → jalankan view yang diberi `@forbidden_view_config` (login).

---

## 🎯 Inti Pembelajaran

* **Authentication** menentukan *siapa* user, **authorization** menentukan  *boleh ngapain* .
* Pyramid menggunakan:
  * **SecurityPolicy** → identitas + izin
  * **Root factory + ACL** → definisi aturan keamanan di level resource
  * **Permission predicate di view** (`permission='edit'`) → proteksi view
* **Principals** = kombinasi:
  * `Everyone`, `Authenticated`, `u:<userid>`, grup (`group:editors`)
* **Forbidden view** bisa diarahkan ke login agar pengalaman pengguna lebih halus (tidak hanya error 403 kering).
