# Результаты эксперимента

**Датасет:** Telco Customer Churn (IBM), 7043 записи, 21 признак
**Random seed:** 42
**Train/test split:** 80/20, стратифицированный

## Распределение целевой переменной

- Churn = No: 5174 (73.5%)
- Churn = Yes: 1869 (26.5%)

Дисбаланс классов ~1:2.8. Учтён через `class_weight="balanced"` (LogReg)
и `scale_pos_weight` (XGBoost).

## Baseline: LogisticRegression

| Метрика | Значение |
|---|---|
| ROC-AUC (test) | 0.8417 |
| Accuracy | 0.739 |
| Precision (churn) | 0.505 |
| Recall (churn) | 0.783 |
| F1 (churn) | 0.614 |

Параметры: `max_iter=1000, class_weight="balanced", random_state=42`.

## Топ-10 коэффициентов LogReg

| Feature | Coef |
|---|---|
| Contract_Two year | −1.403 |
| InternetService_Fiber optic | +1.227 |
| tenure | −1.161 |
| Contract_One year | −0.714 |
| MonthlyCharges | −0.555 |
| TotalCharges | +0.492 |
| StreamingMovies_Yes | +0.422 |
| PaymentMethod_Electronic check | +0.404 |
| StreamingTV_Yes | +0.398 |
| PhoneService_Yes | −0.345 |

Знак: «+» увеличивает риск оттока, «−» снижает.

## XGBoost

ROC-AUC: 0.8353

Параметры: n_estimators=400, max_depth=5, learning_rate=0.05,
subsample=0.8, colsample_bytree=0.8, scale_pos_weight=2.77.

## SHAP - топ-15 драйверов

| Feature | Mean abs SHAP |
|---|---|
| Contract_Two year | 0.681 |
| tenure | 0.593 |
| MonthlyCharges | 0.410 |
| TotalCharges | 0.344 |
| InternetService_Fiber optic | 0.325 |
| Contract_One year | 0.316 |
| PaymentMethod_Electronic check | 0.189 |
| OnlineSecurity_Yes | 0.144 |
| PaperlessBilling_Yes | 0.140 |
| TechSupport_Yes | 0.140 |
| MultipleLines_Yes | 0.123 |
| StreamingMovies_Yes | 0.098 |
| StreamingTV_Yes | 0.092 |
| InternetService_No | 0.084 |
| Dependents_Yes | 0.075 |

## Бизнес-метрики

| Метрика | Значение |
|---|---|
| Churn rate в целом (test) | 26.5% |
| Churn rate в топ-20% по риску | 65.8% |
| Lift@20% | 2.48x |
| Клиентов в кампании | 281 |
| Из них ушли бы | 185 |
| Удержали (uplift 30%) | 55.5 |
| Спасённая выручка | $83,250 |
| Стоимость кампании | $14,050 |
| Чистая прибыль | $69,200 |
| ROI | 4.93x |

## Допущения бизнес-модели

- LTV (потеря с ушедшего клиента): $1500
- Стоимость оффера удержания: $50
- Uplift rate (доля удержанных среди тех, кто ушёл бы): 30%
- Кампания нацелена на топ-20% по риску


## Extrapolation на всю базу

Кампания на топ-20% от 7043 клиентов = 1409 клиентов.
Ожидаемая чистая прибыль ~$345K / год.
(Пропорционально: $69,200 × 7043 / 1409)

## Что не сделано

- Uplift-модель не обучена (нет данных A/B-эксперимента).
