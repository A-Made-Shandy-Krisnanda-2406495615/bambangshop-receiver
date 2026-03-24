# BambangShop Receiver App
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
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create SubscriberRequest model struct.`
    -   [ ] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Notification repository.`
    -   [ ] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Commit: `Implement receive_notification function in Notification service.`
    -   [ ] Commit: `Implement receive function in Notification controller.`
    -   [ ] Commit: `Implement list_messages function in Notification service.`
    -   [ ] Commit: `Implement list function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1
1. `RwLock<Vec<Notification>>` lebih tepat digunakan pada kasus ini karena memungkinkan banyak thread melakukan operasi baca secara bersamaan, sementara operasi tulis tetap dibatasi satu thread pada satu waktu. Dibandingkan `Mutex<Vec<Notification>>`, pendekatan ini lebih efisien karena repository receiver cenderung lebih sering membaca daftar notifikasi daripada menulis data baru. Dengan demikian, penggunaan `RwLock` dapat mengurangi bottleneck saat aplikasi menangani akses data secara konkuren.

2. Pada Rust, `static` variable pada dasarnya bersifat immutable demi menjaga keamanan memori dan thread safety. Oleh karena itu, meskipun kita menggunakan `lazy_static!` untuk membuat singleton database, perubahan isi data tetap harus dibungkus dengan mekanisme sinkronisasi seperti `RwLock` atau `Mutex`. Hal ini berbeda dengan Java yang lebih longgar terhadap mutasi variabel statis. Dalam konteks tutorial ini, kombinasi `lazy_static!` dan `RwLock` diperlukan agar data global dapat diakses dan dimodifikasi secara aman.

#### Reflection Subscriber-2
1. Ya, saya telah mempelajari isi `src/lib.rs` pada project receiver. File tersebut memuat konfigurasi penting aplikasi, seperti `APP_CONFIG`, `REQWEST_CLIENT`, tipe `Result`, serta fungsi pembentuk error response. Salah satu hal yang saya pelajari adalah penggunaan `Default` pada `AppConfig` untuk menyediakan nilai bawaan bagi `instance_root_url`, `publisher_root_url`, dan `instance_name`. Pendekatan ini membuat konfigurasi aplikasi tetap memiliki nilai default yang aman, tetapi tetap fleksibel karena dapat ditimpa melalui environment variable.

2. Observer Pattern mempermudah penambahan subscriber baru karena publisher tidak perlu mengetahui detail implementasi setiap subscriber secara spesifik. Subscriber cukup mendaftarkan diri melalui endpoint subscribe, dan setelah itu akan menerima update sesuai `product_type` yang dipilih. Jika terdapat lebih dari satu instance Main App, pola ini tetap dapat digunakan, tetapi setiap instance publisher akan mengelola daftar subscriber masing-masing. Karena itu, bila sistem ingin benar-benar konsisten antar-instance publisher, diperlukan mekanisme sinkronisasi tambahan di luar implementasi tutorial ini.

3. Ya, saya telah menggunakan Postman untuk menguji endpoint-endpoint pada receiver maupun publisher. Postman sangat membantu karena mempermudah verifikasi request dan response secara langsung, termasuk untuk alur subscribe, unsubscribe, receive notification, dan list messages. Dalam konteks kerja tim, collection Postman juga bermanfaat karena setiap anggota dapat menggunakan skenario pengujian yang sama, sehingga perilaku API dapat diperiksa secara konsisten.
