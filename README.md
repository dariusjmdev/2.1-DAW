# Módulos de Apache

**Autor:** Darius Joanito Marinescu

---

## 1. Introducción

Apache HTTP Server utiliza una arquitectura modular que permite añadir funcionalidades específicas mediante módulos cargables. Esto permite habilitar únicamente las características necesarias para cada entorno, mejorando la seguridad, el rendimiento y la administración del servidor.

El objetivo de esta práctica es habilitar los módulos necesarios para futuras configuraciones de proxy inverso, HTTPS, reescritura de URLs y gestión avanzada de cabeceras HTTP.

Los módulos solicitados son:

- proxy
- proxy_http
- ssl
- rewrite
- headers

---

## 2. Comprobación inicial

Antes de realizar modificaciones se comprobó la lista de módulos activos mediante:

```bash
apache2ctl -M
```

<img width="916" height="728" alt="Captura de pantalla 2026-11-11 165458" src="https://github.com/user-attachments/assets/4c4491fb-bc8d-4997-bc7d-1089e842cce2" />


---

## 3. Activación de módulos

Se activaron los módulos solicitados mediante los siguientes comandos:

```bash
sudo a2enmod proxy
sudo a2enmod proxy_http
sudo a2enmod ssl
sudo a2enmod rewrite
sudo a2enmod headers
```

Apache confirmó la habilitación correcta de todos los módulos.

| Módulo | Función |
|--------|---------|
| proxy | Permite a Apache actuar como proxy hacia otros servicios |
| proxy_http | Añade soporte de proxy para tráfico HTTP y HTTPS |
| ssl | Habilita conexiones seguras TLS/SSL |
| rewrite | Permite la reescritura avanzada de URLs |
| headers | Permite modificar cabeceras HTTP |

<img width="615" height="92" alt="image" src="https://github.com/user-attachments/assets/8fd9e34c-877d-4e36-89c7-27ea5387da33" />


---

## 4. Reinicio del servicio Apache

Tras la activación de los módulos se reinició el servicio:

```bash
sudo systemctl restart apache2
```

Posteriormente se comprobó el estado del servicio:

```bash
sudo systemctl status apache2
```

Resultado esperado:

```text
active (running)
```

<img width="910" height="87" alt="image" src="https://github.com/user-attachments/assets/f9456526-ce8e-4dfc-8a65-eaa386e85d1a" />


---

## 5. Verificación de configuración

Se realizó una validación completa de la configuración mediante:

```bash
sudo apache2ctl configtest
```

Resultado obtenido:

```text
Syntax OK
```

Es posible que durante la validación aparezca el siguiente aviso:

```text
AH00558: Could not reliably determine the server's fully qualified domain name
```

Este mensaje es únicamente informativo y no afecta al funcionamiento del servicio. Se puede evitar definiendo la directiva `ServerName` en la configuración global de Apache.

<img width="917" height="112" alt="image" src="https://github.com/user-attachments/assets/2d581c95-2455-452e-860f-9026e2326af8" />


---

## 6. Verificación de módulos cargados

Para verificar que los módulos permanecen activos tras el reinicio se ejecutó:

```bash
apache2ctl -M | grep -E "proxy|proxy_http|ssl|rewrite|headers"
```

Resultado esperado:

```text
headers_module
proxy_module
proxy_http_module
rewrite_module
ssl_module
```

La presencia de los cinco módulos confirma que la configuración se ha aplicado correctamente.

<img width="911" height="260" alt="Captura de pantalla 2026-11-11 165636" src="https://github.com/user-attachments/assets/962eab86-0183-45ef-8844-17ea5616e3cc" />


---

## 7. Explicación de los módulos

### 7.1. Módulo proxy

Nombre interno:

```text
proxy_module
```

**Función:** permite que Apache actúe como intermediario entre el cliente y otro servidor.

**Ejemplo:**

```apache
ProxyPass / http://127.0.0.1:8000/
ProxyPassReverse / http://127.0.0.1:8000/
```

**Casos de uso:**

- Proxy inverso.
- Balanceadores de carga.
- Publicación de aplicaciones internas.

---

### 7.2. Módulo proxy_http

Nombre interno:

```text
proxy_http_module
```

**Función:** añade compatibilidad con el protocolo HTTP (y HTTPS hacia el backend) al módulo proxy.

**Ejemplo:**

```apache
ProxyPass /api http://127.0.0.1:8080/api
ProxyPassReverse /api http://127.0.0.1:8080/api
```

**Casos de uso:**

- Aplicaciones FastAPI.
- Aplicaciones Flask.
- Aplicaciones Django.
- Aplicaciones Node.js.

---

### 7.3. Módulo ssl

Nombre interno:

```text
ssl_module
```

**Función:** permite habilitar comunicaciones cifradas mediante certificados digitales.

**Ejemplo:**

```apache
<VirtualHost *:443>
    SSLEngine on
    SSLCertificateFile    /etc/ssl/certs/certificado.crt
    SSLCertificateKeyFile /etc/ssl/private/certificado.key
</VirtualHost>
```

**Casos de uso:**

- HTTPS.
- Certificados Let's Encrypt.
- Protección de credenciales y datos en tránsito.

---

### 7.4. Módulo rewrite

Nombre interno:

```text
rewrite_module
```

**Función:** permite modificar URLs mediante reglas avanzadas.

**Ejemplo:**

```apache
RewriteEngine On
RewriteRule ^productos/(.*)$ producto.php?id=$1 [L,QSA]
```

**Casos de uso:**

- URLs amigables.
- Redirecciones.
- SEO.

---

### 7.5. Módulo headers

Nombre interno:

```text
headers_module
```

**Función:** permite modificar o añadir cabeceras HTTP en las peticiones y respuestas.

**Ejemplo:**

```apache
Header always set X-Frame-Options "SAMEORIGIN"
Header always set X-Content-Type-Options "nosniff"
```

**Casos de uso:**

- Seguridad.
- Control de caché.
- Políticas CSP.

---

## 8. Módulos adicionales relevantes

Además de los módulos requeridos, Apache dispone de otros módulos ampliamente utilizados.

### deflate

Permite comprimir contenidos HTTP para reducir el consumo de ancho de banda.

```bash
sudo a2enmod deflate
```

### expires

Permite definir políticas de caché para los navegadores.

```bash
sudo a2enmod expires
```

### http2

Añade compatibilidad con el protocolo HTTP/2.

```bash
sudo a2enmod http2
```

### auth_basic

Permite la autenticación básica mediante usuario y contraseña.

```bash
sudo a2enmod auth_basic
```

---

