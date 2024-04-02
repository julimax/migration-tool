# Herramienta de Migración

Esta herramienta se utiliza para migrar repositorios de BitBucket Server a GitHub. Se ejecuta a través de un flujo de trabajo de GitHub Actions llamado "BitBucket Server Migration Tool Windows", que se activa manualmente utilizando el evento workflow_dispatch.

El flujo de trabajo consta de dos trabajos: Get-Repositories-to-Migrate y Migration.

## Get-Repositories-to-Migrate

Este trabajo se ejecuta en la última versión de Ubuntu. Realiza el checkout del repositorio y establece los datos de la matriz a partir de un archivo llamado projects.json.

## Migration
 
Este trabajo se ejecuta en un runner autohospedado de Windows X64. Depende del trabajo Get-Repositories-to-Migrate. Realiza el checkout del repositorio y realiza la migración.

La migración se realiza un proyecto a la vez (max-parallel: 1). La matriz para este trabajo se establece con los proyectos a migrar.

Las variables de entorno para este trabajo incluyen el Token de Acceso Personal de GitHub (GH_PAT), el nombre de usuario y la contraseña del Servidor BitBucket (BBS), la cadena de conexión de almacenamiento de Azure y la ruta de trabajo del runner (PATH_WORK).

El trabajo lee el archivo projects.json y realiza la migración para cada repositorio en el proyecto objetivo. La migración se realiza utilizando el comando gh bbs2gh migrate-repo.

Después de la migración, el archivo de exportación de Bitbucket se recupera en el directorio de trabajo. Finalmente, el repositorio se migra a GitHub utilizando el comando gh bbs2gh migrate-repo.

## Agregar projectos y repositorios al json

Este archivo projects.json se utiliza para especificar los proyectos y repositorios que se van a migrar.

- include: Es un array que contiene los proyectos que se van a migrar.
- project: Es el nombre del proyecto en BitBucket Server que se va a migrar.
- migrate: Es un array que contiene los repositorios que se van a migrar del proyecto especificado.
- repositories: Es el nombre del repositorio en el proyecto que se va a migrar.

Por ejemplo, en el JSON proporcionado, se va a migrar un proyecto llamado "PROJ". Dentro de este proyecto, se va a migrar un repositorio llamado "repo1".

        {
            "include": [
            {
                "project": "PROJ1",
                "migrate": [
                    {
                        "repositories": "repo1",
                        "repositories": "repo2"
                    }
                ]
            },
            {
                "project": "PROJ2",
                "migrate": [
                    {
                        "repositories": "repo1.1",
                        "repositories": "repo2.1"
                    }
                ]
            }
            ]
        }
