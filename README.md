# KENSHI-NODE-AUTO-RESTART


Herkese merhabalar! Bugünkü yazımda normalde Nano ve Vi'yi anlatacaktım ama kurduğum Node'lardan birisi yeni aldığı güncelleme sonrası sık sık kapandığı için buna bir çözüm yolu bulmak ve bunu sizlerle paylaşmak istedim. Sizin de aynı sorunu yaşayabileceğinizi düşünerek bu yazıyı oluşturdum. Ben bunu "Kenshi" Node'um için yaptım ama benzer şekilde çalışan -docker dışında- başka nodelar için de bu yöntem uygun olabilir.

En: Hello everyone! In today's post, I was going to talk about Nano and Vi, but since one of the nodes I installed was frequently shutting down after a recent update, I wanted to find a solution and share it with you. I created this post thinking that you may have the same problem. I did this for my "Kenshi" node, but this method may be suitable for other nodes - other than docker - that work in a similar way. 

Önce scriptin tam hali ile başlamak istiyorum, sonra teker teker neler yaptığımızı açıklayacağım;
İlk olarak bir "restart_kenshi.sh" dosyası oluşturalım.

nano restart_kenshi.sh
Ardından Ctrl + O (kaydet) ile kaydedelim ve Ctrl + X (kapat) ile çıkalım
Scriptimizin içi boş, ama doldursak bile bu halde bu dosyayı çalıştıramayacağız. Öncelikle dosyanın izinlerini düzenlememiz gerekiyor ki dosya çalıştırılabilir olsun.


sudo chmod +x restart_kenshi.sh
Bu komut ile bu script dosyasına kendi kullanıcımız için çalıştırma izni verdik.

Şimdi scriptimizi "nano" ile tekrar yukarıdaki gibi açıp, içerisine aşağıdaki kodları yapıştıracağız. Ardından ctrl +x, y, enter tuşlarına bu sırayla basarak script dosyasını kaydedip dosyadan çıkış yapalım.

#!/bin/bash

restart_count=0
restart_log="restart_log.txt"

while true; do
    # Start the application
    unchained start conf.yaml

    # Increment the restart count and write to the log file
    ((restart_count++))
    echo "Restart $restart_count at $(date)" >> $restart_log

    # Uygulama çökerse veya durursa, döngü yeniden başlar
    echo "Uygulama durdu. 5 saniye sonra yeniden başlatılacak..."
    sleep 5
done


Şimdi bu kodları adım adım inceleyelim;


Script Açıklaması:
1. Shebang ve Başlangıç
 Shebang (#!/bin/bash gibi), Unix tabanlı bir scriptin en üstünde bulunan özel bir satırdır ve scriptin nasıl çalıştırılacağına dair önemli bilgiler içerir. "Shebang", #! sembollerinin birleşiminden türetilmiş bir terimdir. Bu semboller, scriptin başında bulunan ilk iki karakterdir.
Shebang satırı, scripti çalıştırırken kullanılacak yorumlayıcıyı (interpreter) belirtir. Bu, işletim sistemine scriptin hangi program tarafından çalıştırılması gerektiğini söyler.


/bin/bash ve Bash Yorumlayıcısı
/bin/bash: Bu, Bash yorumlayıcısının yoludur. Bash, Unix ve Linux sistemlerde yaygın olarak kullanılan güçlü bir kabuk (shell) ve komut dili yorumlayıcısıdır.
Bash Yorumlayıcısı: Bash, kullanıcıların komutları yazıp programları çalıştırmalarını sağlayan bir ortam sunar. Ayrıca, karmaşık scriptler yazmayı ve otomatik işlemler gerçekleştirmeyi mümkün kılar.
Örnek Shebang Satırları
Bash için: #!/bin/bash
Python için: #!/usr/bin/python
Perl için: #!/usr/bin/per



2. Değişkenlerin Tanımlanması
restart_count=0
restart_log="restart_log.txt"
restart_count: Bu değişken, restart sayısını takip eder. Başlangıçta 0 olarak ayarlanmıştır.
restart_log: Bu değişken, log kayıtlarının yazılacağı dosyanın adını tutar. Bu örnekte restart_log.txt olarak adlandırılmıştır.


3. Sonsuz Döngü
while true; do
    # buraya döngü içinde çalışacak komutlar yazılır
done



Bash scripting'te, while döngüsü iki anahtar kelime ile tanımlanır: do ve done. do kelimesi, while koşulunun başladığını ve döngü içinde çalıştırılacak komutların başlangıcını belirtir. done ise döngünün sonunu işaret eder.
while true: Bu, koşulun her zaman doğru olduğu anlamına gelir ve bu nedenle döngü sürekli çalışır.
do: Bu kelime, while koşulunun başarılı olduğu durumda çalıştırılacak komut bloğunun başlangıcını belirtir.
4. Uygulamanın Başlatılması
    unchained start conf.yaml
Bu komut, Kenshi için Node başlatmak amacıyla kullanılır. Eğer farklı bir Node kurduysanız ve Node'unuz kendisini kapatıp loglardan çıkıyorsa, buraya o Node'u başlatmak için gerekli komutu yazabilirsiniz. Böylece kurduğunuz Node hata verip çıksa bile sonsuz döngü içerisinde tekrar tekrar yeniden başlatılacaktır.


5. Restart Sayacının Güncellenmesi ve Log'a Yazdırılması
    ((restart_count++))
    echo "Restart $restart_count at $(date)" >> $restart_log
((restart_count++)): Her restart'ta restart_count değişkenini bir arttırır.
echo: Restart sayısını ve o anki tarihi restart_log.txt dosyasına ekler. >> operatörü, dosyanın sonuna yeni bir satır eklemek için kullanılır. Eğer dosya yoksa, otomatik olarak oluşturulur.
6. Bekleme ve Döngünün Devamı
    echo "Uygulama durdu. 5 saniye sonra yeniden başlatılacak..."
    sleep 5
Buradaki echo komutu, uygulamanın durduğunu ve 5 saniye sonra yeniden başlatılacağını bildirir.
sleep 5: Scriptin 5 saniye beklemesini sağlar. Bu, sürekli restartlardan kaçınmak ve sunucuya aşırı yük bindirmemek için kullanılır.

Bunu Kenshi Node'unuzda uygulamak için;

Önce Kenshi'nin çalıştığı ekrana gelin --> "screen -r kenshi" (veya "kenshi" yerine nasıl isim verdiyseniz o şekilde)
Ardından ctrl +c ile çalışan uygulamayı durdurun.
Aşağıdaki komut ile scriptinizi başlatın.
./restart_kenshi.sh 
İşleminiz tamam, artık kenshi node'unuz durduğunda otomatik olarak başlatılacaktır. Her durduğunda restart_log.txt dosyasına bir log basacaktır. Kaç kez restart yapıldığını gçrmek için aşağıdaki komutu kullanabilirsiniz.
cat restart_log.txt



derleme için @lilium hocama teşekkürler
