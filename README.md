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

Pertanyaan 1: In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

Jawaban:

Dalam desain perangkat lunak, pemisahan antara Service dan Repository dari Model dalam pola arsitektur Model-View-Controller (MVC) didasarkan pada prinsip desain yang bertujuan untuk meningkatkan keterbacaan, skalabilitas, dan pemeliharaan kode.

Pertama, pemisahan ini mengikuti prinsip Single Responsibility Principle (SRP) dari SOLID, yang menyatakan bahwa setiap komponen harus memiliki satu tanggung jawab spesifik. Jika Model mencakup logika bisnis (Service) dan akses data (Repository), maka kode menjadi sulit dipelihara dan diperluas.

Kedua, pemisahan ini mendukung Separation of Concerns (SoC), yang memastikan bahwa setiap lapisan dalam arsitektur memiliki tanggung jawab yang jelas. Repository berfokus pada interaksi dengan database, Service menangani logika bisnis, dan Model hanya merepresentasikan struktur data. Dengan ini, perubahan dalam akses data tidak akan berdampak pada logika bisnis, dan sebaliknya.

Ketiga, dari perspektif Testability, pemisahan Service dan Repository mempermudah pengujian unit. Dengan menggunakan lapisan Service, kita dapat melakukan pengujian tanpa bergantung langsung pada database. Ini memungkinkan penggunaan mocking atau stubbing dalam pengujian untuk meningkatkan efisiensi dan kecepatan pengujian.

Keempat, dalam proyek besar, pemisahan ini mendukung Scalability dan Maintainability. Jika ada perubahan dalam logika bisnis, kita cukup memperbarui Service tanpa mengubah akses data. Demikian pula, jika kita mengganti database atau menggunakan ORM yang berbeda, kita hanya perlu memodifikasi Repository tanpa memengaruhi lapisan lainnya.

Kesimpulannya, meskipun MVC secara tradisional tidak memiliki Service dan Repository, pemisahan ini memberikan struktur yang lebih modular, meningkatkan keterbacaan, dan memungkinkan pengembangan yang lebih fleksibel dan teruji dengan baik.

Pertanyaan 2: What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?


Jawaban:

Jika kita hanya menggunakan Model tanpa memisahkan Service dan Repository, seluruh tanggung jawab dalam pengelolaan data dan logika bisnis akan berada di dalam satu struktur. Hal ini menyebabkan kode menjadi sulit dikelola karena setiap perubahan dalam penyimpanan data atau aturan bisnis akan berdampak langsung pada Model. Misalnya, jika Program menangani daftar Subscriber sekaligus mengatur pengiriman Notification, maka perubahan kecil dalam cara penyimpanan atau pengiriman notifikasi dapat memengaruhi banyak bagian dalam kode.

Selain itu, ketergantungan antar-Model akan meningkat. Model Program harus mengetahui detail bagaimana Subscriber disimpan dan bagaimana Notification dikirim, yang menyebabkan kode menjadi kurang fleksibel. Jika suatu saat penyimpanan data Subscriber harus dipindahkan dari memori ke database, maka banyak bagian dalam Model Program yang harus diubah. Hal ini juga membuat pengujian lebih sulit karena sulit untuk mengisolasi bagian kode tertentu tanpa mempengaruhi bagian lainnya.

Dari segi skalabilitas, tanpa pemisahan yang jelas, menambahkan fitur baru seperti segmentasi Subscriber atau aturan notifikasi yang lebih kompleks akan membuat Model semakin besar dan sulit dipahami. Dengan memisahkan Service untuk menangani logika bisnis dan Repository untuk menangani penyimpanan data, kita dapat memastikan bahwa setiap komponen memiliki tanggung jawab yang jelas, lebih mudah diuji, serta lebih fleksibel dalam menghadapi perubahan.

Pertanyaan 3: 

Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

Jawaban:

Ya, saya telah mengeksplorasi lebih lanjut tentang Postman, dan alat ini sangat membantu dalam menguji API yang saya gunakan dalam proyek saat ini, khususnya dalam menangani subscriber dan notification. Dengan Postman, saya bisa dengan mudah mengirim permintaan HTTP ke API yang menangani daftar subscriber, mengelola notifikasi, dan memastikan bahwa setiap permintaan dikirim serta diterima dengan benar oleh server.

Salah satu fitur Postman yang sangat membantu adalah Collections, di mana saya bisa menyimpan dan mengelompokkan berbagai permintaan yang terkait dengan subscriber dan notification dalam satu tempat. Misalnya, saya bisa memiliki satu koleksi untuk API yang menambahkan subscriber, satu untuk menghapusnya, dan satu lagi untuk mengirim notifikasi ke semua subscriber. Ini sangat membantu agar pengujian tetap terorganisir.

Selain itu, fitur Environment Variables sangat berguna dalam proyek ini. Saya bisa menyimpan token autentikasi dan URL API yang berbeda untuk staging maupun production, sehingga saya tidak perlu mengubahnya secara manual setiap kali melakukan pengujian. Fitur Automated Testing juga bermanfaat karena memungkinkan saya menulis skrip uji sederhana untuk memverifikasi apakah data yang dikembalikan dari API sudah sesuai ekspektasi.

Secara keseluruhan, Postman sangat membantu dalam proyek saya saat ini, terutama dalam menguji API yang berhubungan dengan subscriber dan notification. Ke depannya, saya juga melihat bahwa fitur-fitur seperti Monitoring dan Test Scripts bisa sangat berguna dalam proyek perangkat lunak yang lebih kompleks, terutama jika membutuhkan pengujian otomatis dan pemantauan API secara real-time.


#### Reflection Publisher-3

Pertanyaan 1: Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?

Jawaban:

Berdasarkan kode yang diberikan, tutorial ini menggunakan variasi Push Model dari Observer Pattern.
Dalam implementasi ini, kelas NotificationService bertindak sebagai publisher, dan metode notify() mendorong (push) data notifikasi ke semua subscriber yang terdaftar untuk tipe produk tertentu. Hal ini dapat dilihat dalam implementasi metode notify() di src/service/notification.rs.

Beberapa karakteristik push model yang terlihat:

Publisher (NotificationService) secara aktif mengirimkan data notifikasi ke semua subscriber
Data notifikasi lengkap didorong ke subscriber tanpa subscriber perlu meminta
Setiap kali ada perubahan pada produk (create, delete, publish), notifikasi otomatis didorong ke subscriber yang sesuai
Metode update() pada Subscriber langsung menerima payload penuh dari publisher

Berbeda dengan pull model di mana subscriber harus secara eksplisit meminta data dari publisher, pada push model ini, publisher menginisiasi pengiriman data secara otomatis kepada semua subscriber yang terdaftar.

Pertanyaan 2:What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)

Jawaban:

Keuntungan Pull Model:


a. Kontrol Subscriber Lebih Baik

- Subscriber memiliki kendali penuh kapan mereka ingin mengambil data

- Mengurangi beban server karena tidak mengirim data secara otomatis

- Subscriber dapat memutuskan sendiri kapan mereka ingin memperbarui informasi


b. Efisiensi Bandwidth

- Hanya mengambil data ketika diperlukan

- Mengurangi jumlah transfer data yang tidak perlu

- Menurunkan beban jaringan, terutama jika subscriber tidak selalu membutuhkan update terbaru


c. Keamanan dan Privasi

- Subscriber dapat memilih waktu dan frekuensi pengambilan data

- Mengurangi risiko pengiriman data yang tidak diinginkan

Kekurangan Pull Model:


a. Kompleksitas Implementasi

- Memerlukan mekanisme polling atau webhook tambahan

- Subscriber harus secara aktif melakukan request untuk mendapatkan data

- Meningkatkan kompleksitas kode di sisi subscriber


b. Keterlambatan Informasi

- Informasi mungkin tidak segera tersedia

- Jika subscriber jarang melakukan pull, mereka bisa ketinggalan update penting

- Tidak real-time seperti push model


c. Overhead Komputasi

- Setiap subscriber harus melakukan request berkali-kali untuk memeriksa update

- Meningkatkan beban komputasi di sisi klien

- Membutuhkan mekanisme manajemen state untuk melacak update terakhir


Pertanyaan 3: Explain what will happen to the program if we decide to not use multi-threading in the notification process.


Jawaban:

Jika kita memutuskan untuk tidak menggunakan multi-threading dalam proses notifikasi, akan terjadi beberapa konsekuensi penting pada program Bambang Shop:

a. Blocking Proses Utama

Dalam implementasi saat ini, metode notify() menggunakan thread::spawn() untuk mengirim notifikasi secara asynchronous. Tanpa multi-threading, proses pengiriman notifikasi akan berjalan secara synchronous. Artinya:

- Setiap panggilan notify() akan menunggu hingga semua notifikasi terkirim

- Proses utama akan terblokir sampai semua subscriber menerima notifikasi

- Kinerja aplikasi akan sangat terganggu, terutama jika ada banyak subscriber


b. Potensi Masalah Performa

- Jika salah satu subscriber lambat merespons atau mengalami kesalahan

- Proses pengiriman notifikasi akan menghentikan seluruh eksekusi program

- Waktu respons aplikasi akan menjadi sangat lambat


Jadi, dapat disimpulkan bahwa tidak menggunakan multi-threading akan sangat merugikan kinerja aplikasi Bambang Shop. Proses notifikasi akan menjadi bottleneck utama, membuat aplikasi tidak responsif dan tidak efisien dalam menangani update produk.
Oleh karena itu, penggunaan multi-threading dalam metode notify() adalah keputusan arsitektur yang baik untuk menjaga performa dan keandalan sistem notifikasi.