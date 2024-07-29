# mgnotes

Marcelo's notes about everything technical.

## Devtools

### Git

[Initial setup](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup)

```
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

You can view all of your settings and where they are coming from using:

```
git config --list --show-origin
```

Change origin from ssh to https

```
git remote set-url origin https://gitlab.kaust.edu.sa/garcm0b/libnova_api_eval.git
```

## Sysadmin

### Tar

Removing a file from inside a `tar` file

```
a-garcm0b@lthlibprod:/tmp$ tar tvf cert_kaust.tar
drwxr-xr-x root/root         0 2024-04-21 14:18 certs/
-rw-r--r-- root/root      4240 2024-04-15 15:37 certs/ssl.pem_before_2024-04-21
-r-------- root/root      1675 2022-06-21 15:54 certs/ssl.key_before_2024-04-21
-r-------- root/root      1675 2023-05-31 15:21 certs/ssl.key
-rw-r--r-- root/root      4240 2024-04-15 15:41 certs/ssl.pem
a-garcm0b@lthlibprod:/tmp$
a-garcm0b@lthlibprod:/tmp$ sudo tar --delete --file cert_kaust.tar certs/ssl.pem_before_2024-04-21
a-garcm0b@lthlibprod:/tmp$ sudo tar --delete --file cert_kaust.tar certs/ssl.key_before_2024-04-21
a-garcm0b@lthlibprod:/tmp$ tar tvf cert_kaust.tar
drwxr-xr-x root/root         0 2024-04-21 14:18 certs/
-r-------- root/root      1675 2023-05-31 15:21 certs/ssl.key
-rw-r--r-- root/root      4240 2024-04-15 15:41 certs/ssl.pem
a-garcm0b@lthlibprod:/tmp$
```
