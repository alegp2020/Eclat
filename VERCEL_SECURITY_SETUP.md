# Configuración de Headers de Seguridad en Vercel

Esta guía explica cómo implementar todos los headers de seguridad en Vercel para resolver los warnings que estás viendo.

## 📋 Headers Implementados en vercel.json

### 1. Strict-Transport-Security (HSTS)
```json
"key": "Strict-Transport-Security",
"value": "max-age=63072000; includeSubDomains; preload"
```
**Propósito:** Fuerza conexiones HTTPS y protege contra degradación de protocolo
**Parámetros:**
- `max-age=63072000`: 2 años en segundos (recomendado para preload)
- `includeSubDomains`: Aplica a todos los subdominios
- `preload`: Permite incluir en la lista HSTS preload de Chrome

### 2. Content-Security-Policy (CSP)
```json
"key": "Content-Security-Policy",
"value": "default-src 'self'; script-src 'self' 'unsafe-inline' https://cdnjs.cloudflare.com https://formsubmit.co; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com https://cdnjs.cloudflare.com; font-src 'self' https://fonts.gstatic.com https://cdnjs.cloudflare.com; img-src 'self' data: https:; connect-src 'self' https://formsubmit.co; frame-src 'self' https://formsubmit.co; base-uri 'self'; form-action 'self' https://formsubmit.co; upgrade-insecure-requests"
```
**Propósito:** Protege contra XSS, inyección de datos y otros ataques
**Directivas:**
- `default-src 'self'`: Solo permite recursos del mismo dominio por defecto
- `script-src`: Permite scripts de fuentes específicas
- `style-src`: Permite estilos de fuentes específicas
- `font-src`: Permite fuentes de Google Fonts y CDN
- `img-src`: Permite imágenes del dominio y HTTPS
- `connect-src`: Permite conexiones a FormSubmit
- `frame-src`: Permite iframes de FormSubmit
- `upgrade-insecure-requests`: Actualiza HTTP a HTTPS automáticamente

### 3. X-Content-Type-Options
```json
"key": "X-Content-Type-Options",
"value": "nosniff"
```
**Propósito:** Evita que los navegadores hagan MIME-sniffing de respuestas

### 4. X-XSS-Protection
```json
"key": "X-XSS-Protection",
"value": "1; mode=block"
```
**Propósito:** Activa el filtro XSS del navegador
**Nota:** Aunque está obsoleto, sigue siendo útil para navegadores antiguos

### 5. Referrer-Policy
```json
"key": "Referrer-Policy",
"value": "strict-origin-when-cross-origin"
```
**Propósito:** Controla qué información de referencia se envía con las solicitudes

### 6. Permissions-Policy
```json
"key": "Permissions-Policy",
"value": "geolocation=(), microphone=(), camera=(), payment=(), usb=(), magnetometer=(), gyroscope=(), accelerometer=(), interest-cohort=()"
```
**Propósito:** Controla qué características del navegador pueden usar los sitios web
**Características bloqueadas:**
- Geolocalización
- Micrófono
- Cámara
- Pagos
- USB
- Magnetómetro
- Giróscopo
- Acelerómetro
- Cohortes de interés (FLoC)

### 7. X-Frame-Options
```json
"key": "X-Frame-Options",
"value": "DENY"
```
**Propósito:** Protege contra ataques de clickjacking
**DENY:** Prohíbe cualquier iframe de tu sitio

### 8. Cross-Origin-Opener-Policy
```json
"key": "Cross-Origin-Opener-Policy",
"value": "same-origin"
```
**Propósito:** Protege contra ataques cross-origin window

### 9. Cross-Origin-Embedder-Policy
```json
"key": "Cross-Origin-Embedder-Policy",
"value": "require-corp"
```
**Propósito:** Requiere que los documentos embebidos tengan un header CORP

### 10. Cross-Origin-Resource-Policy
```json
"key": "Cross-Origin-Resource-Policy",
"value": "same-origin"
```
**Propósito:** Protege contra ataques cross-origin resource

### 11. X-Permitted-Cross-Domain-Policies
```json
"key": "X-Permitted-Cross-Domain-Policies",
"value": "none"
```
**Propósito:** Controla políticas de dominio cruzado (Flash, etc.)

### 12. X-DNS-Prefetch-Control
```json
"key": "X-DNS-Prefetch-Control",
"value": "off"
```
**Propósito:** Controla el prefetching de DNS para mayor privacidad

## 🚀 Cómo Implementar en Vercel

### Paso 1: El archivo ya está creado
✅ `vercel.json` ya está creado con todos los headers

### Paso 2: Subir a GitHub
```bash
git add vercel.json
git commit -m "Agregar headers de seguridad completos en vercel.json"
git push origin main
```

### Paso 3: Vercel detectará automáticamente los cambios
- Vercel lee automáticamente el archivo `vercel.json`
- Los headers se aplican en el próximo despliegue
- No requiere configuración manual en el dashboard

### Paso 4: Verificar implementación
Después del despliegue, verifica en:
- https://securityheaders.com/?q=eventoseclat.vercel.app&followRedirects=on
- https://observatory.mozilla.org/analyze/eventoseclat.vercel.app

## 📊 Verificación de Headers

### Antes de la implementación:
- ❌ HSTS_NO_INCLUDE_SUBDOMAINS
- ❌ HSTS_NO_PRELOAD
- ❌ CSP_REPORT_ONLY
- ❌ X_CONTENT_TYPE_OPTIONS_MISSING
- ❌ XSS_PROTECTION_MISSING
- ❌ REFERRER_POLICY_MISSING
- ❌ PERMISSIONS_POLICY_MISSING
- ❌ X_FRAME_OPTIONS_MISSING
- ❌ CROSS_ORIGIN_OPENER_POLICY_MISSING
- ❌ CROSS_ORIGIN_EMBEDDER_POLICY_MISSING
- ❌ CROSS_ORIGIN_RESOURCE_POLICY_MISSING

### Después de la implementación:
- ✅ HSTS con includeSubDomains y preload
- ✅ CSP implementado correctamente
- ✅ X-Content-Type-Options implementado
- ✅ X-XSS-Protection implementado
- ✅ Referrer-Policy implementado
- ✅ Permissions-Policy implementado
- ✅ X-Frame-Options implementado
- ✅ Cross-Origin headers implementados

## ⚠️ Precauciones Importantes

### CSP podría bloquear recursos
Si algún recurso deja de funcionar después de implementar CSP:
1. Abre las DevTools (F12)
2. Ve a la pestaña Console
3. Busca errores de CSP
4. Agrega el dominio bloqueado a la directiva correspondiente en vercel.json

### Ejemplo de ajuste de CSP:
Si necesitas agregar un nuevo dominio a script-src:
```json
"script-src 'self' 'unsafe-inline' https://cdnjs.cloudflare.com https://formsubmit.co https://nuevo-dominio.com"
```

### HSTS Preload
Para incluir tu dominio en la lista HSTS preload de Chrome:
1. Implementa HSTS durante al menos 2 meses
2. Ve a https://hstspreload.org/
3. Sigue las instrucciones para registrar tu dominio

## 🔧 Ajustes de CSP para Sitios Externos

Si necesitas permitir recursos de sitios externos, ajusta las directivas correspondientes:

### Para Google Analytics (si lo implementas en el futuro):
```json
"connect-src 'self' https://formsubmit.co https://www.google-analytics.com"
```

### Para servicios de terceros:
Agrega los dominios a las directivas correspondientes en vercel.json

## 📱 Pruebas de Seguridad

Después de implementar, prueba con:

1. **Security Headers:**
   https://securityheaders.com/?q=eventoseclat.vercel.app

2. **Mozilla Observatory:**
   https://observatory.mozilla.org/analyze/eventoseclat.vercel.app

3. **SSL Labs:**
   https://www.ssllabs.com/ssltest/analyze.html?d=eventoseclat.vercel.app

## 🎯 Resolución de Problemas Comunes

### Problema: "Mixed Content" después de implementar CSP
**Solución:** Asegúrate de que todos los recursos usen HTTPS
- Cambia `http://` a `https://` en todos los recursos
- La directiva `upgrade-insecure-requests` ayuda con esto

### Problema: Scripts externos no funcionan
**Solución:** Agrega los dominios a la directiva script-src en vercel.json

### Problema: Fuentes no cargan
**Solución:** Verifica que los dominios de fuentes estén en font-src

## ✅ Checklist de Implementación

- [ ] vercel.json creado con todos los headers
- [ ] Archivo subido a GitHub
- [ ] Vercel desplegado con nuevos headers
- [ ] Verificar en Security Headers
- [ ] Verificar en Mozilla Observatory
- [ ] Probar funcionalidad del sitio
- [ ] Verificar CSP no bloquea recursos necesarios
- [ ] Monitorear logs de Vercel por errores de CSP

## 📞 Soporte

Si encuentras problemas después de implementar:
1. Revisa los logs de Vercel en el dashboard
2. Verifica la Consola del navegador por errores de CSP
3. Consulta la documentación de Vercel: https://vercel.com/docs/concepts/projects/project-configuration