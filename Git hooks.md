## ¿Qué son los "Git Hooks"?
- Los "Git Hooks" son scripts que se ejecutan automáticamente en un repositorio de Git cuando ocurre un evento específico, permitiendo personalizar el comportamiento de Git en puntos clave del ciclo de desarrollo.

![hooks](uploads/113a8b5686bbd3e3bab92947f7896310/hooks.png)

- Estos scripts son una forma de análisis estático, que examina el código sin ejecutarlo, y Git ofrece varios tipos como `pre-commit`, `post-commit`, `pre-push`, y `post-push`, cada uno con un propósito particular, como verificar la calidad del código o ejecutar pruebas automatizadas.

- Para comenzar a usarlos, se debe convertir el proyecto en un repositorio de Git con `git init`, y los hooks se encuentran en el directorio `.git/hooks`.
    ```bash
    git init
    ``` 
  - Es importante tener en cuenta que este directorio suele estar oculto, lo que significa que no será visible por defecto.
  - Si necesitas acceder a este directorio, puedes hacer que sea visible eliminando `**/.git` de la lista de exclusiones en tu configuración de archivos `files: Exclude`

  ![fileexclude](uploads/15f561df307a5f9bfe2e086388eeb192/fileexclude.png) 


  


  - Los "pre-commit hooks" son scripts automatizados que se ejecutan antes de confirmar cambios en Git. 
- Se utilizan para verificar el estilo de código, ejecutar pruebas automáticas, realizar análisis estático del código y prevenir confirmaciones incorrectas. 
- Ayudan a mantener la calidad y consistencia del código, así como a prevenir errores comunes antes de que los cambios se guarden en el repositorio compartido.

## Guía de uso

### 1. Convertir tu proyecto en un repositorio Git

  - Ejecutar el siguiente comando en el directorio de tu proyecto `git init`
    ```bash
    git init
    ```
    - Importante decir que la extensión `.sample` en los archivos dentro de `.git/hooks` indica que estos son ejemplos de scripts que puedes utilizar. Git automáticamente agrega esta extensión `.sample` para que los scripts no se ejecuten de forma predeterminada y no interfieran con el flujo de trabajo del usuario.
    
    - Para hacer que uno de estos scripts se ejecute, necesitas eliminar la extensión `.sample` del archivo correspondiente. 
  
    #### Primero después de ejecutar `git init`, nos salen los scripts con la extensión `.sample`:
    ![hookssample](uploads/c8389630228922cd4c12abd8d83859f8/hookssample.png)
    
    #### A continuación, le quitamos la extensión a los scripts para que se ejecuten:
    ![hooks](uploads/91ad3f04cc16618210b6e374f892ac81/hooks.png)


### 2. Instalar el framework "pre-commit"

  - Se instala ejecutando el siguiente comando `pip install pre-commit`
    ```bash
    pip install pre-commit
    ```

### 3. Descargar/crear el archivo `.pre-commit-congig.yaml` 

  - Link a GitLab donde está el [fichero genérico](https://gitlab.com/satecgroup/satec/devsecops/git-hooks/-/blob/main/.pre-commit-config.yaml).
      - Este fichero incluye las herramientas [Talisman](https://github.com/thoughtworks/talisman), [Kics](https://docs.kics.io/latest/integrations_pre_commit/), [Eslint](https://github.com/pre-commit/mirrors-eslint), [Pylint](https://github.com/pre-commit/mirrors-pylint) y [Trivy](https://github.com/antonbabenko/pre-commit-terraform).
        - Recuerda que para que Eslint funcione tienes que ejecutar `npm init @eslint/config` y para que Trivy funcione tienes que tener Docker Desktop ejecutándose en tu ordenador
          ```bash
          npm init @eslint/config
          ```
  
  - [¿Cómo crear paso a paso tu fichero .pre-commit-config personalizado?](Fichero .pre-commit-config.yaml personalizado)
  
  - Algunas herramientas que podrían ser útiles están en este [link](Herramientas).
  
    ##### Importante: Este archivo tiene que estar en el directorio raíz de tu proyecto (no dentro de ninguna carpeta)

### 4. Ejecutar el comando `pre-commit install`
 
  - Cuando ejecutas `pre-commit install`, estás configurando esta herramienta en tu entorno local. 
    ```bash
    pre-commit install
    ```
  
  - Lo que hace específicamente es buscar un archivo llamado `.pre-commit-config.yaml` en el directorio raíz de tu repositorio (o en un directorio superior si no se encuentra en el directorio actual) y activar los hooks pre-commit definidos en ese archivo.

### 5. Finalmente, ejecutar `git commit -m "prueba"`
  ```bash
  git commit -m "prueba"
  ```
  - Aquí, git commit es el comando principal de Git utilizado para guardar cambios en el repositorio. 
    - La opción -m se utiliza para agregar un mensaje al commit. En este caso, el mensaje proporcionado es "prueba".

  - Antes de realizar dicho commit se realizará el análisis con las herramientas que se hayan incluido en el fichero `.pre-commit-config.yaml`.
      - Si alguno de estos análisis sale como `failed` no se realiza el commit, sin embargo, si sale como resultado final del análisis `passed`, si que se realiza.

## Diferencia entre script pre-commit y framework pre-commit

##### Script pre-commit

![hooks](uploads/9fdfedf7374bad874b86c66d12a8ac78/hooks.png)

  - El archivo pre-commit es un tipo de hook que se ejecuta justo antes de que se realice un commit en Git. 
  
  - Su función principal es permitir que el desarrollador realice verificaciones adicionales antes de confirmar los cambios en el repositorio. 
  
  - Esto puede incluir pruebas de calidad de código, análisis estático o cualquier otra tarea que se considere necesaria para garantizar la integridad del código.

##### Framework pre-commit

![Captura_de_pantalla_2024-02-28_a_las_13.05.49](uploads/648594a6c6ca34aa3c355457379dc819/Captura_de_pantalla_2024-02-28_a_las_13.05.49.png)

  - Por otro lado, el paquete pre-commit install es una herramienta que facilita la configuración y gestión de hooks en un proyecto de Git. 
  
  - Este paquete se utiliza para instalar y configurar hooks pre-commit de forma automatizada en un repositorio.
   
  - Al ejecutar este comando, pre-commit instalará los hooks especificados en el archivo de configuración del proyecto `.pre-commit-config.yaml`, lo que permite que se apliquen las verificaciones y acciones deseadas antes de cada commit.

##### Conclusión

Es importante destacar que, aunque ambos, el archivo pre-commit y el paquete pre-commit install, están relacionados con la ejecución de acciones antes de un commit en Git, tienen propósitos diferentes y se utilizan de manera complementaria en el proceso de desarrollo de software.

## Personalizar el fichero .pre-commit-config.yaml

### 1. Crear el fichero .pre-commit-config.yaml en el directorio de tu proyecto

![fichero_de_config](uploads/076f885e04d64fc5a122b7da59c365c2/fichero_de_config.png)

  - Este paso implica la creación del archivo de configuración `.pre-commit-config.yaml` en el directorio raíz de tu proyecto. 
  - Este archivo es utilizado por la herramienta pre-commit para definir qué hooks `pre-commit` se deben ejecutar y cómo se deben configurar.

### 2. Personalizar el fichero según la herramienta que quieras usar

![Captura_de_pantalla_2024-02-29_a_las_9.43.48](uploads/0ae13ad65cda27c4a007ac53795fcd4c/Captura_de_pantalla_2024-02-29_a_las_9.43.48.png)

  - Después de crear el archivo `.pre-commit-config.yaml`, debes personalizarlo según las herramientas específicas que deseas utilizar para analizar tu proyecto. 
  - Esto puede incluir herramientas de análisis de código estático, linters, formateadores de código, entre otros. 
  - Para encontrar las herramientas que deseas utilizar, puedes buscar en el enlace proporcionado que [lista los repositorios disponibles](https://pre-commit.com/hooks.html). 
  - Una vez que encuentres la herramienta deseada, puedes copiar y pegar la información necesaria desde el repositorio al archivo `.pre-commit-config.yaml`.

    ##### 1. Entrar en la página y buscar la herramienta de análisis que necesitas:
    ![Captura_de_pantalla_2024-02-29_a_las_9.48.42](uploads/b21932c749db03bd6dc1bacf87de3822/Captura_de_pantalla_2024-02-29_a_las_9.48.42.png)

      - En este paso, accedes a la página proporcionada y buscas la herramienta de análisis específica que necesitas para tu proyecto.

    ##### 2. Entrar al link y buscar el apartado de pre-commit:
    ![Captura_de_pantalla_2024-02-29_a_las_9.50.27](uploads/c192ee04c9274bc26d5152e2085e1319/Captura_de_pantalla_2024-02-29_a_las_9.50.27.png)

      - Una vez que encuentras la herramienta deseada, accedes al enlace correspondiente y buscas la sección que contiene información sobre la integración con pre-commit.

    ##### 3. Copiar y pegar la información dentro de `-repo: ...` en nuestro fichero `.pre-commit-config.yaml`:

    ![Captura_de_pantalla_2024-02-29_a_las_9.54.21](uploads/4cadd5575c1779f98f737d18459bf24e/Captura_de_pantalla_2024-02-29_a_las_9.54.21.png)

      - Dentro de la sección específica de pre-commit del repositorio de la herramienta, encontrarás información que puedes copiar y pegar directamente en tu archivo `.pre-commit-config.yaml`. 
      
      - Esta información generalmente incluirá la URL del repositorio de la herramienta, la versión que deseas utilizar y cualquier otra configuración relevante.

    ##### 4. Repetir el proceso para cada herramienta que quieras usar:

    ![Captura_de_pantalla_2024-02-29_a_las_10.04.39](uploads/b0556a11b5fdc2199162e7ecd5a3bb19/Captura_de_pantalla_2024-02-29_a_las_10.04.39.png)

      - Repite este proceso para todas las herramientas que desees utilizar en tu proyecto, pegando la información debajo de la otra en tu archivo `.pre-commit-config.yaml`.


### Conclusión

  - Estos pasos te permiten personalizar y configurar el archivo `.pre-commit-config.yaml` para que pre-commit pueda ejecutar las herramientas de análisis y asegurar la calidad del código en tu proyecto antes de cada confirmación (commit).

- Otras herramientas que también podrían ser útiles están en el siguiente [link](Herramientas).

## Herramientas

### Análisis estático

  - [Kics](https://docs.kics.io/latest/utilities/)
  - [Eslint](https://eslint.org)
      - Recuerda que para que esta herramienta funcione tienes que ejecutar `npm init @eslint/config`
        ```bash
        npm init @eslint/config
        ```
  - [Pylint](https://pypi.org/project/pylint/)
  - [SonarLint](https://marketplace.visualstudio.com/items?itemName=SonarSource.sonarlint-vscode)
    - Para instalar y usar esta herramienta tienes que seguir los siguientes pasos:
      1. Instalar la herramienta siguiendo las instrucciones en este [link](https://www.sonarsource.com/products/sonarlint/).
      2. Seguir los pasos de configuración.
      3. Descargarte este [script](https://gitlab.com/satecgroup/satec/devsecops/git-hooks/-/blob/main/run_sonarlin.sh?ref_type=heads) en el directorio de tu proyecto.
      4. Añadir el código de este [link](https://gitlab.com/satecgroup/satec/devsecops/git-hooks/-/blob/main/sonarlint-pre-commit-config.yaml?ref_type=heads) a tu fichero `.pre-commit-config.yaml`

### Herramienta de seguridad

  - [Talisman](https://github.com/thoughtworks/talisman)

### Análisis de vulnerabilidades en contenedores

  - [Trivy](https://trivy.dev)
    - Recuerda que para que esta herramienta funcione tienes que tener Docker Desktop ejecutándose en tu ordenador.

### Análisis de dependencias

  - [OWASP Dependency-check](https://owasp.org/www-project-dependency-check/)
    - Para instalar y usar esta herramienta tienes que seguir los siguientes pasos:
      1. Ejecutar en el terminal: `brew update && brew install dependency-check`
         ```bash
         brew update && brew install dependency-check
         ```
      2. Descargarte este [script](https://gitlab.com/satecgroup/satec/devsecops/git-hooks/-/blob/main/run_dependency_check.sh) en el directorio de tu proyecto.
      3. Añadir el código en este [link](https://gitlab.com/satecgroup/satec/devsecops/git-hooks/-/blob/main/OWSAP-dependency-check-pre-commit-config.yaml?ref_type=heads) a tu fichero `.pre-commit-config.yaml`. 