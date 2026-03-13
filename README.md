# 🌱 EcoTrack AI — MVP de Huella de Carbono

Aplicación web conversacional que ayuda a pequeños negocios a calcular su huella de carbono describiendo sus actividades en lenguaje natural.

---

## 🚀 Cómo correrlo

**Opción rápida (sin instalar nada):**
1. Ve a [stackblitz.com](https://stackblitz.com) → clic en **React**
2. Reemplaza `src/App.jsx` con el archivo `ecotrack-ai.jsx`
3. Pon tu API key en la línea `const GEMINI_API_KEY = "PEGA_TU_API_KEY_AQUI"`
4. La app corre en vivo a la derecha

**Opción local:**
```bash
npm create vite@latest ecotrack -- --template react
cd ecotrack
npm install
# reemplaza src/App.jsx con ecotrack-ai.jsx
npm run dev
```

---

## 🤖 Funcionalidad de IA Implementada

El corazón del MVP es la **extracción de datos de consumo desde lenguaje natural**. En lugar de pedirle al usuario que llene un formulario, puede escribir algo como:

> *"Hoy usamos 5 camionetas de reparto y gastamos 200 kWh de luz"*

La IA (Gemini 2.0 Flash) interpreta ese texto y devuelve un JSON estructurado con los datos extraídos:

```json
{
  "activities": [
    {
      "type": "delivery_van_km",
      "quantity": 400,
      "description": "5 camionetas de reparto (80km est. c/u)",
      "unit": "km"
    },
    {
      "type": "electricity",
      "quantity": 200,
      "description": "Consumo eléctrico",
      "unit": "kWh"
    }
  ],
  "summary": "Día típico con movilidad e iluminación activa.",
  "tip": "Evalúa rutas optimizadas para reducir km recorridos."
}
```

Con ese JSON, la app aplica factores de emisión estándar y muestra el resultado visual:

| Tipo de consumo | Factor de emisión |
|---|---|
| Electricidad | 0.233 kg CO₂/kWh |
| Diésel | 2.68 kg CO₂/litro |
| Gasolina | 2.31 kg CO₂/litro |
| Vehículo de reparto | 0.27 kg CO₂/km |
| Gas natural | 2.04 kg CO₂/m³ |
| Vuelo comercial | 0.255 kg CO₂/km |
| Residuos sólidos | 0.50 kg CO₂/kg |

---

## 📋 Prompts Principales Utilizados

### Prompt 1 — Master Prompt (arranque del proyecto)

```
Eres un Vibe Coder experto. Necesito construir el MVP de una app web llamada
"EcoTrack AI" para pequeños negocios que quieren conocer su huella de carbono
de forma sencilla.

STACK TÉCNICO: React con hooks, CSS inline, integración con API de IA.

FLUJO PRINCIPAL:
1. El usuario escribe en lenguaje natural sus actividades del día
   (ej: "usamos 5 camionetas y 200 kWh de luz")
2. La IA analiza el texto y extrae: tipo de consumo, cantidad, unidades
3. Se calculan emisiones CO2 con factores estándar
4. Se muestra un breakdown visual con barras de progreso y total en kg CO2e

ESTÉTICA: Minimalista, tonos verdes (#1b5e20, #2e7d32, #4caf50),
          fondo crema (#f9fbe7), interfaz tipo chat con burbujas.

La IA debe responder SOLO con JSON estructurado para facilitar el parsing.
Incluye ejemplos rápidos como chips clickeables. Sin dependencias externas.
```

---

### Prompt 2 — System Prompt enviado a la IA en cada request

```
Eres un analizador experto en huella de carbono para pequeñas empresas.
Tu tarea es extraer datos de consumo de texto en lenguaje natural.

Responde SOLO con un JSON válido con este formato:
{
  "activities": [
    {
      "type": "electricity|diesel_liter|gasoline_liter|natural_gas_m3|delivery_van_km|flight_km|waste_kg",
      "quantity": number,
      "description": "descripción corta en español",
      "unit": "kWh|litros|m³|km|kg"
    }
  ],
  "summary": "frase motivacional corta en español",
  "tip": "un consejo específico para reducir la huella"
}

Reglas de extracción:
- "camionetas" sin litros → estima 80km por vehículo por día → delivery_van_km
- "luz" o "electricidad" → electricity
- "gasolina" → gasoline_liter, "diésel" → diesel_liter
- "gas natural" → natural_gas_m3
- "basura" o "residuos" → waste_kg
- SOLO responde con JSON, sin texto adicional ni bloques ```
```

---

### Prompt 3 — Refinamiento de diseño

```
Haz el diseño más minimalista y orgánico. Usa:
- Bordes redondeados (16px en cards, 20px en chips)
- Sombras sutiles: box-shadow 0 2px 12px rgba(46,125,50,0.08)
- Animación fadeUp de 0.3s en los mensajes nuevos
- Dots de carga animados con pulse 1.2s en lugar de spinner
- El indicador de "Total sesión" solo aparece cuando hay datos calculados
- Chips de ejemplo solo visibles en el primer mensaje
```

---

### Prompt 4 — Debugging del parser JSON

```
Tengo un bug: a veces el JSON viene envuelto en ```json ... ``` y el parser falla.

1. Agrega al system prompt: "SOLO responde con JSON, sin bloques ```"
2. En la función parseActivities(), limpia el texto antes del JSON.parse():
   - Eliminar los delimitadores de bloque de código con replace()
   - Hacer trim() del resultado
   - Envolver en try/catch y retornar null si falla
```

---

## 📁 Archivos del proyecto

```
ecotrack-ai.jsx       → Código fuente completo del MVP
ecotrack_bitacora.pdf → Documentación detallada del proceso
README.md             → Este archivo
```

---

## ⚠️ Nota sobre la API key

La API key está escrita directamente en el código, lo cual está bien para desarrollo y demos. Para producción, muévela a una variable de entorno o crea un backend que actúe de proxy.

---

*Desarrollado con Vibe Coding + IA generativa · EcoTrack AI MVP v1.0*
