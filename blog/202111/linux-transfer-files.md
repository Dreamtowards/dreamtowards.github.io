

## Linux Transfer Files.
_211114_

There enumerated several approaches to transfer files between Linux Server and your Local Computer.
- **lrzsz**  
  - \+ convenient. 
  - \- only supported by several special terminal. (e.g. Xshell. but not supported by OSX system terminal or putty)
- **wget** (receive) + **<http_server>** (send)
  - \- not convenient or safety enough.
- **sftp**
  - \+ safety, essential, reliable.

lrzsz use gui to select file and only supported by several special terminals. it's not in our consideration.

This time we mainly talk about SFTP approach, which is Essential, Reliable and Safety.

## SFTP.

SFTP, is 'Secure/SSH File Transfer Protocol', compared to FTP, SFTP having a underlying secure connection and 'powered' with SSH.

since you can already connect to SSH and have an FTP server, then you should be able to connect SFTP by:

```
sftp -oPort=port username@ip_or_hostname
```

_then, you should be able to use this:_
```
sftp> help
```

### Navigating

```
# for Remote:
sftp> pwd
sftp> ls
sftp> cd

# for Local:
sftp> lpwd
sftp> lls
sftp> lcd
```

### Transferring files

```
sftp> get <remote_file> [local_file]
sftp> get -r <remote_directory>  # recursive get a directory with all stuff inside it.
```

```
sftp> put <local_file> [remote_file] 
sftp> put -r <local_file>
```

## referencing

https://www.digitalocean.com/community/tutorials/how-to-use-sftp-to-securely-transfer-files-with-a-remote-server