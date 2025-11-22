# Guía de Interpretación de Gráficas para Análisis de Residuales

## 📊 Índice

1. [Gráficas de Series Temporales](#1-gráficas-de-series-temporales)
2. [Gráficas de Autocorrelación (ACF y PACF)](#2-gráficas-de-autocorrelación-acf-y-pacf)
3. [Gráficas de Normalidad](#3-gráficas-de-normalidad)
4. [Gráficas de Homocedasticidad](#4-gráficas-de-homocedasticidad)
5. [Gráficas de Intervalos de Confianza](#5-gráficas-de-intervalos-de-confianza)
6. [Gráficas de Dispersión](#6-gráficas-de-dispersión)

---

## 1. Gráficas de Series Temporales

### 1.1. Gráfica de Residuales en el Tiempo

**¿Qué muestra?**
- Los residuales (errores) plotteados a lo largo del tiempo

**Cómo leer la gráfica:**
```
     Residuales
        |
      3 |    *              *
      2 |  *   *        *     *
      1 |*       *    *         *
      0 |___*___*__*___*___*___*___ (línea de referencia)
     -1 |      *    *       *
     -2 |  *              *
        |________________________
            Tiempo →
```

**¿Qué buscar?**

✅ **BUENO (Ruido Blanco):**
```
- Puntos distribuidos aleatoriamente alrededor de cero
- Sin patrones visibles (olas, tendencias, ciclos)
- Amplitud constante a lo largo del tiempo
- Dispersión uniforme
```

❌ **MALO (Problemas):**

**Patrón 1: Tendencia**
```
     |          *  *
     |       *       *
     |    *            *
     |  *
     |*________________
         ↑ Los residuales aumentan con el tiempo
         = El modelo NO capturó una tendencia
```

**Patrón 2: Ciclos/Ondas**
```
     |  *        *        *
     |    *    *    *    *
     |______*______*_______
     |    *    *    *
     |  *        *
         ↑ Patrón cíclico
         = Falta capturar estacionalidad
```

**Patrón 3: Heterocedasticidad (varianza creciente)**
```
     |              *    *  *
     |           *    *  *
     |        *    *
     |     *   *
     |   * *
     |__*_________________
         ↑ Dispersión aumenta
         = Varianza NO constante
```

**Patrón 4: Clusters/Agrupaciones**
```
     |  ***         ***
     |  ***         ***
     |_____*_________*____
     |  ***         ***
     |  ***         ***
         ↑ Agrupaciones
         = Autocorrelación
```

---

### 1.2. Gráfica de Predicciones vs Valores Reales

**¿Qué muestra?**
- Comparación entre valores predichos por el modelo y valores reales

**Estructura:**
```
   Valor
     |
     |      Real _____ (línea continua)
     |     Predicho - - - (línea punteada)
     |    /\  /\  /\
     |   /  \/  \/  \
     |  /            \
     |_________________
         Tiempo →
```

**Interpretación:**

✅ **MODELO EXCELENTE:**
```
Las líneas se superponen casi completamente
Gap pequeño entre real y predicho
Predicciones siguen fielmente los valores reales
```

⚠️ **MODELO ACEPTABLE:**
```
Las líneas siguen la misma dirección
Hay separación pero captura tendencias
Errores sistemáticos pequeños
```

❌ **MODELO DEFICIENTE:**

**Caso 1: Desfase temporal**
```
Real:     /\  /\  /\
Pred:  /\  /\  /\
       ↑ Predicción retrasada
       = Falta información temporal
```

**Caso 2: Suavizado excesivo**
```
Real:  /\/\/\/\  (muy variable)
Pred:  ___----___ (muy suave)
       ↑ No captura variabilidad
       = Modelo muy simple
```

**Caso 3: Amplitudes incorrectas**
```
Real:  /\    /\
Pred:  /\  /\    (más pequeño)
       ↑ Subestima picos
       = Problemas de escala
```

---

## 2. Gráficas de Autocorrelación (ACF y PACF)

### 2.1. ACF (Autocorrelation Function)

**¿Qué mide?**
- Correlación entre el residual en tiempo t y el residual en tiempo t-k

**Estructura de la gráfica:**
```
Correlación
     1.0 |
     0.5 |_____ (banda azul superior)
     0.0 |__________________|__|__|__|__|
    -0.5 |‾‾‾‾‾ (banda azul inferior)
    -1.0 |
         0  1  2  3  4  5  6  7  8  9  10
                    Lag (rezago)
```

**Elementos clave:**

1. **Bandas azules (intervalo de confianza)**:
   - Típicamente ±1.96/√n
   - Representan el umbral de significancia estadística

2. **Barras verticales**:
   - Altura = correlación para ese lag
   - Arriba de cero = correlación positiva
   - Abajo de cero = correlación negativa

**Interpretación:**

✅ **IDEAL (No autocorrelación):**
```
     0.5 |_____
     0.0 ||||||||||||||
    -0.5 |‾‾‾‾‾
         0 1 2 3 4 5

↑ Todas las barras dentro de las bandas
↑ Solo barras pequeñas
= No hay autocorrelación significativa
```

❌ **PROBLEMA: Autocorrelación significativa**

**Patrón 1: Lag 1 alto**
```
     0.5 |_____
     0.0 |█_||||||||
    -0.5 |‾‾‾‾‾
         0 1 2 3 4

↑ Barra en lag 1 sale de las bandas
= Los residuales de hoy dependen de ayer
= Agregar más variables rezagadas
```

**Patrón 2: Decaimiento lento**
```
     0.5 |_____
     0.0 |█▓▓▒▒░░||
    -0.5 |‾‾‾‾‾
         0 1 2 3 4 5 6

↑ Correlaciones altas que decrecen lentamente
= Serie no estacionaria o tendencia
= Necesita diferenciación
```

**Patrón 3: Autocorrelación estacional**
```
     0.5 |_____
     0.0 |||█|||█|||█
    -0.5 |‾‾‾‾‾
         0 3 6 9 12

↑ Picos en intervalos regulares (cada 3 lags)
= Estacionalidad no capturada
= Agregar componentes estacionales
```

---

### 2.2. PACF (Partial Autocorrelation Function)

**¿Qué mide?**
- Correlación entre t y t-k eliminando el efecto de los lags intermedios

**Diferencia con ACF:**
```
ACF:  Mide correlación directa (incluye efectos indirectos)
PACF: Mide correlación "pura" (excluye efectos indirectos)
```

**Interpretación:**

✅ **IDEAL:**
```
     0.5 |_____
     0.0 |█||||||||
    -0.5 |‾‾‾‾‾
         0 1 2 3 4

↑ Solo lag 0 es significativo
↑ Todos los demás dentro de bandas
= No hay correlación parcial
```

**Uso combinado ACF + PACF:**

| ACF | PACF | Interpretación | Modelo Sugerido |
|-----|------|----------------|-----------------|
| Decae lentamente | Corte abrupto en lag p | Proceso AR | AR(p) |
| Corte abrupto en lag q | Decae lentamente | Proceso MA | MA(q) |
| Decae lentamente | Decae lentamente | Proceso mixto | ARMA(p,q) |
| Todas dentro de bandas | Todas dentro de bandas | Ruido blanco ✓ | Ninguno (modelo OK) |

---

## 3. Gráficas de Normalidad

### 3.1. Histograma con Curva Normal

**¿Qué muestra?**
- Distribución empírica de los residuales vs distribución normal teórica

**Estructura:**
```
Frecuencia
     |        ___
     |      _/   \_   (curva roja = normal teórica)
     |    _/       \_
     |   /    ███    \
     |  /   ███████   \
     | /  ███████████  \
     |___███████████████___
        -3  -2  -1  0  1  2  3
              Residuales
```

**Interpretación:**

✅ **NORMAL (BUENO):**
```
- Las barras siguen la forma de campana
- Barras se ajustan a la curva roja
- Simétrico alrededor de cero
- Sin barras extremas alejadas
```

❌ **NO NORMAL (Problemas):**

**Caso 1: Asimetría (Skewness)**
```
Positiva (cola derecha):
     |  ___
     | /   \___
     |/█████___\___
     |___________|___
           ↑ Cola larga a la derecha
           = Valores extremos positivos

Negativa (cola izquierda):
     |      ___
     |  ___/   \
     |_/_______█\
     |___|_______
       ↑ Cola larga a la izquierda
       = Valores extremos negativos
```

**Caso 2: Kurtosis (apuntamiento)**
```
Leptocúrtica (pico alto):
     |      █
     |     ███
     |    █████
     |   ███████
     |__█████████__
         ↑ Muy puntiagudo
         = Muchos valores cerca de 0
         = Más outliers que lo normal

Platicúrtica (pico bajo):
     |   _______
     |  /       \
     | /█████████\
     |/███████████\
     |_____________
         ↑ Muy plano
         = Valores muy dispersos
```

**Caso 3: Bimodal (dos picos)**
```
     |  ___    ___
     | /   \  /   \
     |/█████\/█████\
     |_____________
         ↑ Dos modas
         = Dos poblaciones diferentes
         = Revisar si hay subgrupos
```

---

### 3.2. QQ Plot (Quantile-Quantile Plot)

**¿Qué muestra?**
- Comparación de cuantiles empíricos vs teóricos de la distribución normal

**Estructura:**
```
Cuantiles Muestrales
     |
     |        *
     |      *  *
     |    *  /  *   (línea roja = diagonal de referencia)
     |  *  /      *
     |*  /          *
     |_/_____________
      Cuantiles Teóricos (Normal)
```

**Interpretación:**

✅ **NORMAL PERFECTO:**
```
     |      *
     |    * *
     |  * / *
     |* /   *
     |/_____
       ↑ Todos los puntos sobre la línea roja
       = Perfectamente normal
```

❌ **DESVIACIONES DE NORMALIDAD:**

**Patrón 1: Cola pesada derecha (Heavy right tail)**
```
     |        *  *
     |      *
     |    */
     |  */
     |*/
     |________
       ↑ Puntos se curvan hacia arriba
       = Valores extremos más grandes que lo esperado
```

**Patrón 2: Cola pesada izquierda (Heavy left tail)**
```
     |      *
     |    */
     |  */  *
     |*/      *
     |   *      *
     |___________
       ↑ Puntos se curvan hacia abajo al inicio
       = Valores extremos negativos más pequeños
```

**Patrón 3: Forma S (Ambas colas pesadas)**
```
     |        *  *
     |      *
     |    */
     |  */
     |*/
     |*
     |___________
       ↑ Curva en ambos extremos
       = Más outliers de lo esperado
```

**Patrón 4: Asimetría (Skewness)**
```
Asimetría positiva:
     |      *  *
     |    *
     |  */
     | */
     |*/
     |_______
       ↑ Curvatura consistente hacia arriba

Asimetría negativa:
     |    *
     |  */
     | */  *
     |*/     *
     |  *      *
     |___________
       ↑ Curvatura consistente hacia abajo
```

---

### 3.3. PP Plot (Probability-Probability Plot)

**¿Qué muestra?**
- Comparación de probabilidades acumuladas empíricas vs teóricas

**Diferencia con QQ Plot:**
```
QQ Plot:  Compara valores (cuantiles)
PP Plot:  Compara probabilidades acumuladas
```

**Interpretación:**
```
     1.0 |        *
         |      *
     0.5 |    */
         |  */
     0.0 |*/
         |____________
         0.0  0.5  1.0
         Probabilidad Teórica

✅ Puntos sobre la diagonal = Normal
❌ Desviaciones = No normal (similar a QQ plot)
```

---

### 3.4. Boxplot de Residuales

**Estructura:**
```
         ×  outlier
         |
     ____|____  Q3 + 1.5*IQR (bigote superior)
    |    |    |
    |    |____|  Q3 (tercer cuartil, percentil 75)
    |    |    |
    |____|____|  Mediana (Q2, percentil 50)
    |    |    |
    |____|    |  Q1 (primer cuartil, percentil 25)
         |
     ____|____  Q1 - 1.5*IQR (bigote inferior)
         |
         ×  outlier
```

**Elementos:**
- **Caja**: Rango intercuartílico (IQR = Q3 - Q1)
- **Línea central**: Mediana
- **Bigotes**: Extienden hasta 1.5×IQR
- **Puntos externos**: Outliers

**Interpretación:**

✅ **IDEAL:**
```
         |
     ____|____
    |    |    |
    |____|____|  ← Mediana cerca de 0
    |    |    |
    |____|    |
         |
         
- Caja simétrica alrededor de 0
- Sin outliers o muy pocos
- Bigotes de longitud similar
```

❌ **PROBLEMAS:**

**Asimetría:**
```
Positiva:
         ×  ×  ×
         |
     ____|____
    |    |    |
    |____|____|
    |____    |
         |
         
↑ Bigote superior más largo
↑ Outliers solo arriba
= Asimetría hacia valores positivos
```

**Muchos outliers:**
```
     ×  ×  ×  ×
         |
     ____|____
         |
     ____|____
         |
     ×  ×  ×
     
↑ Muchos puntos fuera
= Distribución con colas pesadas
= Posible heterocedasticidad
```

---

## 4. Gráficas de Homocedasticidad

### 4.1. Residuales al Cuadrado en el Tiempo

**¿Qué muestra?**
- Varianza de los residuales a lo largo del tiempo

**Estructura:**
```
Residual²
     |
     |  *        *     * *
     |    *    *         *
     |  *   * *    *   *    (puntos)
     |_____________________
     |_____________________ (línea roja = varianza media)
              Tiempo →
```

**Interpretación:**

✅ **HOMOCEDASTICIDAD (Varianza constante):**
```
     |  * * *  * *  * * *
     |* *  *  *  * *  * *
     |_____________________ (varianza media)
     |  * * * *  * * * *
     |* *  * *  *  * * *
              
↑ Puntos distribuidos uniformemente alrededor de la media
↑ No hay patrón de aumento o disminución
= Varianza constante ✓
```

❌ **HETEROCEDASTICIDAD (Problemas):**

**Patrón 1: Varianza creciente**
```
     |              * * *
     |           * *   *
     |        * *
     |     * *
     |  * *
     |_____________________
              ↑
        Varianza aumenta con el tiempo
        = Heterocedasticidad temporal
```

**Patrón 2: Varianza decreciente**
```
     |  * *
     |    * *
     |      * *
     |        * *
     |           * *  *
     |_____________________
              ↑
        Varianza disminuye
        = Heterocedasticidad temporal
```

**Patrón 3: Clusters de alta varianza**
```
     |  ***     ***
     |  ***  *  ***
     |____*_*_*______
     |  ***  *  ***
     |  ***     ***
              ↑
        Periodos alternados de alta/baja varianza
        = Volatilidad agrupada (ARCH/GARCH)
```

---

### 4.2. Residuales vs Valores Predichos (Scatter)

**¿Qué muestra?**
- Relación entre el tamaño del error y el valor predicho

**Estructura:**
```
Residuales
     |
   3 |    *        *
   2 |  *   *    *   *
   1 |*       * *      *
   0 |___*_____*___*____*___ (línea y=0)
  -1 |  *   *    *   *
  -2 |    *        *
     |_____________________
        Valores Predichos →
```

**Interpretación:**

✅ **HOMOCEDASTICIDAD:**
```
     |  * * * * * * * *
     |* * * * * * * * *
     |__________________ (y=0)
     |* * * * * * * * *
     |  * * * * * * * *
        
↑ Dispersión constante en todo el rango
↑ Forma de banda horizontal
↑ Sin patrones, sin "embudos"
= Varianza constante ✓
```

❌ **HETEROCEDASTICIDAD:**

**Patrón 1: Embudo creciente (Fan-out)**
```
     |              * * *
     |           *  *  *
     |        *    *
     |     *     *
     |  *      *
     |___*_____________
        ↑
    Dispersión aumenta con valores predichos
    = Varianza proporcional a Y
    = Transformación log puede ayudar
```

**Patrón 2: Embudo decreciente (Fan-in)**
```
     |  * *
     |    * *
     |      * *
     |        * *  *
     |           *  * *
     |_________________
        ↑
    Dispersión disminuye con valores predichos
```

**Patrón 3: Forma curva (No linealidad)**
```
     |      *     *
     |    *   * *   *
     |  *      |      *
     |*        |        *
     |_________|_________
        ↑
    Patrón curvo (U o ∩)
    = Relación no lineal no capturada
    = Agregar términos cuadráticos
```

---

### 4.3. Varianza Móvil (Rolling Variance)

**¿Qué muestra?**
- Varianza calculada en ventanas deslizantes de tiempo

**Estructura:**
```
Varianza
     |
     |    /\    /\
     |   /  \  /  \
     |__/____\/____ (varianza global)
     |              
              Tiempo →
```

**Interpretación:**

✅ **HOMOCEDASTICIDAD:**
```
Varianza
     |  ~~~~~~~~~~~  (línea oscila ligeramente)
     |______________ (varianza global)
              
↑ Línea relativamente plana
↑ Oscilaciones pequeñas alrededor de la media
= Varianza estable ✓
```

❌ **HETEROCEDASTICIDAD:**

**Tendencia creciente:**
```
     |          /
     |        /
     |      /
     |    /
     |  /
     |_____________
        ↑ Varianza aumenta
```

**Picos y valles:**
```
     |  /\  /\  /\
     | /  \/  \/  \
     |____________
        ↑ Volatilidad variable
        = Clusters de volatilidad
```

---

## 5. Gráficas de Intervalos de Confianza

### 5.1. Residuales con Bandas de Confianza

**¿Qué muestra?**
- Residuales con límites superior e inferior del IC

**Estructura:**
```
Residuales
     |
   3 |_ _ _ _ _ _ _ _ _  (límite superior 99%)
     |
   2 |- - - - - - - - -  (límite superior 95%)
     |    *   *      *
   0 |___*_*___*__*______ (media ≈ 0)
     |  *       *    *
  -2 |- - - - - - - - -  (límite inferior 95%)
     |
  -3 |_ _ _ _ _ _ _ _ _  (límite inferior 99%)
     |_____________________
              Tiempo →
```

**Elementos:**
- Línea verde sólida = Media (debe estar en 0)
- Líneas rojas punteadas = IC 95%
- Líneas naranjas punteadas = IC 99%
- Área sombreada = Zona esperada

**Interpretación:**

✅ **MODELO CORRECTO:**
```
95% de puntos dentro del IC 95%
99% de puntos dentro del IC 99%
Distribución uniforme a través del tiempo
Sin clusters fuera de bandas
```

❌ **PROBLEMAS:**

**Muchos outliers:**
```
     |_ _ _ _ _ _×_ _×_ _  
     |- - - - - - - - - -
     |    *   *      *
     |___*_*___*__*______
     |  *       *    *
     |- - - - - - - - - -
     |×_ _ _ _ _ _ _ _×_ 
        ↑
    Muchos puntos fuera del IC 99%
    = Outliers frecuentes
    = Problemas en el modelo
```

**Clusters fuera de bandas:**
```
     |_ _ _×××××_ _ _ _ _  
     |- - - - - - - - - -
     |    *   *      *
     |___*_*___*__*______
     |  *       *    *
     |- - - - - - - - - -
     |_ _ _ _ _ _×××××_ 
        ↑
    Agrupación de outliers
    = Evento no capturado
    = Cambio estructural
```

**Violación sistemática:**
```
     |_ _ _ _ _ _ _ _ _ _  
     |- - -*-*-*-*-*-*- -
     |    *   *      *
     |___*_*___*__*______
     |  
     |- - - - - - - - - -
     |_ _ _ _ _ _ _ _ _ 
        ↑
    Muchos puntos consistentemente cerca del límite
    = Intervalos mal calibrados
    = Revisar distribución
```

---

### 5.2. Comparación IC 95% vs IC 99%

**¿Qué evaluar?**

| Métrica | IC 95% | IC 99% |
|---------|--------|--------|
| % esperado dentro | 95% | 99% |
| Outliers esperados | 5% | 1% |

**Interpretación combinada:**

✅ **Excelente:**
```
IC 95%: 94-96% de puntos dentro
IC 99%: 98-100% de puntos dentro
```

⚠️ **Aceptable:**
```
IC 95%: 90-94% de puntos dentro
IC 99%: 95-98% de puntos dentro
```

❌ **Problemático:**
```
IC 95%: <90% de puntos dentro
IC 99%: <95% de puntos dentro
```

---

## 6. Gráficas de Dispersión

### 6.1. Valores Reales vs Predichos (Scatter)

**¿Qué muestra?**
- Qué tan bien el modelo predice cada observación

**Estructura:**
```
Valores Reales
     |
     |        *
     |      * /  *   (línea diagonal = predicción perfecta)
     |    * /      *
     |  * /          *
     |* /              *
     |/_________________
      Valores Predichos
```

**Interpretación:**

✅ **MODELO PERFECTO:**
```
     |      *
     |    * *
     |  * / *
     |* /   *
     |/_____
       ↑
    Todos los puntos sobre la diagonal
    = Predicción = Real
```

✅ **MODELO EXCELENTE:**
```
     |      *
     |    * * *
     |  * / * *
     |* / * *
     |/_*___
       ↑
    Puntos muy cerca de la diagonal
    = Alta precisión
```

⚠️ **MODELO ACEPTABLE:**
```
     |    *   *
     |  *  / *  *
     | * */ * *
     |* /* *  *
     |/*__*____
       ↑
    Puntos dispersos pero centrados en diagonal
    = Precisión moderada
```

❌ **PROBLEMAS:**

**Subestimación sistemática:**
```
     |      *  *
     |    *  *
     |  *  /
     | * /
     |* /
     |/_________
       ↑
    Puntos arriba de la diagonal
    = Modelo predice valores menores que los reales
```

**Sobrestimación sistemática:**
```
     |        /
     |      /  *
     |    /  *  *
     |  / *  *
     |/*_*______
       ↑
    Puntos debajo de la diagonal
    = Modelo predice valores mayores que los reales
```

**Forma de arco:**
```
     |    *   *
     |  *       *
     | *    /    *
     |*   /       *
     |  /           *
     |/_______________
       ↑
    Curvatura en los puntos
    = Relación no lineal
    = Agregar términos cuadráticos
```

---

## 7. Checklist Rápido de Interpretación

### Al analizar residuales, verificar:

**📋 Gráficas Temporales:**
- [ ] ¿Oscilan aleatoriamente alrededor de cero?
- [ ] ¿Tienen amplitud constante?
- [ ] ¿No hay tendencias, ciclos o patrones?

**📋 ACF/PACF:**
- [ ] ¿La mayoría de barras dentro de las bandas azules?
- [ ] ¿No hay decaimiento lento ni picos repetidos?

**📋 Normalidad:**
- [ ] ¿Histograma con forma de campana?
- [ ] ¿QQ plot con puntos sobre la diagonal?
- [ ] ¿Boxplot simétrico con pocos outliers?

**📋 Homocedasticidad:**
- [ ] ¿Residuales² uniformes en el tiempo?
- [ ] ¿Scatter sin forma de embudo?
- [ ] ¿Varianza móvil relativamente plana?

**📋 Intervalos de Confianza:**
- [ ] ¿~95% de puntos dentro del IC 95%?
- [ ] ¿~99% de puntos dentro del IC 99%?
- [ ] ¿Outliers distribuidos uniformemente?

**📋 Predicciones:**
- [ ] ¿Valores predichos cerca de la diagonal?
- [ ] ¿Sin sesgos sistemáticos?
- [ ] ¿Errores distribuidos simétricamente?

---

## 8. Patrones Comunes y Sus Causas

| Patrón Visual | Causa Probable | Solución |
|---------------|----------------|----------|
| Tendencia en residuales | Tendencia no capturada | Diferenciar serie o agregar tendencia |
| Ciclos/ondas en residuales | Estacionalidad faltante | Agregar componentes estacionales |
| ACF decae lentamente | Serie no estacionaria | Diferenciar la serie |
| Picos en ACF en lags específicos | Autocorrelación | Agregar lags como variables |
| QQ plot con colas pesadas | Outliers o colas gruesas | Usar modelos robustos o transformar Y |
| Embudo en scatter | Heterocedasticidad | Transformación log o usar GARCH |
| Curva en scatter real vs pred | No linealidad | Términos cuadráticos o más capas |
| Clusters de varianza alta | Volatilidad agrupada | Modelo GARCH o ARCH |
| Muchos outliers en bandas | Eventos extremos | Revisar datos o usar modelos robustos |

---

## 9. Ejemplos de Combinaciones

### Ejemplo 1: Modelo EXCELENTE
```
✓ Residuales aleatorios sin patrón
✓ ACF/PACF dentro de bandas
✓ QQ plot lineal
✓ Scatter sin embudo
✓ 95% dentro IC 95%

→ Modelo bien especificado
→ Residuales = ruido blanco
→ Listo para producción
```

### Ejemplo 2: Modelo con AUTOCORRELACIÓN
```
✓ Media cercana a 0
✗ ACF con picos en lags 1-3
✓ Normalidad aceptable
✓ Varianza constante

→ Falta información temporal
→ Solución: Agregar más lags
→ O usar LSTM/ARIMA
```

### Ejemplo 3: Modelo con HETEROCEDASTICIDAD
```
✓ Sin autocorrelación
✓ Media cero
✗ Embudo en scatter
✗ Varianza móvil creciente
✗ Test ARCH rechaza H0

→ Varianza no constante
→ Solución: Transformar Y (log)
→ O usar modelo GARCH
```

### Ejemplo 4: Modelo SUBESPECIFICADO
```
✗ Tendencia en residuales
✗ ACF decae lentamente
✗ Patrón curvo en scatter
✗ Muchos outliers

→ Modelo muy simple
→ Solución: Revisar arquitectura
→ Agregar complejidad
→ Incluir más variables
```

---

## 10. Resumen Visual Rápido

### Gráfica Perfecta vs Problemática

**PERFECTO** ✓
```
Temporales:   * * * * * (aleatorio)
ACF/PACF:     ||||||| (dentro de bandas)
QQ Plot:      * * * (sobre diagonal)
              * * /
Scatter:      * / * (sin embudo)
IC:           95% dentro
```

**PROBLEMÁTICO** ✗
```
Temporales:   /\  /\ (tendencia/ciclos)
ACF/PACF:     █▓▒░ (decaimiento lento)
QQ Plot:      *     * (curvatura)
            *   /   
Scatter:         * * * (embudo)
             * *
           **
IC:           <80% dentro, clusters
```

---

**Última actualización:** Noviembre 2025
**Complemento de:** GUIA_ANALISIS_RESIDUALES.md