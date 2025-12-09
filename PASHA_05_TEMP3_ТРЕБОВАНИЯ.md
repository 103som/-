# 📋 TEMP3 — МАТРИЦА ТРЕБОВАНИЙ (Requirements Matrix)
## Проект: FlowGuard

> **Файл для заполнения:** `Temp3 – RequirementMatrix.xls`
> **Дополняет Устав и связывается с WBS**

---

## 📋 СТРУКТУРА ТАБЛИЦЫ

Рекомендуемые колонки (добавь недостающие в шаблон):

| Колонка | Описание |
|---------|----------|
| Req ID | Идентификатор требования (R-001) |
| Type | Тип: Functional / Non-functional |
| Description | Описание требования |
| Source | Источник (стейкхолдер, ТЗ, регламент) |
| Rationale | Обоснование (зачем нужно) |
| Priority | Приоритет (Must/Should/Could) |
| Acceptance Criteria | Критерии приёмки |
| Verification Method | Метод проверки (Test/Demo/Inspection/Analysis) |
| WBS Link | Связь с узлом WBS |
| Risk Link | Связь с риском (RK-xxx) |
| Status | Статус (Planned/In Progress/Done) |

---

## 📝 ФУНКЦИОНАЛЬНЫЕ ТРЕБОВАНИЯ ДЛЯ FlowGuard

### R-001: Приём PCAP-файлов
| Поле | Значение |
|------|----------|
| Type | Functional |
| Description | Система должна принимать входные дампы сетевого трафика в формате PCAP |
| Source | ТЗ §4.1.1.1 |
| Rationale | PCAP — стандартный формат хранения сетевого трафика |
| Priority | Must |
| Acceptance Criteria | Система принимает PCAP-файл по пути, указанному в CLI |
| Verification Method | Test, Demo |
| WBS Link | 1.2.1 Парсер PCAP |
| Risk Link | RK-002 (некорректные PCAP) |
| Status | Planned |

---

### R-002: Извлечение conn.log
| Поле | Значение |
|------|----------|
| Type | Functional |
| Description | Система должна извлекать метаданные conn.log: 5-tuple, duration, pkts, bytes |
| Source | ТЗ §4.1.1.3.1 |
| Rationale | conn.log — основа для формирования flow-признаков |
| Priority | Must |
| Acceptance Criteria | Для тестового PCAP извлечены все потоки с корректными полями |
| Verification Method | Test |
| WBS Link | 1.2.2 Извлечение conn.log |
| Risk Link | — |
| Status | Planned |

---

### R-003: Извлечение JA4-фингерпринтов
| Поле | Значение |
|------|----------|
| Type | Functional |
| Description | Система должна извлекать JA4-фингерпринты из TLS ClientHello |
| Source | ТЗ §4.1.1.2 |
| Rationale | JA4 позволяет идентифицировать клиента без расшифровки трафика |
| Priority | Must |
| Acceptance Criteria | Для TLS-соединений извлечён JA4-фингерпринт |
| Verification Method | Test, Analysis |
| WBS Link | 1.2.4 Извлечение ja4.log |
| Risk Link | RK-003 (неподдерживаемые версии TLS) |
| Status | Planned |

---

### R-004: Формирование flow-признаков
| Поле | Значение |
|------|----------|
| Type | Functional |
| Description | Система должна вычислять агрегированные flow-признаки для каждого потока |
| Source | ТЗ §4.1.2.2 |
| Rationale | Flow-признаки — основа для ML-детектирования |
| Priority | Must |
| Acceptance Criteria | Каждый поток содержит набор признаков (bytes, pkts, duration, etc.) |
| Verification Method | Test |
| WBS Link | 1.3.2 Вычисление flow-признаков |
| Risk Link | — |
| Status | Planned |

---

### R-005: Объединение flow + JA4
| Поле | Значение |
|------|----------|
| Type | Functional |
| Description | Система должна связывать flow-признаки с JA4-метаданными в единую структуру |
| Source | ТЗ §4.1.2.3 |
| Rationale | Гибридный анализ повышает качество детектирования |
| Priority | Must |
| Acceptance Criteria | Структура flow+ja4 содержит и поведенческие, и идентификационные признаки |
| Verification Method | Test, Inspection |
| WBS Link | 1.3.3 Объединение flow + JA4 |
| Risk Link | — |
| Status | Planned |

---

### R-006: Детектирование аномалий (Isolation Forest)
| Поле | Значение |
|------|----------|
| Type | Functional |
| Description | Система должна выявлять аномалии с помощью алгоритма Isolation Forest |
| Source | ТЗ §4.1.3.2.1 |
| Rationale | Isolation Forest — эффективный метод обнаружения аномалий без разметки |
| Priority | Must |
| Acceptance Criteria | Модель обучается и выдаёт прогноз для каждого потока |
| Verification Method | Test, Analysis |
| WBS Link | 1.4.2 Реализация Isolation Forest |
| Risk Link | RK-004 (переобучение) |
| Status | Planned |

---

### R-007: Расчёт anomaly_score
| Поле | Значение |
|------|----------|
| Type | Functional |
| Description | Каждому потоку должен присваиваться anomaly_score ∈ [0;1] |
| Source | ТЗ §4.1.3.3.1 |
| Rationale | Числовая оценка позволяет ранжировать угрозы |
| Priority | Must |
| Acceptance Criteria | Все потоки имеют anomaly_score в диапазоне [0;1] |
| Verification Method | Test |
| WBS Link | 1.4.4 Расчёт anomaly_score |
| Risk Link | — |
| Status | Planned |

---

### R-008: Классификация по уровням риска
| Поле | Значение |
|------|----------|
| Type | Functional |
| Description | Должно быть реализовано преобразование score в уровни: LOW, MEDIUM, HIGH, CRITICAL |
| Source | ТЗ §4.1.3.4 |
| Rationale | Упрощает интерпретацию для оператора |
| Priority | Should |
| Acceptance Criteria | Каждый поток имеет уровень риска |
| Verification Method | Test, Demo |
| WBS Link | 1.4.5 Пороговая классификация |
| Risk Link | — |
| Status | Planned |

---

### R-009: Экспорт в CSV/JSON
| Поле | Значение |
|------|----------|
| Type | Functional |
| Description | Результаты должны сохраняться в машиночитаемом формате CSV и/или JSON |
| Source | ТЗ §4.1.4.1.2 |
| Rationale | Позволяет интеграцию с другими системами |
| Priority | Must |
| Acceptance Criteria | Генерируется файл CSV/JSON с указанными полями |
| Verification Method | Test, Inspection |
| WBS Link | 1.5.1 CSV-отчёт, 1.5.2 JSON-отчёт |
| Risk Link | — |
| Status | Planned |

---

### R-010: Интерпретация результатов
| Поле | Значение |
|------|----------|
| Type | Functional |
| Description | Для каждого потока должны указываться 1–2 ключевых признака, повлиявших на решение |
| Source | ТЗ §4.1.4.2 |
| Rationale | Повышает доверие оператора к системе |
| Priority | Should |
| Acceptance Criteria | В отчёте есть поле с объяснением |
| Verification Method | Demo, Inspection |
| WBS Link | 1.5.3 Интерпретация |
| Risk Link | — |
| Status | Planned |

---

## 📝 НЕФУНКЦИОНАЛЬНЫЕ ТРЕБОВАНИЯ

### R-011: Язык программирования
| Поле | Значение |
|------|----------|
| Type | Non-functional |
| Description | Python — ML и признаки; C++ — ускоренная обработка трафика |
| Source | ТЗ §4.7.1 |
| Rationale | Баланс скорости и удобства |
| Priority | Must |
| Acceptance Criteria | Код написан на указанных языках |
| Verification Method | Inspection |
| WBS Link | Все ветки разработки |
| Risk Link | — |
| Status | Planned |

---

### R-012: Платформа
| Поле | Значение |
|------|----------|
| Type | Non-functional |
| Description | Система должна работать в Linux (Ubuntu) или совместимой UNIX-среде |
| Source | ТЗ §4.7.2 |
| Rationale | Целевое окружение для анализа трафика |
| Priority | Must |
| Acceptance Criteria | Успешный запуск на Ubuntu 22.04 |
| Verification Method | Test |
| WBS Link | 1.6 Развёртывание |
| Risk Link | — |
| Status | Planned |

---

### R-013: Docker-контейнеризация
| Поле | Значение |
|------|----------|
| Type | Non-functional |
| Description | Система должна распространяться как Docker-образ |
| Source | ТЗ §4.8 |
| Rationale | Упрощает развёртывание и воспроизводимость |
| Priority | Should |
| Acceptance Criteria | docker build и docker run успешны |
| Verification Method | Test |
| WBS Link | 1.6.2 Dockerfile |
| Risk Link | RK-006 (проблемы сборки) |
| Status | Planned |

---

### R-014: CLI-интерфейс
| Поле | Значение |
|------|----------|
| Type | Non-functional |
| Description | Взаимодействие через командную строку, без GUI |
| Source | ТЗ §4.3 |
| Rationale | Упрощает автоматизацию и интеграцию |
| Priority | Must |
| Acceptance Criteria | Система запускается из терминала с параметрами |
| Verification Method | Test, Demo |
| WBS Link | 1.6.1 CLI-интерфейс |
| Risk Link | — |
| Status | Planned |

---

### R-015: Обработка ошибок
| Поле | Значение |
|------|----------|
| Type | Non-functional |
| Description | Система должна корректно обрабатывать ошибки входных данных |
| Source | ТЗ §4.4 |
| Rationale | Надёжность работы |
| Priority | Should |
| Acceptance Criteria | Некорректный PCAP не приводит к аварийному завершению |
| Verification Method | Test |
| WBS Link | 1.2.5 Валидация |
| Risk Link | RK-002 |
| Status | Planned |

---

## ✅ ЧЕКЛИСТ ЗАПОЛНЕНИЯ

- [ ] Заполнено минимум 10-15 требований
- [ ] Есть и функциональные, и нефункциональные
- [ ] У каждого требования есть критерии приёмки
- [ ] Указан метод верификации
- [ ] Есть связь с WBS (номер узла)
- [ ] Критичные требования связаны с рисками
- [ ] Приоритеты расставлены

---

**Следующий шаг:** Открой `PASHA_06_TEMP4_РИСКИ.md` для заполнения реестра рисков.
