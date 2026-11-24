RAG para tickets de soporte
RAG para tickets de soporte 
Proyecto recomendado para el portafolio AI Engineer / Tech Lead (TESYS pide:
ingesta, chunking, embeddings, vector search, retrieval, reranking y generación,
más APIs en C#/Python y Azure AI). Basado en lo que Luis ya hace en Vector
(tickets recurrentes, templates, documentación), con datos sintéticos —
nunca tickets reales de Vector.
Repo sugerido
Nombre: support-ticket-rag
Visibilidad: público (es el portafolio; sin datos reales ni credenciales).
Estructura
support-ticket-rag/
├── README.md                 # qué hace, arquitectura, métricas, cómo correrlo
├── docs/architecture.md      # diagrama: ticket → retrieval → respuesta con citas
├── api/                      # ASP.NET Core, REST: POST /tickets/answer
├── rag-pipeline/             # Python: ingesta, chunking, embeddings, búsqueda
│   ├── ingest.py
│   ├── embed.py
│   └── evaluate.py
├── data/
│   ├── synthetic_tickets.json   # 60 tickets sintéticos (ver abajo)
│   └── docs/                    # 10-15 docs sintéticos de soporte
├── docker-compose.yml        # API + Qdrant (o Azure AI Search)
└── .github/workflows/ci.yml  # build + tests
Stack (lo que Luis ya domina)
Backend: ASP.NET Core, API REST
Pipeline RAG: Python, Azure OpenAI (embeddings + chat), Qdrant o Azure AI Search
Agente: 2 herramientas — buscar en docs, crear borrador de respuesta
Infra: Docker Compose, GitHub Actions

Paso 1 — esta noche (Semana 1, ~1 hora)
Crear el repo en GitHub y clonar.
Crear la estructura de carpetas y este README.
Generar data/synthetic_tickets.json: 60 tickets sintéticos en español,
   en 6 categorías (acceso/cuenta, correo, impresoras, red, software, facturación),
   cada uno con: id, título, descripción, categoría, prioridad, resolución
   y el doc que lo resuelve.
Escribir 10-15 docs sintéticos en data/docs/ (procedimientos cortos,
   150-300 palabras cada uno).
rag-pipeline/ingest.py: leer tickets + docs, chunking (~500 caracteres,
   overlap 50), guardar chunks con metadatos (fuente, categoría).
rag-pipeline/embed.py: embeddings con Azure OpenAI, guardar en Qdrant
   (Docker: docker run -p 6333:6333 qdrant/qdrant).

Paso 2 — Semana 1 (continuación)
Búsqueda vectorial simple: dado un ticket nuevo, top-5 chunks.
Medir: ¿el doc correcto está en el top-5? Anotar precisión antes del reranking.

Paso 3 — Semana 2
API en ASP.NET Core: POST /tickets/answer → respuesta con citas + confianza;
  si confianza baja, pasa a humano.
Agente con 2 herramientas (buscar en docs, crear borrador).
Evaluación: 20-30 preguntas con respuesta esperada; medir latencia,
  costo por consulta y precisión de retrieval antes/después del reranking.

Paso 4 — Semana 3
Docker Compose, CI/CD en GitHub Actions, README con arquitectura,
  métricas y demo (video corto o API viva).
Preguntas de evaluación (semilla, 10 de 20-30)
¿Cómo restablezco mi contraseña si no llega el correo de recuperación?
¿Por qué un ticket se queda sin asignar y a quién se escala?
¿Cómo configuro el correo de Help Desk como remitente por defecto?
¿Qué hacer si la impresora no aparece en la red?
¿Cómo se agrupa la configuración de correo en una sección?
¿Qué datos necesita un ticket para clasificarlo como urgente?
¿Cómo se exportan los campos de una plantilla?
¿Qué hacer si un usuario no puede entrar por token expirado?
¿Cómo se crea un ticket recurrente?
¿Qué revisión hacer antes de actualizar un plugin a una versión mayor?
Reglas
Datos sintéticos / anonimizados únicamente. Nada de Vector real.
Sin credenciales en el repo (.env.example, nunca .env).
Cada respuesta del RAG cita su fuente; sin cita, no se responde, se escala.