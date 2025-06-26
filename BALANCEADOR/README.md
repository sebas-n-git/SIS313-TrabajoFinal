# Balanceador de Carga - SIS313

<div align="justify">

### Función en el Proyecto

El balanceador cumple un rol fundamental dentro de la arquitectura del sistema: es el encargado de distribuir las peticiones de los usuarios entre dos instancias de la aplicación (`APP1` y `APP2`), alojadas en servidores distintos. Esto permite mejorar el rendimiento, disponibilidad y escalabilidad de los servicios ofrecidos, asegurando también la tolerancia a fallos si una de las instancias deja de funcionar.

---

## Configuración de red

### Asignación de IP estática

Editamos el archivo de configuración de red para asignar una IP fija:

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
````

En el archivo colocamos:

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: false
      addresses: [192.168.1.101/24]
      routes:
      - to: default
        via: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
```

---

## Instalación y configuración de Nginx

### Instalación básica

```bash
sudo apt update && sudo apt install nginx
```

### Verificar estado del servicio

```bash
sudo systemctl status nginx
```

### Instalación de módulos PHP requeridos

```bash
sudo apt install php8.3-fpm php8.3-mbstring php8.3-curl php8.3-xml php8.3-mysql php8.3-zip
```

###  Reinicio de servicio

```bash
sudo systemctl restart nginx
```

---

##  Configuración del sitio de balanceo

Creamos el archivo de configuración:

```bash
sudo nano /etc/nginx/sites-available/balanceo_SIS313
```

Dentro del archivo escribimos:

```nginx
upstream mis_apps {
    server 192.168.1.102:3001;
    server 192.168.1.103:3002;
}

server {
    listen 80;
    server_name sis313.final;

    location / {
        proxy_pass http://mis_apps;
        proxy_http_version 1.1;

        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
        proxy_redirect off;
    }
}
```

---

##  Enlace y activación del sitio

###  Habilitar configuración creada

```bash
sudo ln -s /etc/nginx/sites-available/balanceo_SIS313 /etc/nginx/sites-enabled/
```

###  Eliminar configuración por defecto

```bash
sudo rm /etc/nginx/sites-enabled/default
```

###  Validar configuración Nginx

```bash
sudo nginx -t
```

###  Reiniciar Nginx

```bash
sudo systemctl restart nginx
```

---

##  Configuración local del nombre de dominio

En el archivo `C:\Windows\System32\drivers\etc\hosts`, se añade la siguiente línea:

```bash
192.168.1.101 sis313.final
```

Esto permite acceder al sistema desde el navegador con la URL `http://sis313.final`.

</div>
```

---

