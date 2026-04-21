# 🌐 PokéDex — Despliegue en la Nube
Parcial de Sistemas Distribuidos
Estudiante: Daniel David Henríquez Julio
Institución: Fundación Universitaria Tecnológico Comfenalco
URL pública: https://www.danieldhj.dev

📋 Descripción del Proyecto
PokéDex es una aplicación web estática desarrollada con Angular que permite a los usuarios explorar diferentes especies de Pokémon, visualizando información detallada sobre sus características y habilidades. Este repositorio documenta el proceso de despliegue en la nube pública de Microsoft Azure, incluyendo configuración de seguridad, dominio personalizado y auditorías de seguridad.


---

## 📘 Creación de cuenta en Azure for Students
Este documento describe el proceso que se realizó para crear una cuenta en Azure for Students, un programa de Microsoft que ofrece $100 USD créditos gratuitos con vigencia de 12 meses y servicios en la nube para estudiantes.

---

## 🧾 Requisitos utilizados

Para la creación de la cuenta se contó con los siguientes elementos:

- Correo institucional activo
- Cuenta de Microsoft
- Acceso a internet

---

## 🚀 Paso 1: Acceso al sitio

Se ingresó al sitio oficial de Azure for Students mediante el navegador web.  
Una vez en la página principal, se seleccionó la opción **"Empezar gratis"** para iniciar el proceso de registro.

---

## 🔐 Paso 2: Inicio de sesión

Se procedió a iniciar sesión con una cuenta de Microsoft previamente suministrada por la universidad Fundacion Universitaria Tecnologico Comfenalco.  

---

## 🎓 Paso 3: Verificación de estudiante

Se ingresó el correo institucional para validar la condición de estudiante.  
Luego:

1. Se recibió un código de verificación en el correo
2. Se ingresó el código en la plataforma
3. Se confirmó exitosamente la verificación

---

## 💳 Paso 4: Activación de la suscripción

Se aceptaron los términos y condiciones del servicio.  
No fue necesario registrar una tarjeta de crédito.

Posteriormente, la plataforma habilitó automáticamente los beneficios para estudiantes.

---

## ☁️ Paso 5: Acceso al portal de Azure

Una vez activada la cuenta, se accedió al portal de Azure desde el navegador.  
Desde este panel se pudo comenzar a explorar y utilizar los diferentes servicios disponibles.

---

## 📦 Resultados obtenidos

Al finalizar el proceso:

- La cuenta de Azure quedó activa
- Se habilitaron créditos gratuitos para uso académico
- Se obtuvo acceso al panel de administración de servicios en la nube

---

## ✅ Conclusión

El proceso de creación de la cuenta en Azure for Students se realizó de manera satisfactoria.  
Se logró acceder a la plataforma sin inconvenientes, permitiendo el uso de herramientas en la nube para el desarrollo de proyectos académicos.

## 🚀 Resumen del Despliegue

> El proceso completo de despliegue está documentado en [`Despliegue.md`](./Despliegue.md).

| Elemento | Valor |
|---|---|
| Plataforma | Azure Static Web Apps |
| Repositorio | github.com/Danieldavidhenriquezjulio/pokedex |
| URL pública | https://www.danieldhj.dev |
| Dominio registrado en | Name.com |
| SSL | Certificado gratuito gestionado por Azure |
| Calificación securityheaders.com | **A** |
| Calificación SSL Labs | **A+** |

---

## 🔒 Seguridad Implementada

Se configuraron los siguientes encabezados HTTP de seguridad en `staticwebapp.config.json`:

| Encabezado | Propósito |
|---|---|
| `Content-Security-Policy` | Controla qué recursos puede cargar la app (previene XSS) |
| `Strict-Transport-Security` | Fuerza el uso de HTTPS durante 1 año |
| `X-Content-Type-Options: nosniff` | Evita la detección automática de tipos de archivo |
| `X-Frame-Options: DENY` | Previene ataques de clickjacking mediante iframes |
| `Referrer-Policy: no-referrer` | Protege la privacidad del usuario en navegación |
| `Permissions-Policy` | Deshabilita acceso a cámara, micrófono y geolocalización |

### Resultados de auditorías

<img width="1896" height="865" alt="image" src="https://github.com/user-attachments/assets/4e428b7d-5b6a-453b-b5a8-1da8f81a378e" />


- **securityheaders.com:** Calificación **A** *(limitada a A en lugar de A+ por el uso de `'unsafe-inline'` en `script-src`, requerido por Angular en tiempo de build)*

- <img width="1368" height="784" alt="image" src="https://github.com/user-attachments/assets/259cfa37-b72d-45c0-8ce9-6d0a812c5d9e" />

- **SSL Labs:** Calificación **A+** con soporte para TLS 1.3 y TLS 1.2, HSTS activo y Forward Secrecy habilitado.

---

## 🌍 Dominio Personalizado

Se registró el dominio **danieldhj.dev** en Name.com y se vinculó a Azure Static Web Apps mediante registros DNS (CNAME para `www` y TXT para el dominio raíz). Azure generó automáticamente certificados SSL para ambas variantes:

- https://danieldhj.dev
- https://www.danieldhj.dev

---

## 🧠 Reflexión Técnica

### 1. ¿Qué vulnerabilidades previenen los encabezados implementados?

Cada header actúa como una capa de defensa diferente. El `Content-Security-Policy` es el más crítico: evita que scripts maliciosos se inyecten y ejecuten en el navegador del usuario (XSS). `X-Frame-Options: DENY` elimina el riesgo de *clickjacking*, donde un atacante superpone la app dentro de un iframe invisible para engañar al usuario. `Strict-Transport-Security` garantiza que toda comunicación sea cifrada, previniendo ataques de tipo *man-in-the-middle*. `X-Content-Type-Options` impide que archivos maliciosos disfrazados (como una imagen que en realidad es un script) sean ejecutados por el navegador. Finalmente, `Referrer-Policy` y `Permissions-Policy` reducen la superficie de ataque limitando qué información se filtra y qué APIs del dispositivo puede solicitar la app.

### 2. ¿Qué aprendí sobre la relación entre despliegue y seguridad web?

Antes de esta actividad, concebía el despliegue y la seguridad como etapas separadas. El ejercicio demostró que son inseparables: un servidor que sirve archivos sin headers de seguridad es funcional, pero vulnerable por defecto. Plataformas como Azure facilitan el despliegue en segundos, pero la responsabilidad de las capas de seguridad HTTP recae completamente en el desarrollador. También aprendí que herramientas como securityheaders.com y SSL Labs permiten validar objetivamente la postura de seguridad de una aplicación, con criterios claros y accionables.

### 3. ¿Qué desafíos encontré en el proceso?

El primer desafío fue el error en las rutas de activos estáticos: las imágenes no cargaban porque la ruta incluía el prefijo `/pokedex-angular/assets/images` que Azure no reconocía. La solución fue corregir el archivo `environment.prod.ts` para usar rutas relativas desde la raíz. El segundo desafío fue el error `ERR_CERT_COMMON_NAME_INVALID` al configurar el dominio personalizado, causado por propagación DNS incompleta y caché HSTS del navegador; se resolvió esperando la propagación y limpiando la caché. Finalmente, lograr la calificación A en securityheaders.com requirió varias iteraciones en la directiva `Content-Security-Policy` para equilibrar seguridad funcional (Angular necesita `'unsafe-inline'`) con las restricciones recomendadas.

---

## ✅ Checklist de Entregables

- [x] Aplicación desplegada y accesible públicamente
- [x] HTTPS activo con certificado SSL válido
- [x] Dominio personalizado configurado (`danieldhj.dev`)
- [x] Calificación A en securityheaders.com
- [x] Calificación A+ en SSL Labs
- [x] `README.md` con proceso de creación de cuenta y reflexión técnica
- [x] `Despliegue.md` con proceso detallado, errores y soluciones
- [x] Repositorio público en GitHub

---

## 🔗 Enlaces

- **Aplicación:** https://www.danieldhj.dev
- **Repositorio:** https://github.com/Danieldavidhenriquezjulio/pokedex
- **Auditoría SSL Labs:** https://www.ssllabs.com/ssltest/analyze.html?d=danieldhj.dev
- **Auditoría Security Headers:** https://securityheaders.com/?q=danieldhj.dev
