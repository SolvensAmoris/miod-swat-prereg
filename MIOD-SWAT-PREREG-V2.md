# MIOD-SWAT-PREREG-V2

**Estado:** v2, completa. Sustituye a v1, que permanece inmutable en el historial.
**Precondición de ejecución:** no se ejecuta la evaluación definitiva hasta que el protocolo pase sus propios controles (niveles 1 y 2, §5 y §6).
**Regla de desviación:** todo cambio respecto a v2 se registra con fecha, motivo y momento (antes/después de observar datos). Los cambios posteriores a observar datos se reportan como exploratorios y no pueden falsar hipótesis.

---

## 1. Arquitectura experimental en tres niveles

| Nivel | Pregunta | Si falla |
|---|---|---|
| 1. Validez técnica | ¿El mapeador aprende y la infraestructura es correcta? | Bug o pipeline roto. No se interpreta como resultado sobre SwaT. |
| 2. Validez instrumental | ¿La sonda distingue representación con información de representación sin ella? | Instrumento roto. Se rediseña el instrumento. No se interpreta como resultado sobre SwaT. |
| 3. Evidencia científica | ¿SwaT transfiere información? | Resultado sobre el fenómeno. |

Distinción que se declara explícitamente: el test de identidad de §5.3 valida la infraestructura matemática del mapeador. No demuestra que dos arquitecturas distintas compartan una representación equivalente. Esa es una pregunta del nivel 3, no del nivel 1.

---

## 2. Unidad experimental e independencia

- Unidad de análisis: documento d.
- Unidad de independencia: documento. Todas las propiedades y preguntas de un mismo d caen en el mismo split.
- Inferencia agregada por documento o con efectos aleatorios por documento.
- Splits por documento, nunca por pregunta. Cuatro usos: entrenamiento del mapeador, entrenamiento de sondas, validación de hiperparámetros, evaluación final.

---

## 3. Paso 0 — Precondición de pipeline (opción a)

No es una reproducción numérica. El repositorio Ar-Kareem/SwaT no publica checkpoints del mapeador (el mapeador se produce localmente en `<run>/stage2/exit-checkpoint-24000/renderer.pt`). Reproducir una celda de la Tabla 3 exigiría reentrenar el mapeador: es una réplica, no un smoke test.

Precondición adoptada: el código entrena un mapeador en el par Qwen3-4B → Llama3-8B, la pérdida de reconstrucción en validación cumple §5.1, y el pipeline completo (extracción de caché → mapeo → sonda) ejecuta sin errores en el piloto de instrumento.

Entorno fijado:

| Elemento | Valor |
|---|---|
| Python | 3.10 |
| PyTorch | 2.6.0 |
| transformers | 5.2.0 |
| datasets | 4.5.0 |
| GPUs | 2 (emisor en una, receptor en otra) |
| Modelo de GPU | A declarar en el registro de ejecución antes del piloto |

El modelo de GPU no está fijado por el repo ni por el paper. Se declara antes del piloto y no se cambia después.

Nota: la celda numérica del paper queda como referencia informativa, no como criterio. Cualquier comparación con 53.1/34.2 (presupuesto "Lowest", Qwen3-4B → Llama3-8B) se reporta como contexto, no como validación.

---

## 4. Propiedades P

### 4.1 Definición

Tipos: entidad, fecha, cifra, relación.
Forma: afirmación binaria o categórica verificable contra d, con respuesta de referencia.

Requisitos:

- Verificable contra d.
- No deducible analíticamente desde q (requisito de la pregunta, distinto del control empírico de §4.2).
- Ambigüedad resoluble con la guía congelada; si no, se excluye.
- No depende de conocimiento externo a d.

No se usa la invariancia a la paráfrasis como criterio de inclusión. Se reporta como moderador exploratorio (§9), sin justificación teórica en la sección de criterios.

### 4.2 Criterios de inclusión (fijados, no revisables tras el piloto de P)

| Criterio | Regla |
|---|---|
| Varianza | 20 %–80 % de documentos con valor positivo |
| Suelo (dependencia de d) | Receptor sin caché y sin texto (solo q) acierta ≤ azar + margen |
| Techo (control de canal) | Receptor con texto original de d acierta ≥ azar + margen |
| Mínimo por tipo (primario) | ≥ 20 propiedades y ≥ 10 documentos contribuyentes |

Azar por formato: el umbral se fija respecto al azar de cada formato (1/k para binarias/categóricas), no como porcentaje absoluto. El margen se fija por propiedad antes del piloto, en la guía de anotación.

Regla de exclusión separada: una propiedad que pasa suelo y falla techo se excluye. El conteo por tipo se reporta obligatoriamente.

### 4.3 Acuerdo

- α de Krippendorff ≥ 0.80 por tipo; κ de Cohen por par; PABAK/AC1 como robustez; prevalencia reportada.
- α < 0.80: se excluye del primario, se reporta como «en disputa», no falsa H1.
- Tasa de exclusión se reporta por tipo, como resultado.
- Sesgo declarado: la exclusión por ambigüedad es no aleatoria; los análisis quedan condicionados a «propiedades claras».

### 4.4 Anotación

- Dos anotadores independientes; guía congelada antes del piloto.
- Al menos uno naive a las hipótesis.
- Sin acceso a resultados de sondas o del modelo.
- Desacuerdos: exclusión directa, sin tercer anotador.

### 4.5 Si la tasa de exclusión es alta

Se acepta como resultado de diseño. No se reduce el mínimo de 20/10 a posteriori. Los tipos que no alcancen el mínimo quedan como secundarios y sus contrastes se marcan «no evaluable» (§7).

---

## 5. Nivel 1 — Validación técnica

### 5.1 Convergencia del mapeador

Sobre el split de validación de hiperparámetros (nunca el de evaluación):

1. Evaluaciones a intervalos fijos.
2. Ventana móvil de evaluaciones.
3. Pendiente de regresión de la pérdida sobre la ventana ≤ 0. No se exige monotonía estricta: una pérdida estocástica fluctúa y la monotonía puntual confundiría fluctuación con ausencia de aprendizaje.
4. Pérdida final < pérdida del predictor trivial (media por capa y cabeza calibrada con el RMS del receptor).
5. Curva completa reportada, no solo el valor final.

Diagnóstico de sobreajuste a dominio: pérdida reportada por tipo de documento. Si el mapeador solo mejora en el dominio de entrenamiento, se registra como limitación de generalización, no como fallo de convergencia.

### 5.2 Predictor trivial

Media por capa y cabeza calibrada con el RMS del receptor. Es el suelo contra el que se compara la pérdida del mapeador. Se fija antes del piloto.

### 5.3 Test de identidad (desagregado)

Emisor y receptor de la misma arquitectura, caché idéntico como entrada. Se reportan cuatro componentes por separado:

| Componente | Qué detecta |
|---|---|
| Error de reconstrucción numérica | Precisión del mapeo |
| Alineación de capas | Correspondencia de índices |
| Alineación de posiciones / RoPE | Bug de posiciones |
| Correspondencia de dimensiones | Bug de shapes |

Tolerancia determinada por precisión numérica (BF16, FP16, FP32), no umbral universal. Se fija antes del piloto.

### 5.4 Test de integridad del caché

El caché extraído del emisor debe coincidir con el de una segunda ejecución con la misma semilla. Tolerancia por precisión, declarada antes del piloto.

---

## 6. Nivel 2 — Validación instrumental

### 6.1 Criterios del piloto de instrumento

La forma es definitiva. Los números 70/10/δ deben quedar fijados antes del piloto de instrumento para evitar calibración circular. Una vez fijados, no se bajan para que el instrumento pase.

- Control positivo (C_d^R): L o M superan C por ≥ δ en ≥ 70 % de las propiedades recuperables por construcción.
- Control negativo (Z_d): L y M superan C por ≥ δ en ≤ 10 % de las propiedades.
- Brecha: media(positivo) − media(negativo) ≥ δ.
- δ: 5 puntos absolutos de exactitud (fijado antes del piloto de instrumento).

### 6.2 Estadístico de calibración

G = Acc(C_d^R) − Acc(Z_d)

Se reporta:

- Media de G por documento.
- IC 95 % de G.
- Diferencia entre condiciones pareadas.
- Variabilidad entre semillas.

Regla de decisión fijada: si la brecha piloto < δ, se declara el instrumento inviable o se redefine δ con justificación independiente del resultado, nunca para que pase el umbral.

### 6.3 Orden ante fallo del positivo

Primer diagnóstico obligatorio: correr L sobre el texto de d.

- Si L sobre texto ≫ L sobre C_d^R: el caché pierde información o la sonda está desalineada.
- Si L sobre texto ≈ L sobre C_d^R: el problema es la propiedad, no la sonda.

Sin este orden, cada fallo se resolvería ajustando algo distinto y no se sabría qué cambió.

---

## 7. Nivel 3 — Hipótesis, contrastes y multiplicidad

### 7.1 Hipótesis

- H0: el caché transferido no aporta información factual adicional respecto de los controles, descontado el conocimiento previo del receptor (operacionalizado como receptor sin caché).
- H1 (transferencia): recuperabilidad en Ĉ_d > Z_d por δ, y > C por δ, en al menos la mitad de los pares cross-family.
- H2a: similitud geométrica (CKA, Procrustes) predice retención con poder > control.
- H2b: similitud geométrica no es necesaria para transferencia funcional (falsable si ambas métricas predicen con el mismo poder que el control).
- H3 (atribución): la información recuperable en Ĉ_d depende del emisor, no solo del mapeador. Requiere diseño 2×2 con M_S₂ o mapeador compartido como referencia, para no confundir dependencia del emisor con generalización fuera de distribución del mapeador.
- H4 (distribución): la caída al parchear proviene de pérdida de información. Requiere descomposición: perturbación estadística, pérdida factual, cambio en respuesta y cambio en atención (entropía por capa, KL antes/después).

### 7.2 Familia de contrastes

Primaria: 4 hipótesis × 4 tipos = 16 contrastes, Holm-Bonferroni sobre los evaluables.

Contrastes no evaluables: si un tipo no alcanza 20 propiedades / 10 documentos, su contraste se marca «no evaluable» con su n. La familia de Holm se reduce a los evaluables y esto queda preregistrado. Reducirla a posteriori cambiaría el nivel de significación de los demás.

Exploratorios (no falsan): por capa, por representación (K, K+V, secuencia), por longitud, por tipo de documento, moderador de invariancia a la paráfrasis.

---

## 8. Familia de sondas F

| Elemento | Especificación |
|---|---|
| Primaria | V-mean por capa (justificación teórica: V transporta contenido; K regula atención) |
| Secundaria | K-mean por capa |
| Robustez | K+V |
| Control posicional | Secuencia con posiciones barajadas dentro del documento |
| Control de capacidad | Sonda pooled con parámetros efectivos comparables |
| Sonda L | Ridge / regresión logística |
| Sonda M | MLP de una capa oculta (64–128), dropout |
| Selectividad | Etiquetas permutadas (5–10), media, tratada como calibración y no como nivel |

- Mismo presupuesto de entrenamiento para L y M.
- Evaluación out-of-sample en documentos no vistos.
- Selección de hiperparámetros exclusivamente en validación (incluido top-k de M).
- V-mean es preregistrada. Si resulta peor que K, se reporta como hallazgo sobre el fenómeno; no se cambia la representación primaria a posteriori.
- La sonda de secuencia se compara contra (a) secuencia barajada y (b) pooled de capacidad comparable. La señal es la diferencia entre secuencia real y barajada.

---

## 9. Estatus de los análisis

| Categoría | Puede falsar | Incluye |
|---|---|---|
| Primario | Sí | 16 contrastes H×tipo evaluables |
| Exploratorio | No | Capas, representaciones, longitudes, tipos de documento, moderador de paráfrasis, atención como mediador |

---

## 10. Pilotos

Piloto de P (informa v1 → v2 en lo relativo a P):

- 30 documentos independientes, estratificados.
- Estima tasa de exclusión, prevalencia por tipo, propiedades por tipo y documento.
- No observa sondas.

Piloto de instrumento (no informa P; calibra n):

- Documentos distintos del piloto de P.
- Comprueba §6.1–6.3.
- Estima varianza entre documentos, correlación entre condiciones pareadas, variabilidad entre semillas.
- No puede cambiar P, α, ni V-mean.

Potencia: se calcula con IC para la varianza (no con la estimación puntual) y con la tasa de exclusión observada. Si el n resultante es inviable, se declara como resultado de diseño; no se fuerza δ.

---

## 11. Decisiones fijadas en v2

1. Arquitectura en tres niveles (§1), con la distinción test de identidad ≠ equivalencia entre arquitecturas.
2. Paso 0 como precondición de pipeline, no numérica (§3).
3. Entorno de software fijado; GPU declarada antes del piloto (§3).
4. P: tipos, criterios, umbrales relativos al azar, exclusión separada suelo/techo (§4).
5. α = 0.80, robustez y sesgo de exclusión (§4.3).
6. Convergencia por pendiente de ventana + predictor trivial + curva completa (§5.1).
7. Test de identidad desagregado en cuatro componentes, tolerancia por precisión (§5.3).
8. Test de integridad del caché (§5.4).
9. Números 70/10/δ fijados antes del piloto de instrumento; G como estadístico de calibración (§6).
10. Orden de diagnóstico ante fallo del positivo (§6.3).
11. H0–H4 con criterios de falsación y confounds declarados (§7.1).
12. Familia de 16 contrastes; no evaluables preregistrados; Holm sobre evaluables (§7.2).
13. F: V-mean primaria, secuencia barajada, capacidad comparable, hiperparámetros solo en validación (§8).
14. Secundarios y exploratorios no falsan (§9).

---

## 12. Limitaciones declaradas

- Exclusión no aleatoria de propiedades ambiguas.
- Posible participación parcial de anotadores en el diseño (mitigada por el anotador naive).
- Bandas de longitud, dominio e idioma de los documentos a fijar antes del piloto de P; el tamaño de caché depende de la longitud y el matching de Z_d se dificulta con longitudes heterogéneas.
- Generalización limitada: el protocolo mide transferencia en los pares y dominios evaluados; no establece una ley general sobre interoperabilidad latente.
- El test de identidad no valida equivalencia entre arquitecturas.

---

## 13. Estado de ejecución

No se ejecuta la evaluación definitiva. Antes deben pasar:

1. Piloto de P → fija tasa de exclusión y viabilidad por tipo.
2. Números definitivos del Apéndice A (70/10/δ) fijados antes del piloto de instrumento.
3. Piloto de instrumento → valida niveles 1 y 2.
4. Cálculo de n con IC para la varianza.
5. Registro de la GPU y la semilla antes del piloto de instrumento.

Solo entonces se pasa al nivel 3.

---

v1 permanece inmutable. Cualquier modificación de v2 se registra como desviación con fecha, motivo y momento.
