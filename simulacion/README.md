# Simulación (paper trading) de TradingAgents con BTC-USD

Esta carpeta registra una **simulación sin dinero real**. Sigue el método de TradingAgents (analistas → debate alcista/bajista → Research Manager → Trader → debate de riesgo → Portfolio Manager), pero sin el programa en sí: los agentes son subagentes de Claude Code en lugar de llamadas a una API de LLM.

> Es una prueba de investigación, no un consejo financiero.

## Diferencias con el programa original

- **Datos:** en este entorno la red bloquea Yahoo Finance, Reddit, StockTwits y Polymarket, así que los analistas obtienen los datos por **búsqueda web** y citan la fuente y la fecha de cada cifra. Los indicadores técnicos salen de lo que publican esos sitios; no se calculan sobre OHLCV propio.
- **Analista de fundamentales:** para cripto el framework indica que no hay fundamentales de empresa, así que este rol se adapta a **flujos y on-chain**: ETF spot, reservas en exchanges, mineros y stablecoins.
- **Rondas:** se usa 1 ronda de debate de investigación y 1 de riesgo, igual que `max_debate_rounds` y `max_risk_discuss_rounds` por defecto.

## Reglas de la simulación

| Regla | Valor |
|---|---|
| Capital virtual inicial | 10.000 USD |
| Rating **Buy** u **Overweight** | Posición **LARGA** (gana si el precio sube) |
| Rating **Sell** o **Underweight** | Posición **CORTA** (gana si el precio baja) |
| Rating **Hold** | Sin operación; se registra igual, sin ganancia ni pérdida |
| Precio de entrada | Precio spot de BTC en el momento de la decisión, con su fuente |
| Tamaño | % del capital virtual que fija el Portfolio Manager/Trader |
| Salida | Lo primero que ocurra: stop-loss, take-profit o fin del periodo de tenencia |
| Periodo de tenencia | 5 días (`holding_period_days` por defecto del framework; cripto cotiza todos los días) |
| Resultado | `GANA` si el retorno es mayor que 0, `PIERDE` si es menor |
| Comisiones | No se incluyen |

`retorno_pct` es el movimiento del precio a favor de la posición: positivo si fue a favor y negativo si fue en contra. `pnl_usd` es igual a `capital_virtual_usd × tamano_pct_capital × retorno_pct`.

## Archivos

- `registro_operaciones.csv`: una fila por decisión, con la entrada, la salida y el resultado.
- `reportes/`: el reporte completo de cada corrida (los 4 analistas, el debate, el plan, el debate de riesgo y la decisión final).
