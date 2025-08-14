- Tags: #web #Vunlerability #FTP #Capabilites #Analisist 
----

![[Cap_card.png]]

# Enumeración

> Empezamos con el reconocimiento básico de la máquina con un escaneo de puertos a ver que encontramos.
> ![[Cap_allports.png]]
> En este caso vemos que tenemos 3 puertos el 21 que corresponde al [[FTP]] uno 22 el servicio [[Ssh]] y un 80 que corresponde a un servicio [[HTTP]].
> Con esto vamos a realizar un escaneo más exhaustivo para ver las versiones que corren.
>   ![[Cap_Targeted.png]]
>   Con esto vemos que versión corre para el FTP para el SSH y que tipo de servidor corre el servicio HTTP en este caso tenemos gunicorn.  
>   
>   Como no tenemos nada de usuarios para probar ni el FTP ni el SSH vamos a tirar de el servicio web a ver que sacamos.

## Servicio Web

> De primeras tiramos un `whatweb` para ver que se esta corriendo de primeras.
> ![[whatweb_Cap.png]]
> Pues podemos ver que estamos corriendo un gunicorn como servidor que tenemos un panel con varias capturas de red la cual podemos descargar y examinar.
> La pagina principal se ve tal que así:
> ![[Cap_main_web_server.png]]
> Tenemos un posible usuario para probar el FTP y el SSH `Nathan` lo dejamos para más adelante que esto tiene chicha.

### IDOR
> Viendo como se tratan las URL parece que al cambiar el numero tenemos un posible IDOR el cual podemos llevar a tener capturas que no han sido de este usuario a si que vamos a realizar un ataque tipo intruder con Burpsuite a ver si sacamos algo en conclusión si no, con WFUZZ vamos a realizar un ataque de fuera bruta más rápido. 
> 
> Al final los únicos Id válidos son del 0 al 10 los demás nos hacen un  redirect y con el intruder vemos que el Id 0 es interesante 
> Tenemos una captura de una tráfico vía FTP con un inicio de sesión y una contraseña.
> ![[Cap_Captura.png]]
> `nathan:Buck3tH4TF0RM3!` Con esto podemos probar a ver que hay en el FTP y a ver si vía SSH podemos entrar.
>  Vía FTP nos conectamos al `/home` del usuario `nathan` 
>  Y por ssh también funciona.

## Escalada de privilegios

> Vemos que `nathan` no tiene privilegios mas allá de los suyos personales, no hay más usuarios a parte de root y el.
> ![[Cap_etc_passwd.png]]

### Capabilities `python` 
> Este mismo usuario no tiene ningún archivo interesante ni hay binarios con permisos SUID que podamos usar como escalada de privilegios, pero mirando las capabilities vemos esto tan interesante.
> ![[Cap_capabilities.png]]
> Vemos que un binario de Python versión 3.8 tiene la capability de `cap_setuid` la cual por [GTFOBin](https://gtfobins.github.io/gtfobins/python/)  podemos escalar privilegios como usuario root 
> ![[Cap_privesc.png]]