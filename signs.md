# Gold Whale Detector — Señales (v7.6.2)

Documento de referencia completo de todas las señales del sistema `gold_all_claude.py`.  
Incluye señales activas, desactivadas, modificadores globales, listas de traders y sistema de stake.

---

## Índice

1. [Señales activas](#señales-activas)
   - [S2 — Follow NBA favorito moderado](#s2--follow-nba-favorito-moderado)
   - [S2B — Follow NBA favorito alto](#s2b--follow-nba-favorito-alto)
   - [S2C — Follow NBA underdog leve](#s2c--follow-nba-underdog-leve)
   - [S2+ — Follow NBA consenso multi-ballena](#s2--follow-nba-consenso-multi-ballena)
   - [S3 — Follow Nicho ESPORTS](#s3--follow-nicho-esports)
   - [S4 — Counter Crypto intraday](#s4--counter-crypto-intraday)
   - [S7 — Follow ESPORTS favorito](#s7--follow-esports-favorito)
   - [S10 — Follow Soccer zona óptima](#s10--follow-soccer-zona-óptima)
   - [S5-MMA — Follow MMA favorito](#s5-mma--follow-mma-favorito)
   - [S1-MMA-RISKY — Counter RISKY en MMA underdog](#s1-mma-risky--counter-risky-en-mma-underdog)
2. [Señales desactivadas](#señales-desactivadas)
   - [S1 — Counter HIGH RISK NBA/OTHER](#s1--counter-high-risk-nbaother-desactivado-v74)
   - [S1B — Counter Soccer underdog](#s1b--counter-soccer-underdog-suspendida-v73)
   - [S5 — Follow Soccer](#s5--follow-soccer-suspendida-v73)
   - [S6 — Counter ESPORTS zona media](#s6--counter-esports-zona-media-desactivado-v76)
   - [S8 — Follow NHL favorito](#s8--follow-nhl-favorito-desactivado-v74)
   - [S9 — Counter HIGH RISK NHL](#s9--counter-high-risk-nhl-desactivado-v741)
3. [Modificadores globales](#modificadores-globales)
4. [Sistema de stake Kelly fraccionado](#sistema-de-stake-kelly-fraccionado)
5. [Listas de traders](#listas-de-traders)

---

## Señales activas

---

### S2 — Follow NBA favorito moderado

| Campo | Valor |
|---|---|
| **Acción** | FOLLOW |
| **Categoría** | NBA |
| **Rango de precio** | 0.50 – 0.60 |
| **Lado** | Solo BUY |
| **Tiers válidos** | Solo RISKY (o WHITELIST_TIER_OVERRIDE) |
| **Tiers excluidos** | HIGH RISK, BOT/MM, SILVER, BRONZE, STANDARD, GOLD |
| **WR histórico** | 60.0% (N=30, PnL +$449) |
| **Confidence base** | MEDIUM |
| **Stake base** | 2% (MEDIUM), 3% (HIGH) |

#### Por qué existe

Con N=2062 trades (Whales dataset), el desglose en rango 0.50-0.60 por tier reveló que **solo RISKY tiene edge real**:

| Tier | N | WR | PnL |
|---|---|---|---|
| RISKY | 30 | 60.0% | +$449 |
| STANDARD | 37 | 48.6% | -$283 |
| GOLD | 6 | 50.0% | -$8 |
| No-RISKY total | 43 | 48.8% | -$291 |
| HIGH RISK | — | 49.4% | -$818 |
| BOT/MM | — | 30.8% | -$595 |

STANDARD y GOLD son peor que moneda al aire. La señal combinada destruye capital. Solo RISKY se activó en v7.6.1.

#### Modificadores de confidence

- **Trader en WHITELIST_A** → confidence sube a HIGH, stake ×1.5
- **Trader en WHITELIST_B** → stake ×1.25
- **Mercado nicho** → WR 64.4% vs no-nicho 54.7% (PnL nicho +$2,677 vs -$851). Confidence MEDIUM→HIGH.
- **Capital ≥$20K** → MEDIUM→HIGH (WR histórico 76.9%)
- **WHITELIST_TIER_OVERRIDE** → bypasea tier HIGH RISK o BOT/MM con razonamiento explícito

---

### S2B — Follow NBA favorito alto

| Campo | Valor |
|---|---|
| **Acción** | FOLLOW |
| **Categoría** | NBA |
| **Rango de precio** | 0.65 – 0.82 |
| **Lado** | Solo BUY |
| **Tiers válidos** | Todos excepto BOT/MM |
| **Tiers excluidos** | BOT/MM (único excluido desde v7.6.1) |
| **WR histórico global** | 82.4% (N=125, PnL +$1,838) excl. solo BOT |
| **Stake base** | 2% (MEDIUM), 3% (HIGH) |

#### Por qué HIGH RISK se incluye (desde v7.6.1)

Antes, HIGH RISK estaba excluido. Con N=2062 Whales se descubrió que **el tier no importa cuando el precio domina**:

| Subzona | Tier | N | WR |
|---|---|---|---|
| 0.65–0.70 | HIGH RISK | 23 | 69.6% |
| 0.70–0.75 | HIGH RISK | 6 | 100% |
| 0.75–0.80 | HIGH RISK | 6 | 100% |
| Total HR zona | HIGH RISK | ~42 | 83.3% |

Incluir HR suma +$663 de PnL vs excluirlo. BOT/MM siguen degradados en toda la zona NBA.

#### Subzonas y confidence

| Subzona | WR | Confidence | Notas |
|---|---|---|---|
| 0.65–0.70 | 78.6% (sin HR), 69.6% (HR) | **MEDIUM** | Stake ×2; capital whale no indica mayor confianza |
| 0.70–0.80 | 84.6% (sin HR), 100% (HR) | **HIGH** | Stake ×3 |
| 0.80–0.82 | 100% (N=4) | **HIGH** | Stake ×3 |
| >0.82 | — | **IGNORADO** | EV negativo; break-even > WR histórico |

#### Zona de advertencia 0.82–0.85

Genera warning pero no señal: "EV negativo (WR 84% × payout bajo — break-even requiere WR >82%)".

---

### S2C — Follow NBA underdog leve

| Campo | Valor |
|---|---|
| **Acción** | FOLLOW |
| **Categoría** | NBA |
| **Rango de precio** | 0.45 – 0.50 (exclusive) |
| **Lado** | Solo BUY |
| **Tiers válidos** | RISKY, BRONZE |
| **Tiers excluidos** | HIGH RISK, BOT/MM, STANDARD, SILVER |
| **WR histórico** | 73.1% (N=52, PnL +$2,631) |
| **Mejor señal del sistema por PnL total** | ✅ |
| **Stake base** | 2% (MEDIUM), 3% (HIGH) |

#### Por qué existe

La zona 0.45-0.50 es "zona muerta" para categorías non-NBA — no activa ninguna señal. Pero en NBA tiene edge real:

| Tier | N | WR | PnL | Status |
|---|---|---|---|---|
| RISKY | 7 | 85.7% | +$487 | ✅ ACTIVO (conf HIGH) |
| BRONZE | ~15 comb. | 73.3% | +$721 | ✅ ACTIVO (conf MEDIUM) |
| STANDARD | 4 | 25.0% | — | ❌ Excluido v7.3 |
| SILVER | 3 | 33.3% | — | ❌ Excluido v7.4 |
| HIGH RISK | — | — | — | ❌ Excluido |

#### Modificadores de confidence

| Condición | Confidence resultante |
|---|---|
| Tier RISKY | HIGH (WR 85.7%) |
| Tier BRONZE o similar | MEDIUM (WR 73.1%) |
| WHITELIST_A | HIGH |
| WHITELIST_B | MEDIUM + nota stake ×1.25 |
| Mercado nicho | HIGH (RISKY o BRONZE nicho WR 80%, N=5, +$334) |
| Capital ≥$20K | MEDIUM→HIGH |

---

### S2+ — Follow NBA consenso multi-ballena

| Campo | Valor |
|---|---|
| **Acción** | FOLLOW |
| **Categoría** | NBA |
| **Rango de precio** | 0.50 – 0.60 (todas las ballenas deben estar dentro) |
| **Lado** | Solo BUY |
| **Condición** | ≥2 wallets distintos, mismo mercado, mismo lado, ventana de 30 min |
| **WR histórico** | 78.1% |
| **Confidence** | Siempre HIGH |
| **Stake** | 3% |

#### Lógica de consenso

El `ConsensusTracker` acumula entradas por `condition_id` durante 30 minutos, deduplicando por wallet. Cuando se confirman 2+ ballenas NBA todas en rango 0.50-0.60 en el mismo lado, `classify_consensus()` activa S2+.

Si hay dispersión de precios (alguna ballena fuera del rango), no activa y genera diagnóstico explícito.

> **Nota (v7.6.2):** el umbral se redujo de 3 a 2 ballenas tras verificar que con umbral=3 la señal era código muerto en producción.

---

### S3 — Follow Nicho ESPORTS

| Campo | Valor |
|---|---|
| **Acción** | FOLLOW |
| **Categoría** | Solo ESPORTS |
| **Categorías excluidas** | NBA, Soccer, Crypto, Tennis, NHL, MMA, OTHER |
| **Rango de precio** | 0.50 – 0.85 |
| **Tiers válidos** | Todos excepto HIGH RISK |
| **Condición extra** | Mercado debe ser nicho (alta concentración de capital) |
| **WR histórico** | 83.3% (N=6) |
| **Confidence** | LOW |
| **Stake** | 1% |

#### Por qué N bajo pero activa

Con N=6 el edge es prometedor (83.3%) pero insuficiente para confidence MEDIUM. Se activa a LOW para capturar el patrón mientras crece el dataset. Si con N≥15 el WR se mantiene ≥70%, subirá a MEDIUM.

#### Por qué HIGH RISK excluido

S3 global (todos tiers): WR 54.1% (N=37, PnL -$516). Desglose:
- ESPORTS sin HR: WR 83.3% (N=6, +$212) ← único con edge real
- NHL sin HR: WR 60% (N=10) — marginal, excluido por seguridad
- OTHER: WR 33% — destruye
- TENNIS: WR 0% (N=2) — destruye

HIGH RISK tiene WR ~33% en nicho. Suprimirlo transforma la señal de PnL negativo a positivo.

---

### S4 — Counter Crypto intraday

| Campo | Valor |
|---|---|
| **Acción** | COUNTER (apostar contra la ballena) |
| **Categoría** | CRYPTO (solo mercados intraday "Up or Down") |
| **Rango de precio** | ≥ 0.60 |
| **Tiers válidos** | Todos excepto HIGH RISK |
| **WR histórico** | 62.5% (N=32) excl. HIGH RISK |
| **Confidence** | MEDIUM |
| **Stake** | 2% |

#### Por qué COUNTER y no FOLLOW

En crypto intraday, las ballenas sin información privilegiada son contrarians aleatorios que pierden más de lo que ganan. El mercado en formato Up/Down-en-24h descuenta el flujo informativo.

#### Por qué HIGH RISK excluido del counter

Con HIGH RISK incluido, WR counter cae a 25% — la ballena HIGH RISK en crypto intraday **gana**. Probablemente tiene información real sobre el movimiento. Contrariarla es la peor estrategia. El sistema genera warning explícito cuando llega un HIGH RISK en crypto intraday.

#### Zona <0.60

Sin edge confirmado. Se genera warning pero no señal.

#### Para crypto largo plazo (no intraday)

S4 no aplica. El sistema genera warning para validación manual.

---

### S7 — Follow ESPORTS favorito

| Campo | Valor |
|---|---|
| **Acción** | FOLLOW |
| **Categoría** | ESPORTS |
| **Rango de precio** | 0.60 – 0.70 |
| **Tiers válidos** | Todos excepto HIGH RISK |
| **WR histórico** | 83.3% (N=24, confirmado con N=2062 Whales) |
| **Sweet spot real** | 0.60–0.65, WR 88.2% (N=17) |
| **Confidence** | MEDIUM |
| **Stake** | 2% |

#### Evolución de la señal

- v6.1: WR 85.7%, N=14 — cruzó umbral N=15, pasó de LOW a MEDIUM
- v7.6: WR actualizado a 83.3%, N=24 con dataset masivo. Señal confirmada y sólida.

#### Por qué HIGH RISK excluido

Mismo principio que S3: el tier HIGH RISK degrada el WR de forma significativa. Sin HR el edge es claro; con HR se vuelve marginal.

---

### S10 — Follow Soccer zona óptima

| Campo | Valor |
|---|---|
| **Acción** | FOLLOW |
| **Categoría** | Soccer/Fútbol |
| **Rango de precio** | 0.55 – 0.65 |
| **Lado** | Solo BUY |
| **Tiers válidos** | **Todos** (incluyendo HIGH RISK y BOT/MM) |
| **WR histórico** | 70.8% (N=65, PnL +$1,162) |
| **WR BOT/MM específico** | 100% (N=10) — sweet spot |
| **WR HIGH RISK específico** | 63.2% — positivo, no excluir |
| **Confidence** | MEDIUM (general), HIGH (BOT/MM) |
| **Stake** | 2% (MEDIUM), 3% (HIGH) |

#### Por qué esta zona y no Soccer genérico

S5 (Soccer genérico 0.65-0.80) fue suspendida con WR 38.1% (N=21) — reversión total. Contaminación MMA/Esports en el clasificador + Manchester City perdiendo 7 consecutivos degradaron la señal original.

Con N=2062 Whales se identificó que la zona 0.55-0.65 Soccer **sí tiene edge real**, con todos los tiers funcionando.

#### Por qué BOT/MM es HIGH aquí y no en NBA

En NBA, BOT/MM tienen WR degradado (~30%). En Soccer 0.55-0.65 el patrón se invierte: 10/10 wins. Los bots en fútbol en esta zona de precio capturan ineficiencias de línea que el mercado no corrige rápidamente.

---

### S5-MMA — Follow MMA favorito

| Campo | Valor |
|---|---|
| **Acción** | FOLLOW |
| **Categoría** | MMA/UFC |
| **Rango de precio** | 0.60 – 0.70 |
| **Lado** | Solo BUY |
| **Tiers válidos** | Todos excepto BOT/MM y HIGH RISK |
| **WR histórico** | 100% (N=6) ⚠️ muestra pequeña |
| **Confidence** | LOW |
| **Stake** | 1% (mínimo) |

#### Advertencia de muestra pequeña

N=6 es inferior al umbral de confianza (N≥15). Se activa por petición explícita, con stake mínimo. Un run de 3-4 pérdidas puede borrar el edge histórico por completo.

Solo SILVER muestra promise adicional en MMA (dataset Whales), pero N insuficiente para confirmar. Subirá a MEDIUM cuando N≥15 y WR≥80% sostenido.

#### HIGH RISK excluido en MMA 0.60-0.70

Con N=2062 Whales: HIGH RISK en MMA 0.60-0.70 WR 33% — destruye la señal.

---

### S1-MMA-RISKY — Counter RISKY en MMA underdog

| Campo | Valor |
|---|---|
| **Acción** | COUNTER (apostar contra la ballena) |
| **Categoría** | MMA |
| **Rango de precio** | < 0.50 |
| **Tier** | RISKY (excluye HIGH RISK) |
| **WR histórico** | 100% counter (N=5) ⚠️ muestra muy pequeña |
| **Confidence** | LOW |
| **Stake** | 1% |

#### Lógica inversa a S2/S5-MMA

En NBA, RISKY en zona 0.50-0.60 tiene WR follow 60%+ — es señal positiva. En MMA, RISKY comprando underdogs (<0.50) pierde 5/5 veces. El comportamiento de RISKY es categoría-dependiente.

Nota: HIGH RISK en MMA no se incluye porque su comportamiento es distinto (no validado en counter).

---

## Señales desactivadas

---

### S1 — Counter HIGH RISK NBA/OTHER *(Desactivado v7.4)*

| Campo | Valor |
|---|---|
| **Acción original** | COUNTER |
| **Rango** | precio < 0.45, tier HIGH RISK |
| **Motivo de desactivación** | WR global 35.3% (N=51) — peor que moneda al aire |

#### Datos de desactivación

| Categoría | N | WR counter |
|---|---|---|
| NBA | 11 | 26.0% |
| Soccer | 16 | 36.0% |
| NHL | 10 | 57.0% |
| OTHER (0.30-0.40) | 8 | 13.0% (0W/8L) |

La premisa "counter HIGH RISK = win" fue refutada con N suficiente. La única subzona prometedora es NHL 0.40-0.45 WR 83% (N=6), pero N es insuficiente. No reactivar hasta N≥20 por categoría con WR≥65% sostenido.

---

### S1B — Counter Soccer underdog *(Suspendida v7.3)*

| Campo | Valor |
|---|---|
| **Acción original** | COUNTER |
| **Rango** | Soccer, precio < 0.35 |
| **Motivo de suspensión** | WR 52.9% (N=17) — sin edge en ningún subrango |

| Subzona | WR counter |
|---|---|
| < 0.30 | 33% |
| 0.30–0.35 | 50% |
| 0.35–0.40 | 31% |

---

### S5 — Follow Soccer *(Suspendida v7.3)*

| Campo | Valor |
|---|---|
| **Acción original** | FOLLOW |
| **Rango** | Soccer 0.65–0.80 |
| **Motivo de suspensión** | WR 38.1% (N=21) — reversión total |

Causas probables: contaminación MMA/Esports en el clasificador + Manchester City con 7 pérdidas consecutivas. La zona Soccer con edge real es 0.55-0.65 (ahora cubierta por S10, que sí está activa).

---

### S6 — Counter ESPORTS zona media *(Desactivado v7.6)*

| Campo | Valor |
|---|---|
| **Acción original** | COUNTER |
| **Rango** | ESPORTS 0.40–0.50 |
| **Motivo de desactivación** | Con N=2062 Whales: follow WR 58.5% (N=41) → counter solo 41.5%, PnL negativo |

#### Historia del error estadístico

Con N=516 (muestra menor): WR counter aparente 59.1% — parecía señal fuerte. Con N=2062 se reveló como artefacto de muestra pequeña.

En producción Gold (N=22): el mismo 59% reaparece — mismo artefacto. El patrón es sistemático con N pequeño y desaparece con N masivo. No reactivar.

**Hallazgo para futura evaluación:** si se reactiva, solo considerar subzona 0.40-0.46 (WR counter 63.2%, N≈19 en Gold) — no la zona 0.46-0.50 (WR counter 33.3%). Requiere N≥50 exclusivos con WR counter ≥60%.

---

### S8 — Follow NHL favorito *(Desactivado v7.4)*

| Campo | Valor |
|---|---|
| **Acción original** | FOLLOW |
| **Rango** | NHL 0.60–0.70 |
| **Motivo de desactivación** | WR 33.3% (N=6, PnL -$287) |

Desglose: 4 HIGH RISK (WR 50%, PnL -$87) + 1 BRONZE (0W) + 1 NBA mal clasificado (0W). NHL en rango 0.60-0.70 WR 44.4% combinando todas las DBs. Sin edge.

**Hipótesis pendiente:** NHL 0.40-0.50 muestra promise (WR 62.5%, N=8). Documentado, no implementado.

---

### S9 — Counter HIGH RISK NHL *(Desactivado v7.4.1)*

| Campo | Valor |
|---|---|
| **Acción original** | COUNTER |
| **Rango** | NHL HIGH RISK, precio 0.30–0.80 |
| **Motivo de desactivación** | Dataset Whales N=32: WR exactamente 50% — coin flip |

El dato inicial de Gold (N=15, WR 26.7%) era ilusión estadística. Con N=32 confirmó 50%. No reimplementar hasta N≥40 con WR≥65% sostenido.

---

## Modificadores globales

Estos modificadores se aplican **a todas las señales activas**, independientemente del signal_id.

---

### Filtro de capital (duro)

| Capital | Resultado |
|---|---|
| < $3,000 | IGNORADO siempre |
| $3,000 – $5,000 | IGNORADO (excepción: WHITELIST_A reduce umbral a $3K) |
| $5,000 – $10,000 | Base (WR 54.6%, PnL +$1,557) |
| $10,000 – $20,000 | Positivo (WR 57.3%) |
| ≥ $20,000 | MEDIUM→HIGH (WR 76.9%) |

> **WHITELIST_A exception:** traders como `GetaLife` (avg $4.4K) y `jackmala` (avg $3.9K) serían ignorados siempre sin este bypass. Con WR propio ≥66% justificado, el umbral se reduce a $3K solo para ellos.

---

### Sucker bet (edge negativo)

Cuando `edge_pct < -3.0%` (poly tiene precio mayor que Pinnacle en más del 3%), el sistema:
1. Añade warning: "SUCKER BET — 3/3 trades con edge < -3% perdieron (WR 0%)"
2. **Degrada confidence un nivel** si hay señal activa: HIGH→MEDIUM, MEDIUM→LOW, LOW→LOW

---

### Capital ≥$20K

Si hay señal activa y el capital de la ballena es ≥$20K:
- MEDIUM→HIGH
- Se añade nota en reasoning: "Capital ≥$20K refuerza señal (WR histórico 76.9%)"

Este modificador refleja que ballenas que mueven $20K+ tienen información o convicción claramente superior a las de $5K-$10K.

---

### Precio >0.85

Se ignora cualquier señal. EV matemáticamente negativo: el payout es tan bajo que el WR histórico no puede compensarlo.

---

### Conflicto HIGH RISK en ambos lados

Si hay una ballena HIGH RISK comprando Y otra HIGH RISK vendiendo en el mismo mercado, se ignora la señal. Cuando two HIGH RISK chocan, el mercado no tiene dirección clara.

---

### WHITELIST_TIER_OVERRIDE

Para traders cuyo tier es inestable entre capturas (oscila BOT/MM ↔ HIGH RISK según el momento del scraping), el tier snapshot se ignora y se usa el WR histórico personal. El efecto es:

- Bypasea exclusión de BOT/MM y HIGH RISK en S2, S2B, S2C
- SILVER y BRONZE **nunca** se bypasean (su exclusión no es por inestabilidad de tier)
- Criterio de entrada: WR ≥ 60%, N ≥ 15, tier documentado como inestable en producción

Traders actuales en override: `elkmonkey`, `0x4924`.

---

### Dynamic whitelist

Desde v7.5.1, el sistema consulta `trader_stats` en Supabase para construir una whitelist automática. Traders con WR ≥ 60% y N ≥ 15 trades resueltos reciben automáticamente el mismo tratamiento que `WHITELIST_TIER_OVERRIDE`, sin intervención manual.

---

### Warning SILVER en NHL

SILVER en NHL tiene WR 26.7% (N=15, Whales) — trampa confirmada. El sistema genera warning explícito: no hay señal activa para esta combinación independientemente del precio o la señal que aplique.

---

## Sistema de stake Kelly fraccionado

El stake se determina por la combinación `(signal_id, confidence)`:

| Señal | Confidence | Stake |
|---|---|---|
| S2B | HIGH | 3% |
| S2+ | HIGH | 3% |
| S2 | HIGH | 3% |
| S10 | HIGH | 3% |
| S1 | HIGH | 4% |
| S1B | HIGH | 4% |
| S2B | MEDIUM | 2% |
| S2 | MEDIUM | 2% |
| S2C | MEDIUM | 2% |
| S4 | MEDIUM | 2% |
| S7 | MEDIUM | 2% |
| S10 | MEDIUM | 2% |
| S3 | LOW | 1% |
| S5-MMA | LOW | 1% |
| S1-MMA-RISKY | LOW | 1% |
| S1 | LOW | 1% |

> Adicionalmente: WHITELIST_A aplica multiplicador ×1.5 sobre el stake base. WHITELIST_B aplica ×1.25.

---

## Listas de traders

### WHITELIST_A — Traders élite (umbral reducido $3K, stake ×1.5)

`hioa`, `KeyTransporter`, `HOCHI`, `GetaLife`, `jackmala`

Criterio: WR propio ≥66% con N suficiente, documentado en producción.

### WHITELIST_B — Traders validados (stake ×1.25)

`theyseemeloosintheyhatin`, `elkmonkey`, `gmanas`, `synnet`, `takeormake`, `joosangyoo`, `statwC00KS`, `0x4924`

> `gmanas`: WR 88.9% N=9 — a 6 trades de WHITELIST_A. Monitorear.  
> Eliminados v7.6.2: `qqzhi4527` (WR 45.5%, N=11) y `TheOnlyHuman` (WR 54.5%, N=11) — por debajo del umbral ≥60%.

### WHITELIST_TIER_OVERRIDE — Bypass de tier inestable

`elkmonkey` — WR 68.4% NBA, N≥15. Tier oscila BOT/MM ↔ HIGH RISK según captura.  
`0x4924` — WR 61.1% NBA, capital $53K avg. Ídem.

### BLACKLIST — Traders a evitar o considerar contra

`sovereign2013`, `bossoskil1`, `BITCOINTO500K`, `432614799197`, `xdoors`, `hotdogcat`, `Sensei2`, `swisstony`, `VeryLucky888` (WR 50% N=10, PnL -$29), `BWArmageddon` (WR 42.9% N=7, PnL -$34)

> `c4c4` eliminado de blacklist en v7.6.2: WR recuperó de 29% (N=7) a 56.3% (N=16). Monitorear hasta N≥20.
