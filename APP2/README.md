# Aplicación APP2 - SIS313

<div align="justify">

### Rol de esta instancia

`APP2` es la segunda instancia funcional del sistema, idéntica en lógica a `APP1`, pero corriendo en una dirección IP distinta y en otro puerto. Esto permite implementar balanceo de carga y tolerancia a fallos mediante el uso de Nginx. Ambas instancias están conectadas a una base de datos común o replicada, lo que permite mantener consistencia de datos mientras se mejora la disponibilidad del sistema.

---

## Configuración de red (IP estática)

Modificamos el archivo de red para configurar una IP fija:

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
      addresses: [192.168.1.103/24]
      routes:
      - to: default
        via: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
```

---

## Instalación del entorno web

### Instalar Nginx

```bash
sudo apt update && sudo apt install nginx
```

### Verificar servicio

```bash
sudo systemctl status nginx
```

### Instalar módulos PHP y otros requeridos

```bash
sudo apt install php8.3-fpm php8.3-mbstring php8.3-curl php8.3-xml php8.3-mysql php8.3-zip
```

---

## Instalación de Node.js con NVM

### Instalar NVM

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```

Activamos NVM:

```bash
\. "$HOME/.nvm/nvm.sh"
```

### Instalar Node.js

```bash
nvm install 22
```

Verificar versiones:

```bash
node -v
nvm current
npm -v
```

Valores esperados:

* `node -v`: v22.16.0
* `nvm current`: v22.16.0
* `npm -v`: 11.4.2

---

## Preparar estructura del proyecto

### Crear carpetas base

```bash
mkdir myapp2
cd myapp2
mkdir api
cd api
```

### Inicializar con `npm`

```bash
npm init
```

Configuración sugerida:

```text
package name: (api)
version: (1.0.0)
description: API Backend APP2
entry point: (index.js)
keywords: API, SIS313
author: SIS313
license: (ISC)
```

Confirmar con `yes`.

Verificar que `package.json` fue creado:

```bash
ls
```

---

## Instalar dependencias necesarias

```bash
npm install -g npm@11.4.2
```

---

## Configurar archivo principal `index.js`

Crear el archivo:

```bash
nano index.js
```

### Cambios importantes para APP2:

* Puerto: `3002`
* IP: `192.168.1.103`
* Todo lo demás puede mantenerse igual que en APP1

Código de ejemplo (puedes pegar todo el CRUD si lo deseas, pero con puerto `3002`):

```js
const express = require('express');
const app = express();
const PORT = 3002;

app.use(express.json());

app.get('/', (req, res) => {
  res.send('¡Bienvenido a la API SIS313 APP2!');
});

app.listen(PORT, '0.0.0.0', () => {
  console.log(`Servidor APP2 corriendo en http://localhost:${PORT}`);
});
```

---

## Ejecutar y verificar

Ejecuta el servicio:

```bash
node index.js
```

Salida esperada:

```
Servidor APP2 corriendo en http://localhost:3002
```

Accede desde el navegador:

```
http://192.168.1.103:3002
```

---

## Verificación desde balanceador

Si el balanceador está funcionando correctamente, podrás acceder a la instancia de APP2 mediante:

```
http://sis313.final
```

Al recargar varias veces, verás respuestas desde `APP1` o `APP2` de manera aleatoria (o basada en el algoritmo del balanceador).

</div>
```

---
