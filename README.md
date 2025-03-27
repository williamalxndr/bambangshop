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
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [x] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [x] Commit: `Implement publish function in Program service and Program controller.`
    -   [x] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [x] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. Tidak perlu menggunakan interface dalam kasus ini. Dalam contoh di buku Head First Design Pattern, subscriber dapat dibagi menjadi banyak subclass, dalam kasus inilah dibutuhkan interface. Namun, jika dalam aplikasi BambangShop kita ingin menggunakan lebih dari satu macam subscriber yang akan dijadikan subclass dari subscriber, maka kita akan membutuhkan interface untuk subscriber.

2. Menggunakan dashmap akan lebih baik. Pencarian yang digunakan untuk method add, get_by_id, delete, dll akan menjadi lebih cepat yang mencari dengan id (O(1)), sedangkan jika menggunakan Vec, kita memerlukan iterasi setiap isi dari repository tersebut untuk pencarian(O(n)). Selain itu, dengan menggunakan DashMap, kita dapat mengantisipasi jika terdapat Product atau Subscriber yang tidak unik, jika menggunakan Vec, jika hanya menambahkan Product/Subscriber tidak akan terdeteksi jika Product/Subscriber dengan id/url yang sama sudah ada di repository (kecuali kita menggunakan pencarian yang akan memakan waktu O(n)).

3. Menurut saya, implementasi sekarang yang menggunakan DashMap ditambah dengan `lazy_static` untuk lazy initialization saja sudah menerapkan prinsip singleton pattern. Jadi, kita tidak perlu mengubah penggunaan dashmap dan mengimplementasikan ulang lagi.

#### Reflection Publisher-2
1. Service dan repository berperan dalam komunikasi antara model dengan controller untuk MVC pattern. Repository berperan dalam penyimpanan data yang sudah dikonfigurasi di model. Sedangkan service menangani logika bisnis di aplikasi kita. Service berinteraksi dengan repository untuk mendapatkan data yang dibutuhkan, yang kemudian akan diproses dan diolah sebelum diberikan hasilnya ke controller.

2. Jika peran repository dan service dibuat di model, implementasi ini tetap valid untuk MVC pattern. Namun, kode kita akan lebih kompleks dan jauh lebih sulit untuk dimaintain. Hal ini dikarenakan penyatuan banyak tanggung jawab (konfigurasi, penyimpanan data, logika bisnis) dalam satu tempat yaitu model. Dengan penggabungan tanggung jawab ini dalam model, komunikasi antara `Product`, `Subscriber`, dan `Notification` akan jauh lebih rumit dan sulit. Perubahan pada salah satu komponen bisa mempengaruhi bagian lain yang akan menyulitkan proses debugging kita.

3. Postman sangat membantu dalam testing API, baik yang kita buat dalam aplikasi kita ataupun API eksternal. Kita dapat mengirim HTTP request menggunakan API endpoint dan dapat mengetahui HTTP response dari API tersebut baik dalam berbagai format seperti JSON, XML, HTML, dll. Hal ini sangat berguna untuk pengujian API eksternal yang mungkin kita tidak tahu bagaimana cara akses data dari API tersebut atau API tersebut memiliki dokumentasi yang terbatas ataupun untuk pengujian apakah API aplikasi yang kita buat berjalan dengan baik.

#### Reflection Publisher-3
1. Kita menggunakan push model. Hal ini dikarenakan publisher akan mem-push data ke subscriber setiap kali ada data product yang diubah baik itu create, delete, atau publish.

2. Jika menggunakan pull, publisher tidak perlu melakukan notify kepada semua subscriber setiap kali dilakukan create, delete, atau publish. Namun, data hanya akan dipull setiap kali subscriber membutuhkannya. Hal ini akan lebih mengurangi beban pada publisher karena notification hanya akan dipanggil subscriber ketika butuh saja, namun bisa menjadi kelemahan jika banyak subscriber melakukan pull pada waktu yang sama sehingga memicu beban tambahan pada server.

3. Jika tidak menerapkan multi-threading, maka dapat berpotensi pada lambatnya proses notifikasi pada subscribers. Jika terdapat satu notifikasi yang lambat pada satu subscriber (misal karena jaringan lambat), maka notifikasi ke subscriber lain akan menunggu sampai notifikasi subscriber tersebut sukses. 