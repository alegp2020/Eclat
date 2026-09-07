# Configuración de Seguridad Avanzada

Este documento explica cómo implementar las medidas de seguridad avanzadas en tu servidor para máxima protección.

## Headers de Seguridad Implementados

### 1. Content Security Policy (CSP)
Protege contra ataques XSS, inyección de datos y otros vectores de ataque.

### 2. X-Frame-Options
Protege contra ataques de clickjacking al evitar que tu sitio sea incrustado en iframes.

### 3. X-Content-Type-Options
Evita que los navegadores hagan MIME-sniffing de respuestas.

### 4. X-XSS-Protection
Activa el filtro XSS del navegador.

### 5. Strict-Transport-Security (HSTS)
Fuerza conexiones HTTPS y protege contra degradación de protocolo.

### 6. Referrer Policy
Controla qué información de referencia se envía con las solicitudes.

### 7. Permissions Policy
Controla qué características del navegador pueden usar los sitios web.

## Implementación por Servidor

### Apache (.htaccess)
```apache
<IfModule mod_headers.c>
    # Content Security Policy
    Header always set Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' https://cdnjs.cloudflare.com https://formsubmit.co; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com https://cdnjs.cloudflare.com; font-src 'self' https://fonts.gstatic.com https://cdnjs.cloudflare.com; img-src 'self' data: https:; connect-src 'self' https://formsubmit.co; frame-src 'self' https://formsubmit.co; base-uri 'self'; form-action 'self' https://formsubmit.co; upgrade-insecure-requests"
    
    # X-Frame-Options
    Header always set X-Frame-Options "DENY"
    
    # X-Content-Type-Options
    Header always set X-Content-Type-Options "nosniff"
    
    # X-XSS-Protection
    Header always set X-XSS-Protection "1; mode=block"
    
    # Strict-Transport-Security
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
    
    # Referrer Policy
    Header always set Referrer-Policy "strict-origin-when-cross-origin"
    
    # Permissions Policy
    Header always set Permissions-Policy "geolocation=(), microphone=(), camera=(), payment=(), usb=(), magnetometer=(), gyroscope=(), accelerometer=(), interest-cohort=()"
    
    # Cross-Origin Headers
    Header always set Cross-Origin-Opener-Policy "same-origin"
    Header always set Cross-Origin-Resource-Policy "same-origin"
    Header always set Cross-Origin-Embedder-Policy "require-corp"
    
    # Additional Security
    Header always set X-Permitted-Cross-Domain-Policies "none"
    Header always set X-DNS-Prefetch-Control "off"
</IfModule>
```

### Nginx (nginx.conf)
```nginx
server {
    # Content Security Policy
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' https://cdnjs.cloudflare.com https://formsubmit.co; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com https://cdnjs.cloudflare.com; font-src 'self' https://fonts.gstatic.com https://cdnjs.cloudflare.com; img-src 'self' data: https:; connect-src 'self' https://formsubmit.co; frame-src 'self' https://formsubmit.co; base-uri 'self'; form-action 'self' https://formsubmit.co; upgrade-insecure-requests" always;
    
    # X-Frame-Options
    add_header X-Frame-Options "DENY" always;
    
    # X-Content-Type-Options
    add_header X-Content-Type-Options "nosniff" always;
    
    # X-XSS-Protection
    add_header X-XSS-Protection "1; mode=block" always;
    
    # Strict-Transport-Security
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
    
    # Referrer Policy
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    
    # Permissions Policy
    add_header Permissions-Policy "geolocation=(), microphone=(), camera=(), payment=(), usb=(), magnetometer=(), gyroscope=(), accelerometer=(), interest-cohort=()" always;
    
    # Cross-Origin Headers
    add_header Cross-Origin-Opener-Policy "same-origin" always;
    add_header Cross-Origin-Resource-Policy "same-origin" always;
    add_header Cross-Origin-Embedder-Policy "require-corp" always;
    
    # Additional Security
    add_header X-Permitted-Cross-Domain-Policies "none" always;
    add_header X-DNS-Prefetch-Control "off" always;
}
```

### Vercel (vercel.json)
```json
{
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "Content-Security-Policy",
          "value": "default-src 'self'; script-src 'self' 'unsafe-inline' https://cdnjs.cloudflare.com https://formsubmit.co; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com https://cdnjs.cloudflare.com; font-src 'self' https://fonts.gstatic.com https://cdnjs.cloudflare.com; img-src 'self' data: https:; connect-src 'self' https://formsubmit.co; frame-src 'self' https://formsubmit.co; base-uri 'self'; form-action 'self' https://formsubmit.co; upgrade-insecure-requests"
        },
        {
          "key": "X-Frame-Options",
          "value": "DENY"
        },
        {
          "key": "X-Content-Type-Options",
          "value": "nosniff"
        },
        {
          "key": "X-XSS-Protection",
          "value": "1; mode=block"
        },
        {
          "key": "Strict-Transport-Security",
          "value": "max-age=31536000; includeSubDomains; preload"
        },
        {
          "key": "Referrer-Policy",
          "value": "strict-origin-when-cross-origin"
        },
        {
          "key": "Permissions-Policy",
          "value": "geolocation=(), microphone=(), camera=(), payment=(), usb=(), magnetometer=(), gyroscope=(), accelerometer=(), interest-cohort=()"
        },
        {
          "key": "Cross-Origin-Opener-Policy",
          "value": "same-origin"
        },
        {
          "key": "Cross-Origin-Resource-Policy",
          "value": "same-origin"
        },
        {
          "key": "Cross-Origin-Embedder-Policy",
          "value": "require-corp"
        },
        {
          "key": "X-Permitted-Cross-Domain-Policies",
          "value": "none"
        },
        {
          "key": "X-DNS-Prefetch-Control",
          "value": "off"
        }
      ]
    }
  ]
}
```

### Netlify (_headers)
```
/*  Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline' https://cdnjs.cloudflare.com https://formsubmit.co; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com https://cdnjs.cloudflare.com; font-src 'self' https://fonts.gstatic.com https://cdnjs.cloudflare.com; img-src 'self' data: https:; connect-src 'self' https://formsubmit.co; frame-src 'self' https://formsubmit.co; base-uri 'self'; form-action 'self' https://formsubmit.co; upgrade-insecure-requests
/*  X-Frame-Options: DENY
/*  X-Content-Type-Options: nosniff
/*  X-XSS-Protection: 1; mode=block
/*  Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
/*  Referrer-Policy: strict-origin-when-cross-origin
/*  Permissions-Policy: geolocation=(), microphone=(), camera=(), payment=(), usb=(), magnetometer=(), gyroscope=(), accelerometer=(), interest-cohort=()
/*  Cross-Origin-Opener-Policy: same-origin
/*  Cross-Origin-Resource-Policy: same-origin
/*  Cross-Origin-Embedder-Policy: require-corp
/*  X-Permitted-Cross-Domain-Policies: none
/*  X-DNS-Prefetch-Control: off
```

## Sistema de Cookies Seguro

El sistema de cookies implementado incluye:

1. **Cookies con atributos de seguridad:**
   - `Secure`: Solo se envían sobre HTTPS
   - `HttpOnly`: No accesibles via JavaScript
   - `SameSite=Strict`: Protección contra CSRF

2. **Gestión de consentimiento:**
   - Aceptación explícita del usuario
   - Opciones granulares por tipo de cookie
   - Almacenamiento seguro de preferencias

3. **Cumplimiento GDPR:**
   - Información clara sobre el uso de cookies
   - Enlace a política de cookies detallada
   - Opción de rechazar cookies no esenciales

## Recomendaciones Adicionales

1. **HTTPS Obligatorio:** Asegúrate de que tu sitio use HTTPS con certificado SSL válido.
2. **Actualizaciones Regulares:** Mantén todos los componentes y dependencias actualizados.
3. **Monitoreo:** Implementa sistemas de monitoreo de seguridad.
4. **Backups:** Realiza backups regulares de tu sitio.
5. **Pruebas de Seguridad:** Realiza pruebas de penetración periódicas.

## Verificación de Seguridad

Puedes verificar la configuración de seguridad usando herramientas como:
- Security Headers (https://securityheaders.com/)
- Mozilla Observatory (https://observatory.mozilla.org/)
- SSL Labs (https://www.ssllabs.com/ssltest/)