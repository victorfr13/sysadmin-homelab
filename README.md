# 🏠 sysadmin-homelab

Homelab de sysadmin: laboratorio personal de virtualización y administración 
de sistemas, montado sobre un portátil con Proxmox VE, usado para practicar y 
documentar tecnologías reales de entornos IT: virtualización, redes, Windows 
Server, Active Directory y DHCP.

## 🖥️ Infraestructura

- **Host**: Proxmox VE sobre portátil (nodo `serverhp`) — 8 CPU, ~15.5 GB RAM, ~208 GB almacenamiento
- **Acceso**: gestión remota vía interfaz web de Proxmox desde un segundo equipo
- **Conectividad**: el host recibe salida a Internet a través de un segundo 
  equipo, que comparte su conexión WiFi mediante ICS (Internet Connection 
  Sharing) por cable Ethernet directo

## 🗺️ Arquitectura general

```
Internet (WiFi)
   └── Segundo equipo (comparte conexión vía ICS)
         └── Cable Ethernet
               └── Host Proxmox (serverhp) — vmbr0: 192.168.100.2/24
                     └── vmbr1 (red interna NAT) — 192.168.50.0/24
                           ├── DC01 (Windows Server 2025)
                           │     IP fija: 192.168.50.10
                           │     Roles: AD DS, DNS, DHCP
                           │     Dominio: lab.local
                           │
                           └── PC01 (Windows 11)
                                 IP por DHCP: 192.168.50.50
                                 Unido al dominio lab.local
```

## 🛠️ Stack utilizado

- **Hipervisor**: Proxmox VE
- **Sistemas operativos**: Windows Server 2025, Windows 11
- **Redes**: Linux Bridge, NAT/iptables, DHCP, DNS
- **Directorio**: Active Directory Domain Services (AD DS)
- **Drivers**: VirtIO (disco, red)

## 📂 Proyectos

Cada carpeta documenta un bloque de trabajo independiente, con objetivo, 
pasos realizados, problemas encontrados (con causa y solución) y resultado 
final.

0. [Infraestructura base - Proxmox VE](00-infraestructura-base/)
   Instalación y estado inicial del host Proxmox: recursos, red y almacenamiento.

1. [Red interna virtual con NAT](01-red-interna-nat/)
   Creación de una red aislada (`vmbr1`) para las VMs, con salida a Internet 
   mediante NAT vía iptables.

2. [Creación de máquinas virtuales](02-creacion-vms/)
   Despliegue de `DC01` (Windows Server 2025) y `PC01` (Windows 11), 
   incluyendo instalación del sistema operativo, resolución de problemas con 
   drivers VirtIO, y verificación de conectividad a Internet.

3. [Active Directory (AD DS) y DHCP](03-active-directory-dhcp/)
   Promoción de `DC01` a Controlador de Dominio (`lab.local`), configuración 
   de DNS propio, estructura de OUs/usuarios/grupos, e instalación del rol 
   DHCP con su ámbito.

4. [Unión de cliente al dominio](04-union-cliente-dominio/)
   Instalación de Windows 11 en `PC01`, verificación de IP recibida por 
   DHCP, unión al dominio `lab.local` y acceso con usuario de Active Directory.

## 🔧 Aprendizajes destacados

A lo largo del proyecto se documentaron y resolvieron varios problemas reales:
- Segmentación de red y NAT manual con iptables en Proxmox
- Instalación de drivers VirtIO (disco y red) durante instalaciones de Windows
- Diagnóstico escalonado de conectividad (VM → gateway → NAT → ICS → Internet)
- Requisitos específicos de Windows 11 en entornos virtualizados (TPM 2.0, 
  bypass de conexión obligatoria en el OOBE)
- Autorización de servidores DHCP en un dominio de Active Directory

---

📫 Proyecto documentado por [victorfr13](https://github.com/victorfr13) como 
parte de un portfolio técnico de sistemas.
