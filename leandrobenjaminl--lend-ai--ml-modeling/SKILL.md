---
name: ml-modeling
description: > Use when this capability is needed.
metadata:
  author: LeandroBenjaminL
---

# Skill: ml-modeling

Machine learning con generalización, no memorización.

## Trigger

- Tenés datos limpios y querés entrenar un modelo
- Necesitás comparar múltiples algoritmos y elegir el mejor
- Te pidieron explicar por qué el modelo predice lo que predice
- Hay que optimizar hiperparámetros

## Workflow LEND

```
1. ANALIZAR
   ├── Baseline First: modelo simple (regresión lineal / dummy classifier)
   │   → Si XGBoost no le gana al baseline, no vale la complejidad
   ├── Validación: Stratified K-Fold Cross Validation
   │   → Nada de un solo train_test_split si los datos son chicos o desbalanceados
   ├── Desbalanceo: ¿hay 95% clase A y 5% clase B?
   │   → Accuracy miente. Usá F1-Score, Precision-Recall, AUC-ROC
   └── Feature leakage: ¿alguna feature usa información del futuro?

2. OFRECER (Menú del Senior)
   ├── A) Modelos interpretables — regresión logística, árboles chicos, explicables
   ├── B) Ensembles — Random Forest, XGBoost, LightGBM, máxima performance
   └── C) Auto-tuning — Optuna/Hyperopt para exprimir el rendimiento

3. ELEGIR → el usuario confirma

4. HACER
   ├── Split: train_test_split con stratify si es clasificación
   ├── Escalar: StandardScaler para modelos basados en distancias
   │   Tree-based (RF, LGBM) NO necesitan escalado
   ├── Entrenar baseline y modelo elegido
   ├── Evaluar: métrica correcta según el problema
   │   Clasificación balanceada → Accuracy
   │   Clasificación desbalanceada → F1, Precision-Recall, AUC-ROC
   │   Regresión → RMSE, MAE, R2
   ├── Feature importance: SHAP values (direccionalidad + magnitud)
   │   feature_importances_ dice qué, SHAP dice cómo
   └── Guardar modelo con joblib + documentar métricas

5. VERIFICAR
   ├── El modelo generaliza (CV score ≈ test score)
   ├── No hay overfitting (train >> test)
   └── Las predicciones tienen sentido de negocio
```

## Patrones

- **Baseline siempre**: un modelo simple primero te da un piso. Si lo complejo no mejora, no vale la pena.
- **Stratified K-Fold**: preserva proporciones de clases en cada fold. Esencial en datos desbalanceados.
- **SHAP > feature_importances_**: SHAP te dice dirección + magnitud por predicción individual.
- **Escalar después del split**: nunca `fit_transform` en el set completo — filtra información del test al train.
- **Optuna > GridSearch**: búsqueda inteligente, no fuerza bruta. 50 trials bien orientados > 10k combinaciones al pedo.

## Anti-patrones

- ❌ No hacer train/test split — evaluar en los mismos datos que entrenaste es hacer trampa
- ❌ Feature selection antes del split — perdés información de la distribución del test
- ❌ Usar Accuracy con clases desbalanceadas — 95% accuracy no es bueno si una clase tiene 5%
- ❌ GridSearch con 10^4 combinaciones — 50 búsquedas inteligentes con Optuna rinden más
- ❌ Ignorar overfitting — si train >> test, el modelo memorizó, no aprendió
- ❌ No explicar el modelo — "porque el modelo lo dice" no es una respuesta válida

---
> Source: [LeandroBenjaminL/lend-ai](https://github.com/LeandroBenjaminL/lend-ai) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
