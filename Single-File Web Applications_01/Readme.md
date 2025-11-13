# **01 – Single-File Web Applications**

## Ringkasan Singkat

Topik ini menunjukkan cara tercepat memulai aplikasi Pyramid menggunakan satu file Python tanpa struktur package. Contoh sederhana ini memperkenalkan dasar-dasar kerja Pyramid seperti routing, views, request–response cycle, dan cara menjalankan aplikasi melalui server WSGI menggunakan Waitress.

## Analisis

Pendekatan single-file menunjukkan fleksibilitas Pyramid sebagai microframework yang ringan namun tetap mampu berkembang menjadi aplikasi besar. Melalui `Configurator`, pengembang dapat menetapkan routing dan menghubungkannya ke fungsi view secara eksplisit, memperlihatkan inti arsitektur Pyramid yang modular dan terstruktur. Fungsi view `hello_world` menampilkan bagaimana request diproses dan dikonversi menjadi response HTML.

Blok `if __name__ == '__main__':` memastikan aplikasi hanya berjalan ketika file dieksekusi langsung, sedangkan `make_wsgi_app()` menghasilkan aplikasi WSGI sesuai standar Python. Penggunaan Waitress sebagai server menunjukkan alur lengkap cara Pyramid menerima request dari browser dan mengirimkan response.

Contoh ini menjadi landasan penting untuk memahami konsep-konsep lanjutan, karena seluruh topik ke depan tetap mengikuti pola dasar: konfigurasi → routing → view → response → server.

## Inti Pembelajaran

* Pyramid dapat dijalankan hanya dengan satu file Python seperti microframework.
* `Configurator` adalah komponen inti untuk membuat routing dan menghubungkan view.
* Aplikasi Pyramid berjalan menggunakan standar WSGI sehingga kompatibel dengan berbagai server.
* Request diproses oleh view yang menghasilkan objek `Response`.

## Kaitan Dengan Topik Lain

Struktur dasar single-file ini menjadi fondasi untuk memahami topik selanjutnya seperti konfigurasi `.ini`, templating, testing, sesi, database, hingga autentikasi dan otorisasi, yang semuanya tetap berpusat pada konsep routing–view–response.
