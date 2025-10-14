# 🧠 Análisis y Monitoreo del Sistema en Linux  

---

## 📘 Introducción

Este repositorio recopila el desarrollo y documentación de **herramientas esenciales para la administración y diagnóstico de sistemas Linux**, enfocándose en el análisis de rendimiento, la exploración de red y la gestión de protocolos IP.  

Los temas abordados son los siguientes:

1. **Interpretación de la información de `htop`** y su complemento con herramientas como `glances`, `ifconfig`, `nmap` y `lynis`.  
2. **Comprensión de IPv4 e IPv6**, junto con los comandos utilizados en Ubuntu para explorar direcciones de red.  
3. **Instalación y configuración del entorno en Arch Linux** para la ejecución de estas herramientas.

---

## 1. ¿Cuál es el significado de la información que me expone `htop`? y ¿cómo la puedo complementar con `glances`, `ifconfig`, `nmap` y `lynis`?

---

## 📊 ¿Qué es `htop`?

`htop` es una herramienta interactiva en terminal utilizada para monitorear el rendimiento del sistema en tiempo real.  
Muestra información sobre el uso del CPU, la memoria RAM, la memoria de intercambio (swap), los procesos activos y el tiempo de actividad del sistema.  

A diferencia del clásico `top`, `htop` ofrece una interfaz más intuitiva, colorida y navegable, permitiendo desplazarse por los procesos, ordenarlos, filtrarlos o finalizarlos fácilmente.

---

## 🧩 Información que muestra `htop`

### 🔹 Encabezado general del sistema

En la parte superior se visualiza el estado global:

- **Uso de CPU:** muestra barras por cada núcleo, indicando el porcentaje de uso.
- **Memoria RAM:** cantidad total, usada y libre.
- **Memoria Swap:** memoria de intercambio utilizada.
- **Uptime:** tiempo que lleva encendido el sistema.
- **Load average:** carga promedio del sistema en 1, 5 y 15 minutos.
- **Número de tareas y usuarios conectados.**

> Ejemplo:  
> `Tasks: 123, 245 thr; 2 running`  
> `Load average: 0.45 0.65 0.70`  
> `Uptime: 3 days, 04:12`

---

### 🔹 Barras de CPU por núcleo

Cada barra representa un **núcleo lógico del procesador**.  
Los colores indican distintos tipos de actividad:

| Color | Significado |
|-------|--------------|
| Azul | Procesos del usuario (`user`) |
| Rojo | Procesos del sistema (`system`) |
| Verde | Prioridad baja (`nice`) |
| Naranja | Espera de I/O (`iowait`) |
| Morado | Interrupciones (`irq`) |
| Gris | Tiempo inactivo (`idle`) |

> Un alto valor de `iowait` indica que el sistema está esperando operaciones de disco,  
> mientras que un `user` alto significa que procesos del usuario están usando intensamente la CPU.

---

### 🔹 Sección de memoria

`htop` muestra la **RAM total, utilizada, buffers y cache** en forma de barras:

- **Used:** memoria usada directamente por procesos.
- **Buffers:** memoria usada por el kernel para almacenamiento temporal.
- **Cached:** datos almacenados temporalmente para acceso rápido.
- **Free:** memoria completamente libre.

> Linux usa la memoria libre como caché para mejorar el rendimiento,  
> por lo tanto, una barra aparentemente “llena” no siempre indica un problema.


---

### 🔹 Lista de procesos

La parte inferior muestra una tabla con los procesos activos del sistema.  
Algunas columnas importantes:

| Columna | Descripción |
|----------|-------------|
| PID | Identificador del proceso |
| USER | Usuario que ejecuta el proceso |
| PRI / NI | Prioridad y valor de "nice" (baja prioridad = mayor número) |
| VIRT | Memoria virtual total asignada |
| RES | Memoria física usada por el proceso |
| SHR | Memoria compartida con otros procesos |
| S | Estado del proceso (R = running, S = sleeping, D = uninterruptible, Z = zombie) |
| %CPU | Porcentaje de CPU utilizado |
| %MEM | Porcentaje de RAM utilizada |
| TIME+ | Tiempo total de CPU consumido |
| COMMAND | Comando ejecutado o nombre del proceso |

Puedes **ordenar por cualquier columna** con las teclas de función (`F6`) o finalizar procesos con `F9`.

![Image](https://github.com/user-attachments/assets/ff9c3134-e7c5-42f7-a342-dc0b0f440ac1)

---

## 🧠 Interpretación práctica de los datos

1. **CPU alta en un solo núcleo:**  
   Puede indicar un proceso que no aprovecha el paralelismo (single-threaded).

2. **`iowait` alto:**  
   Señala cuellos de botella en el disco o almacenamiento.

3. **Swap en uso constante:**  
   El sistema está usando disco como memoria, lo cual ralentiza todo.  
   Es síntoma de poca RAM o aplicaciones mal optimizadas.

4. **Muchos procesos en estado `D` (uninterruptible):**  
   Problemas con I/O, discos o controladores.

---

## 🧰 Cómo complementar `htop` con otras herramientas

`htop` muestra el estado interno del sistema,  
pero puede complementarse con otras utilidades para un análisis **más completo** del hardware, red y seguridad.

---

###  `glances` — monitoreo avanzado

`glances` amplía las métricas de `htop`, mostrando en una sola pantalla:
- CPU, memoria, swap, disco, red, sensores, procesos, filesystem y temperatura.
- Puede ejecutarse en modo servidor (`glances -w`) y consultarse desde un navegador.

**Instalación:**
```bash
sudo apt update
sudo apt install glances
glances
```
- **Ventajas de htop**
- Muestra I/O de disco, throughput de red y sensores de hardware.
- Permite alertas por colores según niveles de carga.
- Puede exportar estadísticas a JSON o bases de datos.

###  ifconfig o ip — información de red
 Sirven para obtener información sobre las interfaces de red y sus direcciones IP.
```bash
# Mostrar todas las interfaces
ifconfig -a

# Versión moderna del comando
ip addr show
```
**Interpretación:**
- Identifica las direcciones IPv4 e IPv6 del sistema.
- Verifica si una interfaz está activa (UP) o no.
- Permite comprobar conectividad o configuraciones erróneas de red.
(imagen de la salida de ifconfig o ip addr show)

### nmap — escaneo de red y puertos
nmap permite descubrir hosts activos y puertos abiertos dentro de una red.
Ideal para diagnósticos, auditorías y detección de servicios.
```bash
sudo apt install nmap
# Escaneo básico de red local
sudo nmap -sn 192.168.1.0/24

# Escaneo de puertos y servicios
sudo nmap -sS -sV -O 192.168.1.100
```
Complemento con htop:

Si un proceso usa mucha red en htop, se puede usar nmap
para identificar qué servicios están corriendo y en qué puertos.

### lynis — auditoría de seguridad del sistema
lynis analiza la seguridad del sistema operativo, servicios, configuración de red y permisos.
Es útil para identificar vulnerabilidades o configuraciones inseguras.
```bash
sudo apt install lynis
sudo lynis audit system
```
<img width="791" height="402" alt="Image" src="https://github.com/user-attachments/assets/5e0206d3-4903-4d9c-a31b-ec59739b73cc" />

- Evalúa más de 200 parámetros del sistema.
- Genera un puntaje de seguridad (hardening index).
- Sugiere recomendaciones de mejora.

### 🧩 Ejemplo de flujo de diagnóstico combinado
- Ejecuta htop para observar uso alto de CPU o memoria.
- Abre glances para revisar también I/O de disco y tráfico de red.
- Usa ifconfig o ip addr para confirmar las IP activas.
- Con nmap, escanea la red local y detecta otros dispositivos activos.
- Finalmente, ejecuta lynis para revisar configuraciones inseguras o servicios innecesarios.

---
  
## 🌐 2. ¿Qué es IPv4 e IPv6? y ¿Qué comandos se usan en Ubuntu para explorar sus direcciones?


### 🔹 Introducción

Las direcciones IP (**Internet Protocol**) son identificadores únicos que permiten la comunicación entre dispositivos dentro de una red.  
Actualmente existen dos versiones principales en uso: **IPv4** y **IPv6**, cada una con características y estructuras distintas.

---

### 🧩 IPv4 (Internet Protocol versión 4)

- Utiliza **32 bits**, lo que permite aproximadamente **4.3 mil millones** de direcciones únicas.
- Se representa en formato **decimal con puntos**, por ejemplo:  192.168.1.10

- Es la versión más ampliamente utilizada, aunque su espacio de direcciones ya está agotado.
- Soporta técnicas como **NAT (Network Address Translation)** para extender su vida útil.

<img width="650" height="450" alt="Image" src="https://github.com/user-attachments/assets/0228b674-7ca6-432a-857c-aae14c299645" />

---

### 🧩 IPv6 (Internet Protocol versión 6)

- Usa **128 bits**, permitiendo un número casi ilimitado de direcciones (≈ 3.4 × 10³⁸).
- Se representa en **formato hexadecimal**, separado por dos puntos:  2001:0db8:85a3:0000:0000:8a2e:0370:7334
- No requiere NAT, ya que cada dispositivo puede tener una dirección única.
- Incorpora mejoras como:
- **Autoconfiguración automática (SLAAC)**
- **Soporte nativo para IPsec (seguridad)**
- **Encabezado más simple y eficiente**

![Image](https://github.com/user-attachments/assets/51f001b0-c8b4-4ec7-82a2-d91a4be846e8)

---

### ⚙️ Comparativa General

| Característica | IPv4 | IPv6 |
|----------------|------|------|
| Tamaño de dirección | 32 bits | 128 bits |
| Formato | Decimal con puntos (192.168.0.1) | Hexadecimal (2001:db8::1) |
| Espacio de direcciones | ~4.3 × 10⁹ | ~3.4 × 10³⁸ |
| NAT (Traducción de direcciones) | Requerido | No necesario |
| Seguridad (IPsec) | Opcional | Integrado |
| Encabezado | Variable | Fijo (40 bytes) |

---

### 💻 Comandos en Ubuntu para explorar direcciones IP

#### 🔹 Mostrar interfaces y direcciones IP (IPv4/IPv6)
```bash
ip addr show
o
ip a
```
<img width="759" height="294" alt="Image" src="https://github.com/user-attachments/assets/4e0dd9c7-63cb-4f42-99b3-d4abba38a097" />

🔹 Mostrar información específica de una interfaz
```bash
ip addr show eth0
```
Muestra las direcciones asignadas a la interfaz eth0 (puede variar según el nombre de la interfaz).

🔹 Consultar la ruta predeterminada (gateway)
```bash
ip route show
```
Permite verificar la puerta de enlace predeterminada y las rutas activas.

<img width="759" height="294" alt="Image" src="https://github.com/user-attachments/assets/f1133663-ab62-4a40-8994-58bba18d241d" />

🔹 Usando la herramienta clásica ifconfig
```bash
ifconfig
```
Aún disponible en algunos sistemas, muestra las interfaces de red y sus direcciones, aunque ha sido reemplazada por iproute2.

💡 Consejo:
En versiones recientes de Ubuntu, ifconfig no viene instalado por defecto.
Para usarlo: sudo apt install net-tools 

---
