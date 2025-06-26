# Aplicación APP1 - SIS313

<div align="justify">

### Propósito de esta instancia

`APP1` representa una de las instancias del sistema web conectado al balanceador. Su objetivo es brindar servicios de gestión (CRUD) sobre una base de datos de libros. Al estar conectada a una instancia propia de base de datos, permite realizar operaciones como agregar, modificar o eliminar registros, asegurando alta disponibilidad mediante el balanceador.

---

## Configuración de red (IP estática)

Editamos el archivo de configuración de red para asignar una IP fija:

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
````

En el archivo escribimos:

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: false
      addresses: [192.168.1.102/24]
      routes:
      - to: default
        via: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
```

---

## Instalación de servidor web y módulos

### Instalar Nginx

```bash
sudo apt update && sudo apt install nginx
```

### Verificar estado del servicio

```bash
sudo systemctl status nginx
```

### Instalar PHP y módulos necesarios

```bash
sudo apt install php8.3-fpm php8.3-mbstring php8.3-curl php8.3-xml php8.3-mysql php8.3-zip
```

---

## ⚙Configuración de entorno Node.js con NVM

### Instalación de NVM

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```

Activamos NVM:

```bash
\. "$HOME/.nvm/nvm.sh"
```

### Instalación de Node.js

```bash
nvm install 22
```

Verificamos versión:

```bash
node -v
nvm current
npm -v
```

Resultados esperados:

* `node -v`: v22.16.0
* `nvm current`: v22.16.0
* `npm -v`: 11.4.2

---

## Creación de estructura de proyecto con Express.js

### Crear carpeta base para la app

```bash
mkdir myapp1
cd myapp1
```

### Crear subcarpeta para API

```bash
mkdir api
cd api
```

### Inicializar proyecto

```bash
npm init
```

Configuración sugerida durante la creación:

```text
package name: (api)
version: (1.0.0)
description: API Backend SIS313
entry point: (index.js)
keywords: API, SIS313
author: SIS313
license: (ISC)
```

Luego, confirmamos con `yes`.

### 🔍 Verificar existencia del archivo:

```bash
ls
```

Debería mostrarse: `package.json`

---

## 🚀 Instalar dependencias necesarias

```bash
npm install -g npm@11.4.2
```

---

## 🧪 Primera prueba de Express.js

Creamos archivo principal:

```bash
nano index.js
```

Contenido inicial del archivo:

```js
const express = require('express');
const app = express();
const PORT = 3001;

app.use(express.json());

app.get('/', (req, res) => {
  res.send('¡Bienvenido a la API SIS313 APP1!');
});

app.get('/usuarios', (req, res) => {
  res.json([
    { id: 1, nombre: 'Carlos' },
    { id: 2, nombre: 'Ana' }
  ]);
});

app.listen(PORT, '0.0.0.0', () => {
  console.log(`Servidor APP1 corriendo en http://localhost:${PORT}`);
});
```

### Ejecutar la API

```bash
node index.js
```

Salida esperada:

```text
Servidor APP1 corriendo en http://localhost:3001
```

Verificar acceso desde navegador:

```
http://192.168.1.102:3001
```

---

## Prueba de balanceo (opcional)

Si ya tienes configurado el balanceador, puedes probar acceso con el dominio:

```
http://sis313.final
```

---

## Conexión a base de datos y operaciones CRUD

### Instalar dependencias para MySQL/MariaDB

```bash
npm install mysql2
npm install express
```

---

## CRUD completo para biblioteca

Código completo (ya incluido en `index.js`):

**\[El CRUD completo ya está presente, así que no lo repito aquí para no duplicar]**

 Lo único que debes asegurarte es que esté bien insertado dentro del mismo archivo `index.js`, reemplazando el contenido de prueba si deseas.

---

## Verificación final

Al ejecutar:

```bash
node index.js
```

Se espera ver:

```text
Servidor API corriendo en http://localhost:3001
```

Desde el navegador:

```
http://192.168.1.102:3001
```

En el balanceador:

```
http://sis313.final
```

</div>
```

---
