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

***AQUI EMPIEZA EL WORKFLOW n8n***
*(Se deben cargar Credenciales para las APIs de Gemini y Google Sheets y Gmail).*

1. El nodo Webhook recibe las respuestas almacenadas en el Google Sheets.
2. Edit Fields organiza y pasa a limpio los datos para enviarlos al primer Agente.
3. El primer Agente recibe los datos y compara el puesto elegido con las aptitudes y habilidades señaladas para determinar si el postulado es APTO o NO APTO.
4. El nodo IF compara la respuesta del Agente con "APTO", en caso de resultar true envia la informacion para continuar con el flujo, caso contrario se redirije a la rama false.
5. En caso de resultar false se envia un mail al postulado agradeciendo su propuesta pero lamentando no poder ser seleccionado.
6. Si el primer Agente decide que el postulado es APTO continua el flujo hacia el segundo agente el cual redactará 3 preguntas para realizar en la futura entrevista teniendo en cuenta los conocimientos detallados por el postulado.
7. El siguiente nodo Code (javascript) organiza la respuesta del Segundo agente para organizarlos en un JSON:

   '// Obtenemos el texto crudo que viene de Gemini
const rawText = $input.first().json.content.parts[0].text;

// Limpiamos posibles bloques de código markdown (```json ... ```) si la IA los incluyó
const cleanedText = rawText.replace(/```json|```/g, "").trim();
try {

  // Convertimos el texto en un objeto JSON real
  const parsedJson = JSON.parse(cleanedText);
  
  // Devolvemos el objeto para que los siguientes nodos puedan usarlo
  return parsedJson;
} catch (error) {

  // Si falla el parseo, devolvemos un error descriptivo
  return { 
    error: "No se pudo parsear el JSON", 
    raw: rawText 
  };
}'

9. El nodo final Sheets agrega la información del postulado y las preguntas hechas por el agente a una tabla de POSTULANTES APTOS.
