# OJS-Upacifico

Proyecto de configuracion y despliegue de OJS desarrollado por **Isaac Esteban Haro Torres**.

---

## Descripcion

Guia completa para instalar y levantar Open Journal Systems (OJS) en un servidor Ubuntu 24.04 LTS.

Versiones validadas al 2 de marzo de 2026.

---

## Descargas Oficiales

- Sitio oficial OJS (descarga): https://pkp.sfu.ca/software/ojs/download/
- Repositorio oficial OJS (releases): https://github.com/pkp/ojs/releases
- Paquete usado en esta guia (3.5.0-3): https://pkp.sfu.ca/ojs/download/ojs-3.5.0-3.tar.gz

---

## Caracteristicas

- Gestion editorial completa de revistas cientificas
- Flujo de revision por pares
- Publicacion de volumenes y numeros
- Soporte OAI-PMH e indexacion
- Gestion de roles editoriales

---

## Stack Tecnologico

- OJS 3.5.0-3
- PHP 8.2+
- MariaDB/MySQL
- Apache2
- Composer (opcional)
- Certbot para HTTPS

---

## Requisitos del Servidor

- Ubuntu Server 22.04 LTS
- 2 vCPU minimo
- 4 GB RAM minimo
- 40 GB de disco recomendado
- Dominio o subdominio apuntando al servidor

---

## Instalacion Paso a Paso (Servidor)

### 1. Actualizar sistema e instalar dependencias

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y apache2 mariadb-server unzip wget curl
sudo apt install -y php php-mysql php-xml php-mbstring php-gd php-curl php-zip php-intl php-bcmath php-cli libapache2-mod-php
```

### 2. Crear base de datos para OJS

```bash
sudo mysql -u root
```

Dentro de MariaDB:

```sql
CREATE DATABASE ojs_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'ojs_user'@'localhost' IDENTIFIED BY 'OjsPass2026!';
GRANT ALL PRIVILEGES ON ojs_db.* TO 'ojs_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### 3. Descargar OJS

```bash
cd /var/www
sudo wget https://pkp.sfu.ca/ojs/download/ojs-3.5.0-3.tar.gz
sudo tar -xzf ojs-3.5.0-3.tar.gz
sudo mv ojs-3.5.0-3 ojs
```

### 4. Crear directorio de archivos privados

```bash
sudo mkdir -p /var/ojs-files
sudo chown -R www-data:www-data /var/ojs-files
sudo chmod -R 750 /var/ojs-files
```

### 5. Permisos de OJS

```bash
sudo chown -R www-data:www-data /var/www/ojs
sudo find /var/www/ojs -type d -exec chmod 755 {} \;
sudo find /var/www/ojs -type f -exec chmod 644 {} \;
```

### 6. Configurar VirtualHost en Apache

```bash
sudo nano /etc/apache2/sites-available/ojs.conf
```

Contenido:

```apache
<VirtualHost *:80>
    ServerName revistas.tudominio.com
    DocumentRoot /var/www/ojs

    <Directory /var/www/ojs>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/ojs_error.log
    CustomLog ${APACHE_LOG_DIR}/ojs_access.log combined
</VirtualHost>
```

Activar sitio:

```bash
sudo a2enmod rewrite
sudo a2ensite ojs.conf
sudo a2dissite 000-default.conf
sudo systemctl reload apache2
```

### 7. Configurar `config.inc.php`

```bash
cd /var/www/ojs
sudo cp config.TEMPLATE.inc.php config.inc.php
sudo nano config.inc.php
```

Ajustar valores principales:

```ini
base_url = "http://revistas.tudominio.com"
files_dir = /var/ojs-files
driver = mysqli
host = localhost
username = ojs_user
password = OjsPass2026!
name = ojs_db
```

### 8. Ejecutar instalacion web

Abrir en navegador:

`http://revistas.tudominio.com`

Completar formulario:

- Usuario administrador
- Contraseña segura
- Idioma principal
- Datos de BD

### 9. Habilitar HTTPS con Let's Encrypt

```bash
sudo apt install -y certbot python3-certbot-apache
sudo certbot --apache -d revistas.tudominio.com
```

---

## Tareas Programadas (Cron)

OJS usa tareas programadas para notificaciones e indexaciones.

```bash
sudo crontab -u www-data -e
```

Agregar:

```bash
*/5 * * * * php /var/www/ojs/tools/runScheduledTasks.php
```

---

## Comandos de Operacion

- Reiniciar Apache: `sudo systemctl restart apache2`
- Revisar logs OJS: `sudo tail -f /var/log/apache2/ojs_error.log`
- Revisar PHP: `php -v`
- Backup BD: `mysqldump -u ojs_user -p ojs_db > /var/backups/ojs_db.sql`

---

## Desarrollado por Isaac Esteban Haro Torres

**Ingeniero en Sistemas | Full Stack | Automatizacion | Data**

- Email: zackharo1@gmail.com
- WhatsApp: 098805517
- GitHub: https://github.com/ieharo1
- Portafolio: https://ieharo1.github.io/portafolio-isaac.haro/

---

Copyright 2026 Isaac Esteban Haro Torres - Todos los derechos reservados.




