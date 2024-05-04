  -  Ingress-Nginx es un controlador de Ingress específico para Kubernetes que utiliza Nginx para gestionar el tráfico de entrada hacia los servicios dentro del clúster.

# Configuración con controlador de Ingress Nginx existente

### Paso 1: Entrar a Rancher y dirigirse a Cluster Management.
![fotor1](uploads/c88fd75ee5775eb0885090dbe70d6606/fotor1.png)
### Paso 2: Hacer click en Config, en el clúster seleccionado.
![fotor2](uploads/4ac218d4f432178b73f143cacc84d953/fotor2.png)

### Paso 3: Hacer click en la pestaña Add-On Config e introducir el manifiesto en la parte inferior en Additional Manifest.

![083BE43B-1C14-42A9-AD90-69E95F3B8B65](uploads/0ad5cab9d21092018ccad60dd86a65b7/083BE43B-1C14-42A9-AD90-69E95F3B8B65.JPEG)

```
defaultBackend:
  enabled: true
  name: custom-default-backend
  image:
    repository: dvdblk/custom-default-backend
    tag: "latest"
    pullPolicy: Always
  port: 8080
  extraVolumeMounts:
    - name: tmp
      mountPath: /tmp
  extraVolumes:
    - name: tmp
      emptyDir: {}
```
   - Para personalizar los html de las páginas de error, tienes que seguir los pasos que se indican en este [link](https://github.com/dvdblk/custom-default-backend?tab=readme-ov-file#html-customization), donde se encuentra el repositorio empleado.

#### Siguiendo estos pasos, podrás configurar y verificar la personalización de las páginas de error en el backend predeterminado del Ingress-Nginx-Controller existente en tu clúster.

## Configuración sin controlador de Ingress Nginx previamente instalado

### Paso 1: Descargar el controlador de Ingress con Nginx para Kubernetes: helm-chart-4.10.0
- Descarga el [chart de Helm](https://github.com/kubernetes/ingress-nginx/releases/tag/helm-chart-4.10.0) y sitúate en su directorio raíz.

![foto8](uploads/182223c53c9b6f8c1b9ea64e5f8fa104/foto8.png)

### Paso 2: Editar values.yaml

- Aquí tienes el archivo [values.yaml](https://gitlab.com/satecgroup/satec/devsecops/kubernetes/ingress/-/blob/main/ingress-nginx/values.yaml?ref_type=heads) con las modificaciones ya aplicadas.
- El contenido actualizado de defaultBackend es el siguiente:

```
defaultBackend:
  enabled: true
  name: custom-default-backend
  image:
    repository: dvdblk/custom-default-backend
    tag: "latest"
    pullPolicy: Always
  port: 8080
  extraVolumeMounts:
    - name: tmp
      mountPath: /tmp
  extraVolumes:
    - name: tmp
      emptyDir: {}
```

   - Para personalizar los html de las páginas de error, tienes que seguir los pasos que se indican en este [link](https://github.com/dvdblk/custom-default-backend?tab=readme-ov-file#html-customization), donde se encuentra el repositorio empleado.

### Paso 3: Instalación del Controlador de Ingress con Nginx

- Desde el directorio raíz del chart ejecuta el siguiente comando para su instalación con la configuración actualizada.

```
helm install nginx -n "namespace" . -f values.yaml
```

### Paso 4: Verificación de la configuración

- Para asegurarte de que la configuración se ha realizado correctamente, sigue estos pasos:

##### 1. Crea un servicio para tu backend: [service.yaml](https://gitlab.com/satecgroup/satec/devsecops/kubernetes/ingress/-/blob/main/service.yaml?ref_type=heads)

```
apiVersion: v1
kind: Service
metadata:
  name: mi-backend-service
  namespace: # Mismo namespace que el Ingress Controller
spec:
  selector:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/component: controller
  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: http
    - name: https
      protocol: TCP
      port: 443
      targetPort: https
```
   
   ##### 2. Crea un pod con curl para probar la conectividad: [curl-image.yaml](https://gitlab.com/satecgroup/satec/devsecops/kubernetes/ingress/-/blob/main/curl-image.yaml?ref_type=heads)

```
apiVersion: v1
kind: Pod
metadata:
  name: curl-prueba
  namespace: # Cualquier namespace
  labels:
    app.kubernetes.io/name: MyApp
spec:
  containers:
  - name: nginx-curl
    image: debian:latest
    command: ["/bin/bash", "-c"]
    args:
    - |
      apt-get update && apt-get install -y nginx curl && rm -rf /var/lib/apt/lists/*
      nginx -g 'daemon off;'
    ports:
    - containerPort: 80

```
   - Ejecuta un `curl` desde el pod `curl-prueba` a la IP del servicio `mi-backend-service` para verificar que las páginas de error se están sirviendo correctamente.
   ![foto9](uploads/fb85e630cfbc6401114fe7e4e7c29d53/foto9.png)

#### Siguiendo estos pasos, podrás configurar y verificar la personalización de las páginas de error en el backend predeterminado del Ingress-Nginx-Controller.

# Comprobar versión de chart
   - En este [link](https://github.com/rancher/rke2/releases?expanded=true&page=2&q=v1.28.8%2Brke2r1) puedes mirar la versión de los charts asociada a tu versión de rke2.
      - Por  ejemplo, si tu versión de rke2 es v1.28.8+reke2r1, se mira [aquí](https://github.com/rancher/rke2/releases/tag/v1.28.8%2Brke2r1). 

![fto44](uploads/b83383a3fa35a05ed35d07d8681c755c/fto44.png)