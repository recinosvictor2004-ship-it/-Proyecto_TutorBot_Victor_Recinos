# 📚 TutorBot – Sistema Automatizado de Gestión de Tutorías  
Automatización completa de registro, asignación, seguimiento y recordatorios de tutorías académicas usando **Telegram Bot + n8n + Google Sheets + Google Gemini**.

---

## 🖼️ Vista General del Proyecto
> Coloca aquí una imagen general del flujo completo exportado desde n8n.

Ejemplo:

docs/img/flujo_completo.png


---

## 🚀 Descripción General
TutorBot es un asistente académico que permite a estudiantes solicitar tutorías desde Telegram.  
El flujo en **n8n** gestiona automáticamente:

- Registro y seguimiento de sesiones del usuario  
- Solicitud de tutorías mediante un wizard guiado  
- Validación de fechas y materias  
- Asignación automática de tutor disponible  
- Notificación al tutor asignado  
- Consulta y cancelación de tutorías  
- Recordatorios diarios de tutorías próximas  
- Reporte semanal de actividad  
- Base de datos en Google Sheets totalmente integrada  

---

## 🧩 Arquitectura del Sistema

Telegram Bot → n8n → Google Gemini → Google Sheets → Telegram (respuesta)


### 🖼️ Imagen de la Arquitectura
> Coloca aquí tu diagrama PNG exportado desde Draw.io o n8n.

![Texto alternativo](docs/img/arquitectura.png)


---

## 🗂 Estructura del Proyecto
''' text 
TutorBot/
│
├── workflows/
│   └── My workflow 2.json
│
├── docs/
│   ├── arquitectura.png
│   └── img/
│       ├── flujo_completo.png
│       ├── flujo_solicitud.png
│       ├── flujo_asignacion.png
│       ├── flujo_cancelacion.png
│       ├── flujo_recordatorios.png
│       └── flujo_reporte.png
│
├── .gitignore
└── README.md

'''
---

## 📄 Base de Datos (Google Sheets)

El sistema usa un documento llamado **TutorBot_DB** con las siguientes hojas:

### **1. TUTORES**
| Campo | Descripción |
|-------|-------------|
| id_tutor | Identificador |
| nombre | Nombre del tutor |
| especialidad_materias | Materias que puede impartir |
| estado | Activo / Inactivo |

### **2. DISPONIBILIDAD**
| Campo | Descripción |
|-------|-------------|
| id_dispo | ID de disponibilidad |
| id_tutor | Tutor asociado |
| dia_semana | Día |
| hora_inicio | Inicio |
| hora_fin | Fin |
| estado | Libre / Ocupado |

### **3. TUTORIAS**
| Campo | Descripción |
|-------|-------------|
| id_tutoria | ID único |
| id_estudiante | Telegram ID |
| id_tutor | Tutor asignado |
| materia | Materia |
| fecha | Fecha |
| hora | Hora |
| estado | Asignada / Cancelada / Confirmada |

### **4. SESSIONS**
| Campo | Descripción |
|-------|-------------|
| telegram_user | ID del usuario |
| pantalla_actual | Pantalla del wizard |
| paso_actual | Paso del flujo |
| datos_parciales | JSON con datos temporales |

---

## 🔄 Flujo Automatizado (n8n)

### 🖼️ Imagen del Flujo Completo

docs/img/arquitectura.png


---

## 1️⃣ Telegram Trigger
Recibe mensajes del usuario y activa el flujo principal.

---

## 2️⃣ Google Gemini – Agente Inteligente
El nodo **AI Agent** interpreta el mensaje del usuario y ejecuta acciones usando herramientas:

- leer_tutores  
- leer_disponibilidad  
- consultar_tutorias  
- registrar_tutoria  
- cancelar_tutoria  
- leer_sesion  
- guardar_sesion  
- notificar_tutor  

El prompt incluye reglas estrictas para:

- Validar fechas  
- Validar materias  
- No inventar datos  
- Mantener contexto entre mensajes  
- Responder siempre en español  

---

## 3️⃣ Memoria de Sesión
El nodo **Simple Memory** mantiene las últimas 10 interacciones del usuario para conversaciones naturales.

---

## 4️⃣ Herramientas de Google Sheets
El flujo incluye 8 herramientas conectadas a Google Sheets:

- leer_tutores  
- leer_disponibilidad  
- consultar_tutorias  
- registrar_tutoria  
- cancelar_tutoria  
- leer_sesion  
- guardar_sesion  
- Escribir datos iniciales (sembrado)

---

## 5️⃣ Wizard de Solicitud de Tutoría

### 🖼️ Imagen del Subflujo de Solicitud

docs/img/flujo_solicitud.png


Pasos:

1. Mostrar materias disponibles  
2. Solicitar materia  
3. Solicitar fecha  
4. Validar formato y que sea futura  
5. Buscar tutor disponible  
6. Confirmar con el usuario  
7. Registrar tutoría  
8. Notificar al tutor  

---

## 6️⃣ Cancelación de Tutorías

### 🖼️ Imagen del Subflujo de Cancelación

![Texto alternativo](cancelacion.png)



El usuario indica qué tutoría cancelar → se actualiza el estado en Sheets.

---

## 7️⃣ Consulta de Tutorías

### 🖼️ Imagen del Subflujo de Consulta


Se filtran tutorías por el ID del estudiante.

---

## 8️⃣ Recordatorios Automáticos (cada día 07:00)

![Texto alternativo](docs/img/recordatorio.png)


### 🖼️ Imagen del Subflujo de Recordatorios

![Texto alternativo](docs/img/recordatoriotelegram.png)



El sistema:

- Lee tutorías  
- Filtra las que ocurren mañana  
- Envía recordatorio por Telegram  

---

## 9️⃣ Reporte Semanal (cada lunes 08:00)

### 🖼️ Imagen del Subflujo de Reporte

[Texto alternativo](docs/img/reporte.png)


Genera:

- Conteo por estado  
- Conteo por materia  
- Rango de fechas  
- Mensaje resumen para administración  

---

## 🤖 Inteligencia Artificial (Gemini)
Gemini se usa para:

- Interpretar mensajes  
- Guiar el wizard  
- Validar datos  
- Generar respuestas naturales  
- Crear IDs únicos de tutorías  
- Construir mensajes para tutores y estudiantes  

---

## 📦 Archivo del Flujo
El flujo principal se encuentra en:

workflows/My workflow 2.json


---

## 🎓 Resumen para Presentación Académica
TutorBot demuestra:

- Integración avanzada entre n8n, Telegram y Google Sheets  
- Uso de IA para control de flujo y validación  
- Persistencia de estado entre mensajes  
- Automatización de procesos académicos reales  
- Arquitectura modular y escalable  
- Documentación profesional lista para GitHub  

---

## 👨‍💻 Autor
**Victor Manuel Recinos Gómez**  
Ingeniería en Ciencias y Sistemas – USAC  
