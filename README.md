# Отчет по лабораторной работе №1
## Выполнил:
 Шорников Сергей P3311
## Вариант:
 CreateProcessAsUser
### Нагрузчики: 
- bin_search
- ema-search-str
---

#### Задание Часть 1. Реализация командной оболочки

**Цель:** Реализовать оболочку командной строки (shell) на ОС Windows, позволяющую запускать программы с переданными аргументами и подсчитывать реальное время их работы.

**Описание реализации:**
- Я реализовал оболочку командной строки, используя системный вызов `CreateProcessAsUser` для запуска программ.
- После завершения каждого процесса, оболочка фиксирует время завершения программы и рассчитывает общее время выполнения как разницу между временем запуска и завершения процесса.
- Операционная система: Windows.

**Пример запуска программы в оболочке:**
```shell
my_shell.exe
Enter command: binary_search.exe 100000 1 250000000
```

**Результат:**
```
Program execution time: X.XXXX sec
```

---

#### Задание Часть 2. Мониторинг и профилирование

**Цель:** Разработать программы-нагрузчики и проанализировать их производительность с помощью инструментов профилирования на Windows.

**Описание программ-нагрузчиков:**
1. **Бинарный поиск:** Программа выполняет бинарный поиск в отсортированном массиве заданного размера и повторяет поиск указанное количество раз.
   - **Параметры запуска:** `binary_search 100000 1 250000000`
   
2. **Поиск подстроки во внешней памяти:** Программа ищет заданную подстроку в большом файле (размером в несколько сотен мегабайт) и выполняет поиск несколько раз для симуляции нагрузки на дисковую подсистему.
   - **Параметры запуска:** `substring_search D:\large_text.txt R7 10`



### Предполагаемое время выполнения нагрузочных программ

---

### 1. Предсказание времени выполнения для программы `binary_search`

#### Параметры

- **Объем массива**: 100,000 элементов
- **Количество повторений**: 250,000,000
- **Частота процессора**: 2.1 GHz
- **Дополнительные накладные расходы**: переключение контекста и доступ к памяти

#### Оценка количества операций

Бинарный поиск в среднем требует \( \log_2(N) \) операций для поиска одного элемента в массиве из \( N \) элементов. Для массива из 100,000 элементов:
```text
log2(100,000) ≈ 17 операций на поиск
```

Общее количество операций для 250,000,000 повторений:
```text
Total Operations = 250,000,000 × 17 = 4,250,000,000 операций
```

#### Оценка времени выполнения (без учета задержек)

Предполагаем, что для каждой операции бинарного поиска процессор тратит в среднем 3 такта. Тогда общее количество тактов составит:
```text
Total Cycles = 4,250,000,000 × 3 = 12,750,000,000 тактов
```

На процессоре с частотой 2.1 GHz время выполнения (идеализированное) можно оценить как:
```text
Ideal Time = Total Cycles / Processor Frequency = 12,750,000,000 / 2,100,000,000 ≈ 6.07 секунд
```

#### Учёт накладных расходов

Добавим предполагаемые накладные расходы:
- **Задержка на переключение контекста**: 5%
- **Задержка на доступ к памяти**: 10%

С учетом этих факторов:
```text
Adjusted Time = 6.07 × (1 + 0.05 + 0.10) = 6.07 × 1.15 ≈ 6.98 секунд
```

### Ожидаемое время выполнения для `binary_search`: **около 7 секунд** (идеализированно)

---



### Предсказание времени выполнения программы

#### Параметры

- **Объем файла**: 500 МБ
- **Количество повторений для `substring_search`**: 10
- **Скорость чтения диска**: 600 МБ/с
- **Размер буфера чтения**: 1 МБ
- **Количество повторений для `binary_search`**: 25 000 000
- **Размер массива для `binary_search`**: 1 000 000 элементов

#### Оценка времени выполнения для `substring_search`

1. **Объем данных для чтения**:

   Общий объем данных = 500 МБ × 10 повторений = 5 000 МБ = 5 ГБ

2. **Время чтения с диска**:

   Время чтения = Общий объем данных ÷ Скорость чтения диска  
   Время чтения = 5 000 МБ ÷ 600 МБ/с ≈ 8,33 секунды

3. **Время обработки алгоритма КМП**:

   - Общее количество символов = 5 000 МБ = 5 × 10⁹ байт
   - Оценочное количество операций:  
     Операций = Количество символов × Среднее число операций на символ  
     Операций = 5 × 10⁹ × 5 = 25 × 10⁹ операций
   - Время обработки на CPU:  
     Время обработки = Операций ÷ Производительность CPU  
     Время обработки = 25 × 10⁹ операций ÷ (2 × 10⁹ операций/сек) = 12,5 секунды

4. **Общее время для `substring_search` без накладных расходов**:

   Общее время = Время чтения + Время обработки  
   Общее время = 8,33 сек + 12,5 сек = 20,83 секунды

5. **Учет системных накладных расходов (25%)**:

   Скорректированное время = Общее время × (1 + 0,25)  
   Скорректированное время = 20,83 сек × 1,25 = 26,04 секунды

#### Оценка времени выполнения для `binary_search`

1. **Общее количество операций**:

   Операций = Количество повторений × Среднее число операций на поиск  
   Операций = 25 000 000 × 20 = 500 000 000 операций

2. **Время обработки на CPU**:

   Время обработки = Операций ÷ Производительность CPU  
   Время обработки = 500 000 000 операций ÷ (2 × 10⁹ операций/сек) = 0,25 секунды

3. **Скорректированное время (15% накладные расходы)**:

   Скорректированное время = Время обработки × (1 + 0,15)  
   Скорректированное время = 0,25 сек × 1,15 = 0,2875 секунды

#### Итоговое предсказание времени выполнения

- **Время выполнения `substring_search`**: приблизительно 26 секунд
- **Время выполнения `binary_search`**: приблизительно 0,3 секунды
- **Общее ожидаемое время выполнения программы**: приблизительно 26 секунд

#### Заключение

Основное время выполнения программы определяется задачей поиска подстроки. С учетом скорости чтения диска 600 МБ/с и 10 повторений, ожидаемое общее время выполнения составляет около **26 секунд**.

---

### Заключение по предположениям

Ожидаемое время выполнения двух нагрузочных программ с учетом расчетов и накладных расходов:

- **binary_search**: ~7 секунд
- **substring_search**: ~26 секунды

Эти оценки являются идеализированными и могут увеличиться в реальной среде из-за накладных расходов на работу операционной системы, конкуренции за ресурсы с другими процессами и потенциальных задержек в подсистеме хранения данных.

---






### Эксперименты и результаты профилирования

#### 1. Запуск программ-нагрузчиков и фиксация метрик работы

**Описание:** Запуск каждого нагрузчика отдельно, сбор метрик по CPU, памяти и вводу-выводу.

1. **Бинарный поиск:** 
   - **Параметры запуска:** `binary_search 100000 1 250000000`
   - **Метрики:** CPU, SYS, USER, WAIT, переключения контекста
   ### Основные метрики
| Метрика      | Значение   |
|--------------|------------|
| Kernel Time  | 0:00:00.000 |
| User Time    | 0:00:30.125 |
| Total Time   | 0:00:30.125 |
| CPU Usage    | 8.45%       |

### I/O статистика
| Метрика          | Значение |
|------------------|----------|
| Reads            | 0        |
| Read Delta       | 0        |
| Read Bytes Delta | 0        |
| Writes           | 0        |
| Write Delta      | 0        |
| Other            | 10       |

### Системные характеристики
| Метрика                | Значение |
|------------------------|----------|
| Context Switch Delta   | 5,234   |
| Interrupt Delta        | 5,536   |
| DPC Delta              | 361    |
| Working Set            | 3,484 KB |

### Распределение времени
| Тип    | Процент |
|--------|---------|
| USER%  | 8.31%   |
| SYS%   | 0.17%   |
| WAIT%  | 0%      |

### Анализ результатов
1. Преобладание User Time над Kernel Time указывает на высокую загрузку CPU, связанная с операциями бинарного поиска.
2. отсутствуют операции ввода-вывода (I/O).
3. SYS% и WAIT% === 0  указывает на то, что процесс не зависит от операций ввода-вывода.
4. Небольшое использование памяти, рабочий набор составляет 3,484 KB, что говорит о компактном размере данных для поиска.

### Мониторинг и профилирование (Process Explorer) - сводные таблицы

#### Одиночный запуск
| Программа       | USER% | SYS% | WAIT% | Время выполнения | Переключения контекста |
|-----------------|-------|------|-------|------------------|------------------------|
| binary_search   | 100% | 0%| 0%    | 30.1547s        | 13,584                |


2. **Поиск подстроки:** 
   - **Параметры запуска:** `substring_search D:\large_text.txt R7 10`
   - **Метрики:** CPU, I/O, USER, SYS, переключения контекста
   ### Основные метрики

На основании предоставленных изображений и обновленных данных, заполню отчет для профилирования программы с актуальными метриками:

---

285 context switch

2. **Поиск подстроки:** 
   - **Параметры запуска:** substring_search D:\large_text.txt R7 10
   - **Метрики:** CPU, I/O, USER, SYS, переключения контекста

### Основные метрики

| Метрика      | Значение   |
|--------------|------------|
| Kernel Time  | 4.328s     |
| User Time    | 19.718s    |
| Total Time   | 24.046s    |
| CPU Usage    | 8.32%      |

### Системные характеристики

| Метрика                | Значение |
|------------------------|----------|
| Working Set            | 7,244 KB |

### I/O статистика

| Метрика              | Значение     |
|----------------------|--------------|
| Read Delta           | 4,780        |
| Read Bytes Delta     | —            |
| Write Delta          | 14           |
| Write Bytes Delta    | —            |
| Other Delta          | 29           |
| Объем чтения (R)     | 225.0 MB     |
| Объем записи (W)     | 0 MB         |
| Остальные операции   | 0 MB         |

### Распределение времени

| Тип   | Процент |
|-------|---------|
| USER% | 8.32%   |
| SYS%  |  1.5% |

#### Одиночный запуск

| Программа           | USER% | SYS% | Время выполнения | Переключения контекста |
|---------------------|-------|------|------------------|-------------------------|
| substring_modified  | 82.01% | 17.99% | 24.046s         | 285                      |

---

### Анализ результатов
---

### Программа `binary_search`

**Исходные данные:**

- **Параметры запуска**: `binary_search 100000 1 25000000`
- **Ожидаемое время выполнения**: ~7 секунд
- **Фактическое время выполнения**: 30.1547 секунд
- **Оптимизации компилятора**: отключены по заданию

**Причины увеличения времени выполнения:**

1. **Отсутствие оптимизаций компилятора:**

   - **Влияние на производительность:** Отключение оптимизаций приводит к тому, что компилятор не выполняет упрощение кода, не устраняет избыточные вычисления и не использует эффективные инструкции процессора.
   - **Последствия:** Код исполняется медленнее, так как выполняется больше инструкций, и используются менее эффективные пути выполнения.

2. **Большое количество итераций:**

   - **Количество поисков:** Программа выполняет бинарный поиск 25,000,000 раз.
   - **Общая сложность:** Несмотря на логарифмическую сложность O(log N) самого бинарного поиска, общее число операций становится значительным из-за большого количества повторений.

3. **Однопоточное выполнение:**

   - **Использование одного ядра:** Программа выполняется в одном потоке и не использует возможности многопроцессорной системы.
   - **Загрузка CPU:** Загрузка процессора около 8%, что соответствует использованию одного ядра на 12-ядерном процессоре.


**Вывод:**

Основными причинами увеличения времени выполнения программы `binary_search` являются отключенные оптимизации компилятора и большое количество повторений алгоритма. Отключение оптимизаций значительно снижает эффективность кода, а однопоточное выполнение не позволяет использовать всю мощность доступных ядер процессора.

---

### Программа `substring_search`

**Исходные данные:**

### Анализ результатов

**Причины различий между предсказанным и фактическим временем выполнения:**

- **Кэширование на уровне диска или файловой системы:**
  - Несмотря на ожидание чтения 5 ГБ данных (500 МБ файл × 10 повторений), фактический объем чтения составил всего около 225 МБ.
  - Это указывает на то, что после первого чтения файл был закэширован не в оперативной памяти (так как рабочий набор памяти программы всего ~7 МБ), а на уровне диска или файловой системы.
  - Такое кэширование значительно сократило время последующих операций чтения, уменьшив общее время выполнения программы.

- **Низкое количество операций ввода-вывода:**
  - Уменьшение количества операций чтения и записи свидетельствует об эффективном использовании кэша, что снизило нагрузку на диск и повысило производительность.

- **Загрузка CPU и распределение времени:**
  - Низкая загрузка CPU (8,32%) при высоком проценте времени в режиме пользователя (82,01%) говорит о том, что программа эффективно выполняла вычисления с минимальным системным вмешательством.
  - Высокий процент времени(17.99 в режиме ядра указывает на значительную долю системных операций, таких как ввод-вывод (I/O).
  - Небольшое количество переключений контекста (285) указывает на то, что программа работала почти без прерываний, что улучшило ее производительность.

   **Вывод:**Различия в ожидаемых и фактических результатах связаны с кэшированием файла на уровне диска или файловой системы после первого чтения. Это привело к снижению времени ввода-вывода в последующих повторениях и, соответственно, к уменьшению общего времени выполнения программы по сравнению с предсказанным. Небольшой рабочий набор памяти подтверждает, что файл не загружался полностью в оперативную память, а кэшировался аппаратно или системно, что эффективно ускорило операции чтения без увеличения потребления памяти.
---


---

#### 2. Определение количества нагрузчиков для полной загрузки CPU

### Распределение времен, загрузки CPU и переключений контекста:

| Количество экземпляров | CPU Usage | %USER(для substring) | %SYS(для substring) | Переключения контекста |
|------------------------|-----------|-------|------|------------------------|
| 2                      | 16.7%     | 80.3% | 19.7% | 1,341                 |
| 4                      | 33.33%    | 79.0%  | 21.0% | 12,760                |
| 8                      | 66.6%     | 78.0% | 22.0% | ~240,000              |
| 10                     | ~78.8%    | 82.00% | 18.0% | ~430,000              |
| 18                     | ~система упала♿    |~система упала%♿ | ~система упала%♿ | ~~система упала♿   |


### Вывод

На основании проведённого тестирования с различным количеством экземпляров нагрузчика, можем сделать следующие наблюдения:

- **При 2 экземплярах** CPU Usage составляет 16.7%, из которых 80.3% занимает USER-время и 19.7% SYS-время. Количество переключений контекста — 1,341. Это указывает на умеренную загрузку системы, подходящую для легких нагрузок.
  
- **При 4 экземплярах** CPU Usage удваивается до 33.33%, с распределением USER-времени на уровне 79.0% и SYS-времени 21.0%. Переключения контекста резко увеличиваются до 12,760, что свидетельствует о возросшем взаимодействии с ОС и усложнении задачи для процессора.

- **При 8 экземплярах** CPU Usage достигает 66.6%, из которых 78.0% — USER-время и 22.0% — SYS-время. Количество переключений контекста (~240,000) возрастает существенно, что подтверждает интенсивную нагрузку на процессор и ОС.

- **При 10 экземплярах** CPU Usage составляет около 78.8%, а USER-время возрастает до 82.0%, при этом SYS-время сокращается до 18.0%. Переключения контекста увеличиваются до ~430,000. Это указывает на достижение максимальной эффективности, при которой все доступные ядра системы задействованы, и система поддерживает стабильную нагрузку без перегрузки.

- **При 18 экземплярах** система оказывается перегруженной, что приводит к её падению. Это подтверждает, что количество экземпляров свыше 10 создаёт критическую нагрузку, и система не может эффективно справляться с такой нагрузкой.

### Заключение

Оптимальная эффективность достигается при 10 экземплярах нагрузчика. На этом уровне процессорные ресурсы используются практически на полную мощность (~78.8%), и система сохраняет стабильность без перегрузки. USER-время (82.0%) показывает, что основная часть работы выполняется на уровне пользователя, а SYS-время (18.0%) остаётся в разумных пределах. Превышение 10 экземпляров приводит к перегрузке системы и её падению, указывая на то, что оптимальное количество экземпляров для эффективного выполнения задачи без риска перегрузки — это 10.

#### 3. Увеличение числа нагрузчиков вдвое, втрое, вчетверо

**Описание эксперимента:** Увеличение числа экземпляров каждого нагрузчика и анализ изменения производительности.

При увеличении количества нагрузчиков в 2, 3 и 4 раза система начинает испытывать значительное замедление, так как ресурсы CPU полностью заняты обработкой большого числа задач одновременно. Это проявляется следующим образом:

1. **Времена USER% и SYS%** резко возрастают, так как каждый нагрузчик активно потребляет ресурсы процессора, пытаясь выполнять вычисления.
2. **Количество переключений контекста** (вынужденных и невынужденных) значительно увеличивается, что дополнительно нагружает систему из-за работы планировщика операционной системы.
3. **Система начинает зависать**. Это происходит потому, что процессор вынужден переключаться между большим количеством задач, а также из-за исчерпания свободных ресурсов, что снижает реакцию на пользовательские команды.

Таким образом, при увеличении числа нагрузчиков более чем вдвое, система становится неэффективной, так как ресурсы перегружены, и управляемость системой значительно снижается.
---

#### 4. Объединение нагрузчиков в одну программу с потоками

**Описание эксперимента:** Объединение бинарного поиска и поиска подстроки в одну программу с использованием потоков, что позволяет одному процессу загружать все ядра системы.

- **Методика:** Программа создает два потока – один для выполнения бинарного поиска, другой – для поиска подстроки.
### Таблица 2: Нагрузка на CPU для многопоточной реализации (1, 2, 4 экземпляра)

На основе данных с предоставленных скриншотов, заполним таблицу:


| Количество инстансов | USER% | SYS% | Контекстные переключения | Время выполнения |
|----------------------|-------|------|--------------------------|------------------|
| x1                   | 90.65%| 9.35%| 800                      | 55.031 секунд   |
| x2                   | 91.45%| 8.55%| 8,200                    | 58.234 секунд   |
| x4                   | 91.78%| 8.22%| 112,000                 | 73.984 секунд   |

Эта таблица представляет распределение времени и переключений контекста для различных конфигураций инстансов.
### Вывод

После объединения нагрузчиков в одну программу с реализацией через многопоточность общее время выполнения **увеличилось**. 

- **Рост времени выполнения**: В многопоточной реализации, несмотря на эффективное распределение нагрузки между потоками, наблюдается увеличение времени выполнения. Это связано с конкуренцией потоков за ресурсы процессора и необходимостью управления общей памятью, что добавляет накладные расходы на синхронизацию и управление потоками.
  
- **Снижение SYS% и рост USER%**: Хотя USER% вырос, что указывает на более эффективное выполнение в пользовательском режиме, общее увеличение времени выполнения показывает, что системные накладные расходы на координацию потоков в общем процессе возросли.

- **Контекстные переключения**: Многопоточная реализация уменьшила количество контекстных переключений по сравнению с запуском отдельных процессов, однако эти оптимизации не компенсировали увеличение общего времени выполнения из-за вышеуказанных факторов.

**заключение**: Многопоточная реализация обеспечивает лучшее использование CPU и снижает системные накладные расходы на контекстные переключения. Однако, за счет накладных расходов на управление потоками и синхронизацию, время выполнения увеличилось по сравнению с использованием отдельных процессов.
---

#### 5. Включение агрессивной оптимизации компилятора

**Описание эксперимента:** Скомпилировать программу с агрессивными опциями оптимизации (например, `-O2` или `-O3`) и измерить реальное время выполнения.

После компиляции программ-нагрузчиков с флагом оптимизации `-O3`, заметно снизилось реальное время выполнения обеих программ. Ниже представлены изменения:

1. **Binary Search** (`agressive_binary.exe 100000 1 250000000`):
   - **Время выполнения:**  ~0 секунды.
   - **Ранее** (без оптимизации) время выполнения было существенно выше, что указывает на значительное ускорение за счёт агрессивной оптимизации.
   - Снижение времени связано с тем, что оптимизация `-O3` улучшает работу с циклами и логикой программы, минимизируя количество обращений к памяти и ускоряя поиск за счёт эффективного использования инструкций процессора.

2. **Substring Search** (`agressive_substring D:\large_text.txt R7 10`):

#### 1. Время выполнения

- **Без оптимизации**:
  - Общее время выполнения: **24.046 секунд**.
  - USER-время: **19.718 секунд**.
  - SYS-время: **4.328 секунд**.
  
- **С оптимизацией `-O3`**:
  - Общее время выполнения: **11.687 секунд**.
  - USER-время: **7.218 секунд**.
  - SYS-время: **4.468 секунд**.

**Вывод**: Оптимизация с флагом `-O3` привела к значительному сокращению общего времени выполнения (примерно на 51%). Это связано с тем, что компилятор смог улучшить выполнение кода, сократив количество операций, выполняемых в пользовательском режиме, и уменьшив затраты времени на системные вызовы.

#### 2. Использование CPU и процент USER/SYS

- **Без оптимизации**:
  - Загрузка CPU: **8.32%**.
  - %USER: **81.99%**.
  - %SYS: **18.01%**.

- **С оптимизацией `-O3`**:
  - Загрузка CPU: **8.34%**.
  - %USER: **61.8%**.
  - %SYS: **38.2%**.

**Вывод**: Оптимизированная версия распределяет больше времени на системные вызовы, что может указывать на улучшенное управление ресурсами. В результате USER-время уменьшается, так как программа работает более эффективно и достигает тех же результатов с меньшим количеством пользовательских вычислений.

 #### 3. I/O операции

- **Без оптимизации**:
  - I/O объем: **225 MB** чтения.
  - Количество операций чтения: **4,780**.
  - Количество операций записи: **14**.

- **С оптимизацией `-O3`**:
  - I/O объем: **430.8 MB** чтения.
  - Количество операций чтения: **4,780**.
  - Количество операций записи: **14**.

**Вывод**: Опция `-O3` не повлияла на количество операций ввода-вывода, но увеличила объем прочитанных данных до **430.8 MB** по сравнению с **225 MB** без оптимизации. Это может быть связано с тем, что оптимизация улучшает доступ к данным, обеспечивая их более быструю обработку.

#### 4. Потребление памяти

- **Без оптимизации**:
  - Пиковое использование памяти (Private Bytes): **2.6 MB**.
  
- **С оптимизацией `-O3`**:
  - Пиковое использование памяти (Private Bytes): **4.7 MB**.

**Вывод**: Оптимизация с `-O3` привела к увеличению использования памяти. Это связано с тем, что оптимизированный код может создавать дополнительные структуры данных для повышения скорости выполнения, что требует большего объема оперативной памяти.

### Вывод:
Применение оптимизации `-O3` существенно сократило время выполнения обоих программ-нагрузчиков. Время выполнения **binary search** сократилось до долей секунды, а для **substring search** — в 2 раза, что подтверждает эффективность агрессивных оптимизаций для вычислительных задач с высокой нагрузкой на процессор.
**Вывод:** Оптимизация компиляции значительно сократила время выполнения программы за счет улучшения производительности кода. Это подтверждает эффективность компиляторских оптимизаций при работе с интенсивными вычислениями.

---

### Заключение

В ходе лабораторной работы была реализована оболочка командной строки для запуска программ, а также программы-нагрузчики для измерения производительности системы. Проведенные эксперименты показали, как изменяется поведение системы при увеличении нагрузки, использовании многопоточности и оптимизаций компилятора. Инструменты профилирования позволили зафиксировать метрики, такие как USER%, SYS%, WAIT%, количество переключений контекста и другие параметры, что дало представление о влиянии каждой программы на ресурсы системы. 
