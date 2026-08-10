# Blueprint: aplicación personal para crear videos premium de 1 minuto a 1 hora

> Fecha de investigación: 2026-08-10. Este documento está pensado como guía inicial para construir una app de uso personal que convierta una idea en un video completo con escenas, cámara, iluminación, diálogos, música, voz, edición y controles de privacidad.


## 0. Estado actual: ¿ya se puede usar la aplicación?

No. **Todavía no hay una aplicación ejecutable en este repositorio**. Lo que existe ahora es un blueprint/documento de diseño para construirla. En la revisión del repositorio se observan archivos de configuración de la red Sepolia, metadata y assets, pero no hay todavía frontend, backend, instalador, comandos de arranque, ni integración real con proveedores de video/voz.

Para que se pueda usar de verdad, todavía hay que construir como mínimo:

1. **Interfaz de usuario** con botón principal **Crear video completo**.
2. **Backend/orquestador** que reciba idea, prompt o imagen y genere el plan completo.
3. **Adaptadores de proveedores** para video, voz, música/SFX e imágenes.
4. **Sistema de privacidad** para claves, consentimientos, assets, retención, auditoría y borrado de cachés.
5. **Motor de continuidad platino** con Character Bible, referencias maestras y validación por escena.
6. **Ensamblador/render** con FFmpeg o equivalente para unir video, voz, música, subtítulos y exportar MP4.

Entonces, la respuesta correcta es: **no se puede usar aún como app final; sí se puede usar el documento como guía técnica y de producto para empezar a construirla**.

## 1. Objetivo del producto

Crear una aplicación sencilla tipo “estudio de producción” donde el usuario escriba una idea y reciba un paquete completo:

- Guion estructurado por actos, escenas y beats.
- Storyboard con prompts visuales por escena.
- Plan de cámara: encuadres, lentes, movimientos, duración y transiciones.
- Plan de iluminación y look cinematográfico.
- Diálogos, narración, subtítulos y voces sintéticas.
- Música y efectos de sonido con licencias trazables.
- Render final en formatos vertical, horizontal o cuadrado.
- Bitácora de permisos, fuentes, políticas aplicadas y proveedores usados.

La versión inicial debe enfocarse en videos de 1 a 5 minutos. Para llegar a 1 hora, la app debe trabajar por capítulos/segmentos y ensamblar el resultado, porque los proveedores de video generativo suelen producir clips cortos o trabajos asíncronos que después se editan.

## 2. Investigación sustentada de proveedores y límites relevantes

### OpenAI

- La documentación oficial de OpenAI para video con Sora indica que el API de video permite crear, iterar y administrar videos, pero también avisa que los modelos `sora-2` y `sora-2-pro` y el Videos API están deprecados y cierran el 2026-09-24. Por eso no conviene diseñar la app dependiendo exclusivamente de Sora; debe existir una capa intercambiable de proveedores.
- En privacidad de API, OpenAI documenta que, por defecto, genera logs de monitoreo de abuso para uso de API y los conserva hasta 30 días, salvo requerimiento legal o necesidad de protección; también ofrece controles de retención para casos elegibles. La app debe guardar esa condición en su matriz de privacidad y evitar enviar datos personales innecesarios.

Fuentes: OpenAI video generation guide, OpenAI data controls.

### Runway

- Runway ofrece un portal para desarrolladores y API para capacidades generativas de video, imagen, audio y flujos relacionados.
- Runway publica políticas de privacidad, términos de uso y políticas de uso. Su documentación de seguridad indica que los assets subidos se configuran como privados por defecto, pero pueden compartirse si el usuario cambia la configuración. Sus términos también advierten que el usuario es responsable de servicios externos, credenciales, datos y consecuencias cuando habilita servicios no pertenecientes a Runway.
- Esto hace que Runway sea candidato fuerte para el motor de video, pero la app debe incluir control explícito de claves, consentimiento, revisión de contenido y trazabilidad.

Fuentes: Runway Developer Portal, Runway Privacy Policy, Runway Terms of Use, Runway Usage Policy, Runway security/privacy standards.

### ElevenLabs

- ElevenLabs ofrece APIs de texto a voz y herramientas de audio; su página de API indica cifrado en tránsito y reposo, compatibilidad con SOC 2, HIPAA y GDPR, y opciones como EU Data Residency y Zero Retention para controles más estrictos.
- Su política de privacidad explica que puede procesar grabaciones de audio y datos de voz para prestar servicios como síntesis de voz, doblaje, traducción o generación de modelos de voz. Para la app, cualquier clonación o uso de voz debe requerir consentimiento explícito y prueba de derechos.

Fuentes: ElevenLabs API, ElevenLabs Privacy Policy, ElevenLabs Safety.

### Stability AI

- Stability AI mantiene documentación de API REST para generación y edición de imágenes y audio, y existen capacidades históricas de imagen-a-video/Stable Video Diffusion. Puede servir como proveedor secundario o para assets visuales, pero se debe confirmar endpoint, modelo, duración y licencia al momento de implementación.

Fuente: Stability AI developer platform documentation.

## 3. Arquitectura propuesta

```text
[Interfaz web]
      |
      v
[Orquestador de proyecto]
      |-- Idea -> Brief creativo
      |-- Brief -> Guion largo/corto
      |-- Guion -> Escenas
      |-- Escenas -> Prompts visuales + cámara + luces
      |-- Diálogos -> TTS/voz
      |-- Música/SFX -> proveedor o librería licenciada
      |-- Video clips -> generación por escenas
      |-- Ensamble -> timeline + subtítulos + mezcla
      v
[Render final + paquete de trazabilidad]
```

### Módulos principales

1. **Brief inteligente**: transforma una idea simple en género, audiencia, duración, idioma, tono, formato y nivel de realismo.
2. **Guionista**: produce logline, sinopsis, escaleta, guion, diálogos y narración.
3. **Director visual**: define estilo premium, composición, color, cámara, lentes, iluminación y referencias no infractoras.
4. **Productor de escenas**: divide el video en clips de duración controlada y mantiene continuidad de personajes, vestuario, locaciones y objetos.
5. **Motor multi-proveedor**: Runway/OpenAI/Stability u otros detrás de una interfaz común para no depender de un solo proveedor.
6. **Voces y audio**: TTS, doblaje, música, SFX, mezcla, loudness y subtítulos.
7. **Editor automático**: ensambla clips, aplica transiciones, corrige color, sincroniza voz, música y subtítulos.
8. **Compliance y privacidad**: revisa políticas, consentimiento de voz/rostro, marcas, datos personales, menores, contenido sensible y derechos de música.
9. **Exportador**: MP4/WebM, 1080p/4K si el proveedor lo permite, vertical/horizontal/cuadrado, miniatura y descripción.

## 4. Flujo de uso recomendado

1. El usuario escribe: “Quiero un documental premium de 8 minutos sobre X”.
2. La app pregunta lo mínimo: duración, formato, estilo, idioma, público, si aparecerán personas reales y si se permite voz clonada.
3. La app genera un **Plan de Producción** editable.
4. El usuario aprueba o modifica escenas.
5. La app genera assets por escena.
6. La app compone timeline, audio y subtítulos.
7. El usuario revisa una versión preliminar.
8. La app reintenta escenas problemáticas y exporta el final.
9. La app entrega un reporte con proveedores, prompts, licencias, consentimientos y fecha.

## 5. Controles de privacidad, seguridad y automatización

### Reglas obligatorias en la app

- No enviar información personal sensible a proveedores si no es necesaria.
- Separar secretos en variables de entorno o gestor de secretos; nunca guardarlos en prompts, logs ni archivos exportados.
- Guardar consentimiento explícito para cualquier voz clonada, rostro real, parecido reconocible o material subido por el usuario.
- Bloquear contenido de abuso sexual infantil, explotación, incitación a violencia, odio, acoso, suplantación dañina o material ilegal.
- Marcar contenido generado por IA cuando aplique y conservar metadatos de procedencia cuando el proveedor los entregue.
- Validar licencias de música, imágenes y videos externos antes del render final.
- Mantener un modo “personal privado” que borre borradores locales y cachés después de exportar si el usuario lo activa.
- Registrar proveedor, modelo, fecha, prompt resumido, duración, costo aproximado y decisión de moderación por cada escena.

### Datos mínimos que debería guardar

- Proyecto: título, fecha, duración, idioma, formato, estado.
- Escenas: descripción, prompt visual, cámara, luces, voz, música, proveedor, estado.
- Assets: ruta local o URL temporal, hash, licencia, expiración, proveedor.
- Consentimientos: persona/voz/asset, alcance, fecha, documento o confirmación.
- Auditoría: acciones automáticas, errores, reintentos, exportaciones.

## 6. MVP recomendado

### Fase 1: Prototipo útil (1-2 semanas)

- App web local con formulario de idea.
- Generación de plan, guion y escenas.
- Exportación de un paquete JSON/Markdown con storyboard y prompts.
- Sin generación automática de video todavía, para validar calidad narrativa y flujo.

### Fase 2: Video corto automatizado (2-4 semanas)

- Integrar un proveedor de video por clips.
- Integrar TTS.
- Ensamblar con FFmpeg/MoviePy.
- Exportar videos de 1 a 5 minutos.
- Agregar subtítulos automáticos y música de librería autorizada.

### Fase 3: Producción premium (4-8 semanas)

- Multi-proveedor, reintentos por escena y selección de mejores tomas.
- Continuidad visual con bible de personajes/locaciones.
- Mezcla de audio, ducking de música y normalización.
- Panel de costos, privacidad y licencias.

### Fase 4: Videos largos (8+ semanas)

- Capítulos de 3 a 8 minutos con estructura narrativa.
- Render incremental y cola de trabajos.
- Revisión por bloques.
- Cache de assets, recuperación ante fallos y control de presupuesto.

## 7. Stack técnico sugerido

- **Frontend**: Next.js o React con editor de proyecto por escenas.
- **Backend**: Python FastAPI o Node.js/NestJS para orquestar jobs.
- **Cola**: Redis + BullMQ/Celery para generación asíncrona.
- **Base de datos**: PostgreSQL para proyectos, escenas, consentimientos y auditoría.
- **Archivos**: almacenamiento local cifrado para uso personal; S3-compatible si se requiere nube.
- **Render**: FFmpeg para ensamblaje, subtítulos, transiciones, mezcla y exportación.
- **IA texto**: modelo LLM para guion, prompts y revisión de seguridad.
- **IA video**: adaptadores por proveedor.
- **IA voz**: TTS con consentimiento y controles de retención.

## 8. Preguntas que conviene responder antes de construir

1. ¿Quieres que corra local en tu computadora, en la nube o ambas?
2. ¿La prioridad es realismo cinematográfico, animación, marketing, documentales, redes sociales o historias ficticias?
3. ¿Quieres usar personas/rostros/voces reales, o todo será ficticio?
4. ¿Presupuesto mensual aproximado para APIs de video, voz y música?
5. ¿Salida principal: YouTube horizontal, TikTok/Reels vertical, cursos, cine corto o uso privado?
6. ¿Necesitas español latino, español neutro, inglés u otros idiomas?

## 9. Primera decisión recomendada

Construir primero un **generador de planes premium** antes de conectar APIs costosas de video. Esto permite perfeccionar el guion, las escenas, cámara, luces y privacidad sin gastar créditos. Después se conecta un proveedor de video y uno de voz mediante adaptadores reemplazables.

## 10. Fuentes consultadas

- OpenAI, video generation guide: https://developers.openai.com/api/docs/guides/video-generation
- OpenAI, data controls in the API platform: https://developers.openai.com/api/docs/guides/your-data
- Runway Developer Portal: https://dev.runwayml.com/
- Runway Privacy Policy: https://runway.com/privacy-policy
- Runway Terms of Use: https://runway.com/terms-of-use
- Runway Usage Policy: https://runway.com/safety/usage-policy
- Runway security and privacy standards: https://help.runwayml.com/hc/en-us/articles/24300377879827-Understanding-Runway-s-security-and-privacy-standards
- ElevenLabs API: https://elevenlabs.io/api
- ElevenLabs Privacy Policy: https://elevenlabs.io/privacy-policy
- ElevenLabs Safety: https://elevenlabs.io/safety
- Stability AI Developer Platform: https://platform.stability.ai/docs/api-reference
- Google AI for Developers, Gemini API video generation: https://ai.google.dev/gemini-api/docs/video
- Google Cloud, Responsible AI and usage guidelines for Veo: https://docs.cloud.google.com/gemini-enterprise-agent-platform/models/video/responsible-ai-and-usage-guidelines

## 11. Aclaración importante: ¿qué pasa sin PR y sin `gh pr create`?

Si no se usa Pull Request ni el comando `gh pr create`, **no se rompe la aplicación ni se pierde el trabajo**. Lo que cambia es el flujo de entrega:

- **Con commit local, pero sin PR**: los cambios quedan guardados en Git dentro de la rama actual. Sirve perfecto para uso personal, pruebas locales y trabajo individual. El riesgo es que no hay una revisión formal ni una página de PR donde ver diferencias, comentarios y checklist.
- **Con repositorio privado y sin PR**: puedes trabajar directamente en `main` o en una rama personal. Para una app de uso personal es suficiente si haces backups y commits claros.
- **Con PR**: es mejor cuando quieres revisión, historial ordenado, comparación visual, aprobación antes de mezclar, automatizaciones de CI/CD o colaboración con otra persona.
- **Sin `gh pr create`, pero con PR manual**: puedes crear el PR desde la interfaz web de GitHub/GitLab/Bitbucket. El comando `gh pr create` solo automatiza esa acción; no es obligatorio.
- **Sin PR y sin GitHub**: puedes conservar la app localmente, exportar un ZIP, usar un disco externo, o sincronizar con un repositorio privado más adelante.

Para este proyecto personal, mi recomendación práctica es: **sí mantener Git y commits**, y usar PR solo si deseas revisión o despliegue más controlado. El PR no es requisito técnico para que la app funcione; es una herramienta de control de calidad.

## 12. Reenfoque solicitado: video completo desde idea, prompt o imagen

La experiencia debe sentirse como un solo botón: **Crear video completo**. El usuario no debería tener que comprar “créditos internos”, dividir clips manualmente ni pensar en escenas técnicas. Internamente la app sí puede segmentar el trabajo porque los motores de video suelen operar por clips, pero esa complejidad queda oculta.

Entrada admitida:

1. **Idea corta**: “Haz un video premium sobre una ciudad futurista”.
2. **Prompt detallado**: tono, duración, personajes, estilo visual, idioma, música y formato.
3. **Imagen base**: la app extrae estilo, personajes, composición, paleta de color y referencias visuales para construir el video.
4. **Idea + imagen**: flujo recomendado para máxima consistencia.

Salida esperada:

- Video final completo de 1 minuto a 1 hora.
- Guion, diálogos, narración, voces, música, efectos, subtítulos y miniatura.
- Reporte privado de generación: proveedor, fecha, prompts resumidos, consentimientos, licencias y políticas aplicadas.

## 13. Continuidad platino de personajes

Para lograr que los personajes mantengan el mismo rostro, edad, vestuario, proporciones y voz entre escenas, la app debe manejar una **Character Bible** por proyecto:

- **Imagen maestra del personaje**: generada o subida por el usuario, guardada como referencia principal.
- **Ficha de identidad**: nombre, edad aparente, rostro, piel, cabello, ojos, complexión, vestuario, accesorios, gestos y rasgos prohibidos de cambiar.
- **Referencia por escena**: cada prompt visual debe incluir los mismos rasgos esenciales, la imagen maestra y reglas negativas como “no cambiar rostro, edad, estructura facial ni color de ojos”.
- **Control de continuidad**: después de generar una escena, la app debe comparar rostro/vestuario/colores con referencias aprobadas y marcar desviaciones.
- **Regeneración selectiva**: si una escena falla, se repite solo esa escena, no todo el video.
- **Voz consistente**: cada personaje debe tener una voz fija; si es una voz clonada, se requiere consentimiento explícito. Si es una voz sintética ficticia, se guarda el ID de voz/proveedor.

Esto no garantiza perfección absoluta porque depende de cada proveedor, pero sí convierte la continuidad en un requisito medible y repetible, no en algo improvisado.

## 14. Sustento de privacidad, seguridad y automatización por diseño

La app debe implementar privacidad y automatización como parte del producto, no como nota final:

- **OpenAI API**: la documentación oficial de controles de datos indica que los datos enviados por API no se usan para entrenar modelos salvo opt-in, y describe retención para monitoreo/abuso y opciones de retención más estrictas para casos elegibles. Por eso la app debe minimizar datos personales y registrar qué se envió.
- **Runway**: su documentación pública de privacidad/seguridad indica tratamiento de assets y privacidad por defecto en assets subidos, además de políticas de uso. Por eso la app debe guardar estado de privacidad de assets, proveedor y permisos.
- **ElevenLabs**: sus políticas y documentación de voz exigen tener derecho/consentimiento para clonar o usar voces de terceros y bloquean usos engañosos o dañinos. Por eso la app debe pedir consentimiento antes de clonar voz y bloquear suplantación.
- **Google Veo/Gemini API**: su documentación oficial de video y guías de IA responsable describen generación de video, filtros de seguridad y códigos/razones de bloqueo. Por eso conviene diseñar adaptadores que manejen rechazos de seguridad sin romper todo el render.
- **Stability AI**: debe usarse como proveedor de assets visuales o video solo después de validar endpoints, licencias y política vigente al momento de integrar.

Automatizaciones obligatorias:

1. Validar prompt antes de generar.
2. Crear plan de producción interno.
3. Generar Character Bible si hay personajes.
4. Generar escenas internamente aunque el usuario vea “video completo”.
5. Validar continuidad de personaje por escena.
6. Validar licencias de música, voz e imágenes.
7. Ensamblar con FFmpeg o motor equivalente.
8. Exportar video final y reporte privado.
9. Borrar cachés temporales si está activo el modo personal privado.

## 15. Plan corregido de construcción

### Fase A: App personal completa en modo planificación

- Botón principal: **Crear video completo**.
- Entrada por idea, prompt, imagen o combinación.
- Generación automática de guion, personajes, Character Bible, plan visual, cámaras, luces, diálogos, música sugerida y reporte de privacidad.
- Sin créditos internos. Si hay costos, son los costos reales de los proveedores externos que conectes con tus propias claves.

### Fase B: Render automático real

- Integrar proveedor de video por adaptador.
- Integrar proveedor de voz/TTS con consentimiento.
- Generar internamente clips por escenas, pero mostrar al usuario un solo progreso de video completo.
- Ensamblar audio/video/subtítulos con FFmpeg.

### Fase C: Modo platino

- Validación de continuidad facial y de vestuario.
- Reintentos automáticos por escenas defectuosas.
- Control de estilo premium por presets: cine, documental, lujo, animación, tráiler, redes sociales.
- Exportación horizontal, vertical y cuadrada.

### Fase D: Videos largos hasta una hora

- División automática por capítulos.
- Render incremental con recuperación ante fallos.
- Continuidad global de personajes, voz, locaciones y tono.
- Ensamble final con capítulos, música, mezcla, subtítulos y metadatos.
