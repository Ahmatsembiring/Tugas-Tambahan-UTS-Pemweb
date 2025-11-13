# **02 – Python Packages for Pyramid Applications**

## Ringkasan Singkat

Topik ini menjelaskan bagaimana membuat aplikasi Pyramid menggunakan struktur Python package yang benar. Dengan membuat folder berisi `__init__.py` dan file `setup.py`, aplikasi menjadi bagian dari proyek Python yang dapat di-install dalam mode pengembangan (`pip install -e .`). Pendekatan ini membuat struktur aplikasi lebih terorganisir dan siap berkembang menjadi proyek besar.

## Analisis

Penggunaan Python package memberikan struktur yang lebih profesional dibanding single-file. Dengan adanya `__init__.py`, direktori `tutorial/` menjadi sebuah package sehingga mudah dikelola dan diimpor. File `setup.py` berfungsi sebagai identitas proyek, mendefinisikan dependencies (`pyramid`, `waitress`) dan memungkinkan instalasi menggunakan  *development mode* . Mode ini memudahkan pengembang karena setiap perubahan pada kode langsung tercermin tanpa perlu meng-install ulang paket.

Meski kode aplikasinya masih sama seperti contoh sebelumnya, perpindahan dari satu file ke struktur package menunjukkan bagaimana Pyramid mengikuti standar ekosistem Python. Dengan cara ini, aplikasi menjadi modular, dapat diperluas, dan kompatibel dengan tooling Python seperti pip, virtual environment, dan pengelolaan dependency.

Menjalankan aplikasi dengan `python tutorial/app.py` dianggap tidak ideal dalam praktik sebenarnya karena modul dalam package tidak seharusnya dieksekusi langsung sebagai script, namun hal ini digunakan di tutorial untuk memahami proses dari awal.

## Inti Pembelajaran

* `__init__.py` menjadikan folder sebagai package Python yang valid.
* `setup.py` memuat metadata dan dependency proyek.
* `pip install -e .` mengaktifkan  *editable mode* , ideal untuk pengembangan.
* Struktur package memudahkan aplikasi tumbuh menjadi project besar.Kaitan Dengan Topik Lain

Struktur package menjadi dasar untuk topik lanjutan seperti routing, templating, testing, dan integrasi database. Semua fitur Pyramid nantinya dibangun di atas struktur modular yang sudah dibuat pada langkah ini.
