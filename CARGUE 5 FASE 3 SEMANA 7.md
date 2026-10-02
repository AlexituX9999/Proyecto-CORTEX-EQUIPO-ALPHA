# Proyecto CORTEX - EQUIPO ALPHA 🧠

## 1. Descripción del Proyecto

CORTEX es un sistema de gestión de conocimiento permanente para bots y agentes de IA, diseñado para proporcionar capacidades de memoria semántica, episódica y procedimental. El proyecto implementa una arquitectura de memoria multinivel que permite a los agentes mantener, recuperar y actualizar conocimiento de manera persistente y estructurada.

**Objetivo Principal:** Crear una infraestructura robusta que permita que los bots/agentes de IA retengan y utilicen conocimiento contextual a través de múltiples sesiones.

---

## 2. Características Principales

- ✅ **Memoria Semántica (LTM)**: Base de conocimiento estructurada
- ✅ **Memoria Episódica (LTM)**: Registro de interacciones históricas
- ✅ **Memoria Procedimental**: Guías, protocolos y flujos operacionales
- ✅ **Recuperación Inteligente**: Búsqueda y filtrado contextual
- ✅ **Persistencia**: Almacenamiento en base de datos relacional
- ✅ **Escalabilidad**: Diseño modular para múltiples dominios

---

## 3. Arquitectura de Memoria

### 3.1 Estructura de Capas de Memoria

```
┌─────────────────────────────────────────────┐
│       CAPA DE INTERFAZ (API REST)           │
├─────────────────────────────────────────────┤
│    MOTOR DE RECUPERACIÓN (Retrieval)        │
├─────────────────────────────────────────────┤
│      CAPAS DE MEMORIA (Semántica/          │
│      Episódica/Procedimental)              │
├─────────────────────────────────────────────┤
│    BASE DE DATOS PERSISTENTE (PostgreSQL)   │
└─────────────────────────────────────────────┘
```

### 3.2 Tabla de Tipos de Memoria

| Tipo de Memoria | Categoría de Datos | Descripción | Tiempo de Vida | Ejemplo de Entrada |
|---|---|---|---|---|
| **Semántica (LTM)** | Conocimiento Factual | Hechos, definiciones y conceptos generales | Permanente | "Art. 123 CC: Definición de contrato" |
| **Semántica (LTM)** | Normas y Leyes | Legislación vigente y aplicable | Permanente | "Código Civil Colombiano - Vigente 2024" |
| **Semántica (LTM)** | Jurisprudencia | Casos históricos y precedentes relevantes | Permanente | "Caso Sánchez vs. Estado, 2021, Corte Constitucional" |
| **Semántica (LTM)** | Procedimientos | Protocolos y flujos estándar | Permanente | "Procedimiento de Divorcio: 1. Demanda, 2. Notificación..." |
| **Episódica (LTM)** | Historial de Clientes | Datos y contexto de usuarios específicos | Long-term | "Cliente: Juan García, Caso: Divorcio, Estado: En proceso" |
| **Episódica (LTM)** | Conversaciones Previas | Diálogos e interacciones históricas | Long-term | "Conversación del 15/01/2024: Juan preguntó sobre pensión" |
| **Procedimental** | Flujos de Trabajo | Guías operacionales y checklists | Permanente | "Checklist de Validación: ✓ Identidad, ✓ Domicilio..." |
| **Procedimental** | Patrones de Consulta | Respuestas frecuentes y templates | Permanente | "Template: Respuesta a solicitud de asesoría inicial" |

### 3.3 Esquema de Base de Datos

#### Tabla: `semantic_memory`
```sql
CREATE TABLE semantic_memory (
  id UUID PRIMARY KEY,
  category VARCHAR(100),           -- 'law', 'jurisprudence', 'procedure'
  title VARCHAR(500),
  content TEXT,
  domain VARCHAR(100),             -- 'civil', 'penal', 'comercial'
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW(),
  metadata JSONB
);
```

#### Tabla: `episodic_memory`
```sql
CREATE TABLE episodic_memory (
  id UUID PRIMARY KEY,
  user_id UUID,
  session_id UUID,
  interaction_type VARCHAR(50),    -- 'question', 'resolution', 'notification'
  content TEXT,
  context JSONB,                   -- Datos contextuales
  timestamp TIMESTAMP DEFAULT NOW(),
  importance_score INT DEFAULT 5   -- 1-10: relevancia
);
```

#### Tabla: `procedural_memory`
```sql
CREATE TABLE procedural_memory (
  id UUID PRIMARY KEY,
  process_name VARCHAR(200),
  steps JSONB,                     -- Array de pasos
  preconditions TEXT,
  postconditions TEXT,
  domain VARCHAR(100),
  created_at TIMESTAMP DEFAULT NOW(),
  version INT DEFAULT 1
);
```

### 3.4 Ciclo de Vida de la Memoria

```
1. ADQUISICIÓN
   └─ Bot recibe información nueva
   
2. CODIFICACIÓN
   └─ Clasificar tipo (Semántica/Episódica/Procedimental)
   └─ Extraer entidades y relaciones
   
3. CONSOLIDACIÓN
   └─ Validar datos
   └─ Buscar duplicados
   └─ Enriquecer con metadatos
   
4. ALMACENAMIENTO
   └─ Persistir en BD
   └─ Indexar para búsqueda
   
5. RECUPERACIÓN
   └─ Búsqueda contextual
   └─ Ranking por relevancia
   └─ Inyectar en prompt del bot
   
6. UTILIZACIÓN
   └─ Integrar en respuestas
   └─ Generar recomendaciones
```

### 3.5 Ejemplo Práctico: Bot Asesor Legal

**Caso de Uso:** Cliente pregunta "¿Cuál es el tiempo máximo para una demanda de divorcio?"

**Flujo de Memoria:**

| Paso | Memoria Accedida | Contenido | Salida |
|---|---|---|---|
| 1. Entrada | Episódica | "Cliente: Juan García, Caso anterior: Divorcio" | Contextualizar consulta |
| 2. Búsqueda | Semántica (Ley) | "Código Civil Art. 154: Plazo máximo 6 meses" | Información legal vigente |
| 3. Precedente | Semántica (Jurisprudencia) | "Caso Similar 2023: Se completó en 5 meses" | Caso real análogo |
| 4. Procedimiento | Procedimental | "Paso 1: Demanda → Paso 2: Citación → Paso 3: Audiencia" | Guía operacional |
| 5. Respuesta | Integrada | "Según ley y precedentes, el plazo es 6 meses. Procedimiento: ..." | Respuesta completa |

---

## 4. Módulos del Proyecto

### 4.1 Backend (API)
- **Framework:** FastAPI / Django
- **Base de Datos:** PostgreSQL
- **Endpoints principales:**
  - `POST /memory/semantic` - Almacenar conocimiento factual
  - `GET /memory/semantic/search` - Buscar en base de conocimiento
  - `POST /memory/episodic` - Registrar interacción
  - `GET /memory/retrieve` - Recuperar memoria contextual
  - `POST /memory/procedural` - Cargar procedimientos

### 4.2 Frontend (Opcional)
- **Interfaz de Administración:** Dashboard para gestionar memoria
- **Visualización:** Grafos de relaciones entre conceptos

### 4.3 Motor de Recuperación
- **Búsqueda Semántica:** Embeddings + Similitud Coseno
- **Filtrado Contextual:** Metadatos y entidades
- **Ranking:** Por relevancia, actualidad e importancia

---

## 5. Instalación y Uso

### 5.1 Requisitos
```bash
- Python 3.9+
- PostgreSQL 12+
- pip (gestor de paquetes)
```

### 5.2 Instalación
```bash
# Clonar repositorio
git clone https://github.com/AlexituX9999/Proyecto-CORTEX-EQUIPO-ALPHA.git
cd Proyecto-CORTEX-EQUIPO-ALPHA

# Crear entorno virtual
python -m venv venv
source venv/bin/activate  # En Windows: venv\Scripts\activate

# Instalar dependencias
pip install -r requirements.txt

# Configurar variables de entorno
cp .env.example .env
# Editar .env con credenciales de BD
```

### 5.3 Configurar Base de Datos
```bash
# Crear BD
createdb cortex_db

# Ejecutar migraciones
python manage.py migrate
# o (si usas Alembic)
alembic upgrade head
```

### 5.4 Iniciar Servidor
```bash
# FastAPI
uvicorn main:app --reload

# Django
python manage.py runserver
```

---

## 6. API Documentation

### Almacenar Memoria Semántica
```bash
POST /api/v1/memory/semantic
Content-Type: application/json

{
  "category": "law",
  "title": "Artículo 123 - Definición de Contrato",
  "content": "Un contrato es un acuerdo de voluntades...",
  "domain": "civil",
  "metadata": {
    "source": "Código Civil Colombiano",
    "year": 2024,
    "applicability": "nacional"
  }
}

Response:
{
  "id": "uuid-123",
  "status": "stored",
  "message": "Memoria semántica registrada exitosamente"
}
```

### Recuperar Memoria Contextual
```bash
GET /api/v1/memory/retrieve?query=divorcio&domain=civil&limit=5

Response:
{
  "results": [
    {
      "type": "semantic",
      "category": "law",
      "title": "Procedimiento de Divorcio",
      "relevance_score": 0.95
    },
    {
      "type": "episodic",
      "user_id": "user-456",
      "summary": "Cliente consultó sobre divorcio el 15/01/2024",
      "relevance_score": 0.87
    }
  ]
}
```

---

## 7. Estructura de Carpetas

```
Proyecto-CORTEX-EQUIPO-ALPHA/
├── backend/
│   ├── app/
│   │   ├── main.py
│   │   ├── routes/
│   │   │   ├── memory.py
│   │   │   └── retrieval.py
│   │   ├── models/
│   │   │   ├── semantic.py
│   │   │   ├── episodic.py
│   │   │   └── procedural.py
│   │   └── database/
│   │       └── schemas.py
│   ├── migrations/
│   └── requirements.txt
├── docs/
│   ├── architecture.md
│   ├── memory_types.md
│   └── api_reference.md
├── tests/
│   ├── test_memory.py
│   └── test_retrieval.py
├── .env.example
├── docker-compose.yml
└── README.md
```

---

## 8. Ejemplos de Uso Práctico

### Ejemplo 1: Bot Asesor Legal
```python
from cortex import MemoryManager, Retriever

# Inicializar
memory_mgr = MemoryManager(db_connection)
retriever = Retriever(memory_mgr)

# Almacenar ley
memory_mgr.store_semantic(
    category="law",
    title="Divorcio - Art. 154",
    content="Plazo máximo 6 meses desde demanda...",
    domain="civil"
)

# Usuario pregunta
query = "¿Cuánto tarda un divorcio?"
context = retriever.get_context(query, domain="civil")

# Generar respuesta
response = f"Según nuestros registros: {context['law']}. "
response += f"Caso similar: {context['jurisprudence']}."
print(response)
```

### Ejemplo 2: Bot Médico
```python
# Memoria Semántica: Síntomas de COVID
memory_mgr.store_semantic(
    category="disease_info",
    title="COVID-19 - Síntomas",
    content="Fiebre, tos seca, cansancio...",
    domain="medical"
)

# Memoria Episódica: Historial del paciente
memory_mgr.store_episodic(
    user_id="patient_123",
    interaction_type="consultation",
    content="Paciente reporta fiebre y tos"
)

# Recuperar contexto completo
patient_context = retriever.get_context(
    query="síntomas del paciente",
    user_id="patient_123"
)
```

---

## 9. Roadmap y Próximas Fases

### Fase 1 (Actual)
- [x] Diseño de arquitectura
- [x] Estructura de BD
- [ ] Implementación de API básica
- [ ] Tests unitarios

### Fase 2 (Q2 2024)
- [ ] Motor de búsqueda semántica con embeddings
- [ ] Dashboard de administración
- [ ] Documentación completa

### Fase 3 (Q3 2024)
- [ ] Integración con LLM (OpenAI, Claude)
- [ ] Sistema de actualización automática
- [ ] Análisis de relevancia

---

## 10. Contribuyentes

**Equipo ALPHA:**
- Líder del Proyecto: [Tu Nombre]
- Desarrolladores: [Nombres del equipo]
- Roles: Arquitectura, Backend, Frontend, QA

---

## 11. Licencia

Este proyecto está bajo la licencia **MIT**. Ver `LICENSE.md` para más detalles.

---

## 12. Contacto y Soporte

- 📧 Email: equipo.alpha@proyecto-cortex.com
- 📱 Issues: [GitHub Issues](https://github.com/AlexituX9999/Proyecto-CORTEX-EQUIPO-ALPHA/issues)
- 📚 Docs: [Documentación Completa](https://docs.proyecto-cortex.com)

---

**Última actualización:** Enero 2024  
**Versión:** 1.0.0-beta
