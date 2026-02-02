# Directorio de Imágenes

Este directorio contiene las imágenes utilizadas en la documentación.

## Imágenes Requeridas

### banner.png
- **Descripción**: Banner principal de la página de inicio
- **Dimensiones recomendadas**: 1200x400 píxeles
- **Formato**: PNG o JPG
- **Contenido sugerido**: Diseño relacionado con backups, nube, AWS S3

### diagrama.png
- **Descripción**: Diagrama de tipos de backup (Completo, Incremental, Diferencial)
- **Dimensiones recomendadas**: 800x600 píxeles
- **Formato**: PNG
- **Contenido sugerido**: Diagrama de flujo o infografía explicativa

## Cómo Añadir Imágenes

1. Coloca tus archivos de imagen en este directorio (`docs/img/`)
2. Referencia las imágenes en tus archivos Markdown con la sintaxis:

```markdown
![Texto alternativo](img/nombre_imagen.png)
```

## Generación de Placeholders

Si no tienes las imágenes, puedes:

1. **Usar herramientas online**:
   - [Placeholder.com](https://placeholder.com/)
   - [Lorem Picsum](https://picsum.photos/)

2. **Crear con Python/PIL**:
   ```python
   from PIL import Image, ImageDraw, ImageFont
   
   img = Image.new('RGB', (1200, 400), color='#3f51b5')
   d = ImageDraw.Draw(img)
   d.text((10,10), "AWS S3 Backups", fill=(255,255,255))
   img.save('banner.png')
   ```

3. **Usar herramientas de IA**:
   - DALL-E, Midjourney, Stable Diffusion
   - Buscar en [Unsplash](https://unsplash.com/) o [Pexels](https://www.pexels.com/)

## Nota Importante

⚠️ **Placeholders**: Actualmente, las referencias a imágenes en la documentación son placeholders. Reemplázalas con imágenes reales antes del despliegue final.

Para modo de desarrollo, la documentación funciona perfectamente sin las imágenes (solo mostrará el texto alternativo).
