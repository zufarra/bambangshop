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

Pertanyaan 1: In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

Jawaban:

Dalam implementasi BambangShop, kita tidak memiliki variasi dalam cara subscriber berlangganan atau menerima notifikasi. Semua subscriber memiliki struktur data yang sama dan hanya disimpan dalam DashMap berdasarkan jenis produk. Jika hanya ada satu cara subscriber berinteraksi, maka satu model struct sudah cukup. Tidak ada keuntungan langsung dari menggunakan trait karena tidak ada perilaku yang perlu dibedakan di antara berbagai jenis subscriber.

Jika nantinya kita ingin memiliki berbagai jenis subscriber, seperti:

1. EmailSubscriber yang dapat menerima notifikasi via email.

2. WebhookSubscriber yang menerima notifikasi via HTTP request.

3. MobilePushSubscriber untuk aplikasi mobile.

Maka penggunaan trait akan lebih masuk akal agar setiap jenis subscriber bisa mengimplementasikan metode yang berbeda dalam menerima notifikasi.


Jadi, dapat disimpulkan bahwa dalam implementasi BambangShop saat ini, menggunakan satu struct tanpa trait sudah cukup, karena kita hanya memiliki satu jenis subscriber dengan pola penyimpanan yang tetap. Namun, jika nanti ingin menambahkan berbagai mekanisme subscription dengan perilaku berbeda, maka trait akan sangat berguna untuk mendukung fleksibilitas dan ekstensibilitas sistem.

Pertanyaan 2: id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

Jawaban:

Dalam implementasi BambangShop, penggunaan DashMap lebih tepat dibandingkan Vec karena memastikan bahwa id pada Product dan url pada Subscriber tetap unik dengan efisiensi yang lebih tinggi. Jika kita menggunakan Vec, kita harus melakukan pencarian manual dengan kompleksitas O(n) untuk memastikan tidak ada duplikasi sebelum menambahkan data baru, yang menjadi kurang efisien seiring bertambahnya jumlah produk atau subscriber. Dengan DashMap, pencarian dan penyisipan data dapat dilakukan dalam waktu O(1), sehingga lebih cepat dan optimal, terutama dalam skenario dengan banyak data atau akses bersamaan dari beberapa thread. Selain itu, struktur DashMap<String, DashMap<String, Subscriber>> pada SubscriberRepository juga memungkinkan pengelompokan berdasarkan product_type secara lebih terstruktur dan efisien. Oleh karena itu, penggunaan DashMap memang diperlukan dalam kasus ini untuk memastikan integritas data dan meningkatkan performa sistem dibandingkan hanya menggunakan Vec.

Pertanyaan 3: When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

Jawaban:

Dalam konteks BambangShop, penggunaan DashMap tetap diperlukan meskipun kita bisa menerapkan Singleton pattern. Singleton pattern bertujuan untuk memastikan bahwa hanya ada satu instance dari suatu objek dalam aplikasi, yang dalam hal ini bisa digunakan untuk mengelola daftar subscriber secara global. Namun, Singleton sendiri tidak secara otomatis menjamin thread safety, terutama ketika banyak thread ingin membaca dan menulis data secara bersamaan. Rust memiliki aturan kepemilikan yang ketat, tetapi tanpa DashMap, kita perlu menggunakan Mutex<HashMap<>> atau RwLock<HashMap<>> untuk menjaga konsistensi data saat terjadi akses bersamaan, yang bisa menimbulkan bottleneck karena mekanisme locking.

Sebaliknya, DashMap dirancang sebagai thread-safe concurrent HashMap, yang memungkinkan akses paralel tanpa perlu manual locking seperti pada Mutex atau RwLock. Dengan menggunakan DashMap, kita mendapatkan efisiensi yang lebih tinggi dalam pencarian dan pembaruan data tanpa mengorbankan keamanan thread. Oleh karena itu, dalam skenario seperti ini, menggunakan DashMap lebih praktis dan efisien dibandingkan hanya menerapkan Singleton pattern, karena kita tetap membutuhkan mekanisme konkuren untuk menangani banyak subscriber yang berlangganan produk secara bersamaan.

#### Reflection Publisher-2


#### Reflection Publisher-3

