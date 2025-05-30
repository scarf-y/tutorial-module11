# tutorial-module11

## Reflection on Hello Minikube
1. Sebelum deployment dibuat jadi service, pada lognya terdapat pesan status dijalankanya HTTP server dan UDP server, dengan tempat portnya. Setelah deployment diexpose, menjadi sebuah service, client bisa akses lewat url yang ditetapkan dari kubenya. Ketika kita akses url tersebut, pada logs akan bertambah sebuah http method GET/. Tiap kali saya akses page itu di browser, logsnya bertambah, dengan isinya sama yaitu http method GET/ juga.
2. Perintah `kubectl get pods,services -n kube-system` digunakan untuk menampilkan Pod dan Service yang berada di namespace `kube-system`, termasuk komponen seperti `metrics-server`. Opsi `-n` menunjukkan namespace yang ingin diakses, karena tanpa opsi ini, `kubectl` hanya menampilkan resource di namespace default. Itulah mengapa saat menjalankan `kubectl get` tanpa `-n`, Pod atau Service seperti `metrics-server` tidak muncul, karena mereka tidak berada di namespace default. Kubernetes menggunakan namespace untuk memisahkan resource agar lebih terorganisir, contohnya `default` untuk aplikasi umum, dan `kube-system` untuk komponen internal Kubernetes. Jadi, untuk memverifikasi apakah `metrics-server` sudah berjalan, kita harus mencarinya di namespace yang tepat menggunakan opsi `-n kube-system`.

## Reflection on Rolling Update & Kubernetes Manifest File
1. Perbedaannya ada dicara pergantian versinya. Pada Rolling Update, versi pod lama tidak langsung di-'takedown' atau nonaktifkan sekaligus, namun diganti dengan pod versi baru secara bertahap. Sebagian pod lama masih aktif melayani client, sementara pod baru sedang mulai dijalankan. Ini memungkinkan service melakukan 'Zero Downtime', karena layanan masih tersedia selama proses update berlangsung. Sedangkan Recreate, semua pod lama dihapus/dihentikan kemudian digantikan dengan yang baru. Selama proses pergantian itu, tidak ada layanan yang tersedia, sehingga lamanya zero downtime bergantung pada seberapa cepat versi baru dapat dijalankan serentak.
2. Karena secara default tidak bisa atur strategi update ketika deployment pertama kali, jadi dari deployment pertama kita ubah konfigurasinya
![Screenshot 2025-05-30 200836](https://github.com/user-attachments/assets/a961afc8-56a7-466d-b334-9586bf0f2c1a)
mengedit langsung deployment strategynya, Pada windows terbuka notepad berisi konfigurasi deployment yang sebelumnya rollingupdate

![Screenshot 2025-05-30 200809](https://github.com/user-attachments/assets/7f9e68d6-b903-4ed8-92d6-673b9df024d3)

Edit di bagian ini

![Screenshot 2025-05-30 201243](https://github.com/user-attachments/assets/808f57cd-b847-4b52-8903-05af8a7ce0f8)

Lalu, update pods nya, dan verifikasi apakah sudah berhasil

3.  Karena sebelumnya sudah kita atur deployment strategy nya menjadi recreate, kita bisa simpan konfigurasi yamlnya dengan menjalankan ini

``kubectl get deployments/spring-petclinic-rest -o yaml > deployment_recreate.yaml``

4. Benefit paling jelas dari menggunakan manifest files ini adalah kemudahan dan efisiensi ketika ingin melakukan deployment pada device atau tempat yang berbeda. Selain itu, manifest file mempermudah dokumentasi dan versioning karena file YAML dapat disimpan di repository Git. Dengan manifest file, proses deployment menjadi lebih konsisten dan repeatable karena semua konfigurasi sudah tertulis eksplisit, sehingga mengurangi risiko human error dibandingkan jika dilakukan manual dengan perintah `kubectl` satu per satu. Saya merasakan bahwa saat deploy manual, perlu banyak mengetik dan mengingat argumen command-line, sedangkan dengan manifest file cukup satu perintah `kubectl apply -f`, semua resource langsung dibuat atau diperbarui. Manifest juga memungkinkan kolaborasi tim yang lebih baik karena semua orang bisa melihat dan review konfigurasi yang sama.

