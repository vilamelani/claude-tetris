---
name: clima-local
description: Obtiene el clima actual y el pronóstico de la ubicación local del usuario (o de una ciudad indicada). Úsalo cuando pregunte por el clima, la temperatura, si va a llover o el pronóstico.
allowed-tools: Bash(curl *)
---

# Clima local

Consulta el clima con [wttr.in](https://wttr.in), un servicio gratuito que no requiere API key. Responde siempre en español.

## Pasos

1. Determina la ubicación:
   - Si el usuario indicó una ciudad (argumento: `$ARGUMENTS`), úsala, reemplazando espacios por `+` (ej. `Buenos+Aires`).
   - Si no, déjala vacía: wttr.in detecta la ubicación aproximada por IP.

2. Obtén el clima actual en JSON compacto:

   ```bash
   curl -s "https://wttr.in/<CIUDAD>?format=j1&lang=es"
   ```

   Para una línea rápida de resumen también sirve:

   ```bash
   curl -s "https://wttr.in/<CIUDAD>?format=%l:+%c+%t+(sensación+%f),+humedad+%h,+viento+%w&lang=es"
   ```

3. Del JSON (`j1`) usa estos campos:
   - `current_condition[0]`: `temp_C`, `FeelsLikeC`, `humidity`, `windspeedKmph`, `lang_es[0].value` (descripción).
   - `weather[0..2]`: `date`, `mintempC`, `maxtempC`, y `hourly[].chanceofrain` para la probabilidad de lluvia.
   - `nearest_area[0]`: `areaName[0].value` y `country[0].value` para confirmar la ubicación detectada.

4. Presenta un resumen breve:
   - Ubicación detectada
   - Condición actual, temperatura y sensación térmica, humedad y viento
   - Pronóstico de hoy y los próximos días (mín/máx y probabilidad de lluvia)
   - Una recomendación corta si aplica (paraguas, abrigo, etc.)

## Notas

- Usa unidades métricas (°C, km/h).
- La detección por IP es aproximada; si la ciudad no coincide, dilo y ofrece consultar otra.
- Si `curl` falla o no hay conexión, informa el error en vez de inventar datos.
