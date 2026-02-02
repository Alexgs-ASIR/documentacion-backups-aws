# Instalación y Configuración

En esta sección se explica cómo configurar el entorno necesario para el script de backup.

---

## Requisitos Previos

Antes de comenzar, verifica que tu sistema cumpla con estos requisitos:

### Software Necesario

| Componente | Versión Mínima | Verificación |
|------------|----------------|---------------|
| Python | 3.7+ | `python --version` |
| pip | 20.0+ | `pip --version` |
| Cuenta AWS | N/A | Crear en aws.amazon.com |

---

## Paso 1: Instalación de Python

### Windows

```powershell
# Descargar desde python.org y ejecutar el instalador
# Marcar "Add Python to PATH"

# Verificar instalación
python --version
pip --version
```

### Linux (Ubuntu/Debian)

```bash
# Actualizar repositorios
sudo apt update

# Instalar Python 3 y pip
sudo apt install python3 python3-pip

# Verificar instalación
python3 --version
pip3 --version
```

### macOS

```bash
# Instalar Homebrew si no está instalado
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Instalar Python
brew install python

# Verificar instalación
python3 --version
pip3 --version
```

---

## Paso 2: Instalación de Boto3

Boto3 es el SDK de AWS para Python.

### Instalación con pip

```bash
# Instalación
pip install boto3

# Verificar instalación
python -c "import boto3; print(boto3.__version__)"
```

### Instalación en Entorno Virtual (Recomendado)

```bash
# Crear entorno virtual
python -m venv backup_env

# Activar entorno (Windows)
backup_env\Scripts\activate

# Activar entorno (Linux/macOS)
source backup_env/bin/activate

# Instalar boto3
pip install boto3

# Crear archivo de requisitos
pip freeze > requirements.txt
```

!!! tip "Buena Práctica"
    Los entornos virtuales evitan conflictos entre dependencias de diferentes proyectos.

### Verificación

```python
# Ejecutar en Python
import boto3
print("Boto3 instalado:", boto3.__version__)
```

---

## Paso 3: Configuración de AWS

### 3.1. Crear una Cuenta AWS

1. Visita aws.amazon.com
2. Clic en "Crear cuenta de AWS"
3. Completa el registro
4. Se require tarjeta de crédito pero S3 tiene capa gratuita

### 3.2. Crear Usuario IAM

!!! warning "Importante"
    No uses las credenciales de la cuenta root. Crea siempre un usuario IAM.

Pasos en la consola AWS:

1. Ir a IAM (Identity and Access Management)
2. Usuarios → Añadir usuario
3. Nombre: `backup-script-user`
4. Tipo de acceso: Acceso mediante programación
5. Permisos: Adjunta la política `AmazonS3FullAccess`

Política IAM personalizada (ejemplo):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::mi-bucket-backups",
        "arn:aws:s3:::mi-bucket-backups/*"
      ]
    }
  ]
}
```

### 3.3. Obtener Credenciales

Al crear el usuario IAM, AWS genera:

- Access Key ID: Identificador público
- Secret Access Key: Clave privada

!!! danger "Atención"
    Guarda estas credenciales de forma segura. Solo se muestran una vez al crear el usuario. No las compartas ni las publiques.

---

## Paso 4: Configurar Credenciales AWS

### Opción 1: AWS CLI (Recomendado)

```bash
# Instalar AWS CLI
pip install awscli

# Configurar credenciales
aws configure
```

El comando solicitará:
```
AWS Access Key ID: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name: eu-west-1
Default output format: json
```

Ubicación de archivos:
- Linux/macOS: `~/.aws/credentials`
- Windows: `C:\Users\USERNAME\.aws\credentials`

### Opción 2: Variables de Entorno

```bash
# Linux/macOS
export AWS_ACCESS_KEY_ID="AKIAIOSFODNN7EXAMPLE"
export AWS_SECRET_ACCESS_KEY="wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY"
export AWS_DEFAULT_REGION="eu-west-1"

# Windows (PowerShell)
$env:AWS_ACCESS_KEY_ID="AKIAIOSFODNN7EXAMPLE"
$env:AWS_SECRET_ACCESS_KEY="wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY"
$env:AWS_DEFAULT_REGION="eu-west-1"
```

!!! warning "Importante"
    Si usas control de versiones, añade `.env` a tu `.gitignore` para no subir credenciales.

---

## Paso 5: Crear un Bucket S3

### Mediante la Consola AWS

1. Abrir el servicio S3 en la consola AWS
2. Clic en "Crear bucket"
3. Nombre del bucket: `backup-empresa-2026` (debe ser único)
4. Región: Elige la más cercana (ej: `eu-west-1`)
5. Configuración:
   - Bloquear acceso público
   - Habilitar versionado
   - Habilitar cifrado (SSE-S3)
6. Crear bucket

### Mediante Boto3

```python
import boto3

# Crear cliente S3
s3_client = boto3.client('s3')

# Nombre del bucket
bucket_name = 'backup-empresa-2026'

# Crear bucket
s3_client.create_bucket(
    Bucket=bucket_name,
    CreateBucketConfiguration={'LocationConstraint': 'eu-west-1'}
)

print(f"Bucket '{bucket_name}' creado")
```

---

## Verificación Final

Script para verificar la configuración:

```python
import boto3
import sys

def verificar_configuracion():
    try:
        s3 = boto3.client('s3')
        response = s3.list_buckets()
        
        print("Configuración correcta")
        print(f"Conexión establecida con AWS")
        print(f"Buckets disponibles: {len(response['Buckets'])}")
        
        for bucket in response['Buckets']:
            print(f"- {bucket['Name']}")
        
        return True
        
    except Exception as e:
        print("Error en la configuración:")
        print(f"{str(e)}")
        return False

if __name__ == "__main__":
    exito = verificar_configuracion()
    sys.exit(0 if exito else 1)
```

Salida esperada:
```
Configuración correcta
Conexión establecida con AWS
Buckets disponibles: 1
- backup-empresa-2026
```

---

## Conclusión

Si todas las verificaciones funcionan, el entorno está configurado correctamente y puedes continuar con la implementación del script de backup.

[Volver a Introducción](intro.md) | [Ir a Guía de Uso](usage.md)
