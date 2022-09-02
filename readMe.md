## Arsitektur
### Monolith
satu aplikasi besar, mengerjakan semua hal (palugada). semua fitur harus dijalankan semua.
    - Mudah di Develop
    - Mudah di Deploy
    - Mudah di Test
    - Mudah di Scale

    Masalah di arsitektur monolith => Employee
    - mengintimidasi Developer yang baru bergabung
    - scaling development dengan banyak developer agak menyulitkan
    - butuh kontrak panjang dengan teknologi yang digunakan (php, mysql)
    - scaling pada bagian tertentu tidak bisa dilakukan (e-commerce)
    - running app Monolith sangat berat (restart butuh waktu 15 menit)

### Microservices
aplikasi kecil yang saling bekerja sama, fokus mengerjakan satu pekerjaan dengan baik. fitur independert tanpa tergantung dengan aplikasi lain. setiap komponen pada sistem dibuat dalam service. dan komunikasi antar service melalui network-call (http api)
    - Mudah dimengerti, karena relative kecil ukuran servicenya
    - Lebih mudah di develop, di maintain, di test, dan dideploy.
    - Lebih mudah bergonta-ganti teknologi
    - Mudah di scale sesuai kebutuhan
    - Bisa dikerjakan dalam tim-tim kecil

    Masalah di arsitektur microservices
    - Distributed system
    - Komunikasi antar service yang rawan error
    - testing interaksi antar service lebih sulit

Split berdasarkan domain bisnis.

Seberapa kecil aplikasi microservices ?
- single responsibilty
- sekecil mungkin sehingga dimengerti oleh satu orang
- bisa dikerjakan sejumlah x developer

### Perbedaan
- Monolith
    - Simple
    - Konsisten
    - Mudah di Refactor
- Microservices
    - Parsial Development
    - Availability
    - Multiple Platform
    - Easy to Scale

### Database per Service (New Program)
- Decentralized Database

#### Kenapa harus database per service ?
- memastikan bahwa antar service tidak ketergantungan
- tiap service bisa menggunakan aplikasi sesuai dengan kebutuhan
- service tidak perlu tahu kompleksitas internal database service lain

### Shared Database (Transisi)
#### Kapan harus shared database ?
- ketika melakukan transisi dari aplikasi monolith ke microservices
- ketika bingung memecahkan data antar service
- ketika dikejar waktu, sehingga tidak ada waktu untuk bikin API

### NoSQL (Not Only SQL)
Jenis-jenis NoSQL
- Document Oriented Database => embeded json (kebanyakan join) => `MongoDB`
- Key-Value Database => cache => `Redis`
- Column Families Database => penulisan => `Apache Cassandra`
- Graph Database => social network => `Neo4j`
- Search Database => searching => `Elasticsearch`
- Time Series Database => pengecekan waktu => `InfluxDB`
- Dan lain-lain

#### Kenapa butuh tahu NoSQL ?
- agar bisa disesuaikan dengan kebutuhan
- bisa mencari alternatif cara mengolah data
- mempercepat dalam proses penulisan atau pencarian

#### Contoh Kasus
- Product Service => mongoDB
- Catalog Service => ElasticSearch
- Order Service => Postgre
- Member Service => Neo4J
- Activity Service => InfluxDB

### Cara Komunikasi Antar Service
- Remote Procedure Invocation (Call)
    - RESTful API (HTTP)
    - gRPC
    - Apache Thrift
    - SOAP
    - Java RMI
    - Corba 
    - dan lain-lain

    Keuntungan RPI (RPC)
    - Sederhana dan Mudah
    - Biasanya digunakan untuk komunikasi Request dan Response
    - Biasanya digunakan untuk proses sync (Menunggu jawaban)

- Messaging (Email & Sms) => tidak butuh response
    Masalah di komunikasi RPI
    - Proses lama (pada Email service dan SMS service) => diatas 2sec
    - Mengirim data sama yang berkali-kali
    - Membuat paralel process sangat rumit

Komunikasi digunakan untuk komunikasi Async (tanpa harus menunggu proses selesai). Menggunakan message chaneel sebagai jembatan untuk mengirim dan menerima data. Direkomendasikan menggunakan Message Broker untuk managementnya.
    
    Contoh Message Broker
    - Redis (PubSub)
    - Apache Kafka => Populer
    - RabbitMQ => Versi Lebih Ringan
    - NSQ
    - Google PubSub
    - Amazon Web Service SQS
    - dan lain-lain

### Tipe Microservices
- Stateless
    - Biasanya tidak memiliki database
    - digunakan untuk melakukan tugas sederhana
    - biasa digunakan sebagai utility untuk microservices lain
    - tidak bergantung dengan microservices lain
    - contoh: Email dan SMS service.

- Persistence (Statefull)
    - Biasanya memiliki database
    - bisa disebut Master Data microservices
    - Bisa digunakan mengolah data di database (CRUD)
    - contoh: Customer, Product, Order service.

- Aggregation
    - tergantung dengan microservice lain (tidak bisa berdiri sendiri)
    - biasa digunakan sebagai pusat bisnis logic
    - boleh memiliki database maupun tidak
    - contoh: cart, payment service.

#### Service Orchestration Pattern (RPI)
- microservice terpusat pada aggregation (transaction service) => bisnis logic
- call semua service yang dibutuhkan (persistence & stateless)

```
kekurangan
- ketergantungan dengan yang lain
- lebih lambat
- lebih mudah error
- jika perlu microservice baru, maka aggregation terganggu.
```

#### Service Choreography Pattern (Messaging)
- terpusat di message broker
- semua microservice dituntut menjadi pintar

```
kekurangan
- ketergantungan di message broker
- lebih sulit di-debug ketika terjadi masalah
- bisnis logic akan terdistribusi di semua microservice, sehingga sulit dimengerti
```

#### Masalah ekspos microservices
- semua service bisa diakses dari luar
- jika butuh authentikasi, harus diimplementasi disemua service
- rawan terjadi kebocoran data

### API Gateway (Seperti Proxy Server)
aplikasi bertugas sebagai gerbang dari luar (akses dari internet) ke dalam.

```
keuntungan
- lebih aman karena satu gerbang
- service tidak perlu implementasi proses authentikasi, cukup di API Gateway
- api gateway sebagai load balancer
- bisa digunakan sebagai rate limiter
- bisa digunakan sebagai pengaman sehingga error dari service tidak terekpos
```

```
contoh API Gateway
- Nginx
- Apache HTTPD
- Kong
- Netflix Zuul
- Spring CLoud Gateway
```

### Authentication & Authorization
```
Authentication
- Memvalidasi krendensial untuk verifikasi pemilik identitas

Autorization
- Proses setelah authentication
- Memvalidasi apakah pemilik identitias memiliki hak akses untuk resource yang diminta

Teknologi Pendukung
- Secure Cookie
- Oauth
- JSON Web Token
- Basic Auth
- API Key / SecretKey
```

### Backend For Frontend (API)
- pengembangan backend untuk tiap frontend bisa terisolasi satu sama lain
- logic untuk frontend tidak tercampur di satu backend

```
- alternative API => GraphQL -> query language untuk API -> memanipulasi response API secara runtime
- frontend bebas menentukan data apa saja yang ingin didapatkan
- backend hanya perlu menyediakan data lengkap.

kekurangan GraphQL
- butuh development server di BE
- butuh development client di FE
```

### Command Query Responsibility Segregation (CQRS) => Messaging
Proses membedakan operasi command dan query
- command => operasi mengubah data (create, update, delete)
- query => operasi mengambil data (get, search)

```
keuntungan CQRS
- bisa memilih database berbeda yang optimal untuk proses command dan query, sehingga lebih cepat.
- membedakan model untuk command dan query di aplikasi akan lebih mudah.
- performa aplikasi akan lebih baik.

keuntungan memakai messaging
- aplikasi command dan query terpisah, sehingga bisa dikerjakan oleh tim yang berbeda secara paralel.
- aplikasi command tidak perlu pusing memikirkan struktur data aplikasi query, hanya mengirim datanya ke message broker
- scalling aplikasi sesuai kebutuhan
- jika aplikasi query stop atau error, data dari aplikasi command tetap aman tersimpan di message broker.
- mekanisme retry akan lebih mudah dilakukan jika melalui message broker
```

### Server Side Discovery
- membuat server khusus sebagai router atau load balancer ke service
- client hanya butuh terkoneksi ke router atau load balancer
- jika jumlah node service bertambah atau berkurang, router yang hanya perlu dirubah, client tidak perlu.

```
kekurangan
- load balancer di setiap service
- agar tidak terjadi single point of failure, maka router atau load balancer harus disetup 2x instance
- cost biaya akan lebih mahal, karena 1 service menjalankan 2 router.
```

### Client Side Discovery
- solusi agar client harus bisa tahu lokasi semua service yang akan dituju.
- tidak perlu lagi router atau load balancer untuk komunikasi dengan service lain
- semua logic untuk mendistribusikan traffic harus di client atau service yang akan melakukan request

```
kekurangan
- client harus tahu lokasi semua service
- jika jumlah node service bertambah atau berkurang, client harus diubah untuk lokasi barunya
- jika client salah mengimplementasikan logic untuk load balancer, maka traffic ke service yang dituju bisa tidak merata.
```

### Service Registry => fitur Health Check
- aplikasi yang digunakan sebagai tempat untuk menyimpan semua informasi yang berhubungan dengan lokasi service
- semua service akan meregistrasikan alamat lokasi nya di service registry ketika pertama kali nyala
- semua service akan laporan ke service registry jika akan berhenti beroperasi, sehingga registry akan menghilangkan informasi service tersebut agar tidak mendapat traffic dari service yang bertanya.

```
contoh aplikasi
- Hashicorp Consul
- Netflix Eureka
```

### Centralized Configuration
- konfigurasi adalah sesuatu yang tidak asing lagi saat membuat aplikasi
- pertanyaannya? dimana sebaiknya menyimpan konfigurasi, agar mudah di maintain dan digunakan oleh aplikasi kita.

``` 
contoh konfigurasi
- Database
- File
- Environment Variable
```

centralized configuration adalah pattern dimana kita menyimpan semua konfigurasi disebuah aplikasi atau service.
```
contoh aplikasi
- Hashicorp Consul
- Hashicorp Vault (Encrypt)
- Etcd
- Zookeeper
- Doozerd
```

