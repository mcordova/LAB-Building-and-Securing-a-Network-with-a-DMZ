# Informe de configuración de DMZ con Cisco Packet Tracer


### 1. Objetivo del laboratorio

> Explica brevemente qué se buscaba lograr con este laboratorio.

Configurar un router y crear una DMZ segura, aplicando NAT y listas de control de acceso (ACLs) para filtrar el tráfico interno de la red local LAN, la red aislada DMZ y con respecto a la red externa.

---

### 2. Topología implementada

> Describe la red. Puedes incluir una imagen si el software lo permite (captura de Packet Tracer).

- Cantidad de redes: 3 redes clase C
- Dispositivos usados: 1 router, 3 switches, 1 servidor, 2 PC's (uno interno, otro externo)
- Breve descripción de la función de cada zona (LAN, DMZ, Externa).



### 3. Plan de direccionamiento IP

Completa la tabla con las IPs asignadas (puedes copiarla del enunciado si no cambió).

| Dispositivo             | IP              | Máscara           | Gateway           |
|-------------------------|------------------|-------------------|-------------------|
| PC_Internal             | 192.168.1.10     | 255.255.255.0     | 192.168.1.1       |
| Server_DMZ              | 192.168.2.10     | 255.255.255.0     | 192.168.1.1       |
| PC_External             | 192.168.3.10     | 255.255.255.0     | 192.168.1.1       |
| Router_FW Gi0/0 (LAN)   | 192.168.1.1      | 255.255.255.0     |                   |
| Router_FW Gi0/1 (DMZ)   | 192.168.2.1      | 255.255.255.0     |                   |
| Router_FW Gi0/2 (Ext)   | 192.168.3.1      | 255.255.255.0     |                   |


### 4. Configuración aplicada (resumen)

> Resume los comandos o pasos más relevantes que ejecutaste. Usa texto + fragmentos de código cuando sea necesario.

- Interfaces configuradas con `ip address`
- NAT:
```bash
interface GigabitEthernet0/1
ip nat inside
exit

interface GigabitEthernet0/2
ip nat outside
exit

ip nat inside source static 192.168.2.10 192.168.3.1
exit
```
- ACLs:
```bash
ip access-list extended ACL_EXTERNA 
permit tcp any host 192.168.3.1 eq 80
exit

# ! ACL para evitar tráfico interno desde el servidor de la DMZ
ip access-list extended ACL_DMZ 
permit tcp 192.168.2.0 0.0.0.255 192.168.1.0 0.0.0.255 established
deny icmp 192.168.2.0 0.0.0.255 any
deny ip 192.168.2.0 0.0.0.255 192.168.1.0 0.0.0.255
permit ip any any
exit
```



### 5. Verificaciones realizadas

> He capturado pantalla de los ping y el acceso web antes y después de crear las ACL.

#### 5.1 Verificaciones realizadas antes de ACL

- `ping` desde PC_Internal al gateway del router: ✅
- `ping` desde PC_Internal al servidor web (dentro de la DMZ): ✅
- Acceso web desde PC_External: ✅
- Acceso web desde PC_Internal: ✅

#### 5.2 Verificaciones realizadas después de ACL

- Bloqueo de acceso desde DMZ a LAN: ✅
- `ping` desde PC_Internal al gateway del router: ❌
- `ping` desde PC_Internal al servidor web (dentro de la DMZ): ❌
- Acceso web desde PC_External: ✅
- Acceso web desde PC_Internal: ✅
- Respuesta Ok a peticiones http desde DMZ a LAN: ✅


### 6. Conclusiones y recomendaciones

> ¿Qué aprendiste con este ejercicio? ¿Qué mejorarías?

Aprendí a gestionar un router, aplicando NAT y ACLs en un entorno simulado (Packet Tracer). 

Recomiendo documentar todos los pasos bien, y tener copias de seguridad de la configuración que funciona, puesto que una nueva regla, puede bloquear todo el tráfico!.


### 7. Capturas de evidencia

> Hay capturas de pantalla de cada paso en la carpeta evidencias, y he generado un único PDF con el enunciado, el código y toda las ![Capturas de pantalla](M18-DMZ-capturas.pdf)
