# Scalper Crypto

### Señales rápidas con EMA, RSI y gestión basada en ATR

Scalper Crypto es un Expert Advisor desarrollado en **MQL4 para MetaTrader 4**.

Este repositorio comparte el código fuente de la **versión 1.00** para estudio, revisión y experimentación.

Combina cruces y condiciones de momentum de medias móviles con filtros de RSI, spread, horario y movimiento relativo del precio.

## Contexto del proyecto

Como neuropsicólogo y desarrollador, me interesa explorar cómo se transforma información cambiante en condiciones explícitas de decisión.

Scalper Crypto permite inspeccionar esas condiciones y su relación con el tamaño de posición, los costes de ejecución y la gestión de salidas.

Es un sistema basado en reglas. No incorpora inteligencia artificial ni aprendizaje automático.

## Señales de entrada

### Compras

Puede generar una señal compradora cuando:

- La EMA rápida cruza por encima de la lenta; o
- La EMA rápida está por encima de la lenta y el precio supera la EMA rápida.

Además, el RSI debe estar por debajo de `RSI_CompraMax`.

### Ventas

Puede generar una señal vendedora cuando:

- La EMA rápida cruza por debajo de la lenta; o
- La EMA rápida está por debajo de la lenta y el precio queda por debajo de la EMA rápida.

Además, el RSI debe estar por encima de `RSI_VentaMin`.

Los valores predeterminados son **EMA5, EMA10 y RSI7**.

Los indicadores utilizan el período del gráfico. Las condiciones incluyen la vela en formación, por lo que las señales pueden cambiar durante esa vela.

No exige un nuevo cruce para cada operación: las condiciones de momentum pueden permanecer activas.

## Gestión de posiciones

Incluye:

- Stop loss inicial de **ATR × 1.5**.
- Take profit inicial de **ATR × 2.5**.
- Lotaje fijo o cálculo por porcentaje del equity.
- Trailing stop configurable.
- Ajuste del stop hacia la entrada más un desplazamiento.
- Hasta tres órdenes por dirección y símbolo, con la configuración inicial.

No incorpora una progresión de martingala ni una separación tipo grid entre entradas.

El porcentaje usado para dimensionar una operación no representa un límite de riesgo para toda la cartera.

## Filtros

Las aperturas comprueban:

- Horario, si está activado.
- Exclusión de fines de semana.
- Spread máximo.
- Intervalo mínimo desde la última apertura exitosa.
- Filtro de movimiento relativo del precio.
- Margen disponible.
- Límites configurados de órdenes e instrumentos.

El intervalo entre operaciones es global para la instancia: una apertura en un símbolo puede demorar las entradas de los demás.

## Filtro de movimiento

La función denominada `VelocidadOk` compara el precio con una referencia almacenada y calcula su variación relativa.

El umbral predeterminado es `0.0002`, equivalente a una variación relativa del **0.02%**.

No divide ese movimiento por el tiempo transcurrido. Por tanto, no es una medición estricta de velocidad por tick.

La implementación multiactivo requiere corrección, como se detalla en las limitaciones.

## Bloqueo por drawdown

Compara el equity de toda la cuenta con el balance registrado al inicializar el EA.

Al alcanzar `MaxDrawdownPct`, bloquea nuevas aperturas.

**No cierra las posiciones existentes ni garantiza una pérdida máxima.**

El trailing y el ajuste hacia breakeven continúan si el procesamiento del símbolo llega a esas rutinas.

El bloqueo permanece durante esa ejecución y se restablece al reinicializar el EA.

## Parámetros principales

| Parámetro | Valor predeterminado | Función |
|---|---:|---|
| `NumeroMagico` | `3300` | Identificador base |
| `AutoEjecutar` | `true` | Declarado, sin efecto sobre aperturas en esta versión |
| `EMA_Rapida` | `5` | Período de la EMA rápida |
| `EMA_Lenta` | `10` | Período de la EMA lenta |
| `RSIPeriodo` | `7` | Período del RSI |
| `RSI_CompraMax` | `70.0` | Límite superior de RSI para compras |
| `RSI_VentaMin` | `30.0` | Límite inferior de RSI para ventas |
| `ATR_Periodo` | `14` | Período del ATR |
| `UsarFiltroVelocidad` | `true` | Activa el filtro de movimiento |
| `VelocidadMinima` | `0.0002` | Variación relativa mínima |
| `UsarLotePorRiesgo` | `true` | Activa cálculo inicial por riesgo estimado |
| `RiesgoPorcentaje` | `0.5` | Porcentaje del equity usado en el cálculo |
| `LoteFijo` | `0.01` | Lote alternativo |
| `MaxOrdenesPorLado` | `3` | Máximo por dirección y símbolo |
| `MaxParesSimultaneos` | `2` | Límite previsto de instrumentos |
| `ATR_Multiplo_SL` | `1.5` | Distancia inicial del stop |
| `ATR_Multiplo_TP` | `2.5` | Distancia inicial del objetivo |
| `SpreadMaximoPips` | `30.0` | Umbral de spread |
| `MaxDrawdownPct` | `20.0` | Umbral de bloqueo |
| `TrailingPips` | `8.0` | Distancia del trailing |
| `BreakevenPips` | `6.0` | Umbral para ajustar el stop |
| `FiltrarHorario` | `false` | Restricción horaria |
| `FiltrarFinSemana` | `true` | Excluye sábados y domingos |
| `MinutosEntreTrades` | `1` | Intervalo global entre aperturas |

Los valores describen el archivo publicado. No son recomendaciones.

Las unidades de pip y su conversión a precios deben validarse para cada contrato.

## Instrumentos

Permite seleccionar:

BTCUSD, ETHUSD, SOLUSD, XRPUSD, ADAUSD, DOGEUSD, LTCUSD, BNBUSD, DOTUSD, LINKUSD, AVAXUSD, MATICUSD, BCHUSD y XLMUSD.

Por defecto están activados **BTCUSD, ETHUSD y SOLUSD**.

La disponibilidad depende del bróker. El programa opera los instrumentos ofrecidos en MetaTrader 4; no incluye conexión directa a exchanges ni gestión de billeteras.

## Instalación para pruebas

1. Abrir MetaTrader 4 en una cuenta demo.
2. Seleccionar **Archivo → Abrir carpeta de datos**.
3. Copiar `SCALPER_CRYPTO.mq4` dentro de `MQL4/Experts`.
4. Abrir el archivo en MetaEditor y compilar.
5. Revisar errores y advertencias.
6. Actualizar la lista de Expert Advisors.
7. Cargar el EA y comprobar símbolos, históricos, parámetros y permisos.

El procesamiento multiactivo depende de los ticks del gráfico donde se instala.

Utiliza `NumeroMagico` para compras y `NumeroMagico + 1` para ventas. Evitar coincidencias con otros sistemas o instancias.

## Estado y limitaciones conocidas

Puntos identificados mediante lectura del código:

- **Control de ejecución:** `AutoEjecutar` no se consulta al abrir operaciones. No utilizarlo como interruptor de pausa.
- **Filtro de movimiento multiactivo:** comparte una única referencia estática entre símbolos y puede comparar precios de instrumentos diferentes.
- **Actualización de referencia:** el filtro renueva su referencia al inicializarse o después de más de 60 segundos, no después de cada tick.
- **Límite de símbolos:** el conteo se realiza antes del recorrido y no se actualiza tras cada apertura.
- **Lotaje:** el redondeo por decimales no garantiza compatibilidad con todos los pasos permitidos.
- **Lote mínimo:** puede elevar el riesgo por encima del porcentaje previsto.
- **Valor del tick:** si no está disponible, el cálculo utiliza `1.0` como alternativa, lo que puede distorsionar el dimensionamiento.
- **Stops iniciales:** si no se supera la comprobación de distancia mínima, la orden puede enviarse con SL o TP en cero.
- **Unidades:** mezcla conversiones basadas en puntos y tamaño del tick; requiere validación por instrumento.
- **Gestión condicionada por datos:** si el ATR o tamaño del tick no son válidos, se omite también la gestión posterior de ese símbolo.
- **Panel:** su P/L total corresponde al símbolo del gráfico, no a toda la cartera.
- **Ejecución:** varias modificaciones de órdenes no comprueban su resultado.
- **Datos y parámetros:** falta validación integral de históricos, períodos y restricciones del bróker.

El término scalper describe la intención de la estrategia. El código no acredita una infraestructura de trading de alta frecuencia ni un tiempo máximo de permanencia en posición.

## Resultados y validación

La documentación se basa en la lectura del código fuente. No acredita compilación, ejecución ni resultados de backtesting.

El repositorio no incluye un historial auditado ni una validación independiente de rentabilidad.

La evaluación debe contemplar spread, comisiones, deslizamiento y condiciones de ejecución.

## Autor

Desarrollado por **Emanuel**, neuropsicólogo y desarrollador del proyecto **WineBOX**.

- https://winebox.cloud
- https://wineboxtool.cloud

## Contribuciones

Se reciben reportes de errores y propuestas mediante Issues y Pull Requests.

Incluir versión de MetaTrader 4, instrumento, período del gráfico, parámetros y registros relevantes, sin credenciales ni datos personales.

## Licencia

Este proyecto se distribuye bajo la licencia **Apache License 2.0**.

Consultar el archivo [LICENSE](LICENSE) para conocer los términos completos.

## Aviso

Material técnico y educativo. No constituye asesoramiento financiero ni una promesa de resultados.
