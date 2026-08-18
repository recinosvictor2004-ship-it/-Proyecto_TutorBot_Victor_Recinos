# 📘 Proyecto TutorBot — Sistema Automatizado de Tutorías (n8n + IA + Telegram + Google Sheets)

## 📌 Descripción General

TutorBot es un sistema automatizado que permite gestionar tutorías mediante un chatbot en Telegram.  
El proyecto está dividido en cuatro flujos independientes dentro de n8n, cada uno con su propio trigger, pero todos conectados por la misma base de datos en Google Sheets.

Los flujos son:
1. AI Agent (Chatbot de tutorías)
2. Sembrado de base de datos (manual)
3. Recordatorios diarios (programado)
4. Reporte semanal (programado)

---

# 🗂️ Base de Datos (Google Sheets)

El documento contiene cuatro hojas:

## 1. **TUTORES**
- id_tutor  
- nombre  
- especialidad_materias  
- estado  

## 2. **DISPONIBILIDAD**
- id_dispo  
- id_tutor  
- dia_semana  
- hora_inicio  
- hora_fin  
- estado  

## 3. **TUTORIAS**
- id_tutoria  
- id_estudiante  
- id_tutor  
- materia  
- fecha  
- hora  
- estado  

## 4. **SESSIONS**
- telegram_user  
- pantalla_actual  
- paso_actual  
- datos_parciales  

---

# 🤖 PARTE 1 — AI Agent (Chatbot de Tutorías)

Este flujo atiende al estudiante en tiempo real mediante Telegram.

### Componentes del flujo

### **Telegram Trigger**
- Activa el flujo cuando el usuario envía un mensaje.
- Configurado para escuchar únicamente mensajes de texto.

### **AI Agent**
- Es el cerebro del sistema.
- Interpreta el mensaje del usuario.
- Usa un `systemMessage` que contiene:
  - reglas del bot
  - estructura de la base de datos
  - flujo de solicitud de tutoría
  - flujo de consulta de tutorías
  - flujo de cancelación
- Decide qué herramienta usar.
- Genera la respuesta final.

### **Modelo de Lenguaje (Gemini)**
- Procesa lenguaje natural.
- Extrae datos como fechas, materias e IDs.
- Genera respuestas coherentes.

### **Simple Memory**
- Guarda las últimas 10 interacciones del usuario.
- Permite flujos de varios pasos.

### **Herramientas del AI Agent**
El agente usa herramientas para interactuar con Google Sheets:

- leer_tutores  
- leer_disponibilidad  
- consultar_tutorias  
- registrar_tutoria  
- cancelar_tutoria  
- leer_sesion  
- guardar_sesion  
- notificar_tutor  

Cada herramienta está configurada con:
- `documentId`
- `sheetName`
- `operation`
- `matchingColumns`
- valores dinámicos con `$fromAI()`

### **Responder por Telegram**
- Envía la respuesta final al usuario.

---

# 🌱 PARTE 2 — Sembrado de Base de Datos (Manual)

Este flujo se ejecuta una sola vez para crear las tablas iniciales.

### Componentes:
1. Manual Trigger  
2. Nodos Code (generan datos en JSON)  
3. Google Sheets (appendOrUpdate)

Ejemplo de nodo Code para sembrar tutorías:

```javascript
return [
  {
    json: {
      id_tutoria: 'TU001',
      id_estudiante: '1794562485',
      id_tutor: 'T001',
      materia: 'Matemática',
      fecha: '2026-08-17',
      hora: '08:00',
      estado: 'Asignada'
    }
  }
];
-----
### ⏰ PARTE 3 — Recordatorios Diarios (Programado)
Este flujo envía recordatorios automáticos de tutorías del día siguiente.

Componentes:
Schedule Trigger (07:00)

Leer TUTORIAS

Nodo Code (filtra tutorías de mañana)

Telegram (envía recordatorio)

Ejemplo de filtrado:

const tutorias = $input.all();
const hoy = new Date();
const manana = new Date(hoy.getFullYear(), hoy.getMonth(), hoy.getDate() + 1);

const fechaManana = manana.toISOString().split('T')[0];

return tutorias
  .filter(t => t.json.fecha === fechaManana && (t.json.estado === 'Asignada' || t.json.estado === 'Confirmada'))
  .map(t => ({
    json: {
      id_estudiante: t.json.id_estudiante,
      mensaje: `Recordatorio: tienes tutoría de ${t.json.materia} el ${t.json.fecha} a las ${t.json.hora}.`
    }
  }));


-------

📊 PARTE 4 — Reporte Semanal (Programado)
Este flujo genera métricas de actividad cada lunes.

Componentes:
Schedule Trigger (lunes 08:00)

Leer TUTORIAS

Nodo Code (calcula métricas)

Telegram (envía reporte)

Ejemplo de cálculo:

const tutorias = $input.all();
const hoy = new Date();
const semanaPasada = new Date(hoy.getFullYear(), hoy.getMonth(), hoy.getDate() - 7);

const recientes = tutorias.filter(t => new Date(t.json.fecha) >= semanaPasada);

const porEstado = {};
const porMateria = {};

recientes.forEach(t => {
  porEstado[t.json.estado] = (porEstado[t.json.estado] || 0) + 1;
  porMateria[t.json.materia] = (porMateria[t.json.materia] || 0) + 1;
});

return [
  {
    json: {
      mensaje: `Reporte semanal:\n\nPor estado:\n${JSON.stringify(porEstado, null, 2)}\n\nPor materia:\n${JSON.stringify(porMateria, null, 2)}`
    }
  }
];

-----

🔗 Conexión entre las 4 partes
Las partes no están conectadas con flechas en n8n.
La conexión real es:

👉 El mismo Google Sheets (documentId compartido)
El AI Agent escribe tutorías.

El flujo diario las lee para enviar recordatorios.

El flujo semanal las lee para generar métricas.

El flujo manual crea las tablas iniciales.

-------

🧠 Sub‑flujos del AI Agent
1. Solicitar tutoría
Elegir materia

Elegir fecha

Confirmar tutoría

Registrar en Google Sheets

Notificar al tutor

2. Consultar tutorías
Leer tutorías del estudiante

Mostrar estado y fechas

3. Cancelar tutoría
Verificar tutoría

Actualizar estado

Notificar al tutor

Confirmar al estudiante

🛠️ Tecnologías Utilizadas
n8n

Telegram Bot API

Google Sheets API

Google Gemini AI

JavaScript (nodos Code)

🏁 Conclusión
TutorBot es un sistema modular, escalable y completamente automatizado que integra IA, mensajería y base de datos en un flujo robusto.
Su arquitectura basada en triggers y herramientas permite:

atención en tiempo real

persistencia de datos

automatización diaria

reportes semanales

flujos conversacionales inteligentes