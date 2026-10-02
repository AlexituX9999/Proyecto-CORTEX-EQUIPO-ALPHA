# Proyecto-CORTEX-EQUIPO-ALPHA
Perfil del Asistente : Asistente de moda 
<img width="617" height="498" alt="WhatsApp Image 2026-08-14 at 11 51 43 AM" src="https://github.com/user-attachments/assets/9b46e3b8-079c-4cfc-9ff6-fca41296bd13" />

## 2.Atención con las reglas lógicas definidas.

Módulo de preprocesamiento que filtra y prioriza la entrada del usuario antes de que llegue al motor de recomendación/generación. Su función es reducir ruido y asegurar que el modelo atienda a las señales relevantes para moda (prenda, talla, color, estilo, ocasión, presupuesto, marca).

1. Definición de "Ruido"

Ruido es toda la información contenida en el mensaje del usuario que no aporta valor semántico o funcional para resolver una tarea de moda — es decir, que no ayuda a identificar la prenda, el atributo (talla/color/material), la ocasión, el presupuesto o la acción solicitada (buscar, comparar, recomendar, combinar).
Lo que NO es ruido (señal, aunque sea breve)
Sustantivos de dominio: vestido, blazer, tenis, cartera
Atributos: talla M, color negro, algodón, entallado
Ocasión: boda, oficina, casual, entrevista
Restricciones: presupuesto de $50, sin tacón, veg-friendly (cuero sintético)
Negaciones y condicionales: "no quiero estampados", "a menos que tengan envío rápido"
Verbos de acción: comparar, combinar, recomendar, buscar, devolver
Ajustes recomendados para el dominio de moda:
La regla base es un buen punto de partida, pero en moda el pedido real suele depender también de verbos de acción y restricciones/negaciones, que no son sustantivos y pueden aparecer en cualquier parte del texto, no solo al final.
Reglas complementarias
Umbral variable, no fijo: en lugar de solo "500 palabras", considerar también densidad informativa (ratio sustantivos-de-dominio / palabras totales). Un mensaje de 200 palabras con puro relleno también debería activar el filtro.
Prioridad a atributos numéricos: precios, tallas y fechas ("$50", "talla 38", "para el viernes") nunca se descartan, sin importar su posición en el texto.
Ventana de contexto reciente: si el usuario corrige algo ("mejor que sea azul, no rojo"), la corrección más reciente sobrescribe a la anterior.
[memory_architecture_implementation.py](https://github.com/user-attachments/files/32971642/memory_architecture_implementation.py)
"""
CORTEX - Sistema de Arquitectura de Memoria
Implementación simulada de las capas de memoria (LTM, STM, Procedimental)
Semana 7 - Diseño de Base de Datos de Memoria Semántica
"""

from dataclasses import dataclass
from typing import Dict, List, Optional
from enum import Enum
import json
from datetime import datetime

# ============================================================================
# 1. ENUMERACIONES Y TIPOS
# ============================================================================

class MemoryType(Enum):
    """Tipos de memoria según el modelo cognitivo"""
    SEMANTIC = "Semántica (LTM)"      # Long-Term Memory - Enciclopedia
    EPISODIC = "Episódica (STM)"      # Short-Term Memory - Contexto actual
    PROCEDURAL = "Procedimental"      # Algoritmos y flujos


# ============================================================================
# 2. MEMORIA SEMÁNTICA (LTM) - Enciclopedia Interna
# ============================================================================

@dataclass
class FashionConcept:
    """Conceptos fundamentales de moda en la memoria semántica"""
    concept_id: str
    name: str
    description: str
    typical_colors: List[str]
    typical_silhouettes: List[str]
    
    def to_dict(self):
        return self.__dict__


@dataclass
class CompatibilityRule:
    """Reglas de compatibilidad entre prendas"""
    rule_id: str
    garment1: str
    garment2: str
    compatible: bool
    reason: str
    
    def to_dict(self):
        return self.__dict__


@dataclass
class SocialContext:
    """Contextos sociales y códigos de vestimenta"""
    context_id: str
    name: str
    dress_code: str
    recommended_colors: List[str]
    ideal_silhouettes: List[str]
    restrictions: str
    
    def to_dict(self):
        return self.__dict__


class SemanticMemory:
    """
    Memoria Semántica - Enciclopedia interna del bot
    Contiene conocimiento general que NO cambia entre usuarios
    """
    
    def __init__(self):
        self.concepts: Dict[str, FashionConcept] = {}
        self.compatibility_rules: Dict[str, CompatibilityRule] = {}
        self.social_contexts: Dict[str, SocialContext] = {}
        self._initialize_default_data()
    
    def _initialize_default_data(self):
        """Carga los datos por defecto de moda"""
        
        # Conceptos de moda
        self.concepts = {
            "001": FashionConcept(
                concept_id="001",
                name="Minimalismo",
                description="Menos es más, líneas limpias",
                typical_colors=["Neutral", "Blanco", "Negro"],
                typical_silhouettes=["Oversize", "Relajado"]
            ),
            "002": FashionConcept(
                concept_id="002",
                name="Maximalismo",
                description="Abundancia de patrones y colores",
                typical_colors=["Multicolor", "Vibrante"],
                typical_silhouettes=["Ajustado", "Decorado"]
            ),
            "003": FashionConcept(
                concept_id="003",
                name="Casual-Sport",
                description="Moda urbana y atlética",
                typical_colors=["Negro", "Gris", "Azul"],
                typical_silhouettes=["Oversize", "Cómodo"]
            ),
            "004": FashionConcept(
                concept_id="004",
                name="Elegancia Clásica",
                description="Formal y refinado",
                typical_colors=["Negro", "Azul marino", "Blanco"],
                typical_silhouettes=["Ajustado", "Estructurado"]
            ),
        }
        
        # Reglas de compatibilidad
        self.compatibility_rules = {
            "001": CompatibilityRule(
                rule_id="001",
                garment1="Camisa Blanca",
                garment2="Pantalón Negro",
                compatible=True,
                reason="Contraste limpio, combinación clásica"
            ),
            "002": CompatibilityRule(
                rule_id="002",
                garment1="Estampado",
                garment2="Estampado",
                compatible=False,
                reason="Demasiados patrones, visual caótico"
            ),
            "003": CompatibilityRule(
                rule_id="003",
                garment1="Color Pastel",
                garment2="Oversize",
                compatible=True,
                reason="Suave y elegante, proporciones armónicas"
            ),
        }
        
        # Contextos sociales
        self.social_contexts = {
            "001": SocialContext(
                context_id="001",
                name="Cena Romántica",
                dress_code="Smart Casual/Elegante",
                recommended_colors=["Negro", "Vino", "Azul"],
                ideal_silhouettes=["Ajustado", "Sofisticado"],
                restrictions="Evitar casual"
            ),
            "002": SocialContext(
                context_id="002",
                name="Reunión Corporativa",
                dress_code="Formal Profesional",
                recommended_colors=["Neutro", "Negro", "Azul"],
                ideal_silhouettes=["Clásico", "Estructurado"],
                restrictions="Evitar estampados vibrantes"
            ),
            "003": SocialContext(
                context_id="003",
                name="Fin de Semana",
                dress_code="Casual Cómodo",
                recommended_colors=["Variado"],
                ideal_silhouettes=["Oversize", "Relajado"],
                restrictions="Ninguna"
            ),
        }
    
    def get_concept(self, concept_id: str) -> Optional[FashionConcept]:
        """Recuperar un concepto de moda"""
        return self.concepts.get(concept_id)
    
    def check_compatibility(self, garment1: str, garment2: str) -> Optional[CompatibilityRule]:
        """Verificar compatibilidad entre dos prendas"""
        for rule in self.compatibility_rules.values():
            if (rule.garment1.lower() in garment1.lower() and 
                rule.garment2.lower() in garment2.lower()):
                return rule
        return None
    
    def get_context(self, context_name: str) -> Optional[SocialContext]:
        """Obtener un contexto social específico"""
        for context in self.social_contexts.values():
            if context.name.lower() == context_name.lower():
                return context
        return None


# ============================================================================
# 3. MEMORIA EPISÓDICA (STM) - Contexto del Usuario
# ============================================================================

@dataclass
class UserProfile:
    """Perfil del usuario actual - Memoria Episódica"""
    user_id: str
    name: str
    age: int
    gender: str
    preferred_style: str
    budget: str
    size: str
    restrictions: List[str]
    
    def to_dict(self):
        return self.__dict__


@dataclass
class ConversationContext:
    """Contexto de la conversación actual"""
    conversation_id: str
    user_id: str
    timestamp: str
    current_topic: str
    recommendations_given: List[str]
    user_feedback: List[str]
    
    def to_dict(self):
        return self.__dict__


class EpisodicMemory:
    """
    Memoria Episódica - Contexto actual de la sesión
    Se reinicia cada vez que inicia una nueva sesión
    """
    
    def __init__(self):
        self.user_profile: Optional[UserProfile] = None
        self.conversation_context: Optional[ConversationContext] = None
        self.interaction_history: List[Dict] = []
    
    def load_user(self, user_profile: UserProfile):
        """Cargar perfil del usuario al iniciar sesión"""
        self.user_profile = user_profile
        print(f"✓ Usuario cargado: {user_profile.name}")
    
    def start_conversation(self, conversation_id: str, topic: str):
        """Iniciar una nueva conversación"""
        if not self.user_profile:
            raise ValueError("No hay usuario cargado")
        
        self.conversation_context = ConversationContext(
            conversation_id=conversation_id,
            user_id=self.user_profile.user_id,
            timestamp=datetime.now().isoformat(),
            current_topic=topic,
            recommendations_given=[],
            user_feedback=[]
        )
        print(f"✓ Conversación iniciada: {topic}")
    
    def add_recommendation(self, recommendation: str):
        """Agregar una recomendación al historial"""
        if self.conversation_context:
            self.conversation_context.recommendations_given.append(recommendation)
    
    def add_user_feedback(self, feedback: str):
        """Registrar feedback del usuario"""
        if self.conversation_context:
            self.conversation_context.user_feedback.append(feedback)
    
    def get_user_preferences(self) -> Dict:
        """Obtener preferencias del usuario"""
        if not self.user_profile:
            return {}
        
        return {
            "style": self.user_profile.preferred_style,
            "budget": self.user_profile.budget,
            "size": self.user_profile.size,
            "restrictions": self.user_profile.restrictions
        }


# ============================================================================
# 4. MEMORIA PROCEDIMENTAL - Algoritmos y Flujos
# ============================================================================

class ProceduralMemory:
    """
    Memoria Procedimental - Algoritmos y flujos de trabajo
    Define CÓMO el bot realiza sus tareas
    """
    
    @staticmethod
    def generate_recommendation(
        user_preferences: Dict,
        social_context: SocialContext,
        semantic_memory: SemanticMemory
    ) -> List[str]:
        """
        ALGORITMO DE RECOMENDACIÓN
        Pasos:
        1. Filtrar por contexto social
        2. Filtrar por preferencias del usuario
        3. Verificar compatibilidades
        4. Retornar sugerencias
        """
        
        recommendations = []
        
        # Paso 1: Obtener colores recomendados del contexto
        context_colors = social_context.recommended_colors
        user_preference_style = user_preferences.get("style")
        
        # Paso 2: Buscar conceptos que coincidan con el estilo
        matching_concept = None
        for concept in semantic_memory.concepts.values():
            if user_preference_style.lower() in concept.name.lower():
                matching_concept = concept
                break
        
        # Paso 3: Construir recomendación
        if matching_concept:
            recommendation = {
                "style": matching_concept.name,
                "colors": [c for c in matching_concept.typical_colors 
                          if c in context_colors or len(context_colors) == 1],
                "silhouettes": matching_concept.typical_silhouettes,
                "context": social_context.name
            }
            recommendations.append(json.dumps(recommendation, indent=2))
        
        return recommendations
    
    @staticmethod
    def disambiguate_query(ambiguous_query: str) -> Dict:
        """
        PROTOCOLO DE DESAMBIGUACIÓN
        Si la consulta es ambigua, generar preguntas clarificadoras
        """
        
        clarifying_questions = []
        
        if "outfit" in ambiguous_query.lower() and "?" not in ambiguous_query:
            clarifying_questions.append("¿Para qué ocasión necesitas este outfit?")
            clarifying_questions.append("¿Cuál es tu presupuesto?")
            clarifying_questions.append("¿Tienes algún color o estilo preferido?")
        
        return {
            "is_ambiguous": len(clarifying_questions) > 0,
            "questions": clarifying_questions
        }


# ============================================================================
# 5. SISTEMA INTEGRADO - CORTEX Memory Manager
# ============================================================================

class CORTEXMemoryManager:
    """
    Sistema integrado de memoria que combina LTM, STM y Procedimental
    Simula el funcionamiento de la memoria del bot CORTEX
    """
    
    def __init__(self):
        self.semantic = SemanticMemory()      # LTM - Permanente
        self.episodic = EpisodicMemory()      # STM - Sesión
        self.procedural = ProceduralMemory()  # Algoritmos
    
    def initialize_session(self, user_data: Dict, conversation_topic: str):
        """Inicializar una sesión completa"""
        
        print("\n" + "="*60)
        print("CARGANDO MEMORIA - CORTEX INICIALIZANDO...")
        print("="*60)
        
        # 1. Cargar Memoria Semántica
        print("\n[1/3] Cargando Memoria Semántica (LTM)...")
        print(f"   ✓ Conceptos de moda cargados: {len(self.semantic.concepts)}")
        print(f"   ✓ Reglas de compatibilidad: {len(self.semantic.compatibility_rules)}")
        print(f"   ✓ Contextos sociales: {len(self.semantic.social_contexts)}")
        
        # 2. Cargar Memoria Episódica
        print("\n[2/3] Cargando Memoria Episódica (STM)...")
        user_profile = UserProfile(**user_data)
        self.episodic.load_user(user_profile)
        self.episodic.start_conversation(
            conversation_id=f"conv_{user_data['user_id']}_{datetime.now().timestamp()}",
            topic=conversation_topic
        )
        
        # 3. Memoria Procedimental lista
        print("\n[3/3] Memoria Procedimental activada")
        print("   ✓ Algoritmos de recomendación listos")
        print("   ✓ Protocolos de respuesta activos")
        
        print("\n" + "="*60)
        print("✓ CORTEX LISTO PARA INTERACTUAR")
        print("="*60 + "\n")
    
    def process_user_query(self, query: str) -> Dict:
        """Procesar una consulta del usuario"""
        
        print(f"\n[USUARIO]: {query}")
        
        # 1. Desambiguar si es necesario
        disambiguation = self.procedural.disambiguate_query(query)
        
        if disambiguation["is_ambiguous"]:
            print("\n[SISTEMA]: Consulta ambigua detectada")
            print("Preguntas clarificadoras:")
            for q in disambiguation["questions"]:
                print(f"  • {q}")
            return {"status": "ambiguous", "questions": disambiguation["questions"]}
        
        # 2. Extraer información y actualizar STM
        if "cena" in query.lower() or "romántica" in query.lower():
            context = self.semantic.get_context("Cena Romántica")
        elif "corporativa" in query.lower() or "reunion" in query.lower():
            context = self.semantic.get_context("Reunión Corporativa")
        else:
            context = self.semantic.get_context("Fin de Semana")
        
        # 3. Generar recomendación
        recommendations = self.procedural.generate_recommendation(
            self.episodic.get_user_preferences(),
            context,
            self.semantic
        )
        
        # 4. Registrar en historial
        for rec in recommendations:
            self.episodic.add_recommendation(rec)
        
        return {
            "status": "success",
            "context": context.name,
            "recommendations": recommendations
        }


# ============================================================================
# 6. EJEMPLO DE USO
# ============================================================================

if __name__ == "__main__":
    
    # Crear el manager de memoria de CORTEX
    cortex = CORTEXMemoryManager()
    
    # Inicializar sesión con datos del usuario
    user_data = {
        "user_id": "001",
        "name": "Juan",
        "age": 28,
        "gender": "M",
        "preferred_style": "Casual-Sport",
        "budget": "$500",
        "size": "M",
        "restrictions": ["Lana"]
    }
    
    cortex.initialize_session(user_data, "Recomendación de outfit")
    
    # Procesar consultas del usuario
    queries = [
        "Necesito un outfit para una cena romántica este viernes",
        "¿Qué colores me recomiendas?",
        "Prefiero algo con colores oscuros"
    ]
    
    for query in queries:
        result = cortex.process_user_query(query)
        
        if result["status"] == "success":
            print(f"\n[BOT]: Basándome en {result['context']}")
            print("Recomendación:")
            for rec in result["recommendations"]:
                print(rec)
        elif result["status"] == "ambiguous":
            for q in result["questions"]:
                print(f"[BOT]: {q}")
    
    # Mostrar estado de memoria al final
    print("\n" + "="*60)
    print("ESTADO FINAL DE MEMORIA")
    print("="*60)
    print(f"Conversaciones registradas: {len(cortex.episodic.interaction_history)}")
    print(f"Recomendaciones dadas: {len(cortex.episodic.conversation_context.recommendations_given)}")
    print(f"Conceptos en LTM: {len(cortex.semantic.concepts)}")
    print("="*60 + "\n")
    [arquitectura_memoria_cortex.md](https://github.com/user-attachments/files/32971666/arquitectura_memoria_cortex.md)

## 3. Arquitectura de Memoria

El sistema de memoria de CORTEX está diseñado para simular las capacidades cognitivas humanas mediante tres capas de almacenamiento semántico. Estas tablas definen la estructura de la base de datos interna del bot.

### 3.1 Memoria Semántica (Enciclopedia Interna - LTM)

La memoria semántica contiene el conocimiento general y las categorías de información que el bot utiliza para entender y responder en su dominio de especialización.

| Tipo de Memoria | Categoría de Datos | Descripción | Ejemplo de Entrada | Durabilidad |
|---|---|---|---|---|
| **Semántica (LTM)** | Conceptos Fundamentales | Definiciones y principios básicos del dominio | "Moda sostenible: ropa hecha con materiales ecológicos" | Permanente |
| **Semántica (LTM)** | Tendencias y Estilos | Diferentes categorías de moda, épocas y gustos | "Minimalismo: uso de colores neutros, líneas limpias" | Permanente |
| **Semántica (LTM)** | Marcas y Diseñadores | Base de datos de marcas, sus características y valores | "Marca ZARA: moda rápida, accesible, actualizada" | Permanente |
| **Semántica (LTM)** | Reglas de Compatibilidad | Normas de combinación de colores, texturas y siluetas | "Los tonos pastel combinan bien con prendas oversize" | Permanente |
| **Semántica (LTM)** | Contextos Sociales | Códigos de vestimenta para diferentes eventos | "Evento corporativo: traje formal, colores oscuros" | Permanente |

### 3.2 Memoria Episódica (Contexto Conversacional - STM)

La memoria episódica almacena información específica del usuario actual y del historial reciente de la conversación.

| Tipo de Memoria | Categoría de Datos | Descripción | Ejemplo de Entrada | Durabilidad |
|---|---|---|---|---|
| **Episódica (STM)** | Perfil de Usuario | Datos demográficos y preferencias del cliente actual | "Nombre: Juan, Edad: 28, Género: M, Estilo: Casual-Sport" | Sesión Actual |
| **Episódica (STM)** | Preferencias Personales | Colores, marcas y siluetas favoritas | "Juan prefiere azul marino, tonos neutros, ajuste relajado" | Sesión Actual |
| **Episódica (STM)** | Contexto de la Consulta | Motivo de la conversación y objetivo | "Juan busca outfit para cena romántica este viernes" | Conversación Actual |
| **Episódica (STM)** | Historial de Recomendaciones | Sugerencias previas en esta sesión | "Ya recomendamos: camisa blanca, pantalón negro, sneakers" | Sesión Actual |
| **Episódica (STM)** | Límites y Restricciones | Presupuesto, tallas, alergias a materiales | "Presupuesto: $500, Talla: M, Evita: Lana" | Sesión Actual |

### 3.3 Memoria Procedimental (Habilidades y Algoritmos)

La memoria procedimental contiene los procesos, algoritmos y flujos de trabajo que el bot ejecuta automáticamente.

| Tipo de Memoria | Categoría de Datos | Descripción | Ejemplo de Entrada | Activación |
|---|---|---|---|---|
| **Procedimental** | Flujos de Recomendación | Pasos para generar sugerencias de outfit | 1. Analizar contexto 2. Filtrar por preferencias 3. Verificar compatibilidad | Automática |
| **Procedimental** | Algoritmo de Compatibilidad | Reglas de combinación de prendas | Si (color1 == neutral) AND (estilo1 == casual) → compatible | Consulta |
| **Procedimental** | Estrategia de Adaptación | Proceso de ajuste según feedback del usuario | Si (usuario rechaza) → explorar alternativas similares | Feedback |
| **Procedimental** | Protocolo de Desambiguación | Cómo resolver ambigüedades en preguntas del usuario | Si (consulta ambigua) → hacer preguntas clarificadoras | Necesidad |
| **Procedimental** | Escalada de Consultas Complejas | Pasos para manejar preguntas fuera del dominio | Si (tema != moda) → derivar a especialista o respuesta genérica | Necesidad |

### 3.4 Estructura de Base de Datos Simulada

#### 3.4.1 Tabla: Usuarios (Memoria Episódica)

```
usuario_id | nombre  | edad | genero | estilo_preferido | presupuesto | talla | restricciones
-----------|---------|------|--------|------------------|-------------|-------|---------------
001        | Juan    | 28   | M      | Casual-Sport     | $500        | M     | Lana
002        | María   | 34   | F      | Minimalista      | $800        | S     | Ninguna
003        | Carlos  | 45   | M      | Formal-Clásico   | $1200       | L     | Poliéster
```

#### 3.4.2 Tabla: Conceptos de Moda (Memoria Semántica)

```
concepto_id | nombre              | descripcion                          | colores_tipicos    | siluetas
------------|---------------------|--------------------------------------|-------------------|-------------
001         | Minimalismo         | Menos es más, líneas limpias         | Neutral, Blanco    | Oversize
002         | Maximalismo         | Abundancia de patrones y colores     | Multicolor, Vibrante| Ajustado
003         | Vintage             | Inspiración en décadas pasadas       | Ocre, Marrón       | Variado
004         | Streetwear          | Moda urbana y casual                 | Negro, Gris        | Oversize
005         | Elegancia Clásica   | Formal y refinado                    | Negro, Azul marino | Ajustado
```

#### 3.4.3 Tabla: Reglas de Compatibilidad (Memoria Procedimental)

```
regla_id | prenda1       | prenda2          | compatible | razon
---------|---------------|------------------|------------|---------------------------------------
001      | Camisa Blanca | Pantalón Negro   | SÍ        | Contraste limpio, combinación clásica
002      | Estampado 1   | Estampado 2      | NO        | Demasiados patrones, visual caótico
003      | Blanco        | Blanco           | DEPENDE   | Requiere texturas diferentes
004      | Color Pastel  | Oversize         | SÍ        | Suave y elegante, proporciones armónicas
005      | Rojo Vibrante | Azul Marino      | NO        | Contraste agresivo, difícil de armonizar
```

#### 3.4.4 Tabla: Contextos Sociales (Memoria Semántica)

```
contexto_id | nombre              | codigo_vestimenta      | colores_recomendados | siluetas_ideales | restricciones
------------|---------------------|----------------------|----------------------|------------------|--------------------
001         | Cena Romántica      | Smart Casual/Elegante  | Negro, Vino, Azul    | Ajustado          | Evitar casual
002         | Reunión Corporativa | Formal Profesional     | Neutro, Negro, Azul  | Clásico           | Evitar estampados
003         | Fin de Semana       | Casual Cómodo          | Variado              | Oversize          | Libre
004         | Boda               | Elegancia Formal       | Negro, Blanco, Oro   | Sofisticado       | Accesorios formales
005         | Evento Deportivo    | Deportivo Casual       | Colores del equipo    | Deportivo         | Practicidad
```

### 3.5 Flujo de Carga de Memoria

```
┌─────────────────────────────────────────────────────────────┐
│         INICIO DE SESIÓN - CARGA DE MEMORIA                │
└─────────────────────────────────────────────────────────────┘
                            ↓
              ┌─────────────────────────────┐
              │  1. Cargar Memoria Semántica│
              │  (Permanente - LTM)         │
              │  - Conceptos de moda        │
              │  - Reglas de compatibilidad │
              │  - Contextos sociales       │
              └─────────────────────────────┘
                            ↓
              ┌─────────────────────────────┐
              │  2. Cargar Memoria Episódica│
              │  (Variable - STM)           │
              │  - Datos del usuario actual │
              │  - Preferencias personales  │
              │  - Historial de sesión      │
              └─────────────────────────────┘
                            ↓
              ┌─────────────────────────────┐
              │  3. Cargar Memoria          │
              │     Procedimental           │
              │  (Algoritmos - Reglas)      │
              │  - Flujos de recomendación  │
              │  - Protocolos de respuesta  │
              └─────────────────────────────┘
                            ↓
        ┌─────────────────────────────────────┐
        │  4. BOT LISTO PARA INTERACTUAR     │
        │  Esperando input del usuario...     │
        └─────────────────────────────────────┘
```

### 3.6 Ciclo de Actualización de Memoria

Durante una conversación, la memoria se actualiza así:

```
USUARIO: "Prefiero colores oscuros"
                        ↓
    ┌──────────────────────────────────┐
    │ 1. RECIBIR: Parsear la consulta  │
    └──────────────────────────────────┘
                        ↓
    ┌──────────────────────────────────┐
    │ 2. EXTRAER: Información relevante │
    │    → Preferencia: Colores oscuros │
    └──────────────────────────────────┘
                        ↓
    ┌──────────────────────────────────┐
    │ 3. VALIDAR: Contra Semántica     │
    │    ¿Existen colores oscuros en   │
    │    nuestra base?                  │
    └──────────────────────────────────┘
                        ↓
    ┌──────────────────────────────────┐
    │ 4. ACTUALIZAR: STM del usuario   │
    │    Juan.preferencias.colores =   │
    │    ["Negro", "Azul marino",      │
    │     "Gris oscuro"]               │
    └──────────────────────────────────┘
                        ↓
    ┌──────────────────────────────────┐
    │ 5. RESPONDER: Con nuevo contexto │
    │    (Futuras recomendaciones      │
    │     usarán esta información)     │
    └──────────────────────────────────┘
```

### 3.7 Limitaciones y Consideraciones

| Limitación | Descripción | Solución |
|---|---|---|
| **Pérdida de Contexto** | La STM se borra al cerrar sesión | Implementar persistencia en BD externa |
| **Conflicto de Datos** | Preferencias contradictorias | Priorizar preferencias recientes |
| **Escalabilidad** | Tablas grandes ralentizan búsquedas | Usar índices y caché en memoria |
| **Actualización Semántica** | LTM desactualizado vs realidad | Revisar y actualizar trimestral |
| **Privacidad de Datos** | Información sensible del usuario | Encriptar STM, cumplir RGPD |

---

**Próximo paso (Semana 8):** Implementar estas estructuras en código usando Python y una BD simulada o real (SQLite, PostgreSQL).
