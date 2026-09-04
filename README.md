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
