# coffee-review
What is the best coffee? Taste and price analytics based on CoffeeReview reviews
03.06.2025 - початок роботи з датасетом "top-rated-coffee-clean", посилання --> https://www.kaggle.com/datasets/asimmahmudov/top-rated-coffee?resource=download

Хоча в описі датасету вказано, що це очищений файл, на початку роботи з ним я виявила відсутні значення майже в кожній колонці, а також значення, які не були приведені до одного формату. 
Статистика відсутніх значень по кожній колонці: coffee_name - 0; total_score - 3 відсутніх значення; roaster_location - 0; coffee_origin - 6; roast_level - 23; est_price - 116; agtron_ground - 10; agtron_roast - 10; origin_country - 441; price_per_ounce - 259 пустих значень. В колонках total_score, coffee_origin, roast_level я замінила пусті значення на "UNKNOWN". 

В колонці coffee_origin я замінила знак ';' на ',' і за допомогою формули REGEXEXTRACT дістала назву країни з цієї колонки. Далі привела цю колонку до єдиного формату.
Колонку est_price покроково почала приводити до формату "ціна в доларах за 1 г". Колонку price_per_ounce повністю видалила, адже дані в ній були не вірно пораховані. 
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

5. Створюю колонку coffee_in_grams і переводжу унції в грами.

![5](https://github.com/user-attachments/assets/9a6d3a65-94cb-4afe-aea2-072c4531582d)

6. Створюю колонку price_in_USD, щоб привести ціну за каву тільки в долари США.

![6](https://github.com/user-attachments/assets/b4d2a411-d699-4218-9693-957e05caafe5)

7. Створюю фінальну колонку price_of_1_gram_in_USD, щоби дізнатись ціну за 1 г кави в доларах США.

![7](https://github.com/user-attachments/assets/64719ed6-4839-47fe-a5f2-7b1fed6d24ec)





06.06.2025 Починаю роботу з колонкою coffee_origin.
Починаючи досліджувати колонку coffee_origin, я виявила, що в кожному окремому рядку містяться записи як однієї країни походження кави, так і декількох (тобто купаж). 

Щоб впоратися з суперечливими географічними даними в полі coffee_origin, я розділила рядок походження кави формулою SPLIT (їх вийшло 5), нормалізувала кожен сегмент (наприклад, замінивши "Huehuetenango" на "Guatemala"), а потім зіставила їх з контрольованим списком відомих країн-виробників кави (який створила на окремому аркуші 'Countries').

Для кожної колонки, яку я отримала після ділення coffee_origin, я написала умову: якщо в рядку зустрічається країна зі списку 'Countries' -> став '1', якщо ні - '0'. 

![8](https://github.com/user-attachments/assets/40cd52a5-9e35-4a1f-b685-5692d9fecf95)

Створила колонку SUM_country, де склала всі значення з попереднії п'ятьох колонок.
Створила колонку category, де прописала умову: якщо колонка SUM_country = 0 -> категорія "Unknown", якщо 1 -> категорія 'Single origin', все інше - 'Blended origin'.

![9](https://github.com/user-attachments/assets/169e2cba-3377-4b61-b770-06dce0b874f0)


Це дозволило правильно класифікувати каву як односортну або купажовану на основі реальних згадок про країну, навіть якщо вони заховані в складному тексті.


13.06.2025 - робота з колонкою roaster_location.
Я вирішила розбити колонку roaster_location по комах, щоб отримати назву країн обсмажування кави. Це допоможе створити окрему колонку roaster_country, що дасть змогу:
-Зробити глобальні карти обсмажування;
- Порівняти ціну/якість за країною обсмажування;
- Побачити, які країни мають спеціалізовані обсмажувальні центри (наприклад, США, Японія, Тайвань).








Завдання на майбутнє: створити таблицю з курсами валют, яка автоматично оновлюється, і прив'язати цей проект до такої таблиці.
