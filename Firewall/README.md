## Firewall  
Firewall adalah sistem keamanan jaringan yang berfungsi untuk melakukan filtering terhadap traffic yang akan masuk ke jaringan.
Pada kesempatan ini saya akan mendokumentasikan praktik mengenai firewall pada ubuntu server menggunakan **iptables**.

#### Topologi 

![alt text](https://github.com/LuthfanHilmi/linuxServer-main/blob/main/linuxServer/Firewall/images/topologi.png)

> Di sini ubuntu server saya fungsikan sebagai router yang melakukan IP forwarding dan NAT, sehingga komputer client dapat mengakses internet.


1. Setup pertama install iptables dan dengan perintah  
```bash
sudo apt install iptables -y
```  
Sekalian juga install iptables-persistent yang fungsinya untuk menyimpan rules yang telah dibuat.

```bash 
apt install iptables-persistent
 ```


 2.  Setelah terinstall maka aktifkan ip forwarding pada file sysctl.conf yang terletak di /etc/sysctl.conf. Hapus komen (#) untuk mengaktifkannya.
 Gambar

 3. Setelah ip forwarding diaktifkan maka saya akan membuat NAT agar komputer client mendapat akses internet, dengna perintah :  
 ```bash
 iptables -t nat -A POSTROUTING -s 192.168.10.2/24 -o enp0s3 -j MASQUERADE
 ```
 > Penjelasan 
 -t : table == nat.
 -A : append == menambahkan POSTROUTING.
 -s : source == menambahkan ip address milik client karna yang akan mendapatkan akses internet adalah client.
 -o : output == traffic tersebut akan keluar ke internet melalui jalur enp0s3.
 -j : jump == MASQUERADE.

 kita bisa save rule tersebut denga perintah :
 ```bash
 iptables-save > /etc/iptables/rules.4
 ```

 4. Cek apakah client sudah mendapatkan internet  
 Gambar

 5. Setelah client terhubung dengan internet maka saya akan memberikan rules pada firewall.


6. Client pada ip 192.168.10.2 tidak bisa mengakses atau melakukan ping ke router :  

```bash
iptables -A INPUT -s 192.168.10.2 -j DROP
```  
![](https://github.com/LuthfanHilmi/linuxServer-main/blob/main/linuxServer/Firewall/images/rule_mencegah_client_mengakses_ip_server.png)

>client mencoba mengakses ip router tapi gagal.

7. Memberikan rule agar client yang memiliki MAC address `00:22:08:9A:15` tidak dapat koneksi internet.

```bash
iptables -A FORWARD -m mac --mac-source 00:22:08:9A:15 -o enp0s3 -j DROP
```

8. Memberikan rule untuk ip 192.168.10.2 agar tidak dapat mengakses ip youtube.

```bash
iptables -A FORWARD -s 192.168.10.2 -d 74.125.68.190 -o enp0s3 -J DROP
```
![]()

>> Untuk melihat rules yang telah dibuat bisa menggunakan perintah iptables -L -v -n.
>> Untuk menghapus seluruh konfigurasi bisa menggunakan perintah iptables -F.
>> Untuk menghpus rule tertentu misal iptables -A INPUT -s 192.168.10.2 -j DROP, tinggal ganti -A menjadi -D.

### Selesai
