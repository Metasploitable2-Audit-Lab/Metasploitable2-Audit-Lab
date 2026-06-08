Auditoría Técnica de Seguridad: Entorno Metasploitable 2

📝 Resumen Ejecutivo

Este repositorio contiene la documentación técnica de una auditoría de seguridad y análisis de vulnerabilidades realizada sobre un servidor Linux virtualizado (Metasploitable 2). El objetivo de la práctica fue identificar servicios expuestos, evaluar sus versiones, explotar fallas de configuración y analizar la robustez de las políticas de contraseñas del sistema en un entorno controlado de laboratorio.

🛠️ Fases del Proyecto

1. Configuración del Entorno de Laboratorio

Máquina Atacante: Ubuntu Linux.

Máquina Víctima: Metasploitable 2.

Red: Configuración de direccionamiento IP estático interno para asegurar la persistencia y estabilidad de las pruebas (10.0.0.2).

2. Fase de Reconocimiento y Escaneo (Nmap)

Se ejecutó un escaneo de puertos y detección de versiones detallado utilizando Nmap para mapear la superficie de ataque expuesta por la víctima:

nmap -sV 10.0.0.2


Otros servicios obsoletos detectados: FTP (vsftpd 2.3.4), HTTP (Apache 2.2.8), MySQL (5.0.51a).

3. Fase de Explotación y Acceso Inicial (Netcat)

Aprovechando la vulnerabilidad crítica detectada por Nmap en el puerto 1524, se utilizó Netcat para interactuar con la shell desprotegida:

nc 10.0.0.2 1524


Resultado: Se obtuvo acceso remoto inmediato e interactivo con el nivel de privilegios más alto del sistema operativo (root).

4. Post-Explotación y Criptoanálisis (John the Ripper)

Con acceso total al servidor, se procedió a evaluar la política de credenciales locales:

Se exfiltró el contenido del archivo de contraseñas (/etc/shadow).

Se trasladó el hash a la carpeta local de auditoría de la máquina atacante.

Se ejecutó John the Ripper para descifrar el hash protegido con el algoritmo MD5crypt:

john hash.txt


Resultado del cracking: En menos de un segundo, la herramienta descifró la clave del usuario administrador secundario:

Usuario: msfadmin

Contraseña: msfadmin

Esto demostró una vulnerabilidad crítica de credenciales por defecto / contraseñas débiles coincidentes con el nombre de usuario.

🔒 Recomendaciones de Mitigación

Cierre de Puertos Críticos: Deshabilitar de forma inmediata el servicio de bindshell en el puerto 1524 y bloquear cualquier tráfico no autorizado mediante reglas de Firewall (iptables).

Robustecimiento de Credenciales: Cambiar la política de contraseñas para prohibir claves por defecto y migrar los hashes de autenticación a algoritmos modernos y resistentes como SHA-512 o bcrypt.
