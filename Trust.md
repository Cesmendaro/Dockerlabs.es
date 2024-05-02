Lanzamos la maquina

`sudo bash auto_deploy.sh trust.tar`
![image](https://github.com/Cesmendaro/Dockerlabs.es/assets/153618246/c16ba732-c73f-45e3-99f6-20418d084296)

Lanzamos escaneo con nmap.
`nmap -p- --open -sS -sC -sV --min-rate 2000 -n -vvv -Pn 172.17.0.2
![[Pasted image 20240429215958.png]]

El escaneo nos dice que tenemos dos puertos abiertos, el 22 que corresponde al protocolo SSH y el puerto 80 que tiene corriendo un apache versión 2.4.57.

Revisamos la aplicación web a ver con lo que nos encontramos.
![[Pasted image 20240429220145.png]]

Se traba de la plantilla por defecto del apache, revisamos también su código fuente y no hay nada extraño.

Fuzzing.
`sudo gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -u "http://172.17.0.2/" -x .php,.sh,.py,.txt`
![[Pasted image 20240429220912.png]]

Nos encuentra el directorio `/secret.php` y miramos a ver que hay.
![[Pasted image 20240429221006.png]]

Se trata de un simple mensaje, pero ahora tenemos un nombre de usuario "Mario", procedemos a hacer un ataque de fuerza bruta con hydra al protocolo SSH.
`sudo hydra -l mario -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2`

![[Pasted image 20240429221237.png]]

Ahora que tenemos usuario y contrase;a procedemos a conectarnos por ssh.
![[Pasted image 20240429221406.png]]

Una vez accedemos estamos dentro de la maquina, solo falta escalar privilegios con el comando.
`sudo -l`
![[Pasted image 20240429221459.png]]

Nos dice que podemos ejecutar el binario "vim" como sudo, revisamos en gtfobins algun comando para escalar provilegios.
![[Pasted image 20240429221612.png]]

Solo falta pegar el comando que sacamos de gtfobins únicamente cambiando la ruta que aparece por la ruta absoluta.

![[Pasted image 20240429221728.png]]

Hecho esto ya somos usuario root, por ultimo hacemos el tratamiento de la tty.
`script /dev/null -c bash`
![[Pasted image 20240429222105.png]]
