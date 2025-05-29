### 2.1 Ubicació física i estructura  

![alt text](image-66.png) 
> **Figura:** Vista en tall d’un edifici amb **una única habitació** (zona segura, “Sala CPD”) i l’exterior (zona calenta).

- El CPD se situa **dins d’un edifici** poc visible i segur.  
- Aquesta única habitació:
  - **Terra tècnic** per canalitzar cables.
  - **Sostre fals** per col·locar climatització i conduccions.
  - Zona interior (🟦 **fria**) on s’ubiquen racks i equips.
- Tot l’exterior (🟥 **calenta**) està reservat a la **refrigeració** i extractors.  
- S’han preparat **diagrames** de planta i secció per documentar:
  - Distribució de la sala
  - Fluxe d’aire fred/calor
  - Emplaçament de portes, alarmes i càmeres

---

### 2.2 Infraestructura IT (servidors, racks, switchos)

- **Rack 1** (Web & Vídeo):
  - Apache2  
  - Servidor RTMP / OBS  
- **Rack 2** (Bases de dades & Àudio):
  - MySQL (bases de dades)  
  - Icecast2 + Darkice (servei àudio)  
- Altres serveis instal·lats pels companys:
  - **Bind9** (DNS intern)  
  - **vsftpd** (FTP)  
  - **Docker** (contenidors)  
  - **Glances** (monitoratge)  
  - **Fail2Ban** (protecció d’entrades)  

---

### 2.3 Infraestructura elèctrica i climatització

#### Climatització  
- **Aire condicionat de precisió**:
  - Temperatura: **20 °C–24 °C**  
  - Humitat: **40 %–60 %**  
  - Filtres HEPA per pols  
- **Free-cooling**: ventilació nocturna o ambient exterior

#### Electricitat i SAI  
- **Dues fonts elèctriques** redundants  
- **SAI 2000 VA** amb autonomia mínima de 15 min  
  ```text
  Càlcul:
  4 servidors × 400 W = 1 600 W
  15 min = 0,25 h → 1 600 W × 0,25 h = 400 Wh

  # Projecte de transmissió

# Proyecto de Servicios de Red y Seguridad

## Contenido

- Configuración de red en AWS
- Instalación y configuración de servicios
- Firewall (iptables)
- FTP, DNS, Web, Docker, Fail2Ban, Netdata, Glances...
- Monitorización y seguridad
- Comprobaciones de conexión

---

## Creación de la instancia en AWS

Se ha creado una instancia EC2 con sistema Ubuntu Server 22.04.  
La instancia tiene acceso restringido mediante clave PEM y grupo de seguridad.  
Configuración de IP privada para conexión en VPC con los compañeros.

---

## Configuración de red y conexión SSH

```bash
chmod 400 clau_ibrahim.pem
ssh -i "clau_ibrahim.pem" ubuntu@<IP pública>
```

Se comprueba conexión a compañeros mediante `ping`.

---

## Instalación de Bind9 (DNS)

```bash
sudo apt update
sudo apt install bind9
sudo cp /etc/bind/db.local /etc/bind/db.cpd.local
sudo nano /etc/bind/named.conf.local
sudo nano /etc/bind/db.cpd.local
sudo systemctl restart bind9
```

Prueba con:

```bash
dig @localhost video.cpd.local
```

---

## Instalación de FTP (vsftpd)

```bash
sudo apt install vsftpd
sudo systemctl enable vsftpd
sudo systemctl start vsftpd
```

Creación de usuario `fitxers` y prueba con `ftp` para enviar/recibir archivos.

---

## Instalación de Netdata

1. Requisitos:
```bash
sudo apt install git curl bash cmake gcc g++ make libz-dev libelf-dev pkg-config -y
```

2. Clonar y ejecutar:
```bash
git clone https://github.com/netdata/netdata.git --depth=100
cd netdata
sudo ./netdata-installer.sh
```

Acceso vía navegador: `http://<IP>:19999`

---

## Instalación de Glances

```bash
sudo apt install glances -y
```

Muestra información en terminal sobre CPU, RAM, disco, etc.

---

## Firewall con iptables

Reglas básicas para limitar acceso solo desde IPs concretas (compañeros):

```bash
sudo iptables -A INPUT -s 172.18.4.129 -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -j DROP
```

Guardar configuración con `iptables-save`.

---

## Instalación de Fail2Ban

```bash
sudo apt install fail2ban
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

Protección contra intentos fallidos SSH, FTP, etc.

---

## Instalación de Apache2 (Web)

```bash
sudo apt install apache2
sudo systemctl start apache2
```

Movemos el HTML descomprimido a `/var/www/html/`  
Acceso: `http://<IP>`

---

## Instalación de Docker

```bash
sudo apt install docker.io
sudo systemctl enable docker
sudo systemctl start docker
```

Prueba:

```bash
docker run hello-world
```

---

## Configuración final y comprobaciones

- Todos los servicios están activos
- Los puertos están abiertos en el grupo de seguridad
- Los compañeros pueden acceder vía VPC
- El DNS responde correctamente
- FTP permite subir y bajar archivos
- La página web carga correctamente
- Netdata funciona vía navegador
- Fail2Ban está activo

---

## IP de la màquina: `172.18.5.129`


## Creació de la nostra instància a AWS
![alt text](image-3.png)
### Connexió al servidor per terminal (SSH)
![alt text](image-4.png)

## Creació de VPCs
![alt text](image-5.png)

### Creem les interconnexions amb els nostres companys
![alt text](image-7.png)


Perquè funcioni, necessitem configurar les nostres rutes a la **taula d'encaminament**.

![alt text](image-8.png)

Amb això ja tindrem connexió entre nosaltres.

---

## INSTAL·LACIÓ I CONFIGURACIÓ DEL SERVIDOR D'ÀUDIO

### Instal·lació d’Icecast2
![alt text](image-9.png)
> **Nota:** Cal tenir activat el port **8000** al nostre grup de seguretat d'AWS.
![alt text](image-10.png)

### Accés a Icecast

![alt text](image-11.png)

### Instal·lació i configuració de Darkice
![alt text](image-13.png)
![alt text](image-12.png)

#### Fitxer de configuració

![alt text](image-14.png)


### Comprovació de funcionament de Darkice

![alt text](image-15.png)

### Instal·lació de mòduls extres a Linux

![alt text](image-16.png)

### Instal·lació i configuració d'Alsa

![alt text](image-17.png)
![alt text](image-18.png)
### Configuració del loopback com a predeterminat
![alt text](image-19.png)
![alt text](image-20.png)

### Reinici d'Alsa i execució de Darkice

![alt text](image-21.png)

### Execució de l’script d’àudio
![alt text](image-25.png)
![alt text](image-23.png)
![alt text](image-24.png)
![alt text](image-26.png)
---

## INSTAL·LACIÓ I CONFIGURACIÓ DEL SERVIDOR DE VÍDEO

### Creació del fitxer instal·lador de GStreamer

![alt text](image-27.png)

#### Servidor

![alt text](image-28.png)

#### Client

![alt text](image-29.png)

---

## COMPROVACIÓ DE L'AMPLADA DE BANDA

### Servidor

![alt text](image-30.png)

### Client

![alt text](image-31.png)

#### Comprovació gràfica

![alt text](image-32.png)

### Execució de l’streaming de vídeo
![alt text](image-35.png)
![alt text](image-36.png)
![alt text](image-33.png)
![alt text](image-34.png)

---

## Instal·lació i configuració de Nginx
![alt text](image-37.png)
### Descompressió de la pàgina web
![alt text](image-38.png)

### Moviment al directori `/www`

![alt text](image-39.png)
### Canvi de nom i configuració del domini

![alt text](image-40.png)

### Configuració de Nginx per mostrar la pàgina

![alt text](image-41.png)
![alt text](image-42.png)
![alt text](image-43.png)
![alt text](image-44.png)
### Inici de la pàgina web

![alt text](image-45.png)

# Base de dades i conveni

## Instal·lació del servidor MySQL

Veiem que s'ha descarregat el paquet `mysql-server`.

![alt text](image-46.png)

---

## Creació de la base de dades

Es crea una nova base de dades per gestionar la informació d'empleats, departaments i nivells.

![alt text](image-47.png)
---

## Creació de les taules
![alt text](image-48.png)
![alt text](image-49.png)
![alt text](image-50.png)
### Taula d'Empleats

Es defineixen les estructures dels empleats amb les seves dades corresponents.

![alt text](image-51.png)

### Taula de Departaments

Conté informació dels departaments de l'empresa.

![alt text](image-52.png)

### Taula de Nivells

Inclou la informació sobre:
- Consultoria
- Tecnologies de la informació
- Estudis de mercat i opinió pública

![alt text](image-53.png)

---

## Resultat de les taules completades

Mostra del contingut de les taules després d’afegir les dades.

![alt text](image-54.png)
![alt text](image-55.png)
![alt text](image-56.png)

---

## Comprovació dels privilegis

### Comprovació com a `victor`

- Pot veure les taules
- No pot crear ni modificar dades

![alt text](image-57.png)
![alt text](image-58.png)
![alt text](image-59.png)

### Comprovació com a `elena`

- Pot crear i eliminar usuaris i dades

![alt text](image-60.png)
![alt text](image-61.png)
![alt text](image-62.png)
![alt text](image-63.png)
---

## Client: accés a base de dades i conveni

Vista des del client accedint a la base de dades configurada.
![alt text](image-65.png)

