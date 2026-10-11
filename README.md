# Infraestructura 2 – Seguridad de Redes | ITLA

**Institución:** Instituto Tecnológico de Las Américas (ITLA)  
**Asignatura:** TSI-203 – Seguridad de Redes  
**Estudiante:** Jennifer López  
**Matrícula:** 20240860  
**Fecha:** Octubre de 2026

---

## 🎥 Video demostrativo

**[VER VIDEO DEMOSTRATIVO] https://itlaedudo-my.sharepoint.com/:f:/g/personal/20240860_itla_edu_do/IgBCOxOxKrdmT7lW4z-URXITAZgE_xpigE5JC0A8B_EAt6k?e=rbmBjN

Video de demostración de la infraestructura, configuraciones de seguridad, pruebas de conectividad, políticas de acceso y resultados obtenidos.

## 🎯 Propósito del laboratorio

Implementar una infraestructura de red segmentada mediante FortiGate, utilizando VLAN, DHCP, servidores independientes y políticas de firewall basadas en el principio de mínimo privilegio.

El laboratorio contempla un servidor de salto (Jump Server) para centralizar el acceso administrativo, un servidor web con el Sistema de Caja y una VPN de acceso remoto restringida al Jump Server.

## 🖧 Topología de red

![Topología real de Infraestructura 2](diagramas/topologia-infraestructura-2-real.png)

La topología fue desarrollada en GNS3 utilizando FortiGate, switches virtuales, clientes VPCS, un servidor Windows Server 2022 mediante VMware y un servidor Ubuntu Docker.

### Direccionamiento IP

| Dispositivo o segmento | Dirección IP |
|---|---|
| FortiGate – VLAN 10 | 10.86.10.1/25 |
| USER-NORMAL | 10.86.10.20/25 |
| USER-ADMIN | 10.86.10.21/25 |
| FortiGate – Red Jump | 10.86.50.1/29 |
| JUMP-SERVER | 10.86.50.2/29 |
| FortiGate – Red Web | 10.86.60.1/29 |
| WEB-SERVER | 10.86.60.2/29 |
| FortiGate – WAN | 192.168.42.242/24 |
| Gateway WAN | 192.168.42.1 |
| Rango configurado para VPN | 10.86.70.10–10.86.70.20 |

## 🔐 Configuraciones realizadas

### 1. FortiGate

- Configuración de interfaces y segmentos de red.
- Creación de VLAN 10.
- Configuración del servicio DHCP.
- Creación de objetos de direcciones.
- Configuración de políticas de acceso entre redes.
- Restricciones explícitas de acceso SSH para usuarios.
- Configuración de salida a Internet del WEB-SERVER mediante NAT.
- Configuración parcial de SSL-VPN, portal, usuario, grupo y política hacia JUMP-SERVER.
- Respaldo de configuración en la memoria interna de FortiGate.

Parte de las configuraciones se realizó mediante CLI debido a dificultades de acceso a la interfaz gráfica.

### 2. Windows Server 2022 – JUMP-SERVER

- Implementación de Windows Server 2022 en VMware.
- Configuración de dirección IP estática.
- Configuración de puerta de enlace.
- Habilitación del servicio RDP.
- Pruebas de conectividad y rutas.
- Pruebas de acceso hacia WEB-SERVER por HTTPS y SSH.

**Limitación:** las pruebas de comunicación hacia WEB-SERVER no fueron satisfactorias. No se completó la publicación de RemoteApp ni RemoteApp Web Client.

### 3. Ubuntu – WEB-SERVER

- Configuración de red y gateway.
- Configuración de DNS.
- Instalación y configuración de Nginx.
- Implementación del Sistema de Caja.
- Instalación y configuración de OpenSSH.
- Generación de certificado SSL autofirmado.
- Configuración de HTTPS.
- Instalación de XRDP y entorno gráfico XFCE.
- Verificación local de servicios y puertos.

La prueba local de HTTPS devolvió `HTTP 200 OK`. El servicio XRDP quedó configurado, aunque no se verificó una sesión gráfica remota completa.

### 4. Usuarios y control de acceso

- USER-NORMAL y USER-ADMIN conectados a VLAN 10.
- Asignación de direcciones mediante DHCP.
- Configuración de reglas diferenciadas de acceso.
- Restricción explícita de SSH desde la VLAN de usuarios.
- Políticas de acceso hacia JUMP-SERVER.

## 📁 Documentación técnica

La documentación incluye procedimientos, comandos, capturas originales, pruebas satisfactorias, errores y limitaciones.

**Documento principal:**

`documentacion/INFRAESTRUCTURA_2_DOCUMENTACION_COMPLETA_CON_WINDOWS_SERVER.docx`

## 📸 Evidencias

Las evidencias incluyen:

- Topología original de GNS3.
- Configuraciones y políticas de FortiGate.
- Configuración de Windows Server 2022.
- Tabla de rutas y pruebas fallidas desde JUMP-SERVER.
- Instalación y funcionamiento local de Nginx.
- Certificado SSL y HTTPS.
- Servicio SSH.
- Instalación y configuración de XRDP.
- Sistema de Caja.

## ⚙️ Configuraciones y scripts

Los archivos disponibles de configuración se organizan en `configuraciones/` y los scripts utilizados en `scripts/`.

Los respaldos nativos y running-configs deben incorporarse cuando estén disponibles, excluyendo contraseñas, claves privadas y otra información sensible.

## ⚠️ Incidencias y limitaciones

Durante la implementación se presentaron dificultades técnicas que afectaron algunos requisitos:

1. Acceso a la interfaz gráfica de FortiGate.
2. Comunicación entre JUMP-SERVER y WEB-SERVER.
3. Configuración y validación completa de SSL-VPN.
4. Publicación de RemoteApp y RemoteApp Web Client.
5. Validación de una sesión gráfica RDP hacia Ubuntu.

Estas incidencias, las pruebas realizadas y los procedimientos de diagnóstico se encuentran documentados en el informe técnico.

## 📊 Resultados

Se implementó la segmentación de redes, se configuró DHCP, se crearon políticas de seguridad y se habilitaron servicios locales en el WEB-SERVER.

Se comprobó la respuesta HTTPS local del Sistema de Caja y la disponibilidad local de los servicios SSH y XRDP. No se presenta como validada la conectividad entre servidores ni el acceso remoto por VPN.

## 📚 Conclusión

La práctica permitió implementar y documentar componentes de una infraestructura de red segura utilizando GNS3, FortiGate, VMware, Windows Server y Ubuntu.

Se aplicaron conceptos de segmentación, políticas de firewall, servicios de red, cifrado HTTPS y acceso remoto. Los resultados y las limitaciones técnicas se documentaron como parte del proceso de implementación y validación.

---

**Instituto Tecnológico de Las Américas – ITLA**  
**TSI-203 | Seguridad de Redes | Infraestructura 2**
