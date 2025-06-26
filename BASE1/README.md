# Configuración del Servidor Maestro - MariaDB 

<div align="justify">

## Objetivo del Servidor Maestro

Configurar el servidor principal de base de datos `MariaDB`, que contendrá la base de datos original denominada `jugueteria`. Este servidor se encargará de almacenar todos los datos y generar los logs binarios necesarios para la replicación hacia un servidor esclavo. Todas las operaciones de escritura (INSERT, UPDATE, DELETE) se realizarán aquí.

Este servidor maestro actúa como la **fuente de verdad**, replicando sus datos al servidor esclavo para asegurar alta disponibilidad y tolerancia a fallos.

---

## Paso 1: Configurar archivo del servidor

Editar:

```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
````

Agregar al final:

```ini
[mysqld]
server-id=1
log-bin=mysql-bin
binlog-do-db=jugueteria
```

---

## Paso 2: Reiniciar el servicio

```bash
sudo systemctl restart mariadb
```

---

## Paso 3: Crear usuario de replicación

```sql
CREATE USER 'replica_user'@'%' IDENTIFIED BY 'clave_segura';
GRANT REPLICATION SLAVE ON *.* TO 'replica_user'@'%';
FLUSH PRIVILEGES;
```

---

## Paso 4: Crear base de datos y tabla de ejemplo

```sql
CREATE DATABASE jugueteria;
USE jugueteria;

CREATE TABLE juguetes (
  id INT AUTO_INCREMENT PRIMARY KEY,
  nombre VARCHAR(100),
  fabricante VARCHAR(100),
  anio INT,
  categoria VARCHAR(100),
  precio DECIMAL(10,2)
);

INSERT INTO juguetes (nombre, fabricante, anio, categoria, precio) VALUES
('Robot Max', 'TecnoKids', 2021, 'Robótica', 250.00);
```

---

## Paso 5: Generar volcado con información de replicación

```bash
mysqldump -u root --databases jugueteria --master-data --single-transaction > jugueteria.sql
```

Buscar en el archivo `.sql` una línea como esta:

```sql
-- CHANGE MASTER TO MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=328;
```

---

## Prueba de replicación

### Inserta un registro desde el maestro:

```sql
USE jugueteria;
INSERT INTO juguetes (nombre, fabricante, anio, categoria, precio)
VALUES ('Muñeca Sofía', 'DreamToys', 2022, 'Muñecas', 180.00);
```

Este cambio deberá replicarse automáticamente al esclavo.

---

Para la configuración del RAID 1 asociado a este servidor, consulta: [RAID1.md](../RAID1/README.md)

</div>
```

---

