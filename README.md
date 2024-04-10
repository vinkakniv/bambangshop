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

#### Reflection Publisher-3
