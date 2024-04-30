# adpro-tutorial9-rustgRPC

# Reflection

## 1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

| RPC Methods                  | Definition                                                                                                                                                                            |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Unary RPC                    | Methode yang paling simpel dari RPC. Client mengirimkan single request ke server dan menerima single response. Cocok digunakan untuk simple data fetching dan low latency operations. |
| Server Streaming RPC         | Client mengirim sebuah request, lalu menerima a stream of messages sebagai responnya. Cocok digunakan untuk large data transfers.                                                     |
| Client Streaming RPC         | Client mengirim a stream of messages ke server, lalu menerima a single response. Cocok untuk large uploads.                                                                           |
| Bi-directional Streaming RPC | Client dan server sama-sama mengirim stream messages dan responses. Cocok untuk real-time communication                                                                               |

## 2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

| Kategori        | Security considertaions                                                                                     |
| --------------- | ----------------------------------------------------------------------------------------------------------- |
| Authentication  | Pastikan secrets seperti tokens dan private keys di manage dengan aman, bukan hard-coded di source codenya. |
| Authorization   | Definisikan separation of concern yang jelas antara autentikasi dan authorisasi.                            |
| Data encryption | Konfigurasi TLS (Transport Layer Security). Update konfigurasi secara regular untuk mencegah eksploitasi    |

## 3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

Dalam chat application, kita membutuhkan interaksi real time yang dapat diperoleh dengan bidirectional streaming. Namun, gRPC memiliki potensi sulit memastikan tidak terjadinya race condition dan juga diperlukan sinkronisasi data. Masalah lainnya dapat timbul akibat koneksi yang hidup lama dan tidak bisa diputuskan sehingga akan membebankan server.

## 4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?

|               |                                                                                                                                           |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Advantages    | - Asynchronous yang mempercepat pengolahan data. - Simplicity, mengsimplify conversion dari `tokio::sync::mpsc::Receiver` menjadi streams |
| Disadvantages | Kontrol atas concurrency terbatas. Memiliki risiko data overflow. Di design untuk single consumer scenario                                |

## 5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

Beberapa strategi yang bisa diterapkan:

- Protobuf contracts

Mengorganisir `.proto` files dan menggunakan packages untuk memaintain clear boundaries.

- Crate modularization

Menggunakan separate crates untuk business logic dan gRPC definitions.

- Layered architecture

Mengimplementasi sebuah service layer untuk menjembatani gRPC handlers dan business logic.

## 6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

Dapat diubah menjadi server streaming daripada unary yang memungkinkan pengiriman data yang kompleks lebih cepat dan mengurangi overhead dalam pembuatan koneksi antara klien dan server.

## 7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

Dengan adopsi gRPC sebagai commmuncation protocol, kita tidak perlu khawatir tentang cara akses modul melalui HTTP method karena gRPC secara otomatis menghubungkan pemanggilan method yang diinginkan melalui file proto. Hal ini memungkinkan client seolah-olah langsung memanggil fungsi dari server, yang memudahkan konektivitas dan operasi antarteknologi, platform, dan sistem terdistribusi.

## 8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

Keuntungan dari HTTP/2 adalah kemampuannya untuk mengelola banyak request dan respons melalui satu koneksi tanpa harus memutusnya. Kekurangannya dibandingkan dengan HTTP/1.1 adalah HTTP/2 perlu menjaga satu koneksi untuk berbagai request dan respons. Hal ini membutuhkan biaya overhead yang lebih besar dalam performance dan memori. Ini membuat HTTP/2 lebih efisien untuk pengiriman data dalam jumlah yang besar, namun memiliki biaya yang lebih tinggi untuk pengiriman data kecil dibandingkan dengan HTTP/1.1.

## 9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

Perbedaan antara REST APIs dan gRPC terlihat dari cara kerja mereka. REST APIs membutuhkan waktu lebih lama dibandingkan gRPC untuk komunikasi real-time. Hal ini terjadi karena REST APIs memerlukan pembuatan koneksi baru untuk setiap request dari klien ke server, sementara gRPC hanya memerlukan satu koneksi untuk menyelesaikan semua request. Karena itulah gRPC lebih efektif untuk komunikasi real-time dan responsivitas yang lebih baik, menjadikannya lebih cepat dan "lebih real-time" dibandingkan dengan REST APIs.

## 10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

Dengan menggunakan protocol buffer, klien tidak perlu khawatir apakah JSON yang diterima sesuai dengan informasi yang dibutuhkan karena gRPC bersama protocol buffernya menciptakan antarmuka yang mengotomatisasi serialisasi dan deserialisasi pesan pada saat runtime. Ini memastikan bahwa setiap data yang dikirimkan dan diterima sudah pasti dapat digunakan dan sesuai tipe yang diharapkan. Berbeda dengan JSON pada REST APIs, yang lebih rentan terhadap pengiriman data tidak sesuai karena tidak adanya pembatasan ketat mengenai jenis data yang harus dikirimkan.
