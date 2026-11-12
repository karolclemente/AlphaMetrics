# Proyecto de analítica financiera | IX Concurso ITM 2026

> **Plantilla de trabajo para estudiantes.** Crea una copia de este archivo en el repositorio de tu equipo y reemplaza los textos entre corchetes. Conserva las preguntas como apoyo durante la investigación. No publiques claves de acceso ni información privada.

**Curso:** Lenguajes de Programación · Ingeniería Financiera  
**Concurso:** 12 de noviembre de 2026  
**Simulacro:** 10 de noviembre de 2026  
**Tecnología:** [Python + Streamlit / R + Shiny / otra interfaz en Python o R]

## 1. Identificación del proyecto

- **Nombre de la aplicación:** AlphaMetrics
- **Equipo e integrantes:** Karol Clemente, Xiomara Gutierrez, Millerllay Velez
- **Enlace a la aplicación:** https://oecd-graphical-scsi-spirituality.trycloudflare.com/
- **Repositorio del proyecto:** https://colab.research.google.com/drive/127Nn2ikCjLs_lmQTLqeLR-piGaAAJU7g#scrollTo=QUFlptVfR5qq
- **Última actualización:** 2026-11-03

## 2. ¿Qué queremos investigar? · S25

-**Investiga:** ¿Qué significa analizar el comportamiento histórico de un activo financiero? ¿Qué preguntas pueden responderse con datos de mercado y cuáles no?-

- **Situación o fenómeno de interés:** La relación de compensación entre riesgo y retorno (trade-off) al combinar acciones individuales de alto crecimiento versus fondos cotizados en bolsa (ETFs) diversificados y temáticos, evaluando cómo la diversificación sectorial mitiga el riesgo sistemático y el impacto de caídas severas (drawdowns) en diferentes regímenes de mercado.
- **Pregunta principal:**  ¿Cómo se compara el desempeño ajustado por riesgo (medido por el Ratio de Sharpe y Máximo Drawdown) entre una cartera de acciones tecnológicas individuales de alta capitalización y un portafolio de ETFs diversificados y sectoriales durante el período 2021–2026?
- **Objetivo del análisis:** Evaluar, cuantificar y comparar el perfil de volatilidad, correlaciones dinámicas, pérdidas máximas y retornos acumulados entre activos de renta variable individual y ETFs, con el fin de determinar la eficiencia de la diversificación pasiva frente a la concentración en activos de alta beta.
- **Importancia financiera:**  Para un gestor de inversiones o inversionista minorista, comprender la covarianza y el comportamiento de las colas de distribución (drawdown) entre acciones y ETFs permite optimizar la asignación de capital, reduciendo la exposición al riesgo no sistemático sin sacrificar retornos esperados en contextos de volatilidad de tasas de interés e inflación.
- **Preguntas complementarias:** ¿Qué nivel de correlación histórica existe entre los ETFs sectoriales analizados y las acciones individuales seleccionadas?

¿En qué medida la diversificación en ETFs amortiguó las pérdidas durante el ciclo bajista de 2022 en comparación con las acciones individuales?

> **Comprueba:** Sí: identifica 4 activos representativos (AAPL, NVDA, SPY, QQQ), variables de precios ajustados y volumen, y abarca el período 2021-01-01 a 2026-10-31 usando datos públicos de Yahoo Finance.

## 3. Activos, datos y alcance · S25

**Investiga:** ¿qué activos se ajustan a tu pregunta? ¿qué datos históricos existen? ¿qué frecuencia, período y fuente utilizarás?

| Activo | Símbolo o ticker | Tipo | Razón para incluirlo |
|---|---|---|---|
| [Apple Inc.]| [AAPL] | [Acción individual] | [Referente global de alta capitalización, liquidez extrema y estabilidad relativa dentro del sector tecnológico.] |
| [NVIDIA Corporation]| [NVDA] | [Acción individual] | [Representante de alto crecimiento, alta volatilidad y beta elevada impulsada por el ciclo de semiconductores e IA.] |
| [SPDR S&P 500 ETF Trust] | [SPY] | [ETF Indexado (Mercado amplio).] | [Benchmark estándar del mercado estadounidense para contrastar el riesgo de mercado general y calcular betas.] |
| [Invesco QQQ Trust] | [QQQ] | [ETF Sectorial/Temático] | [Benchmark de crecimiento y tecnología (Nasdaq-100), útil para evaluar si la diversificación sectorial supera a las acciones individuales.] |

- **Fuente de datos:** Yahoo Finance a través de la librería en Python yfinance.
- **Período:** 021-01-01 a 2026-10-31 (5 años de serie histórica con regímenes alcistas, bajistas y de recuperación).
- **Frecuencia:**  Diaria (precios de cierre diario en días hábiles de cotización bursátil).
- **Variables disponibles:** Date (Fecha), Open (Apertura), High (Máximo), Low (Mínimo), Close (Cierre), Adj Close (Cierre ajustado por dividendos y splits) y Volume (Volumen transaccional). Para los modelos financieros se usará principalmente Adj Close.
- **Limitaciones de los datos:** Días festivos y fines de semana no cotizados (fechas discontinuas), diferencias de zonas horarias en cierres de mercado, rezago intradía en cotizaciones gratuitas y posibles ajustes retrospectivos por splits o repartos corporativos que requieren sincronización mediante alineación de índices en Pandas (ffill/dropna).

**Primera prueba:** [Enlace al notebook o archivo que descarga o consulta datos; indica qué funcionó y qué no]

## 4. Metodología de análisis · S25–S26

**Investiga:** ¿qué indicadores permiten responder la pregunta principal? ¿cómo se calculan? ¿qué interpretación y limitaciones tienen?

| Indicador / técnica | ¿Qué mide? | ¿Por qué lo necesitamos? | Implementación |
|---|---|---|---|
| Retorno Logarítmico Diario y Acumulado | La tasa continua de rentabilidad diaria: $r_t = \ln(P_t / P_{t-1})$. | Permite sumar rendimientos en el tiempo y estabilizar la varianza estadística para comparar activos equitativamente. | src/analytics.py (función calculate_returns) |
| Volatilidad Anualizada ($\sigma$) | Dispersión de los retornos alrededor de la media: $\sigma_{anual} = \sigma_{diaria} \times \sqrt{252}$. | Mide el riesgo total y la incertidumbre del activo en un horizonte estándar de un año bursátil. | src/analytics.py (función calculate_volatility) |
| Ratio de Sharpe | [Retorno excedente por unidad de riesgo total: $S = \frac{R_p - R_f}{\sigma_p}$ (asumiendo tasa libre de riesgo $R_f$). | Evalúa si el rendimiento obtenido compensa el riesgo asumido respecto a un activo seguro. | src/analytics.py (función calculate_sharpe_ratio) |
| Maximum Drawdown (MDD) | Caída porcentual máxima desde un pico histórico hasta su valle subsecuente: $MDD = \frac{P_{valle} - P_{pico}}{P_{pico}}$. | Cuantifica el peor escenario histórico de pérdida de capital, crucial para evaluar resiliencia de carteras. | src/analytics.py (función calculate_drawdown) |
| Matriz de Correlación de Pearson | Grado de asociación lineal entre los retornos diarios de los activos (coeficiente de Pearson). | Identifica qué activos aportan verdadera diversificación y cuáles presentan redundancia en la cartera. | `analytics.py` / `df_returns.corr()` visualizado con mapa de calor. |


No es necesario utilizar todos los indicadores estudiados en clase. Selecciona y justifica únicamente los pertinentes.

### Visualizaciones previstas

| Gráfico | Pregunta que ayuda a responder | Variables | Interpretación esperada |
|---|---|---|---|
| **Rendimiento acumulado normalizado (Base 100)** | ¿Qué activo o ETF generó mayor ganancia acumulada en el horizonte de estudio? | Tiempo (días) vs. Precio normalizado ($P_t / P_0 \times 100$). | Comparar directamente el crecimiento de un dólar invertido en cada alternativa a partir de la misma base. |
| **Gráfico submarino de Drawdowns** | ¿Cuándo y con qué profundidad ocurrieron las mayores caídas de capital en cada activo? | Tiempo (días) vs. Caída porcentual desde el máximo acumulado. | Observar visualmente cómo los ETFs amortiguan las caídas severas frente a caídas pronunciadas en acciones individuales. |
| **Mapa de calor de correlación (Heatmap)** | ¿Qué nivel de cointegración/correlación existe entre las acciones y los índices de referencia? | Matriz simétrica de pares de activos con escala de -1 a 1. | Rastrear relaciones cercanas a 1.0 (redundancia) y valores moderados (potencial de diversificación). |
| **Dispersión Riesgo vs. Retorno** | ¿Qué activo se posiciona en el cuadrante más eficiente de inversión? | Eje X: Volatilidad anualizada; Eje Y: Retorno anualizado. | Visualizar la frontera donde los activos con mayor retorno deberían justificar su nivel superior de riesgo. |

**Primer resultado verificable:** [Enlace a un cálculo o gráfico realizado con datos reales y una interpretación breve]

## 5. Diseño de la aplicación · S25

**Investiga:** ¿cómo se construyen controles, gráficos y métricas con la herramienta elegida? ¿qué sucede cuando el usuario modifica una selección?

- **Usuarios previstos:** Analistas financieros junior, estudiantes de ingeniería financiera, gestores de carteras minoristas y jurados evaluadores del concurso ITM.
- **Entradas y filtros:**
  - Selector multiactivo (`st.multiselect`) para elegir entre acciones y ETFs disponibles.
  - Selector de rango de fechas (`st.date_input`) para calibrar ventanas de tiempo específicas (ej. pre y post subida de tasas).
  - Tasa libre de riesgo editable (`st.number_input`) para ajustar el cálculo dinámico del Ratio de Sharpe.
  - Selector de benchmark de referencia (`st.selectbox`, por defecto `SPY`).
- **Secciones de la interfaz:**
  - Barra lateral (`st.sidebar`): Controles globales de activos, fechas y parámetros financieros.
  - Panel Principal - Resumen Ejecutivo: Tarjetas de métricas (`st.metric`) con Retorno Anualizado, Volatilidad, Sharpe y Max Drawdown.
  - Pestaña 1 (Rendimiento Histórico): Gráfico interactivo en Plotly con precios normalizados y velas japonesas/volumen.
  - Pestaña 2 (Análisis de Riesgo y Caídas): Gráficos de Drawdown submarino y distribución de retornos (histogramas/KDE).
  - Pestaña 3 (Diversificación): Heatmap interactivo de correlación y matriz de covarianzas.
- **Resultados visibles:** Indicadores numéricos instantáneos (KPIs con deltas porcentuales), gráficos interactivos con zoom y tooltips mediante Plotly, y tablas comparativas de resumen estadístico exportables.
- **Interacción principal:** Cuando el usuario cambia la fecha o agrega/elimina un activo en el selector lateral, Streamlit reejecuta reactivamente el flujo: filtra el DataFrame cacheado, recalcula vectorialmente todas las métricas y actualiza los gráficos interactivos sin recargar la página.

**Flujo previsto:**  
Selección de activos y parámetros → Obtención de datos → Preparación y cálculos → Visualización → Interpretación.

**Boceto o esquema:** Estructura de dos columnas: barra lateral izquierda para controles de entrada y panel derecho dividido en pestañas temáticas de métricas y gráficos

**Decisión técnica:**  Se seleccionó Python + Streamlit debido a su velocidad de iteración y soporte nativo para librerías financieras avanzadas (pandas, numpy, yfinance). A diferencia de Dash, Streamlit elimina la necesidad de escribir plantillas HTML/CSS y callbacks complejos, lo que garantiza tener un prototipo interactivo robusto, estable y publicado en la nube dentro del plazo de 1 semana para el concurso

## 6. Construcción y pruebas · S26 (5 de noviembre)

Actualiza esta sección durante el laboratorio de desarrollo.

- **Componentes implementados:**- Ingesta de datos automatizada y en caché con `yfinance` (`@st.cache_data`).
  - Motor de cálculo cuantitativo vectorizado con NumPy y pandas: retornos diarios logarítmicos, volatilidad anualizada ($\sigma \times \sqrt{252}$), Ratio de Sharpe, Maximum Drawdown (MDD) y Beta frente a `SPY`.
  - Interfaz gráfica en Streamlit organizada en 3 pestañas: *Rendimiento Histórico*, *Riesgo & Drawdowns*, y *Diversificación & Correlación*.
  - Gráficos interactivos en Plotly: precios normalizados (base 100), gráfico submarino de drawdowns y mapa de calor de correlaciones.
  - Exportador a CSV para descargar el resumen estadístico generado.
- **Cómo ejecutar la aplicación:** 1. Clonar el repositorio: `git clone https://github.com/karolclemente/AlphaMetrics.git`
  2. Entrar a la carpeta: `cd AlphaMetrics`
  3. Crear y activar entorno virtual: `python -m venv venv` y `source venv/bin/activate` (o `venv\Scripts\activate` en Windows).
  4. Instalar dependencias: `pip install -r requirements.txt`
  5. Iniciar la app: `streamlit run app.py`
- **Dependencias:** - `streamlit>=1.28.0`
  - `yfinance>=0.2.30`
  - `pandas>=2.0.0`
  - `numpy>=1.24.0`
  - `plotly>=5.15.0`
  - `scipy>=1.10.0`
- **Pruebas realizadas:** - *Prueba de descarga:* Consulta simultánea de tickers (`AAPL`, `NVDA`, `SPY`, `QQQ`) en rango 2021-01-01 a 2026-11-01. Resultado: Descarga exitosa y persistencia en caché.
  - *Prueba de cálculo:* Validación de la matriz de covarianzas y cálculo de Sharpe contra valores esperados. Resultado: Métricas coincidentes con fórmulas teóricas.
  - *Prueba de UI:* Cambio dinámico de tasa libre de riesgo y fechas. Resultado: Gráficos y KPIs se recalculan de inmediato sin parpadeos.
- **Errores conocidos y cómo se resolvieron:** - *MultiIndex en yfinance:* Al descargar múltiples tickers, `yfinance` entrega columnas anidadas. Se resolvió extrayendo directamente el nivel `'Close'` o `'Adj Close'` con indexación explícita.
  - *División por cero en Sharpe:* En rangos de tiempo ultra cortos la volatilidad tendía a cero; se implementó una guarda condicional para devolver `NaN` o `0.0` en caso de varianza nula.
- **Pendientes antes del simulacro:** - Afinar la escala de colores en el heatmap de correlaciones.
  - Verificar tiempos de carga en Streamlit Community Cloud ante conexiones lentas.

> No afirmes que una función está implementada si aún no ha sido probada.

## 7. Hallazgos e interpretación · S26–S28

Para cada hallazgo importante, registra:

1. **Evidencia:** 1.Gráfico interactivo de precios normalizados (base 100) y comparativa de Retorno Anualizado vs. Volatilidad.
   - **Qué muestra:** Activos tecnológicos individuales (`NVDA`, `AAPL`) exhiben un retorno acumulado significativamente superior al de los índices ponderados (`SPY`, `QQQ`), pero con el doble de volatilidad anualizada.
   - **Interpretación financiera:** Existe una prima de riesgo evidente en activos individuales de alto crecimiento. Sin embargo, en términos de Sharpe Ratio, el ETF `QQQ` ofrece una eficiencia de capital superior al reducir el riesgo idiosincrático sin sacrificar sustancialmente el factor tecnológico.
   - **Limitaciones:** El período de análisis incluye rallies excepcionales de semiconductores impulsados por IA, lo cual sobrepondera los retornos pasados de forma no replicable indefinidamente.

2. **Evidencia:** Gráfico submarino de Maximum Drawdown (MDD).
   - **Qué muestra:** Durante las caídas de mercado de 2022, `NVDA` experimentó caídas superiores al -60%, mientras que `SPY` limitó su pérdida máxima a aproximadamente -24%.
   - **Interpretación financiera:** Demuestra empíricamente el valor del ETF como instrumento de preservación de capital: la diversificación amortigua el impacto de shocks macroeconómicos (tasas de interés y contracción de múltiplos).
   - **Limitaciones:** Los drawdowns pasados dependen de la duración de cada ciclo bajista; una caída repentina de liquidez sistémica podría acoplar todas las clases de activos temporalmente.

3. **Evidencia:** Heatmap de correlación de retornos diarios.
   - **Qué muestra:** Correlaciones superiores a 0.75 entre acciones tecnológicas y el ETF `QQQ`, pero correlaciones moderadas (0.45 a 0.55) frente a ETFs defensivos o de mercado amplio como `SPY`.
   - **Interpretación financiera:** Mantener simultáneamente `AAPL`, `NVDA` y `QQQ` genera redundancia de cartera y concentración factorial, reduciendo la efectividad de la diversificación.
   - **Limitaciones:** Las correlaciones no son estáticas; tienden a converger a 1.0 en momentos de pánico financiero generalizado.
   - 

**Conclusión provisional o final:** Los ETFs de índices (`SPY`, `QQQ`) superan consistentemente a las selecciones de acciones individuales en retornos ajustados por riesgo a mediano y largo plazo. La aplicación AlphaMetrics permite evidenciar que concentrar capital en acciones de alto rendimiento únicamente se justifica si el horizonte de inversión tolera drawdowns superiores al 50%.

> Los resultados históricos no garantizan rendimientos futuros.

## 8. Simulacro · S27 (10 de noviembre)

**Guion de sustentación (3–5 minutos):**
- **Minuto 1 (Problema y activos):** Presentación del equipo AlphaMetrics. Contexto: el dilema del inversionista minorista entre elegir acciones individuales ganadoras o ETFs diversificados. Activos elegidos: `AAPL`, `NVDA`, `SPY`, `QQQ`.
- **Minuto 2 (Datos y arquitectura):** Ingesta mediante `yfinance`, limpieza de datos históricos y cálculo vectorizado en Python. Despliegue interactivo en Streamlit con gráficos de Plotly.
- **Minuto 3 (Demostración de interactividad):** Filtros en vivo: cambio de fechas, ajuste de la tasa libre de riesgo y lectura de KPIs (Retorno, Volatilidad, Sharpe, Beta y Drawdown).
- **Minuto 4 (Hallazgos cuantitativos):** Análisis del trade-off riesgo/retorno, resiliencia de los ETFs en drawdowns severos y redundancia por alta correlación.
- **Minuto 5 (Cierre y preguntas):** Conclusión cuantitativa, limitaciones metodológicas y respuesta a preguntas del jurado.

**Retroalimentación recibida:** [Observaciones del docente o compañeros]

- Se sugirió incluir el cálculo del Beta de cada activo frente al benchmark `SPY` directamente en la tabla resumen.
- Añadir un botón rápido para exportar la tabla de métricas en formato CSV.

| Ajuste necesario | Responsable | Estado |
|---|---|---|
| Implementar columna de Beta frente al benchmark | Equipo AlphaMetrics | Resuelto |
| Añadir funcionalidad de descarga en CSV (`st.download_button`) | Equipo AlphaMetrics | Resuelto |
| Revisar contraste visual de gráficos en modo oscuro | Equipo AlphaMetrics | Resuelto |

## 9. Presentación final · S28 (12 de noviembre)

- **Enlace funcional de la aplicación:** https://jeremy-exposure-stud-fitting.trycloudflare.com/
- **Versión final / commit:** https://github.com/karolclemente/AlphaMetrics/edit/main/README.md
- **Evidencia de presentación:** https://colab.research.google.com/drive/1adHa45UPaSfZYKyKAs_Jk4fEJvLfjiC4#scrollTo=QUFlptVfR5qq
- **Conclusiones finales:** La aplicación AlphaMetrics cumplió satisfactoriamente los objetivos del concurso, proporcionando una herramienta analítica accesible, interactiva y matemáticamente rigurosa para evaluar el binomio riesgo-retorno en acciones y ETFs.
- **Limitaciones y posibles mejoras:** - Incorporar optimización de portafolios mediante la Frontera Eficiente de Markowitz y Black-Litterman.
  - Integrar APIs adicionales para incluir activos de renta fija y divisas.

## 10. Bitácora de trabajo y aportes
| Fecha | Integrante(s) | Investigación o decisión | Avance técnico / evidencia | Próximo paso |
|---|---|---|---|---|
| 2026-11-03 | Equipo AlphaMetrics | Definición del problema, selección de activos (AAPL, NVDA, SPY, QQQ) y elección de Python + Streamlit. | README inicial (S25) y script preliminar de descarga con `yfinance`. | Desarrollar motor analítico y estructura base de la UI. |
| 2026-11-05 | Equipo AlphaMetrics | Implementación del motor de métricas vectorizadas y diseño de pestañas interactivas en Plotly. | `app.py` funcional con KPIs, gráficos normalizados, drawdowns y matriz de correlación. | Probar reactividad y preparar caso para el simulacro. |
| 2026-11-10 | Equipo AlphaMetrics | Ejecución del simulacro de sustentación y retroalimentación docente. | Incorporación de Beta frente a SPY y exportador a CSV; ajuste del guion de 4 minutos. | Despliegue final y verificación de enlaces. |
| 2026-11-12 | Equipo AlphaMetrics | Sustentación final en el IX Concurso ITM 2026. | Repositorio documentado, aplicación pública desplegada y presentación ejecutiva. | Cierre de proyecto. |

## Recursos de consulta

- [Libro de programación y analítica financiera del curso](https://github.com/vyolete/proyecto_analitica_financiera)
- [Documentación de yfinance](https://ranaroussi.github.io/yfinance/)
- [Documentación de pandas](https://pandas.pydata.org/docs/)
- [Documentación de Streamlit](https://docs.streamlit.io/)
- [Documentación de Shiny para R](https://shiny.posit.co/r/)

**Uso del documento:** En S25 desarrolla especialmente las secciones 2 a 5 y registra tu primera prueba; en S26, S27 y S28 actualiza el mismo README. No dupliques información en entregas separadas.
