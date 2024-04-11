# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

> _In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?_

Berdasarkan _Observer design patterns_, mendefinisikan `Subscriber` sebagai _interface_ memberikan fleksibilitas tinggi karena memungkinkan berbagai objek untuk diobservasi setelah mengimplementasikan _interface_ `Subscriber` tersebut. Dalam konteks penggunaan Rust, konsep _interface_ ini sejalan dengan penggunaan _trait_. Dengan mendefinisikan _trait_ `Subscriber`, memungkinkan berbagai tipe `Subscriber` yang berbeda, masing-masing dengan cara reaksi mereka sendiri terhadap pembaruan. Hal ini sangat berguna terutama ketika terdapat kebutuhan untuk memperlakukan `Subscriber` dengan cara yang berbeda sesuai dengan tipe atau keperluan mereka.

Jika menggunakan _single Model struct_ sebagai pengganti, semua `Subscriber` akan terbatas pada satu bentuk dan harus merespon pembaruan dengan cara yang sama. Dalam kasus Bambangshop yang saat ini hanya terdiri dari satu jenis `Subscriber` saja, pendekatan ini mungkin memadai, namun, ini secara signifikan membatasi kemampuan ekstensibilitas dan adaptasi kode terhadap perubahan atau penambahan fitur baru di masa depan.

> _id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?_

Bambangshop yang menggunakan identifier unik (seperti `id` dalam Program dan `url` dalam Subscriber), `DashMap` akan lebih cocok digunakan daripada `Vec`. Hal ini dikarenakan `DashMap` menggunakan mekanisme _hashing_ untuk langsung mengakses lokasi suatu elemen, sedangkan `Vec` memerlukan penelusuran elemen satu per satu hingga elemen yang diinginkan ditemukan. Map/dictionary juga secara alami memaksa keunikan kunci. Ini sejalan dengan kebutuhan akan `id` dan `url` yang unik. Selain itu, operasi pencarian, penyisipan, dan penghapusan dalam `DashMap` umumnya lebih cepat (rata-rata O(1)) dibandingkan dalam `Vec` (O(n)). Meskipun secara teknis `Vec` bisa bekerja, `DashMap` akan lebih efisien dan lebih sesuai dengan kebutuhan dalam kasus ini.

> _When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread-safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?_

_Singleton pattern_ memastikan hanya ada satu instansi kelas. Ini sangat berguna untuk sumber daya seperti _database connection pool_, yang harus dibagi di seluruh aplikasi. Dengan _Singleton pattern_, kita memastikan bahwa hanya ada satu titik akses ke sumber daya ini. Sedangkan `DashMap` adalah HashMap yang aman untuk thread. Ini menjadi penting ketika kita perlu melakukan operasi baca/tulis secara bersamaan oleh beberapa thread tanpa risiko konflik data. Dalam pengembangan multi-thread, `DashMap` memungkinkan akses dan modifikasi bersamaan tanpa mengorbankan keamanan. Menggunakan _singleton pattern_ untuk daftar Subscriber saja tidak cukup karena keamanan thread tetap harus dijamin. Sehingga untuk kebutuhan seperti mengelola daftar Subscriber secara aman di lingkungan multi-thread, `DashMap` adalah solusi yang lebih cocok dan efisien.


#### Reflection Publisher-2

> In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

Memisahkan `Service` dan `Repository` dari Model dalam MVC _compound pattern_ sangat penting karena beberapa alasan. Pertama, hal ini mendukung prinsip _Single Responsibility_, di mana setiap bagian sistem hanya fokus pada satu fungsi, membuat kode lebih mudah dijaga dan dikembangkan. Kedua, pemisahan ini memfasilitasi fleksibilitas dalam pengembangan, memungkinkan kita untuk mengubah atau menambahkan cara penyimpanan data (`Repository`) atau logika bisnis (`Service`) tanpa mengganggu struktur Model. Ini juga memperkuat prinsip _Open/Closed_ karena kita bisa memperluas fungsi tanpa mengubah kode yang ada. Ketiga, dengan adanya pemisahan, kita memperoleh manfaat dari prinsip _Dependency Inversion_, di mana komponen sistem bergantung pada abstraksi, bukan detail konkret, yang mendorong desain yang lebih modular dan mudah diadaptasi. Terakhir, memisahkan `Service` dan `Repository` dari Model mempermudah pengujian, karena setiap komponen dapat diuji secara terpisah dengan kebutuhan yang lebih sedikit untuk _setup_ yang kompleks. Kesimpulannya, pemisahan ini membantu dalam membangun sistem yang bersih, terorganisir, dan mudah untuk di-_maintain_ atau dikembangkan lebih lanjut.

> What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

Jika kita hanya menggunakan `Model` dalam sistem, semua tanggung jawab, mulai dari penyimpanan data hingga logika bisnis, akan terpusat pada `Model` tersebut. Hal ini akan membuat setiap model seperti `Program`, `Subscriber`, dan `Notification` menjadi sangat kompleks karena mereka harus menangani lebih dari satu jenis tugas. Misalnya, Model `Program` tidak hanya menyimpan informasi tentang program itu sendiri, tetapi juga harus mengelola subscriber dan mengirim notifikasi. Ini akan membuat kode dalam Model `Program` menjadi sangat padat dan rumit karena harus menangani berbagai aspek yang seharusnya tidak menjadi bagian dari _task_-nya.

Interaksi antara model akan menyebabkan peningkatan ketergantungan langsung, di mana perubahan pada satu model dapat berdampak langsung pada model lain. Ini akan membuat sistem menjadi sulit untuk di-_maintain_ dan di-_update_ karena perubahan kecil bisa memiliki efek domino yang mungkin tidak diinginkan. Selain itu, menguji sistem seperti ini akan menjadi sangat sulit. Kita harus memastikan bahwa setiap bagian dari model bekerja sesuai harapan sambil juga memastikan bahwa interaksi antar model tidak memunculkan _bug_. Ini akan membutuhkan banyak sekali _setup_ pengujian dan _mock object_, yang menambah kompleksitas pengujian. Menurut saya, memisahkan _task_ ke dalam komponen seperti `Service` untuk logika bisnis dan `Repository` untuk operasi data akan sangat membantu. Hal ini tidak hanya membuat kode lebih terorganisir dan mudah dibaca, tetapi juga mempermudah pengujian dan pemeliharaan sistem. Kita bisa mengubah atau menambahkan fungsionalitas dengan lebih mudah tanpa khawatir akan mempengaruhi bagian lain dari sistem secara tidak terduga.

> Have you explored more about Postman? Tell us how this tool helps you to test your current work. Maybe you want to also list which features in Postman you are interested in or feel like it’s helpful to help your Group Project or any of your future software engineering projects.

Saya telah menggunakan Postman dan menemukan beberapa fiturnya sangat mendukung pekerjaan saya, khususnya dalam pengujian API. Fitur _Collections_ sangat memudahkan dalam mengorganisir dan berkolaborasi dengan tim karena memungkinkan pengelompokan permintaan yang terkait, menjadikan _task_ lebih terstruktur. Fitur _Automated Tests_ mempercepat proses verifikasi apakah API berfungsi sesuai harapan tanpa intervensi manual, menghemat waktu dan upaya. Postman juga menyediakan _Documentation_ yang dihasilkan secara otomatis dari _Collection_, yang sangat membantu dalam memahami dan berkomunikasi tentang API yang sedang dikembangkan. Kesimpulannya, Postman tidak hanya meningkatkan efisiensi kerja melalui pengujian yang lebih cepat dan lebih efektif, tetapi juga memperkuat kolaborasi tim dan kelancaran pengembangan proyek.

#### Reflection Publisher-3
