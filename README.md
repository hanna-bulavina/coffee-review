# coffee-review
What is the best coffee? Taste and price analytics based on CoffeeReview reviews
03.06.2025 - початок роботи з датасетом "top-rated-coffee-clean", посилання --> https://www.kaggle.com/datasets/asimmahmudov/top-rated-coffee?resource=download

Хоча в описі датасету вказано, що це очищений файл, на початку роботи з ним я виявила відсутні значення майже в кожній колонці, а також значення, які не були приведені до одного формату. 
Статистика відсутніх значень по кожній колонці: coffee_name - 0; total_score - 3 відсутніх значення; roaster_location - 0; coffee_origin - 6; roast_level - 23; est_price - 116; agtron_ground - 10; agtron_roast - 10; origin_country - 441; price_per_ounce - 259 пустих значень. В колонках total_score, coffee_origin, roast_level я замінила пусті значення на "UNKNOWN". 

В колонці coffee_origin я замінила знак ';' на ',' і за допомогою формули REGEXEXTRACT дістала назву країни з цієї колонки. Далі привела цю колонку до єдиного формату.
Колонку est_price покроково почала приводити до формату "ціна в доларах за 1 г". 
1. В першу чергу витягла всі можливі варіанти та символи валюти в стовпці est_price. Ось відформатований список унікальних валют.

![1](https://github.com/user-attachments/assets/c9f40738-34f3-438d-92df-d7e06b627d16)

2. Наступний крок - витягла ціну незалежно від валюти (число після абревіатур чи символів валют).

![2](https://github.com/user-attachments/assets/15f28cdd-efd7-4fd4-993d-ced9776fd5aa)

3. Витягла одиниці виміру з колонки est_price, подивилась унікальні записи вимірів, відкинула все, крім грамів та унцій, створила колонку unit_cleaned
 
 !!!Handling Measurement Units

Normalized weight-related units to either grams or ounces.

Converted kg to grams by multiplying by 1000.

Excluded rows where measurement units were ml, fluid, or packaging-based (e.g., can, bottle, packet, capsules) as they are not convertible to weight.

Also excluded rows where unit could not be extracted (marked as #N/A) — total of 20 rows removed (~0,9% of dataset).!!!

![3](https://github.com/user-attachments/assets/4a4bf64f-136e-4997-9ae1-3631613f2ae0)

  
4. Створюю колонку exchange_rate_to_USD і прописую формулу для перерахунку кожної валюти в долар по курсу на 06.06.2025. Всі валюти з колонки est_price та курс до долара на 06.06.2025р. представлені у таблиці:

| Валюта                | Символ | Курс до USD|
| --------------------- | ------ | ------- |
| Долар США             | `$`    | 1.00 |
| Фунт стерлінгів       | `£`    | 1.35|
|  Єна                   | `¥`    | 0.0069 |
|  Австралійський долар  |  `AUD` |   0.65|     
|  Канадський долар      |  `CAD` |   0.73 |   
| Гонконгський долар    |  `HKD` |   0.13   |  
|Індонезійська рупія    | `IDR`  | 0,000062|
| Вона (Південна Корея) | `KRW`  | 0.00074 |
| Тайванський долар     | `NT`   | 0.033   |

![4](https://github.com/user-attachments/assets/0e6ccffc-b560-4164-97a8-cdf330e9248b)



6. Створюю колонку numeric_part, щоб витягти ціну з колонки est_price.

7. Створюю колонку currency для визначення валюти.






Завдання на майбутнє: створити таблицю з курсами валют, яка автоматично оновлюється, і прив'язати цей проект до такої таблиці.
