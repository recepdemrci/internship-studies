> path : Kontrolu atlatmak için getpath fonkisyonunun ret'ine system adresi denk getirilmiştir. (A*80)

> systemAddrs: Bu adres gdb ile öğrenilmiştir.

> exitAddrs: JUNK

> binshAddrs: libc'de tüm kodlar var.O yüzden önce (info proc main) yaparak libc kütüphanesinin hem adresine
hemde tam ismine bakılmıştır. Daha sonra (strings -t x /lib/libc-2.11.2.so | grep "/bin/sh") ile binsh'ın ofseti bulunmuş 
ve bunu ilk bulunan kütüphane adresi ile toplayarak asıl adres elde edilmiştir.



1- 80 tane A'dan sonra ret instructer'ına müdahale edilebilmiştir.

2- system fonksiyonunun adresi gdb ile bulunmuştur ve /bin/sh adreside gdb ile bulunmuştur.(libc kütüphanesinde bin/sh 
hazır durmaktadır)

	(gdb) x system
	(gdb) info proc main
	(gdb) strings -t x /lib/libc-2.11.2.so | grep "/bin/sh"		# bu bulunan offsettir. Bunu '/lib/libc-2.11.2.so' adresine
                                                             		# ekleyerek asıl adresi bulunmuştur.


3- program exploit ile çalıştırılmıştır. 	

	$(python /tmp/exploit.py; cat) | ./stack6             # bu şekilde çalıştırmamızın sebebi stack6 ilk çalıştırdığımız pythondan
                                                              # input alıyor. shelli açıyor ama input alma bitince shell
                                                              # kapanıyor. Bizde cat komutunu kullanıyoruzki yansıtma özelliğine
                                                              # sahip ekrana yazdığımızı yansıtsn ve stack6 programıda input olarak alsın.
