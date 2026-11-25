# MIOD-SWAT-PREREG-V2.1

**Estado:** v2.1, con los puntos 1, 3, 4, 6 y 7 cerrados. Los puntos 2, 5, 8 y 9 quedan marcados como pendientes con fecha límite antes del piloto de P.
**Sustituye a:** v2. v1 y v2 permanecen inmutables en el historial.
**Marcado:** [D] = definitivo, fijado antes de observar datos. [P] = provisional, se fija en acta con fecha previa al piloto correspondiente y no se modifica después.

---

## 1. Umbrales de instrumento (cierra punto 1)

| Parámetro | Valor | Estado |
|---|---|---|
| δ (diferencia mínima de exactitud) | 5 puntos absolutos | [D] |
| Control positivo: fracción de propiedades con L o M > C por ≥ δ | ≥ 70 % | [D] |
| Control negativo: fracción de propiedades con L o M > C por ≥ δ | ≤ 10 % | [D] |
| Brecha mínima: media(positivo) − media(negativo) | ≥ δ | [D] |
| Estadístico G por documento | G = Acc(C_d^R) − Acc(Z_d) | [D] |

**Justificación de δ = 5 (independiente de datos del instrumento):** la variabilidad entre semillas de las celdas del paper (Tabla 3, desviaciones de ±2–6 en el cambio) y el ruido típico de anotación en P hacen que diferencias menores no sean distinguibles. Esto se mantiene como criterio, no como resultado.

**Regla de decisión:** si la brecha piloto < δ, el instrumento se declara inviable. No se redefine δ para que pase.

---

## 2. Convergencia del mapeador (pendiente, fecha límite: piloto de P)

Ya fijado en v2 en forma de criterio; faltan cifras:

- Intervalo de evaluación: [P] cada 500 pasos en el split de validación.
- Ventana: [P] últimas 10 evaluaciones.
- Escala: [P] pendiente de regresión sobre log(pérdida).
- Criterio de paso: pendiente ≤ 0 y pérdida final < predictor trivial.

---

## 3. Tolerancias del test de identidad (cierra punto 3)

Procedimiento, no umbral universal:

- Se calcula el error relativo máximo sobre caches idénticos en tres precisiones (BF16, FP16, FP32).
- Tolerancia = percentil 99 del error relativo en FP32 de referencia, multiplicado por 10 para BF16 y por 100 para FP16 [D].
- Estos factores son [D] y se justifican por el orden de magnitud de la mantisa de cada formato (≈ 2⁻⁸, 2⁻¹¹, 2⁻²³ respecto a FP32 de referencia).

Los valores numéricos concretos son [P] y se registran en el piloto de instrumento, antes de medir el mapeador.

---

## 4. Margen de suelo y techo (cierra punto 4)

**Regla [D]:** el margen m_p por propiedad p es 2 veces el error típico de la sonda L sobre C_d^R, estimado en el piloto de P sin usar datos de sondas del instrumento.

Concretamente, el piloto de P no tiene sondas, así que la regla se traslada a esto: m_p = 2 × σ_p, donde σ_p es el error típico de la respuesta de referencia entre los dos anotadores para p. Si el acuerdo es alto, σ_p es pequeño y m_p también.

Para propiedades binarias con azar 1/k:

- Suelo: Acc(sin caché, sin texto) ≤ 1/k + m_p.
- Techo: Acc(texto original) ≥ 1/k + m_p.

Esto reemplaza el 50 % absoluto de v1 y v2.

---

## 5. Criterios de H2a y H2b (cierra punto 6)

**Control:** predictor de retención basado solo en tamaño relativo de modelos (número de parámetros del emisor / receptor) y familia (igual o distinta). Es el predictor que no usa geometría.

**Métrica de comparación:** correlación de Spearman entre predictor y retención por par, con IC bootstrap sobre pares (n = 10 pares).

- **H2a se sostiene si:** la similitud CKA o Procrustes mejora la predicción sobre el control por ΔR² ≥ 0.10 con IC 95 % que excluye 0.
- **H2b se sostiene si:** ni CKA ni Procrustes superan al control en ese criterio.
- **Ambas se falsan si:** el resultado cae entre ambos extremos (ΔR² entre 0 y 0.10, o IC incluyendo 0). Este caso se reporta como indeterminado, no como confirmación de ninguna.

Limitación explícita: con n = 10 pares, el poder para detectar ΔR² = 0.10 es bajo. Un resultado nulo no es evidencia de H2b.

---

## 6. Criterio operacional de H4 (cierra punto 7)

**Caída atribuible a pérdida de información:** la caída de rendimiento al parchear cachés se considera atribuible a pérdida de información relevante si se cumplen las tres condiciones:

1. La caída con parcheo entre documentos equivalentes (misma longitud, mismo tipo de pregunta) es ≥ δ.
2. La caída con parcheo entre documentos equivalentes sin pérdida de contenido es < δ.
3. La caída se reduce al sustituir solo las capas o cabezas que la sonda L identifica como portadoras de la propiedad afectada.

Si no se cumple 2, la caída se atribuye a cambio de distribución (incompatibilidad de estados fuera del rango del receptor). Si no se cumple 3, no se atribuye a un componente concreto.

**Umbral de entropía/KL:** no se usa como mediador hasta validar piloto. Se reporta como exploratorio (§9).

---

## 7. Pendientes con fecha límite (v2.1 → v2.2, antes del piloto de P)

| Punto | Pendiente | Fecha límite |
|---|---|---|
| 2 | Cifras de convergencia (§2) | Antes del piloto de P |
| 5 | Diseño 2×2 de H3: elegir M_S₂ o mapeador compartido, con justificación | Antes del piloto de P |
| 8 | Método de IC para la varianza (bootstrap por documento, propuesta) y fracción conservadora | Antes del piloto de P |
| 9 | Bandas de longitud, dominio e idioma | Antes del piloto de P |

Los valores numéricos de la tolerancia del test de identidad (§3) y el predictor de control (§5) se fijan en el mismo acta.

---

## 8. Declaración de estado

v2.1 es falsable en los puntos 1, 4, 6 y 7. No es todavía un preregistro completo: H3 y la potencia siguen sin cerrar, y la evaluación definitiva no puede ejecutarse hasta cerrar §7.

Una observación de honestidad sobre lo que he escrito en §1: "δ = 5" está justificado por la variabilidad del paper, no por nuestros datos. Eso es correcto, pero conviene no sobrevender que es una calibración. Es una decisión de diseño que puede ser demasiado estricta o demasiado laxa, y debe leerse así.
