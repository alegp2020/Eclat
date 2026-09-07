# Guía Rápida de Implementación DMARC

## 🚀 Implementación en 5 Pasos

### Paso 1: Acceder a tu proveedor de DNS
- **Si usas Cloudflare:** Ve a DNS > Records
- **Si usas GoDaddy:** Ve a DNS Management > Records
- **Si usas Namecheap:** Ve to Advanced DNS
- **Si usas otro:** Busca "DNS Records" o "Zone Editor"

### Paso 2: Agregar registro SPF
**Tipo:** TXT  
**Nombre:** @  
**Valor:** `v=spf1 include:_spf.google.com include:formsubmit.co ~all`  
**TTL:** 3600 (o predeterminado)

### Paso 3: Agregar registro DKIM
**Si usas Google Workspace:**
1. Ve a Admin Console > Apps > Google Workspace > Gmail > Authentication
2. Haz clic en "Generate New Key"
3. Selecciona eventoseclat.com
4. Copia el registro DNS que te da Google

**Tipo:** TXT  
**Nombre:** `google._domainkey` (o el que te dé Google)  
**Valor:** El valor largo que te da Google  
**TTL:** 3600

### Paso 4: Agregar registro DMARC
**Tipo:** TXT  
**Nombre:** `_dmarc`  
**Valor:** `v=DMARC1; p=none; rua=mailto:dmarc@eventoseclat.com; ruf=mailto:dmarc@eventoseclat.com; fo=1; aspf=s; adkim=s`  
**TTL:** 3600

### Paso 5: Verificar configuración
Ve a: https://dmarc.valimail.com/  
Ingresa: eventoseclat.com  
Verifica que todos los registros aparezcan como válidos

## ⏱️ Timeline de Implementación

**Día 1:** Agregar los 3 registros DNS  
**Día 2-3:** Esperar propagación DNS (24-48 horas)  
**Día 4:** Verificar configuración con herramientas  
**Semana 2-3:** Monitorear reportes DMARC  
**Semana 4:** Ajustar configuración según reportes  
**Semana 5-6:** Escalar a p=quarantine  
**Semana 7-8:** Escalar a p=reject

## 📈 Progresión de Políticas DMARC

### Inicio (Día 1):
```
v=DMARC1; p=none; rua=mailto:dmarc@eventoseclat.com; ruf=mailto:dmarc@eventoseclat.com; fo=1; aspf=s; adkim=s
```

### Después de 2 semanas (si todo está bien):
```
v=DMARC1; p=quarantine; rua=mailto:dmarc@eventoseclat.com; ruf=mailto:dmarc@eventoseclat.com; fo=1; aspf=s; adkim=s; pct=50
```

### Después de 4 semanas (si no hay problemas):
```
v=DMARC1; p=quarantine; rua=mailto:dmarc@eventoseclat.com; ruf=mailto:dmarc@eventoseclat.com; fo=1; aspf=s; adkim=s
```

### Final (después de 6-8 semanas):
```
v=DMARC1; p=reject; rua=mailto:dmarc@eventoseclat.com; ruf=mailto:dmarc@eventoseclat.com; fo=1; aspf=s; adkim=s
```

## 🛠️ Herramientas Útiles

**Verificación rápida:**
- https://dmarc.valimail.com/ (Mejor para verificación general)
- https://mxtoolbox.com/dmarc.aspx (Verificación DMARC)
- https://www.mail-tester.com/ (Prueba de email)

**Análisis de reportes:**
- https://dmarc.postmarkapp.com/ (Gratis, análisis básico)
- https://www.dmarcanalyzer.com/ (Pago, análisis avanzado)

**Si usas Google Workspace:**
- https://postmaster.google.com/ (Requiere cuenta Google)

## ⚠️ Problemas Comunes y Soluciones

**Problema:** "SPF record not found"  
**Solución:** Espera 24-48 horas para propagación DNS, verifica que el nombre sea "@" y no el dominio completo

**Problema:** "DKIM record not found"  
**Solución:** Verifica que el nombre sea exactamente el que te dio tu proveedor de email (ej: google._domainkey)

**Problema:** "DMARC record not found"  
**Solución:** Verifica que el nombre sea "_dmarc" (con guion bajo al principio)

**Problema:** No recibo reportes DMARC  
**Solución:** 
- Verifica que el email dmarc@eventoseclat.com exista
- Revisa carpeta de spam
- Algunos proveedores tardan 24-48 horas en enviar primer reporte

## 📞 ¿Necesitas Ayuda?

**Si usas Cloudflare:**
- Soporte: https://support.cloudflare.com/

**Si usas Google Workspace:**
- Documentación: https://support.google.com/a/topic/9257709

**General:**
- Comunidad DMARC: https://dmarc.org/wiki/Community

## ✅ Checklist Final

Antes de escalar a p=reject, verifica:
- [ ] SPF aparece como válido en herramientas de verificación
- [ ] DKIM aparece como válido en herramientas de verificación  
- [ ] DMARC aparece como válido en herramientas de verificación
- [ ] Estás recibiendo reportes DMARC en dmarc@eventoseclat.com
- [ ] No hay IPs desconocidas enviando emails en reportes
- [ ] FormSubmit está autorizado en SPF
- [ ] Tu proveedor de email está autorizado en SPF
- [ ] Has monitoreado durante al menos 2 semanas
- [ ] No has perdido emails legítimos durante el monitoreo