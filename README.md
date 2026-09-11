# 🚀 Ready Guacamole Server

Docker kullanmadan **Apache Guacamole** kurulumu için hazırlanmış rehber.

Bu rehber, Windows üzerinde **WSL2 + Ubuntu** kullanarak Apache Guacamole'u Docker olmadan kurmayı anlatır.

---

## 🖥️ Sistem Yapısı

```text
Windows
   │
   └── WSL2
        │
        └── Ubuntu
             │
             ├── guacd
             │
             ├── Apache Guacamole
             │
             └── Apache Tomcat
📋 Gereksinimler

Kurulum için:

Windows 10/11
WSL2
Ubuntu
İnternet bağlantısı
Yönetici (sudo) yetkisi

Docker gerekli değildir. ❌🐳

1️⃣ WSL Ubuntu'yu Açma

Windows CMD'yi açın:

wsl

Ubuntu terminaline girdikten sonra:

sudo apt update

Sistemi güncelleyin:

sudo apt upgrade -y
2️⃣ Gerekli Paketleri Kurma

Aşağıdaki paketleri yükleyin:

sudo apt install -y \
gcc g++ make autoconf automake libtool pkg-config \
libcairo2-dev libjpeg-turbo8-dev libpng-dev \
libossp-uuid-dev uuid-dev \
libavcodec-dev libavformat-dev libavutil-dev \
libswscale-dev \
libpango1.0-dev libssh2-1-dev \
libtelnet-dev libvncserver-dev \
libpulse-dev libwebp-dev libssl-dev \
freerdp2-dev
3️⃣ Apache Guacamole Server'ı İndirme

Guacamole sürümünü indirin:

cd ~
wget https://downloads.apache.org/guacamole/1.6.0/source/guacamole-server-1.6.0.tar.gz

Arşivi çıkartın:

tar -xzf guacamole-server-1.6.0.tar.gz

Klasöre girin:

cd guacamole-server-1.6.0
4️⃣ Guacamole Server'ı Derleme

Yapılandırmayı başlatın:

./configure --with-systemd-dir=/usr/local/lib/systemd/system

Derleme işlemini başlatın:

make -j$(nproc)

Kurulumu tamamlayın:

sudo make install

Ardından:

sudo ldconfig
5️⃣ guacd Servisini Başlatma

Systemd'yi yeniden yükleyin:

sudo systemctl daemon-reload

Guacamole daemon'u otomatik başlatılacak şekilde ayarlayın:

sudo systemctl enable --now guacd

Durumu kontrol edin:

systemctl status guacd

Şuna benzer bir sonuç görmelisiniz:

Active: active (running)

🎉 guacd çalışıyor!

6️⃣ Java ve Tomcat Kurulumu

Java ve Tomcat'i yükleyin:

sudo apt install -y openjdk-17-jdk tomcat10

Java sürümünü kontrol edin:

java -version

Tomcat servisinin durumunu kontrol edin:

systemctl status tomcat10
7️⃣ Guacamole Web Uygulamasını Kurma

Guacamole web uygulamasını indirin:

cd ~
wget https://downloads.apache.org/guacamole/1.6.0/binary/guacamole-1.6.0.war

WAR dosyasını Tomcat'e kopyalayın:

sudo cp guacamole-1.6.0.war /var/lib/tomcat10/webapps/guacamole.war

Tomcat'i yeniden başlatın:

sudo systemctl restart tomcat10
8️⃣ Guacamole Yapılandırma Klasörü

Guacamole yapılandırma klasörünü oluşturun:

sudo mkdir -p /etc/guacamole

Yapılandırma dosyasını oluşturun:

sudo nano /etc/guacamole/guacamole.properties

Örnek yapılandırma:

guacd-hostname: localhost
guacd-port: 4822

Kaydetmek için:

CTRL + O
ENTER
CTRL + X
9️⃣ Guacamole Yapılandırma Değişkenini Ayarlama
echo 'GUACAMOLE_HOME=/etc/guacamole' | sudo tee /etc/default/tomcat10

Tomcat'i yeniden başlatın:

sudo systemctl restart tomcat10
🔟 guacd Kontrolü

Guacamole daemon portunu kontrol edin:

sudo ss -lntp | grep 4822

4822 portunun dinlendiğini görmelisiniz.

🌐 Guacamole'ya Bağlanma

Tomcat çalışıyorsa tarayıcıdan:

http://localhost:8080/guacamole/

adresini açabilirsiniz.

WSL'nin IP adresini öğrenmek için:

hostname -I

Ardından Windows tarayıcısından:

http://WSL-IP:8080/guacamole/

şeklinde erişebilirsiniz.

🔐 Güvenlik

Üretim ortamında kullanırken:

HTTPS kullanın.
Güçlü parolalar kullanın.
Gereksiz portları internete açmayın.
Yönetici hesabını koruyun.
Guacamole'yi güncel tutun.
Gizli bilgileri GitHub'a yüklemeyin.

❌ .env

❌ API anahtarları

❌ Şifreler

❌ Özel erişim tokenları

❌ Özel sertifikalar

🐳 Docker Kullanılmıyor

Bu kurulum yöntemi Docker gerektirmez.

Docker      ❌
Guacamole   ✅
guacd       ✅
Tomcat      ✅
Java        ✅
WSL2        ✅
Ubuntu      ✅
🛠️ Sorun Giderme
guacd çalışmıyor

Durumu kontrol edin:

systemctl status guacd

Logları görüntüleyin:

journalctl -u guacd -n 100 --no-pager
Tomcat çalışmıyor
systemctl status tomcat10

Logları görüntülemek için:

journalctl -u tomcat10 -n 100 --no-pager
8080 portunu kontrol etme
sudo ss -lntp | grep 8080
4822 portunu kontrol etme
sudo ss -lntp | grep 4822
🔄 Servisleri Yeniden Başlatma

guacd:

sudo systemctl restart guacd

Tomcat:

sudo systemctl restart tomcat10
📁 Temel Dosya Konumları
/etc/guacamole/
    └── guacamole.properties

/var/lib/tomcat10/webapps/
    └── guacamole.war

/usr/local/
    └── guacamole-server
💻 Hızlı Komutlar
guacd başlat
sudo systemctl start guacd
guacd durdur
sudo systemctl stop guacd
guacd yeniden başlat
sudo systemctl restart guacd
guacd durumunu kontrol et
systemctl status guacd
Tomcat başlat
sudo systemctl start tomcat10
Tomcat durdur
sudo systemctl stop tomcat10
Tomcat yeniden başlat
sudo systemctl restart tomcat10
⭐ Ready Guacamole Server

Docker kullanmadan Apache Guacamole kurmak için hazırlanmış kurulum rehberi.

🌐 Web tabanlı uzak masaüstü

🖥️ Apache Guacamole

⚙️ guacd

☕ Java + Tomcat

🐧 WSL2 + Ubuntu

🚀 Docker'sız kurulum

📜 Lisans

Apache Guacamole, Apache Software Foundation tarafından geliştirilen açık kaynaklı bir projedir.

Bu repository Apache Guacamole projesinin resmi repository'si değildir.

Apache Guacamole hakkında daha fazla bilgi için resmi Apache Guacamole dokümantasyonuna bakabilirsiniz.



⚠️ **Küçük ama önemli not:** README'deki bazı paket/sürüm komutları Ubuntu sürümüne göre değişebilir. Özellikle `freerdp2-dev` ve `tomcat10` paket adlarının kullandığın Ubuntu sürümünde mevcut olup olmadığını kurulum sırasında kontrol etmek iyi olur.
