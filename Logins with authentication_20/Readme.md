# **20 – Logins with Authentication**

## Ringkasan

Langkah ini menambahkan **fitur login & logout** pada aplikasi Pyramid dengan memanfaatkan:

* **SecurityPolicy** kustom
* **AuthTktCookieHelper** untuk menyimpan status login di cookie
* **bcrypt** untuk hashing password
* Template login untuk menerima username & password

Di tahap ini kita baru fokus ke **authentication** (siapa pengguna), sedangkan **authorization** (boleh ngapain) akan dibahas di langkah berikutnya.

## 🧩 Konsep Utama Authentication di Pyramid

1. **Authentication vs Authorization**
   * **Authentication** → proses memastikan “kamu ini siapa?” (login).
   * **Authorization** → setelah diketahui siapa, “kamu boleh ngapain?”.
   * Di langkah ini, kita baru mengurus  **login/logout + user identity** , belum mengatur hak akses per role.
2. **Security Policy**
   * Pyramid pakai konsep **security policy** untuk menentukan:
     * bagaimana membaca identitas user dari request
     * bagaimana menyimpan identitas (misalnya di cookie)
     * bagaimana melupakan identitas (logout)
   * Kita membuat class `SecurityPolicy` sendiri yang membungkus `AuthTktCookieHelper`.
3. **AuthTktCookieHelper**
   * Ini helper bawaan Pyramid yang menyimpan **user id** dalam bentuk ticket di cookie.
   * Ticket ditandatangani dengan **secret key** (dari INI) agar tidak bisa dipalsukan.
4. **Hashing Password dengan bcrypt**
   * Password  **tidak disimpan dalam bentuk plain text** .
   * Password ‘editor’ dan ‘viewer’ disimpan dalam bentuk hash yang dihasilkan oleh `bcrypt`.
   * Saat login, input password di-hash lagi dan dibandingkan dengan hash yang tersimpan.

---

## Struktur & Alur Besar

### 1. Konfigurasi Dependensi (setup.py)

<pre class="overflow-visible!" data-start="1905" data-end="2003"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>requires = [
    </span><span>'bcrypt'</span><span>,
    </span><span>'pyramid'</span><span>,
    </span><span>'pyramid_chameleon'</span><span>,
    </span><span>'waitress'</span><span>,
]
</span></span></code></div></div></pre>

* Menambahkan **bcrypt** sebagai dependensi untuk hashing password.

---

### 2. Pengaturan Secret di development.ini

<pre class="overflow-visible!" data-start="2124" data-end="2263"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-ini"><span><span>[app:main]</span><span>
</span><span>use</span><span> = egg:tutorial
</span><span>pyramid.reload_templates</span><span> = </span><span>true</span><span>
</span><span>pyramid.includes</span><span> =
    pyramid_debugtoolbar
</span><span>tutorial.secret</span><span> = </span><span>98</span><span>zd
</span></span></code></div></div></pre>

* `tutorial.secret` ini  **tidak disimpan di kode** , tapi di konfigurasi.
* Digunakan untuk **menandatangani cookie auth ticket** (AuthTkt).

---

### 3. Modul security.py – Inti Authentication

<pre class="overflow-visible!" data-start="2459" data-end="2804"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>import</span><span> bcrypt
</span><span>from</span><span> pyramid.authentication </span><span>import</span><span> AuthTktCookieHelper


</span><span>def</span><span></span><span>hash_password</span><span>(</span><span>pw</span><span>):
    pwhash = bcrypt.hashpw(pw.encode(</span><span>'utf8'</span><span>), bcrypt.gensalt())
    </span><span>return</span><span> pwhash.decode(</span><span>'utf8'</span><span>)

</span><span>def</span><span></span><span>check_password</span><span>(</span><span>pw, hashed_pw</span><span>):
    expected_hash = hashed_pw.encode(</span><span>'utf8'</span><span>)
    </span><span>return</span><span> bcrypt.checkpw(pw.encode(</span><span>'utf8'</span><span>), expected_hash)
</span></span></code></div></div></pre>

* `hash_password(pw)` → meng-hash password dengan salt.
* `check_password(pw, hashed_pw)` → membandingkan password input dengan hash di database/dictionary.

<pre class="overflow-visible!" data-start="2964" data-end="3067"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>USERS = {
    </span><span>'editor'</span><span>: hash_password(</span><span>'editor'</span><span>),
    </span><span>'viewer'</span><span>: hash_password(</span><span>'viewer'</span><span>),
}
</span></span></code></div></div></pre>

* Sementara ini, user disimpan di dictionary `USERS`.
* Dalam aplikasi nyata, ini bisa diganti ke database.

#### Class `SecurityPolicy`

<pre class="overflow-visible!" data-start="3209" data-end="3866"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>class</span><span></span><span>SecurityPolicy</span><span>:
    </span><span>def</span><span></span><span>__init__</span><span>(</span><span>self, secret</span><span>):
        self.authtkt = AuthTktCookieHelper(secret=secret)

    </span><span>def</span><span></span><span>identity</span><span>(</span><span>self, request</span><span>):
        identity = self.authtkt.identify(request)
        </span><span>if</span><span> identity </span><span>is</span><span></span><span>not</span><span></span><span>None</span><span></span><span>and</span><span> identity[</span><span>'userid'</span><span>] </span><span>in</span><span> USERS:
            </span><span>return</span><span> identity

    </span><span>def</span><span></span><span>authenticated_userid</span><span>(</span><span>self, request</span><span>):
        identity = self.identity(request)
        </span><span>if</span><span> identity </span><span>is</span><span></span><span>not</span><span></span><span>None</span><span>:
            </span><span>return</span><span> identity[</span><span>'userid'</span><span>]

    </span><span>def</span><span></span><span>remember</span><span>(</span><span>self, request, userid, **kw</span><span>):
        </span><span>return</span><span> self.authtkt.remember(request, userid, **kw)

    </span><span>def</span><span></span><span>forget</span><span>(</span><span>self, request, **kw</span><span>):
        </span><span>return</span><span> self.authtkt.forget(request, **kw)
</span></span></code></div></div></pre>

* `identify(request)`
  * Membaca cookie auth dari request, mengembalikan `identity` jika valid.
  * Dicek lagi apakah `userid` masih ada di `USERS`.
* `authenticated_userid(request)`
  * Mengembalikan **user id** (misalnya: `'editor'`) jika sudah login, atau `None` jika belum.
* `remember()`
  * Menghasilkan **header HTTP** untuk menyetel cookie auth.
* `forget()`
  * Menghasilkan header untuk **menghapus** cookie auth (logout).

---

### 4. Mendaftarkan Security Policy di  **init** .py

<pre class="overflow-visible!" data-start="4371" data-end="4923"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>from</span><span> pyramid.config </span><span>import</span><span> Configurator
</span><span>from</span><span> .security </span><span>import</span><span> SecurityPolicy

</span><span>def</span><span></span><span>main</span><span>(</span><span>global_config, **settings</span><span>):
    config = Configurator(settings=settings)
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

* `config.set_security_policy(...)`

  Mendaftarkan `SecurityPolicy` sehingga:

  * `request.authenticated_userid` bisa dipakai di view.
  * `remember()` dan `forget()` bisa digunakan untuk mengatur cookie auth.
* Menambahkan rute:

  * `/login` → form login
  * `/logout` → logout

---

### 5. View Class: Login, Logout, & Status Login

<pre class="overflow-visible!" data-start="5263" data-end="5463"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>from</span><span> pyramid.httpexceptions </span><span>import</span><span> HTTPFound
</span><span>from</span><span> pyramid.security </span><span>import</span><span> remember, forget
</span><span>from</span><span> pyramid.view </span><span>import</span><span> view_config, view_defaults
</span><span>from</span><span> .security </span><span>import</span><span> USERS, check_password
</span></span></code></div></div></pre>

#### Inisialisasi View Class

<pre class="overflow-visible!" data-start="5495" data-end="5682"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>@view_defaults(renderer='home.pt'</span><span>)
</span><span>class</span><span></span><span>TutorialViews</span><span>:
    </span><span>def</span><span></span><span>__init__</span><span>(</span><span>self, request</span><span>):
        self.request = request
        self.logged_in = request.authenticated_userid
</span></span></code></div></div></pre>

* `self.logged_in` berisi:
  * `None` jika belum login
  * `'editor'` atau `'viewer'` jika sudah login
* Properti ini dipakai di template untuk menampilkan link  **Log In / Logout** .

#### View Home & Hello

<pre class="overflow-visible!" data-start="5891" data-end="6094"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>    @view_config(route_name='home'</span><span>)
    </span><span>def</span><span></span><span>home</span><span>(</span><span>self</span><span>):
        </span><span>return</span><span> {</span><span>'name'</span><span>: </span><span>'Home View'</span><span>}

</span><span>    @view_config(route_name='hello'</span><span>)
    </span><span>def</span><span></span><span>hello</span><span>(</span><span>self</span><span>):
        </span><span>return</span><span> {</span><span>'name'</span><span>: </span><span>'Hello View'</span><span>}
</span></span></code></div></div></pre>

* Masih sederhana, hanya mengembalikan data untuk template.

#### View Login

<pre class="overflow-visible!" data-start="6174" data-end="7279"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>    @view_config(route_name='login'</span><span>, renderer=</span><span>'login.pt'</span><span>)
    </span><span>def</span><span></span><span>login</span><span>(</span><span>self</span><span>):
        request = self.request
        login_url = request.route_url(</span><span>'login'</span><span>)
        referrer = request.url
        </span><span>if</span><span> referrer == login_url:
            referrer = </span><span>'/'</span><span></span><span># jangan jadikan halaman login sebagai came_from</span><span>
        came_from = request.params.get(</span><span>'came_from'</span><span>, referrer)

        message = </span><span>''</span><span>
        login = </span><span>''</span><span>
        password = </span><span>''</span><span>

        </span><span>if</span><span></span><span>'form.submitted'</span><span></span><span>in</span><span> request.params:
            login = request.params[</span><span>'login'</span><span>]
            password = request.params[</span><span>'password'</span><span>]
            hashed_pw = USERS.get(login)
            </span><span>if</span><span> hashed_pw </span><span>and</span><span> check_password(password, hashed_pw):
                headers = remember(request, login)
                </span><span>return</span><span> HTTPFound(location=came_from,
                                 headers=headers)
            message = </span><span>'Failed login'</span><span>

        </span><span>return</span><span></span><span>dict</span><span>(
            name=</span><span>'Login'</span><span>,
            message=message,
            url=request.application_url + </span><span>'/login'</span><span>,
            came_from=came_from,
            login=login,
            password=password,
        )
</span></span></code></div></div></pre>

**Penjelasan alur:**

1. **Menentukan `came_from`**
   * `came_from` adalah URL tujuan setelah login berhasil.
   * Jika user datang dari halaman lain, referrer dipakai.
   * Jika referrer = login sendiri, paksa kembali ke `/` agar tidak looping.
2. **GET Request (awal buka login)**
   * Tidak ada `form.submitted` → return form kosong.
   * Template memakai `message`, `login`, `password`, dan `came_from`.
3. **POST Request (submit form)**
   * Cek `login` dan `password` dari `request.params`.
   * Ambil hash dari `USERS[login]` jika user ada.
   * `check_password(password, hashed_pw)` → verifikasi.
   * Jika benar:
     * `headers = remember(request, login)` → buat cookie auth.
     * Redirect ke `came_from` dengan `HTTPFound` dan header cookie.
   * Jika salah:
     * `message = 'Failed login'` dan form ditampilkan lagi.

#### View Logout

<pre class="overflow-visible!" data-start="8136" data-end="8395"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-python"><span><span>    @view_config(route_name='logout'</span><span>)
    </span><span>def</span><span></span><span>logout</span><span>(</span><span>self</span><span>):
        request = self.request
        headers = forget(request)
        url = request.route_url(</span><span>'home'</span><span>)
        </span><span>return</span><span> HTTPFound(location=url,
                         headers=headers)
</span></span></code></div></div></pre>

* `forget(request)` menghasilkan header untuk  **menghapus cookie auth** .
* Setelah logout, user diarahkan kembali ke halaman `home`.

---

### 6. Template Login (login.pt)

<pre class="overflow-visible!" data-start="8570" data-end="9039"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-html"><span><span><h1</span><span>>Login</span><span></h1</span><span>>
</span><span><span</span><span></span><span>tal:replace</span><span>=</span><span>"message"</span><span>/>

</span><span><form</span><span></span><span>action</span><span>=</span><span>"${url}"</span><span></span><span>method</span><span>=</span><span>"post"</span><span>>
    </span><span><input</span><span></span><span>type</span><span>=</span><span>"hidden"</span><span></span><span>name</span><span>=</span><span>"came_from"</span><span></span><span>value</span><span>=</span><span>"${came_from}"</span><span>/>
    </span><span><label</span><span></span><span>for</span><span>=</span><span>"login"</span><span>>Username</span><span></label</span><span>>
    </span><span><input</span><span></span><span>type</span><span>=</span><span>"text"</span><span></span><span>id</span><span>=</span><span>"login"</span><span></span><span>name</span><span>=</span><span>"login"</span><span></span><span>value</span><span>=</span><span>"${login}"</span><span>/></span><span><br</span><span>/>
    </span><span><label</span><span></span><span>for</span><span>=</span><span>"password"</span><span>>Password</span><span></label</span><span>>
    </span><span><input</span><span></span><span>type</span><span>=</span><span>"password"</span><span></span><span>id</span><span>=</span><span>"password"</span><span></span><span>name</span><span>=</span><span>"password"</span><span></span><span>value</span><span>=</span><span>"${password}"</span><span>/></span><span><br</span><span>/>
    </span><span><input</span><span></span><span>type</span><span>=</span><span>"submit"</span><span></span><span>name</span><span>=</span><span>"form.submitted"</span><span></span><span>value</span><span>=</span><span>"Log In"</span><span>/>
</span><span></form</span><span>>
</span></span></code></div></div></pre>

* Hidden input `came_from` menyimpan URL tujuan setelah login.
* Pesan error (jika login gagal) ditampilkan di `message`.

---

### 7. Template Home (home.pt) – Menampilkan Log In / Logout

<pre class="overflow-visible!" data-start="9231" data-end="9552"><div class="contain-inline-size rounded-2xl relative bg-token-sidebar-surface-primary"><div class="sticky top-9"><div class="absolute end-0 bottom-0 flex h-9 items-center pe-2"><div class="bg-token-bg-elevated-secondary text-token-text-secondary flex items-center gap-4 rounded-sm px-2 font-sans text-xs"></div></div></div><div class="overflow-y-auto p-4" dir="ltr"><code class="whitespace-pre! language-html"><span><span><div</span><span>>
    </span><span><a</span><span></span><span>tal:condition</span><span>=</span><span>"view.logged_in is None"</span><span>
       </span><span>href</span><span>=</span><span>"${request.application_url}/login"</span><span>>Log In</span><span></a</span><span>>
    </span><span><a</span><span></span><span>tal:condition</span><span>=</span><span>"view.logged_in is not None"</span><span>
       </span><span>href</span><span>=</span><span>"${request.application_url}/logout"</span><span>>Logout</span><span></a</span><span>>
</span><span></div</span><span>>

</span><span><h1</span><span>>Hi ${name}</span><span></h1</span><span>>
</span><span><p</span><span>>Visit </span><span><a</span><span></span><span>href</span><span>=</span><span>"${request.route_url('hello')}"</span><span>>hello</span><span></a</span><span>></span><span></p</span><span>>
</span></span></code></div></div></pre>

* Jika belum login (`logged_in is None`) → tampilkan link  **Log In** .
* Jika sudah login → tampilkan link  **Logout** .

---

## 🔍 Alur Login–Logout Secara Singkat

1. User klik **Log In** → diarahkan ke `/login`.
2. User mengisi username & password → POST ke `/login`.
3. View `login`:
   * cek kredensial di `USERS` + `check_password()`.
   * jika valid → set cookie auth (via `remember`) + redirect.
   * jika gagal → tampilkan pesan `Failed login`.
4. Setelah login, `request.authenticated_userid` berisi `'editor'` atau `'viewer'`.
5. View `logout`:
   * memanggil `forget()` → hapus cookie auth.
   * redirect ke halaman `home`.

---

## ⚠️ Catatan Keamanan Penting

* Password **tidak boleh** disimpan plain text → selalu hash (bcrypt/scrypt/argon2).
* Secret (`tutorial.secret`) harus  **rahasia dan cukup panjang** , jangan asal.
* Production sebaiknya pakai HTTPS agar cookie auth tidak mudah diintip.

---

## Inti Pembelajaran

* Pyramid menyediakan **security model** yang fleksibel dan pluggable.
* **SecurityPolicy** menentukan bagaimana identitas user dibaca dan disimpan.
* `AuthTktCookieHelper` menyimpan user id di cookie yang sudah ditandatangani.
* `bcrypt` digunakan untuk hashing satu arah password.
* Login flow:
  * GET → tampilkan form.
  * POST → validasi kredensial → set cookie auth → redirect.
* `request.authenticated_userid` menjadi cara standar untuk mengecek user yang sedang login.
* Template dapat menyesuaikan tampilan (Log In / Logout) berdasarkan status login.
