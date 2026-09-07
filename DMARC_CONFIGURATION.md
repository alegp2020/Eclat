# Configuración DMARC para eventoseclat.com

Esta guía te ayudará a implementar DMARC, SPF y DKIM para proteger tu dominio de email y obtener reportes sobre el uso legítimo y posibles abusos.

## 📋 Resumen de Configuración

### Dominio: eventoseclat.com
### Email: info@eventoseclat.com

## 🔐 Paso 1: Configurar SPF (Sender Policy Framework)

El SPF autoriza qué servidores pueden enviar emails desde tu dominio.

### Registro DNS SPF para eventoseclat.com:

```
TXT
@
v=spf1 include:_spf.google.com include:formsubmit.co ~all
```

**Explicación:**
- `v=spf1`: Versión de SPF
- `include:_spf.google.com`: Permite emails desde Gmail/Google Workspace
- `include:formsubmit.co`: Permite emails desde FormSubmit (tu formulario)
- `~all`: Soft fail - rechaza emails no autorizados pero permite entrega con advertencia

**Si usas otro proveedor de email, ajusta los includes:**
- Outlook/Office365: `include:spf.protection.outlook.com`
- Otros proveedores: Consulta su documentación SPF

## 🔑 Paso 2: Configurar DKIM (DomainKeys Identified Mail)

DKIM firma digitalmente tus emails para verificar que no han sido modificados.

### Opción A: Si usas Google Workspace:

1. Ve a tu admin de Google Workspace
2. Navega a Apps > Google Workspace > Gmail > Authentication
3. Genera una clave DKIM para eventoseclat.com
4. Google te dará un registro DNS TXT para agregar

**Formato típico del registro DKIM de Google:**
```
TXT
google._domainkey
v=DKIM1; k=rsa; p=CLAVE_PUBLICA_LARGA_AQUI
```

### Opción B: Si usas otro proveedor:

Consulta la documentación de tu proveedor de email para obtener los registros DKIM específicos.

### Opción C: Generar tu propia clave DKIM:

Si necesitas generar DKIM manualmente, usa herramientas como:
- https://www.dmarcly.com/dkim-generator
- https://www.port25.com/dkim-wizard

## 📊 Paso 3: Configurar DMARC

DMARC combina SPF y DKIM con políticas de acción y reportes.

### Registro DNS DMARC para eventoseclat.com:

```
TXT
_dmarc
v=DMARC1; p=none; rua=mailto:dmarc@eventoseclat.com; ruf=mailto:dmarc@eventoseclat.com; fo=1; aspf=s; adkim=s
```

**Explicación de parámetros:**
- `v=DMARC1`: Versión de DMARC
- `p=none`: Política inicial (ninguna acción, solo monitoreo)
- `rua=mailto:dmarc@eventoseclat.com`: Enviar reportes agregados a este email
- `ruf=mailto:dmarc@eventoseclat.com`: Enviar reportes forenses a este email
- `fo=1`: Enviar reportes si falla SPF o DKIM
- `aspf=s`: Modo estricto de alineación SPF
- `adkim=s`: Modo estricto de alineación DKIM

## 📈 Estrategia de Implementación DMARC

### Fase 1: Monitoreo (p=none) - 1-2 semanas
```
v=DMARC1; p=none; rua=mailto:dmarc@eventoseclat.com; ruf=mailto:dmarc@eventoseclat.com; fo=1; aspf=s; adkim=s
```
- Sin acción sobre emails fallidos
- Recibir reportes para identificar configuraciones incorrectas
- Ajustar SPF/DKIM según sea necesario

### Fase 2: Cuarentena (p=quarantine) - 2-4 semanas
```
v=DMARC1; p=quarantine; rua=mailto:dmarc@eventoseclat.com; ruf=mailto:dmarc@eventoseclat.com; fo=1; aspf=s; adkim=s; pct=50
```
- `p=quarantine`: Emails fallidos van a spam
- `pct=50`: Aplica política al 50% de emails (testing)
- Monitorear impacto en entregabilidad

### Fase 3: Rechazo (p=reject) - Después de confirmar
```
v=DMARC1; p=reject; rua=mailto:dmarc@eventoseclat.com; ruf=mailto:dmarc@eventoseclat.com; fo=1; aspf=s; adkim=s
```
- Rechaza emails que no pasen SPF/DKIM
- Máxima protección contra phishing y spoofing

## 🛠️ Herramientas de Verificación

Después de configurar los registros DNS, verifica con:

1. **MXToolbox SPF Lookup:**
   https://mxtoolbox.com/spf.aspx

2. **MXToolbox DKIM Lookup:**
   https://mxtoolbox.com/dkim.aspx

3. **DMARC Analyzer:**
   https://www.dmarcanalyzer.com/

4. **Google Postmaster Tools:**
   https://postmaster.google.com/

5. **Valimail DMARC Inspector:**
   https://dmarc.valimail.com/

## 📧 Configuración para FormSubmit

FormSubmit necesita estar autorizado en tu SPF:

```
v=spf1 include:_spf.google.com include:formsubmit.co ~all
```

FormSubmit no requiere DKIM ya que envía emails desde sus propios servidores.

## 📱 Servicios de Análisis de Reportes DMARC

Para analizar los reportes DMARC, considera:

1. **DMARC Analyzer** (https://www.dmarcanalyzer.com/)
   - Interfaz amigable
   - Alertas en tiempo real
   - Análisis detallado

2. **Valimail** (https://www.valimail.com/)
   - Solución empresarial
   - Automatización avanzada

3. **Postmark DMARC** (https://dmarc.postmarkapp.com/)
   - Gratuito para análisis básico
   - Interfaz simple

4. **Mailhardener** (https://mailhardener.com/)
   - Enfoque en seguridad
   - Reportes detallados

## ⚠️ Precauciones Importantes

1. **No saltes directamente a p=reject**
   - Empieza con p=none para monitorear
   - Ajusta gradualmente para evitar perder emails legítimos

2. **Monitorea los reportes regularmente**
   - Busca IPs desconocidas enviando emails
   - Identifica servicios que necesiten autorización

3. **Actualiza SPF cuando agregues nuevos servicios**
   - Cada nuevo servicio de email necesita include en SPF
   - Limpiar includes antiguos de servicios que ya no usas

4. **Mantén DKIM actualizado**
   - Rota las claves DKIM periódicamente (cada 6-12 meses)
   - Mantén copias de seguridad de claves privadas

## 🔄 Proceso de Actualización

1. **Agregar registros DNS** según esta guía
2. **Esperar propagación DNS** (puede tomar 24-48 horas)
3. **Verificar configuración** con herramientas mencionadas
4. **Monitorear reportes** durante 1-2 semanas
5. **Ajustar configuración** según reportes
6. **Escalar a p=quarantine** y luego p=reject**

## 📞 Soporte

Si necesitas ayuda con la implementación:
- Consulta la documentación de tu proveedor de DNS
- Revisa los reportes DMARC para identificar problemas específicos
- Considera contratar un especialista en seguridad de email para configuraciones complejas

## ✅ Checklist de Implementación

- [ ] Configurar registro SPF en DNS
- [ ] Configurar registro DKIM en DNS
- [ ] Configurar registro DMARC en DNS (p=none)
- [ ] Verificar propagación DNS
- [ ] Configurar análisis de reportes DMARC
- [ ] Monitorear reportes durante 1-2 semanas
- [ ] Ajustar SPF/DKIM según reportes
- [ ] Escalar a p=quarantine
- [ ] Monitorear durante 2-4 semanas
- [ ] Escalar a p=reject si todo está correcto