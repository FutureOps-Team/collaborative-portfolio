#  Laboratorio de Hardening de Puertos SMB en Windows Host

##  Objetivo del Laboratorio
Comprobar y fortalecer la seguridad del sistema operativo **Windows** frente a servicios y puertos comúnmente explotados. 
El análisis y endurecimiento se realizaron desde una **máquina virtual Kali Linux** conectada mediante **VirtualBox**.

---

##  Entorno del Laboratorio

| Elemento | Descripción |
|-----------|-------------|
| **Host (Windows)** | Windows 11 Pro |
| **Guest (VM)** | Kali Linux |
| **Virtualización** | VirtualBox |
| **Modo de red** | NAT (y pruebas posteriores en modo Bridge) |
| **Herramientas principales** | Nmap, PowerShell, Windows Defender Firewall |

---

##  Etapas del Laboratorio

### 1️ Escaneo inicial de puertos con Nmap
Desde Kali se ejecutó:
bash
nmap -sS 192.168.X.X

Puertos detectados:

135/tcp → Microsoft RPC

139/tcp → NetBIOS Session Service

445/tcp → SMB (Microsoft-DS)

5357/tcp → HTTPAPI (Web Services on Devices)

- Análisis:
Estos servicios corresponden al stack de red de Windows y son frecuentemente explotados (EternalBlue, Pass-the-Hash, WannaCry).

### 2 Verificación de versiones SMB
nmap --script smb-protocols -p445 192.168.X.X

Resultado:
Soporta SMB 2.x y 3.x → ✅ SMBv1 deshabilitado (seguro).

### 3 Hardening del sistema host
Desde PowerShell (Administrador):

- Desactivar SMBv1
Disable-WindowsOptionalFeature -Online -FeatureName "SMB1Protocol"

- Crear regla de firewall para bloquear SMB
New-NetFirewallRule -DisplayName "Block SMB TCP 445" -Direction Inbound -LocalPort 445 -Protocol TCP -Action Block
Set-NetFirewallRule -DisplayName "Block SMB TCP 445" -Profile Any -InterfaceType Any

- Desactivar descubrimiento de red y compartición
Ruta:
Panel de Control → Centro de redes → Configuración avanzada → Desactivar descubrimiento de red y compartición.

- Detener servicio SMB (LanmanServer)
Stop-Service LanmanServer
Set-Service LanmanServer -StartupType Disabled

### 4 Verificación de reglas y servicios
Get-NetFirewallRule -DisplayName "Block SMB TCP 445" | Format-List DisplayName, Enabled, Profile, Direction, Action

Resultado esperado:
DisplayName : Block SMB TCP 445
Enabled     : True
Profile     : Any
Direction   : Inbound
Action      : Block

Get-Service LanmanServer

Resultado esperado:
Status     : Stopped
StartType  : Disabled

### 5 Re-escaneo con Nmap (modo Bridge)
nmap -p135,139,445,5357 192.168.X.X

Resultado final:
135/tcp filtered
139/tcp filtered
445/tcp filtered
5357/tcp closed

Conclusión:
El host Windows bloquea o no responde a peticiones entrantes en los puertos más críticos, validando un hardening exitoso.


Hallazgos y Aprendizaje:
Los puertos 135, 139, 445 y 5357 representan un riesgo elevado si se exponen a la red.

El modo NAT puede generar falsos positivos (VirtualBox NAT responde, no el host).

Firewall + desactivación de SMBv1/v2/v3 proveen una defensa efectiva a nivel local.

Comprender las configuraciones de red virtual es clave para interpretar correctamente escaneos con Nmap.


Colaboradores:
Federico Arce Minuet - Configuración de seguridad, hardening y documentación en Windows.
Manuel Rossi Sanz - Escaneo, validación y registro de resultados en Kali.


Resultado Final:
Hardening de puertos SMB y RPC completado exitosamente.
Los escaneos externos muestran los puertos críticos como filtered o closed, validando la efectividad de las políticas de firewall y la desactivación de servicios innecesarios.



