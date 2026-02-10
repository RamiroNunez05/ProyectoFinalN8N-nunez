### Recruiter Assistant (n8n) - Núñez Ramiro
# Proyecto Final - Curso AI Automation | Coderhouse

Este Workflow consiste en un filtro de postulantes para uno o varios puestos de trabajo según se disponga en un formulario de Google,
con el fin de realizar una interetapa donde se facilite el trabajo manual de un Reclutador del área de RRHH para desplazar
postulantes a la etapa final de la entrevista, agregando incluso preguntas técnicas redactadas por un agente de IA para poder
corroborar los conocimientos así descritos por el entrevistado.

**IMPORTANTE:**
Este flujo necesita una integración previa de un Google Forms y Google Sheets al mismo.

Los campos usados para el forms en este proyecto son los siguientes:
- Puesto al que postula (al menos uno)
- Nombre Completo
- Correo electrónico
- Número de teléfono (opcional)
- ¿Cuál es su nivel educativo más alto alcanzado? (Educación Secundaria/Bachillerato ... Doctorado)
- Años de experiencia laboral relevante (0 ... más de 10)
- Indique su disponibilidad para comenzar a trabajar (inmediata ... más de 4 semanas)
- ¿Cómo calificaría su dominio del Inglés como idioma? (básico ... nativo)
- Describa sus Habilidades Técnicas y Blandas (texto largo)

Posteriormente las respuestas se alojaban en un Google Sheets el cual utilizaba el siguiente App Script para enviar las respuestas
automaticamente al Webhook del workflow:

'function alRecibirPostulacion(e) {

  var urlDen8n = 'PEGAR_LA_URL_DE_SU_WEBHOOK';
  
  // Obtener las respuestas del formulario
  var respuestas = e.namedValues;
  
  // Mapeo de campos
  var datos = {
    "puesto": respuestas["Puesto al que postula"] ? respuestas["Puesto al que postula"][0] : "",
    "nombre": respuestas["Nombre Completo"] ? respuestas["Nombre Completo"][0] : "",
    "email": respuestas["Correo Electrónico"] ? respuestas["Correo Electrónico"][0] : "",
    "telefono": respuestas["Número de Teléfono"] ? respuestas["Número de Teléfono"][0] : "",
    "educacion": respuestas["¿Cuál es su nivel educativo más alto alcanzado?"] ? respuestas["¿Cuál es su nivel educativo más alto alcanzado?"][0] : "",
    "experiencia": respuestas["Años de experiencia laboral relevante"] ? respuestas["Años de experiencia laboral relevante"][0] : "",
    "disponibilidad": respuestas["Indique su disponibilidad para comenzar a trabajar"] ? respuestas["Indique su disponibilidad para comenzar a trabajar"][0] : "",
    "nivel_ingles": respuestas["¿Cómo calificaría su dominio del Inglés como idioma?"] ? respuestas["¿Cómo calificaría su dominio del Inglés como idioma?"][0] : "",
    "habilidades": respuestas["Describa sus Habilidades Técnicas y Blandas"] ? respuestas["Describa sus Habilidades Técnicas y Blandas"][0] : ""
  };
  
  // Configuración del envío
  
  var opciones = {
    'method': 'post',
    'contentType': 'application/json',
    'payload': JSON.stringify(datos)
  };
  
  // Ejecución directa (sin manejo de errores)
  
  UrlFetchApp.fetch(urlDen8n, opciones);
}'

**------------------------------------------------------------------------------------------------------------------**
***En caso de probar solo el workflow podria enviar los siguientes casos de prueba al webhook de n8n:***

Caso 1 (perfil apto):
{
  "Puesto al que postula": "Analista de Datos Junior",
  "Nombre Completo": "Mariana Costa",
  "Correo Electrónico": "un_correo@ejemplo.com",
  "Número de Teléfono": "+54 1111 222333",
  "¿Cuál es su nivel educativo más alto alcanzado?": "Licenciatura/Grado",
  "Años de experiencia laboral relevante": 2,
  "Indique su disponibilidad para comenzar a trabajar": "inmediata",
  "¿Cómo calificaría su dominio del Inglés como idioma?": "intermedio",
  "Describa sus Habilidades Técnicas y Blandas": "Técnicas: Dominio de SQL, Python (Pandas/Matplotlib), Tableau y limpieza de datos en Excel. Blandas: Pensamiento crítico, gran capacidad de organización y facilidad para explicar hallazgos técnicos a audiencias no técnicas."
}

------------------------------------------------------------------------------------------------------------------
Caso 2 (perfil no apto):
{
  "Puesto al que postula": "Diseñador UX/UI",
  "Nombre Completo": "Carlos Martínez",
  "Correo Electrónico": "un_correo@ejemplo.com",
  "Número de Teléfono": "+54 1111 222333",
  "¿Cuál es su nivel educativo más alto alcanzado?": "Educación Secundaria/Bachillerato",
  "Años de experiencia laboral relevante": 0,
  "Indique su disponibilidad para comenzar a trabajar": "más de 4 semanas",
  "¿Cómo calificaría su dominio del Inglés como idioma?": "basico",
  "Describa sus Habilidades Técnicas y Blandas": "Técnicas: Manejo básico de Paint y navegación en internet. Blandas: Simpático y con ganas de aprender cosas nuevas desde cero."
}

------------------------------------------------------------------------------------------------------------------
Caso 3 (perfil apto):
{
  "Puesto al que postula": "Desarrollador de Software Senior",
  "Nombre Completo": "Ramiro Nuñez",
  "Correo Electrónico": "un_correo@ejemplo.com",
  "Número de Teléfono": "+54 1111 222333",
  "¿Cuál es su nivel educativo más alto alcanzado?": "Licenciatura/Grado",
  "Años de experiencia laboral relevante": 8,
  "Indique su disponibilidad para comenzar a trabajar": "inmediata",
  "¿Cómo calificaría su dominio del Inglés como idioma?": "avanzado",
  "Describa sus Habilidades Técnicas y Blandas": "Técnicas: Arquitectura de microservicios, AWS, React, Node.js y Python. Blandas: Liderazgo de equipos técnicos, mentoría y comunicación asertiva en entornos remotos."
}
