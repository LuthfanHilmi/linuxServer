## Konfigurasi DNS (Domain Name System) menggunakan Bind9

### Pengertian DNS Server
DNS Server (Domain Name System Server) adalah sebuah server yang bertugas untuk menerjemahkan nama domain (seperti www.google.com) menjadi alamat IP (seperti 142.250.190.78) yang dapat dikenali oleh komputer. Fungsi utama DNS adalah mempermudah pengguna dalam mengakses layanan di internet tanpa harus menghafal deretan angka IP.


File-file yang akan dikonfigurasi
- /etc/netplan/00-installer-config.yaml
- /etc/resolv.conf
- /etc/bind/named.conf.local
- /etc/bind/db.local


1. Install bind9 di linux server
   sebelum install bind9 disarankan update dan upgrade linux server
   ```bash
   sudo apt update && upgrade -y
   ```
   ```bash
   sudo apt install bind9
   ```

3. Sebelum konfigurasi DNS kita setting ip agar static di file **/etc/netplan/00-installer-config.yaml**
   Inline-style: ![alt text](https://github.com/lutvan/linuxServer/blob/main/linuxServer/Membuat%20DNS/image/settingIPstatic.png "konfigurasi ip static")  
   ip tersebut yang nantinya akan kita ubah menjadi domain.
   


5. Setelah bind9 terinstall di linux server, kita bisa masuk ke direktori **/etc/bind**
   ```bash
   cd /etc/bind/
   ```

6. Di dalam direktori bind/ kita akan copy isi file dari db.local ke db.praktik dan db.192.
   ```bash
   cp db.local db.praktik
   ```
   ```bash
   cp db.local db.192
   ```
   file db.praktik dan db.192 akan otomatis terbuat ketika kita mengcopykan isi file nya.

7. lakukan konfigurasi file **named.conf.local**  
   sebelum konfigurasi sudo su dulu agar masuk ke root
   ```bash
   nano named.conf.local
   ```  
   isi file seperti dibawah ini
   ```
   zone "test.com" {
   type master;
   file "/etc/bind/db.praktik";
   };
   
   zone "1.168.192.in-addr.arpa" {
   type master;
   file "/etc/bind/db.192";
   };
   ```
   ##### Penjelasan
   
   >> zone "test.com" : nama domain yang akan digunakan.
   >> type master : Artinya server ini adalah master DNS (utama) untuk zona ini
   >> file : letak direktori file konfigurasi zone.
   
   >> zone "1.168.192.in-addr.arpa" : Menunjukkan zona reverse untuk jaringan 192.168.1.13/24.
   Ini digunakan agar ketika seseorang melakukan query reverse (misalnya nslookup 192.168.1.13), DNS dapat menjawab dengan nama domain seperti server.test.com.
   >> type master : Artinya server ini adalah master DNS (utama) untuk zona ini
   >> file : letak direktori file konfigurasi zone.


8. Selanjutnya konfigurasi file **db.praktik**
   
   ![alt text](https://github.com/LuthfanHilmi/linuxServer/blob/main/Membuat%20DNS/image/konfigurasiFiledbpraktik.png)
   #### Penjelasan
   >> www, ftp, dan mail adalah subdomain.

10. Berikutnya di file **db.192**
    
    ![alt text](https://github.com/LuthfanHilmi/linuxServer/blob/main/Membuat%20DNS/image/konfigurasiFiledb192.png)


11. Setelah file db.praktik dan db.192 dikonfigurasi, langkah selanjutnya  masuk ke file **/etc/resolv.conf**
    ```bash
    nano /etc/resolv.conf
    ```
    ![alt text](https://github.com/LuthfanHilmi/linuxServer/blob/main/Membuat%20DNS/image/fileresolv.png)

12. langkah terakhir restart bind9 dengan
    ```bash
    systemctl restart bind9
    ```
13. Setelah semua terkonfigurasi kita bisa test domain tadi dengan ping dan nslookup
    lakukan tes :
    - test.com
    - www.test.com
    - ftp.test.com
    - mail.test.com
      
    Hasil ping :  
    ![alt text](https://github.com/LuthfanHilmi/linuxServer/blob/main/Membuat%20DNS/image/hasilping.png)

    Hasil nslookup :  
    ![alt text](https://github.com/LuthfanHilmi/linuxServer/blob/main/Membuat%20DNS/image/hasilnslookuppng.png)
   



