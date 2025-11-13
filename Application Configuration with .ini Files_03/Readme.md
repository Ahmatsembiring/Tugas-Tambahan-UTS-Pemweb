# 03 – Application Configuration with .ini Files

## 📌 Ringkasan Singkat

Topik ini menjelaskan bagaimana Pyramid memisahkan **konfigurasi** dari **kode** dengan menggunakan file `.ini` dan perintah `pserve`. Melalui `setup.py` dengan `entry_points` dan file `development.ini`, aplikasi Pyramid dapat dijalankan dengan cara yang lebih terstruktur, mudah diatur, dan siap untuk lingkungan berbeda (development, production, dll).

## 📌 Analisis Konsep

Berbeda dengan pendekatan yang seluruhnya mengandalkan kode Python, Pyramid menyediakan mekanisme konfigurasi berbasis `.ini` yang dibaca oleh `pserve`. File `.ini` ini menentukan:

- **Aplikasi mana** yang akan dijalankan (`[app:main]` → `use = egg:tutorial`)
- **Server WSGI apa** yang digunakan (`[server:main]` → `waitress` + port)
- **Pengaturan logging** dan konfigurasi lain.

Kunci pentingnya ada pada **entry point** di `setup.py`:

```
entry_points={
    'paste.app_factory': [
        'main = tutorial:main',
    ],
},
```


Baris ini memberi tahu sistem bahwa untuk membuat WSGI app, Pyramid harus memanggil fungsi `main` di package `tutorial`. Saat `pserve development.ini` dijalankan, ia membaca `[app:main]`, menemukan `egg:tutorial`, lalu melalui entry point memanggil `tutorial.main(settings=...)`.

Pendekatan ini membuat aplikasi:

* **Lebih fleksibel** → cukup ganti `.ini` untuk ubah port, server, logging, dll.
* **Lebih rapi** → bootstrap aplikasi dipusatkan di fungsi `main()` dalam `__init__.py`.
* **Lebih cocok untuk real project** → mudah punya banyak environment (`development.ini`, `production.ini`, dst).


**Setup.py**

```
setup(
    name='tutorial',
    install_requires=['pyramid', 'waitress'],
    entry_points={'paste.app_factory': ['main = tutorial:main']},
)
```
