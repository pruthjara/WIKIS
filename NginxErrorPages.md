En esta guía, aprenderás a personalizar las páginas de error de un servidor Nginx en un entorno de Kubernetes utilizando ConfigMaps y Deployments.

### Paso 1: Editar el archivo Default.conf en el ConfigMap

Vamos a sobreescribir la configuración del servidor Nginx utilizando un ConfigMap.

   1. Localiza el archivo de configuración `default.conf` dentro del servidor Nginx: `etc/nginx/conf.d/default.conf`.

   2. Dentro del archivo `default.conf`, añade la configuración de la página de error 404:
  ```
  error_page 404 /404.html;
  location = /404.html {
    root /usr/share/nginx/html;
  }
  ```

   - Esto indica que cuando se produce un error 404, Nginx redirigirá al usuario a la página `/404.html`.

### Paso 2: Crear el ConfigMap

Definimos un ConfigMap que contendrá la configuración de Nginx, incluidas las páginas de error personalizadas.

   1. Utiliza un archivo YAML para definir el ConfigMap con el nombre `nginx50x` en el espacio de nombres `nginx`.

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx50x
  namespace: nginx
data:
  default.conf: |
    # Contenido del archivo default.conf modificado
    server {
      listen       80;
      listen  [::]:80;
      server_name  localhost;

      location / {
          root   /usr/share/nginx/html;
          index  index.html index.htm;
      }

      error_page 404 /404.html;
      location = /404.html {
          root /usr/share/nginx/html;
      }

      error_page   500 502 503 504  /50x.html;
      location = /50x.html {
          root   /usr/share/nginx/html;
      }
    }

  404.html: |
    <!-- Contenido del archivo 404.html personalizado -->

```

### Paso 3: Crear el Deployment

Definimos un Deployment para orquestar el despliegue de los contenedores Nginx utilizando la configuración proporcionada por el ConfigMap.

   1. Utiliza un archivo YAML para definir el Deployment con el nombre `nginx-satec` en el espacio de nombres `nginx`.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-satec
  namespace: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 9080
        volumeMounts:
        - name: nginx-config-volume
          mountPath: /etc/nginx/conf.d/default.conf
          subPath: default.conf
        - name: nginx-config-volume
          mountPath: /usr/share/nginx/html/404.html
          subPath: 404.html
      volumes:
      - name: nginx-config-volume
        configMap:
          name: nginx50x

```

   ##### Descripción de los volúmenes y rutas utilizadas:

   - Los volúmenes se utilizan para montar la configuración del ConfigMap en los contenedores Nginx. En este Deployment, se define un volumen llamado `nginx-config-volume`.
   
      - Rutas de los volúmenes:
      
         - Ruta del archivo `Default.conf`:
            - mountPath: `/etc/nginx/conf.d/default.conf`
            - subPath: `default.conf`
               - Estas rutas indican que el archivo de configuración `default.conf` dentro del ConfigMap será montado en el directorio `/etc/nginx/conf.d/` del contenedor Nginx, y será utilizado como la configuración predeterminada del servidor Nginx.   
               
         - Ruta del archivo 404.html:
            - mountPath: `/usr/share/nginx/html/404.html`
            - subPath: `404.html`
               - Estas rutas especifican que el archivo 404.html dentro del ConfigMap será montado en el directorio `/usr/share/nginx/html/` del contenedor Nginx. Esto permite que el servidor Nginx acceda al archivo y lo sirva como la página de error personalizada para el error 404

### Paso 4: Implementación en Kubernetes

   1. Aplicar el ConfigMap:
   
      Ejecutas el comando `kubectl apply -f <ruta_al_archivo_del_configmap.yaml>` para crear el ConfigMap en tu clúster Kubernetes.
```
kubectl apply -f <ruta_al_archivo_del_configmap.yaml>
```
   2. Aplicar el Deployment:
   
      Ejecuta el comando `kubectl apply -f <ruta_al_archivo_del_deployment.yaml>` para crear el Deployment en tu clúster Kubernetes.
```
kubectl apply -f <ruta_al_archivo_del_deployment.yaml>
```

### Paso 5: Verificar la Implementación

Verificar que los recursos se crearon correctamente:
   1. Ejecuta `kubectl get configmaps` para verificar que el ConfigMap se haya creado correctamente.
   
```
kubectl get configmaps
```

   2. Ejecuta `kubectl get deployments` para verificar que el Deployment esté en ejecución.

```
kubectl get deployments
```
  
Probar las páginas de error personalizadas:

   - Accede a una URL inexistente en tu servidor Nginx para provocar un error 404 y verifica que se muestre la página personalizada que definiste en el ConfigMap.


# Conclusión

   - Siguiendo estos pasos, deberías poder cambiar las páginas de error de un servidor Nginx en Kubernetes utilizando el ConfigMap y el Deployment proporcionados. 
   - Recuerda ajustar los nombres de los archivos y rutas según sea necesario en tu entorno específico.