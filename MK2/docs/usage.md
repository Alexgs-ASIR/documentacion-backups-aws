# Guía de Uso

En esta sección se muestra el script completo de backup y cómo utilizarlo.

---

## Objetivo

El script realiza las siguientes tareas:

1. Comprime archivos locales
2. Los sube a Amazon S3
3. Registra las operaciones
4. Maneja errores
5. Permite configuración

---

## Script Principal de Backup

Código completo del script:

```python
#!/usr/bin/env python3
"""
Script de Backup Automatizado a AWS S3
Autor: Alejandro Gámez
Versión: 1.0
"""

import os
import sys
import boto3
import logging
from datetime import datetime
from pathlib import Path
import zipfile
from botocore.exceptions import ClientError

# Configuración de logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('backup.log'),
        logging.StreamHandler(sys.stdout)
    ]
)
logger = logging.getLogger(__name__)


class BackupManager:
    """Gestor de backups a AWS S3"""
    
    def __init__(self, bucket_name, region='eu-west-1'):
        """
        Inicializa el gestor de backups
        
        Args:
            bucket_name: Nombre del bucket S3
            region: Región de AWS
        """
        self.bucket_name = bucket_name
        self.region = region
        self.s3_client = boto3.client('s3', region_name=region)
        logger.info(f"BackupManager inicializado para bucket: {bucket_name}")
    
    def comprimir_directorio(self, ruta_origen, nombre_archivo_zip=None):
        """
        Comprime un directorio en un archivo ZIP
        
        Args:
            ruta_origen: Ruta del directorio a comprimir
            nombre_archivo_zip: Nombre del archivo ZIP
            
        Returns:
            Ruta del archivo ZIP creado
        """
        if nombre_archivo_zip is None:
            timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
            nombre_archivo_zip = f"backup_{timestamp}.zip"
        
        logger.info(f"Comprimiendo directorio: {ruta_origen}")
        
        with zipfile.ZipFile(nombre_archivo_zip, 'w', zipfile.ZIP_DEFLATED) as zipf:
            for root, dirs, files in os.walk(ruta_origen):
                for file in files:
                    ruta_completa = os.path.join(root, file)
                    ruta_relativa = os.path.relpath(ruta_completa, ruta_origen)
                    zipf.write(ruta_completa, ruta_relativa)
        
        tamaño_mb = os.path.getsize(nombre_archivo_zip) / (1024 * 1024)
        logger.info(f"Compresión completada: {nombre_archivo_zip} ({tamaño_mb:.2f} MB)")
        
        return nombre_archivo_zip
    
    def subir_a_s3(self, ruta_archivo_local, objeto_s3_key=None):
        """
        Sube un archivo a Amazon S3
        
        Args:
            ruta_archivo_local: Ruta del archivo local
            objeto_s3_key: Nombre del objeto en S3
            
        Returns:
            True si la subida fue exitosa
        """
        if objeto_s3_key is None:
            objeto_s3_key = os.path.basename(ruta_archivo_local)
        
        try:
            logger.info(f"Iniciando subida a S3: {objeto_s3_key}")
            
            self.s3_client.upload_file(
                ruta_archivo_local,
                self.bucket_name,
                objeto_s3_key,
                ExtraArgs={
                    'ServerSideEncryption': 'AES256',
                    'Metadata': {
                        'original_name': os.path.basename(ruta_archivo_local),
                        'upload_date': datetime.now().isoformat(),
                        'source': 'automated_backup'
                    }
                }
            )
            
            logger.info(f"Subida exitosa: s3://{self.bucket_name}/{objeto_s3_key}")
            return True
            
        except ClientError as e:
            logger.error(f"Error al subir archivo: {e}")
            return False
    
    def verificar_backup(self, objeto_s3_key):
        """
        Verifica que el backup existe en S3
        
        Args:
            objeto_s3_key: Nombre del objeto en S3
            
        Returns:
            True si el objeto existe
        """
        try:
            self.s3_client.head_object(Bucket=self.bucket_name, Key=objeto_s3_key)
            logger.info(f"Verificación exitosa: {objeto_s3_key} existe en S3")
            return True
        except ClientError:
            logger.error(f"El objeto {objeto_s3_key} no existe en S3")
            return False
    
    def ejecutar_backup_completo(self, ruta_directorio):
        """
        Ejecuta el proceso completo de backup
        
        Args:
            ruta_directorio: Directorio a respaldar
            
        Returns:
            True si el backup fue exitoso
        """
        logger.info("INICIANDO PROCESO DE BACKUP")
        
        try:
            # Comprimir
            archivo_zip = self.comprimir_directorio(ruta_directorio)
            
            # Subir a S3
            exito = self.subir_a_s3(archivo_zip)
            
            if not exito:
                return False
            
            # Verificar
            objeto_key = os.path.basename(archivo_zip)
            verificado = self.verificar_backup(objeto_key)
            
            # Limpiar archivo local
            if verificado:
                os.remove(archivo_zip)
                logger.info(f"Archivo temporal eliminado: {archivo_zip}")
            
            logger.info("BACKUP COMPLETADO")
            
            return True
            
        except Exception as e:
            logger.error(f"Error durante el backup: {e}")
            return False


def main():
    """Función principal"""
    # Configuración
    BUCKET_NAME = 'backup-empresa-2026'
    DIRECTORIO_A_RESPALDAR = '/ruta/a/tus/datos'
    
    # Crear gestor de backups
    backup_manager = BackupManager(BUCKET_NAME)
    
    # Ejecutar backup
    exito = backup_manager.ejecutar_backup_completo(DIRECTORIO_A_RESPALDAR)
    
    sys.exit(0 if exito else 1)


if __name__ == "__main__":
    main()
```

---

## Ejecución del Script

### Uso Básico

```bash
# Ejecutar el script
python backup_script.py
```

Salida esperada:
```
2026-01-28 17:30:00 - INFO - BackupManager inicializado para bucket: backup-empresa-2026
2026-01-28 17:30:01 - INFO - INICIANDO PROCESO DE BACKUP
2026-01-28 17:30:02 - INFO - Comprimiendo directorio: /ruta/a/tus/datos
2026-01-28 17:30:15 - INFO - Compresión completada: backup_20260128_173000.zip (45.32 MB)
2026-01-28 17:30:16 - INFO - Iniciando subida a S3: backup_20260128_173000.zip
2026-01-28 17:31:20 - INFO - Subida exitosa: s3://backup-empresa-2026/backup_20260128_173000.zip
2026-01-28 17:31:21 - INFO - Verificación exitosa
2026-01-28 17:31:21 - INFO - BACKUP COMPLETADO
```

---

## Funcionamiento Paso a Paso

### 1. Inicialización

El script crea una instancia de BackupManager que establece conexión con AWS S3.

### 2. Compresión

Se recorre el directorio recursivamente y se añaden todos los archivos a un ZIP.

### 3. Subida a S3

El archivo ZIP se transfiere mediante HTTPS con cifrado AES-256.

### 4. Verificación

Se confirma que el archivo existe en S3.

### 5. Limpieza

Se elimina el archivo temporal local.

---

## Registros

El script genera un archivo `backup.log` con toda la actividad:

```log
2026-01-28 17:30:00 - INFO - BackupManager inicializado
2026-01-28 17:30:01 - INFO - Comprimiendo directorio
2026-01-28 17:30:15 - INFO - Compresión completada
2026-01-28 17:31:20 - INFO - Subida exitosa
```

---

## Opciones Avanzadas

<details>
<summary>Clic para ver configuraciones avanzadas</summary>

### Backup Incremental

Subir solo archivos modificados:

```python
import hashlib

def calcular_hash(ruta_archivo):
    hash_md5 = hashlib.md5()
    with open(ruta_archivo, 'rb') as f:
        for chunk in iter(lambda: f.read(4096), b""):
            hash_md5.update(chunk)
    return hash_md5.hexdigest()
```

### Políticas de Ciclo de Vida

Eliminar backups antiguos automáticamente:

```python
def configurar_ciclo_vida(self):
    lifecycle_policy = {
        'Rules': [
            {
                'Id': 'EliminarBackupsAntiguos',
                'Status': 'Enabled',
                'Prefix': 'backup_',
                'Expiration': {
                    'Days': 90
                }
            }
        ]
    }
    
    self.s3_client.put_bucket_lifecycle_configuration(
        Bucket=self.bucket_name,
        LifecycleConfiguration=lifecycle_policy
    )
```

</details>

---

## Programación Automática

### Linux/macOS (Cron)

```bash
# Editar crontab
crontab -e

# Ejecución diaria a las 2:00 AM
0 2 * * * /usr/bin/python3 /ruta/al/backup_script.py >> /var/log/backup.log 2>&1
```

### Windows (Task Scheduler)

```powershell
$action = New-ScheduledTaskAction -Execute "python" -Argument "C:\Scripts\backup_script.py"
$trigger = New-ScheduledTaskTrigger -Daily -At 2:00AM

Register-ScheduledTask -TaskName "BackupDiario" -Action $action -Trigger $trigger
```

---

## Errores Comunes

### Error: "NoCredentialsError"

Causa: Credenciales AWS no configuradas

Solución:
```bash
aws configure
```

### Error: "NoSuchBucket"

Causa: El bucket no existe

Solución:
```python
s3_client.create_bucket(Bucket='backup-empresa-2026')
```

### Error: "AccessDenied"

Causa: El usuario IAM no tiene permisos

Solución: Revisar políticas IAM y asignar permisos necesarios

---

## Ejemplo de Uso

```bash
# Crear estructura
mkdir proyecto_backup && cd proyecto_backup

# Crear entorno virtual
python -m venv venv
source venv/bin/activate

# Instalar dependencias
pip install boto3

# Configurar AWS
aws configure

# Ejecutar
python backup_script.py

# Verificar en S3
aws s3 ls s3://backup-empresa-2026/
```

---

[Volver a Instalación](install.md) | [Ir a Conclusiones](conclusions.md)
