# Configuración del Servidor Esclavo - MariaDB 

<div align="justify">

## Objetivo del Servidor Esclavo

Este servidor `MariaDB` se configura como esclavo, permitiéndole recibir automáticamente los cambios realizados en el servidor maestro (192.168.1.104), que contiene la base de datos `jugueteria`.

Opera en modo **solo lectura**, ideal para consultas, análisis y respaldo, sin afectar el rendimiento del servidor principal.

---

## Paso 1: Configurar archivo del servidor

Editar:

```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
````

Agregar al final:

```ini
[mysqld]
server-id=2
relay-log=mariadb-relay-bin
```

---

## Paso 2: Reiniciar MariaDB

```bash
sudo systemctl restart mariadb
```

---

## Paso 3: Transferir archivo SQL desde el maestro

```bash
scp -P 2205 jugueteria.sql usuario@192.168.1.105:~
```

---

## Paso 4: Importar la base de datos

```bash
mysql -u root < jugueteria.sql
```

---

## Paso 5: Conectar con el maestro

En la consola de `MariaDB`:

```sql
STOP SLAVE;

CHANGE MASTER TO
  MASTER_HOST='192.168.1.104',
  MASTER_USER='replica_user',
  MASTER_PASSWORD='clave_segura',
  MASTER_LOG_FILE='mysql-bin.000001',
  MASTER_LOG_POS=328;

START SLAVE;
```

---

## Paso 6: Verificar estado de replicación

```sql
SHOW SLAVE STATUS\G;
```

Revisa que aparezca:

* `Slave_IO_Running: Yes`
* `Slave_SQL_Running: Yes`

---

## Paso 7: Comprobar replicación

Inserta desde el maestro:

```sql
INSERT INTO juguetes (nombre, fabricante, anio, categoria, precio)
VALUES ('Auto a control remoto', 'Speedy Toys', 2023, 'Vehículos', 320.00);
```

En el esclavo:

```sql
SELECT * FROM juguetes;
```

El nuevo registro debería estar presente.

</div>
```

---
