
# pokedex: Despliegue
Parcial de sistemas distribuidos.
Daniel David Henriquez Julio.

---
# Etapa 1: Repositorio y Despliegue en azure.

en esta etapa se realizo la creacion del dominio y el despliegue en azure.

## Paso 1:Clonacion y Creacion de repositorio
### Clonacion:
1. Se descargó el archivo ZIP del repositorio suministrado para la actividad.
2. Luego, se instalaron las dependencias del proyecto con el siguiente comando:
    
    ```
     npm install
    ```
    
3. Se creó la carpeta de despliegue utilizando el comando:
    
    ```
     npm run build
    ```
    
4.  Al finalizar la ejecución del comando anterior, se creo dentro de la carpeta **\pokedex-angular** una subcarpeta llamada **dist\pokedex-angular**
    
5.  Todo el contenido de la subcarpeta **dist\pokedex-angular** quedo listo para ser desplegada a cualquier servidor.
---
### Creacion de Repositorio propio
1. Se creó un repositorio en GitHub con el nombre **pokedex**.
2. En la ruta del proyecto, se inicializó Git con el siguiente comando:
    ```
    git init
    ```
3. Se agregó el repositorio remoto:  
    ```
    git remote add origin https://github.com/Danieldavidhenriquezjulio/pokedex.git
   ```
  
4. Se agregaron los archivos del proyecto:  
	 ```
	 git add .
	```
  
5. Se realizó el primer commit:  
	```
	git commit -m "Primer commit pokedex angular"
	```
  
6. Se cambió la rama principal a **main**:  
	```
	git branch -M main
	```
  
7. Finalmente, se subió el proyecto al repositorio remoto:  
	```
	git push -u origin main
	```
  
---
## Paso 2: Despliegue en la Nube (Azure)
1.  Se inició sesión en el portal de Azure.  
2.  Se creó un recurso de tipo **Static Web App**.
3.  **Se configuraron los datos básicos:**  
	- Nombre de la aplicación:```  pokedexdanielhenriquezjulio```
	
	- Grupo de recursos :``` rg-pokedex-prod-danielhenriquezj```
	
	- Región:``` global```
4. **Detalles de la implementación**:
	-Fuente:```github```
	se vinculo la cuena de github donde esta el repo y se cargo el repositorio.
5. **Detalles de construcción:**
	
	-Configuración predefinida:```angular```
	
	-Ubicación de la aplicación:```/```
	
	-Ubicación de salida:```dist/pokedex-angular```
	

7. Se hizo clic Revisar y crear y luego en crear. Después del despliegue, se accedió a la URL pública proporcionada por Azure para verificar el funcionamiento de la aplicación.  

### Errores presentados
1. elementos estaticos como [https://kind-mud-0f0879a10.7.azurestaticapps.net/pokedex-angular/assets/images/pokemon-green.png](https://kind-mud-0f0879a10.7.azurestaticapps.net/pokedex-angular/assets/images/pokemon-green.png) presentaban error a la hora de cargar 

### Soluccion
1.se corrigio la ruta en el el archivo environment.prod.ts de '/pokedex-angular/assets/images' a '/assets/images'

---
##  Resultados etapa una 1  
  
- El proyecto fue construido correctamente  
- Se subió a GitHub sin inconvenientes  
- Se desplegó en la nube usando Azure  
- La aplicación quedó accesible mediante una URL pública

- <img width="1559" height="327" alt="image" src="https://github.com/user-attachments/assets/178e866a-012d-4cbf-80ce-49dd59108c2e" />


---
# Etapa 2: Seguridad y Dominio
en esta etapa se implementaronn las buenas practicas de seguridad y vinculacion de un dominio y dns.

## Paso 1: Configurar los Security Headers
### creacion del archivo staticwebapp.config.json
En la **raíz del proyecto** (no dentro de `src/`), cree el archivo `staticwebapp.config.json` con el siguiente contenido:
``` 
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/assets/*", "/*.{png,jpg,gif,ico,css,js}"]
  },
  "globalHeaders": {
    "X-Content-Type-Options": "nosniff",
    "X-Frame-Options": "DENY",
    "Referrer-Policy": "no-referrer",
    "Strict-Transport-Security": "max-age=31536000; includeSubDomains; preload",
    "Content-Security-Policy": "default-src 'self'; img-src 'self' https://raw.githubusercontent.com https://pokeapi.co https://assets.pokemon.com; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; style-src-elem 'self' 'unsafe-inline' https://fonts.googleapis.com; font-src 'self' https://fonts.gstatic.com; connect-src 'self' https://pokeapi.co https://beta.pokeapi.co;",
    "Permissions-Policy": "camera=(), microphone=(), geolocation=()"
  }
}
```
#### ¿Qué hace cada sección?

**`navigationFallback`** — Le indica a Azure que cualquier ruta que no corresponda a un archivo físico debe redirigirse a `/index.html`, lo cual es necesario para que el enrutamiento de Angular funcione correctamente. La propiedad `exclude` evita que esa redirección aplique a archivos estáticos reales como imágenes, CSS y JS, ya que esos sí deben servirse directamente.

**`globalHeaders`** — Define los encabezados HTTP de seguridad que Azure enviará en cada respuesta. Cada uno cumple una función específica:

-   **`X-Content-Type-Options: nosniff`** — Impide que el navegador intente detectar automáticamente el tipo de contenido de un archivo. Sin este header, un atacante podría subir un archivo disfrazado (por ejemplo, una imagen que en realidad es un script) y el navegador lo ejecutaría.
-   **`X-Frame-Options: DENY`** — Prohíbe que la aplicación sea incrustada dentro de un `<iframe>` en otro sitio. Esto previene ataques de tipo _clickjacking_, donde un sitio malicioso superpone la app de forma invisible para engañar al usuario.
-   **`Referrer-Policy: no-referrer`** — Evita que el navegador envíe la URL de origen en el header `Referer` cuando el usuario navega a otro sitio. Esto protege la privacidad del usuario y evita que sitios externos sepan desde qué página llegó.
-   **`Strict-Transport-Security`** — Fuerza al navegador a usar siempre HTTPS para comunicarse con el sitio, incluso si el usuario escribe `http://`. El valor `max-age=31536000` lo aplica por un año, `includeSubDomains` lo extiende a todos los subdominios, y `preload` permite que el sitio sea incluido en la lista de precarga HSTS de los navegadores.
-   **`Content-Security-Policy`** — Es el header más completo. Define exactamente qué recursos puede cargar la aplicación y desde dónde, previniendo ataques XSS. Cada directiva controla un tipo de recurso distinto: `default-src 'self'` establece que por defecto solo se permiten recursos del mismo dominio; `img-src` permite imágenes desde GitHub, PokeAPI y assets.pokemon.com; `script-src` permite scripts propios e inline (requerido por Angular); `style-src` y `style-src-elem` permiten estilos inline y desde Google Fonts; `font-src` permite cargar las fuentes desde Google; y `connect-src` permite las llamadas HTTP a la PokeAPI REST y GraphQL.
-   **`Permissions-Policy`** — Restringe el acceso a APIs sensibles del navegador. Con `camera=(), microphone=(), geolocation=()` se declara explícitamente que la aplicación no necesita ni solicita acceso a la cámara, micrófono ni ubicación del usuario, reduciendo la superficie de ataque.

### Nota 
La calificación en https://securityheaders.com fue de una A está limitada a A en lugar de A+ debido al uso de `'unsafe-inline'` en `script-src`, necesario porque Angular genera scripts inline en tiempo de build. Para eliminar este warning se requeriría configurar un nonce o hash CSP, lo cual implica modificar el proceso de build de Angular.

---
## Paso 2: Configuración de Dominio Personalizado

Se configuró un dominio personalizado usando Name.com y Azure Static Web Apps.

----------

### Registro del dominio

Se adquirió el dominio **danieldhj.dev** en name.com. Se eligió un dominio corto para mejorar accesibilidad y recordación.



### Configuración en Azure

1.  Se accedió a **Custom Domains**
2.  Se seleccionó:
	-Custom domain on other DNS
3.  Se agregaron:
https://www.danieldhj.dev
https://danieldhj.dev

Ambos dominios fueron validados correctamente por Azure.

<img width="1571" height="398" alt="image" src="https://github.com/user-attachments/assets/36d8de61-e668-4b4d-9ba7-c29f7fb43aef" />




### Configuración DNS

En Name.com se configuró:

Subdominio (www)
```
Type: CNAME  
Host: www  
Value: kind-mud-0fxxx.azurestaticapps.net  
TTL: 300
```

### Dominio raíz

```
Type: TXT  
Host: @  
Value: asuid.danieldhj.dev=XXXXXXXX
```



## #Validación

-   Se esperó la propagación DNS
-   Se validó en Azure
-   Se generó automáticamente el certificado SSL



### HTTPS

Azure generó automáticamente certificados SSL para ambos dominios:

-   `www.danieldhj.dev`
-   `danieldhj.dev`

Azure proporciona:
-   Certificado SSL gratuito
-   Redirección automática a HTTPS
-   Renovación automática



## Problema encontrado

Error:

ERR_CERT_COMMON_NAME_INVALID



## Solución

-   Verificar configuración DNS
-   Esperar propagación
-   Limpiar caché HSTS del navegador



### Resultado final

La aplicación quedó disponible en:

https://www.danieldhj.dev  
https://danieldhj.dev


### Consideraciones

-   Se configuró tanto el dominio raíz como el subdominio `www` para mejorar accesibilidad.
-   El uso de registros A y CNAME permite compatibilidad con Azure Static Web Apps.
-   La implementación de HTTPS garantiza comunicación segura.

---

# Conclusión

Se logró:

-   Desplegar una aplicación Angular en la nube
-   Configurar seguridad mediante headers
-   Asociar un dominio personalizado
-   Implementar HTTPS

La aplicación cumple con buenas prácticas de despliegue, seguridad y accesibilidad.


## SSL / TLS Security Report

<img width="1368" height="784" alt="image" src="https://github.com/user-attachments/assets/86a3d6be-277d-4355-8a9f-db6b250f4465" />


Se realizó un análisis de seguridad utilizando SSL Labs sobre el dominio de la aplicación desplegada.

El resultado obtenido fue una calificación A+, lo que indica una configuración segura y moderna del protocolo HTTPS.

El servidor soporta TLS 1.3 y TLS 1.2, con deshabilitación de protocolos inseguros como SSLv2, SSLv3 y TLS 1.0. Además, se encuentra habilitado HTTP Strict Transport Security (HSTS), lo cual obliga el uso de conexiones seguras HTTPS en el navegador.

El certificado SSL es válido, confiable y emitido por una autoridad certificadora reconocida. También se verifica el uso de Forward Secrecy, lo cual mejora la protección de las sesiones de usuario.

En general, la aplicación cumple con buenas prácticas de seguridad en transporte de datos.
