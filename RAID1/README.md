# Configuración de RAID 1 para el Servidor Maestro (MariaDB)

<div align="justify">

## Objetivo

Implementar RAID 1 en el servidor de base de datos maestro para garantizar **alta disponibilidad** y **tolerancia a fallos**. Se busca proteger la integridad de la base de datos `jugueteria` replicando automáticamente los datos en dos discos físicos.

---

## Entorno

- Ubuntu Server 24.04
- MariaDB 10.11
- Discos disponibles: `/dev/sdb` y `/dev/sdc`

---

## Pasos de configuración

### 1. Verificar discos disponibles

```bash
lsblk
````

### 2. Instalar `mdadm`

```bash
sudo apt update
sudo apt install mdadm -y
```

### 3. Crear RAID 1

```bash
sudo mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdb /dev/sdc
```

### 4. Formatear el volumen RAID

```bash
sudo mkfs.ext4 /dev/md0
```

### 5. Montar RAID

```bash
sudo mkdir -p /mnt/raid1
sudo mount /dev/md0 /mnt/raid1
```

### 6. Configurar montaje automático

```bash
sudo blkid /dev/md0
sudo nano /etc/fstab
```

Agregar línea con UUID:

```
UUID=xxxx-xxxx /mnt/raid1 ext4 defaults,nofail,discard 0 0
```

---

## Mover base de datos `jugueteria` al RAID

```bash
sudo systemctl stop mariadb
sudo cp -r /var/lib/mysql /mnt/raid1/
sudo mv /var/lib/mysql /var/lib/mysql.bak
sudo ln -s /mnt/raid1/mysql /var/lib/mysql
sudo chown -R mysql:mysql /mnt/raid1/mysql
sudo systemctl start mariadb
```

---

## Realizar backup en RAID

```bash
sudo mkdir -p /mnt/raid1/backups
sudo sh -c 'mysqldump -u root --databases jugueteria > /mnt/raid1/backups/jugueteria.sql'
ls -lh /mnt/raid1/backups/
cat /mnt/raid1/backups/jugueteria.sql | head
```

---

## Simulación de fallo y recuperación

### Simular fallo

```bash
sudo mdadm --manage /dev/md0 --fail /dev/sdb
sudo mdadm --manage /dev/md0 --remove /dev/sdb
```

Verificar:

```bash
cat /proc/mdstat
```

### Recuperar disco dañado

```bash
sudo mdadm --add /dev/md0 /dev/sdb
```

Observar recuperación:

```bash
cat /proc/mdstat
```

---

## Impacto en replicación

* Si el RAID está operativo, la replicación sigue normalmente.
* Si un disco falla pero RAID está activo, no se interrumpe el servicio.
* Si no hay RAID y falla el disco maestro, se pierde la replicación.

---

## Conclusión

RAID 1 refuerza la confiabilidad del sistema de base de datos `jugueteria`, complementando la estrategia de replicación entre maestro y esclavo.

</div>
```

---
