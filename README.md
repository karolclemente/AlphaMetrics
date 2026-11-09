# Proyecto de analítica financiera | IX Concurso ITM 2026

> **Plantilla de trabajo para estudiantes.** Crea una copia de este archivo en el repositorio de tu equipo y reemplaza los textos entre corchetes. Conserva las preguntas como apoyo durante la investigación. No publiques claves de acceso ni información privada.

**Curso:** Lenguajes de Programación · Ingeniería Financiera  
**Concurso:** 12 de noviembre de 2026  
**Simulacro:** 10 de noviembre de 2026  
**Tecnología:** [Python + Streamlit / R + Shiny / otra interfaz en Python o R]

## 1. Identificación del proyecto

- **Nombre de la aplicación:** AlphaMetrics
- **Equipo e integrantes:** Karol Clemente, Xiomara Gutierrez, Millerllay Velez
- **Enlace a la aplicación:** [Pendiente / URL]
- **Repositorio del proyecto:** [URL]
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
| Matriz de Correlación de Pearson | Grado de asociación lineal entre los retornos de cada par de activos: $\rho_{X,Y} = \frac{\operatorname{Cov}(X,Y)}{\sigma_X \sigma_Y}$. | Identifica el potencial de diversificación y reducción de riesgo no sistemático al combinar la cartera. | src/analytics.py (función correlation_matrix) |

No es necesario utilizar todos los indicadores estudiados en clase. Selecciona y justifica únicamente los pertinentes.

### Visualizaciones previstas

| Gráfico | Pregunta que ayuda a responder | Variables | Interpretación esperada |
|---|---|---|---|
| [Gráfico 1] | [Pregunta] | [Variables] | [Qué se podrá observar] |
| [Gráfico 2] | [Pregunta] | [Variables] | [Qué se podrá comparar] |

**Primer resultado verificable:** [Enlace a un cálculo o gráfico realizado con datos reales y una interpretación breve]

## 5. Diseño de la aplicación · S25

**Investiga:** ¿cómo se construyen controles, gráficos y métricas con la herramienta elegida? ¿qué sucede cuando el usuario modifica una selección?

- **Usuarios previstos:** Analistas financieros junior, estudiantes de ingeniería financiera, gestores de carteras minoristas y jurados evaluadores del concurso ITM.
- **Entradas y filtros:** -Selector multiactivo (st.multiselect) para elegir entre acciones y ETFs disponibles.

-Selector de rango de fechas (st.date_input) para calibrar ventanas de tiempo específicas (ej. pre y post subida de tasas).

-Tasa libre de riesgo editable (st.number_input) para ajustar el cálculo dinámico del Ratio de Sharpe.

-Selector de benchmark de referencia (st.selectbox, por defecto SPY).

- **Secciones de la interfaz:** [Sidebar: Controles globales de activos, fechas y parámetros financieros.

Panel Principal - Resumen Ejecutivo: Tarjetas de métricas (st.metric) con Retorno Anualizado, Volatilidad, Sharpe y Max Drawdown.

Pestaña 1 (Rendimiento Histórico): Gráfico interactivo en Plotly con precios normalizados y velas japonesas/volumen.

Pestaña 2 (Análisis de Riesgo y Caídas): Gráficos de Drawdown submarino y distribución de retornos (histogramas/KDE).

Pestaña 3 (Diversificación): Heatmap interactivo de correlación y matriz de covarianzas.

- **Resultados visibles:** Indicadores numéricos instantáneos (KPIs con deltas porcentuales), gráficos interactivos con zoom y tooltips mediante Plotly, y tablas comparativas de resumen estadístico exportables.
- **Interacción principal:**Cuando el usuario cambia la fecha o agrega/elimina un activo en el selector lateral, Streamlit reejecuta reactivamente el flujo: filtra el DataFrame cacheado, recalcula vectorialmente la matriz de retornos y actualiza instantáneamente todos los gráficos y KPIs en pantalla sin recargar la página.

**Flujo previsto:**  
Selección de activos y parámetros → Obtención de datos → Preparación y cálculos → Visualización → Interpretación.

**Boceto o esquema:** Estructura de dos columnas: barra lateral izquierda para controles de entrada y panel derecho dividido en pestañas temáticas de métricas y gráficos

**Decisión técnica:**  Se seleccionó Python + Streamlit debido a su velocidad de iteración y soporte nativo para librerías financieras avanzadas (pandas, numpy, yfinance). A diferencia de Dash, Streamlit elimina la necesidad de escribir plantillas HTML/CSS y callbacks complejos, lo que garantiza tener un prototipo interactivo robusto, estable y publicado en la nube dentro del plazo de 1 semana para el concurso

## 6. Construcción y pruebas · S26 (5 de noviembre)

Actualiza esta sección durante el laboratorio de desarrollo.

- **Componentes implementados:** [Qué funciona realmente]
- **Cómo ejecutar la aplicación:** [Comandos, dependencias y pasos]
- **Dependencias:** [Archivo `requirements.txt`, `renv.lock` u otro]
- **Pruebas realizadas:** [Caso probado, resultado esperado y resultado obtenido]
- **Errores conocidos y cómo se resolvieron:** [Explicación]
- **Pendientes antes del simulacro:** [Tareas concretas]

> No afirmes que una función está implementada si aún no ha sido probada.

## 7. Hallazgos e interpretación · S26–S28

Para cada hallazgo importante, registra:

1. **Evidencia:** [Gráfico, tabla o indicador con enlace]
2. **Qué muestra:** [Describe el comportamiento observado]
3. **Interpretación financiera:** [Explica su significado en relación con la pregunta]
4. **Limitaciones:** [Qué no puedes concluir de los datos]

**Conclusión provisional o final:** [Respuesta sustentada a la pregunta principal]

> Los resultados históricos no garantizan rendimientos futuros.

## 8. Simulacro · S27 (10 de noviembre)

**Guion de sustentación (3–5 minutos):**
- Problema financiero y activos.
- Datos y metodología.
- Demostración de interactividad.
- Hallazgos e interpretación.
- Conclusión y limitaciones.

**Retroalimentación recibida:** [Observaciones del docente o compañeros]

| Ajuste necesario | Responsable | Estado |
|---|---|---|
| [Ajuste] | [Integrante] | [Pendiente / en proceso / resuelto] |

## 9. Presentación final · S28 (12 de noviembre)

- **Enlace funcional de la aplicación:** [URL]
- **Versión final / commit:** [Enlace]
- **Evidencia de presentación:** [Enlace autorizado]
- **Conclusiones finales:** [Síntesis]
- **Limitaciones y posibles mejoras:** [Descripción]

## 10. Bitácora de trabajo y aportes

| Fecha | Integrante(s) | Investigación o decisión | Avance técnico / evidencia | Próximo paso |
|---|---|---|---|---|
| 2026-11-03 | [Nombre] | [Qué investigamos] | [Enlace a código o documento] | [Acción] |
| 2026-11-05 | [Nombre] | [Decisiones] | [Evidencia] | [Acción] |
| 2026-11-10 | [Nombre] | [Retroalimentación] | [Cambio] | [Acción] |

## Recursos de consulta

- [Libro de programación y analítica financiera del curso](https://github.com/vyolete/proyecto_analitica_financiera)
- [Documentación de yfinance](https://ranaroussi.github.io/yfinance/)
- [Documentación de pandas](https://pandas.pydata.org/docs/)
- [Documentación de Streamlit](https://docs.streamlit.io/)
- [Documentación de Shiny para R](https://shiny.posit.co/r/)

**Uso del documento:** En S25 desarrolla especialmente las secciones 2 a 5 y registra tu primera prueba; en S26, S27 y S28 actualiza el mismo README. No dupliques información en entregas separadas.
