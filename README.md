# coffee-review
What is the best coffee? Taste and price analytics based on CoffeeReview reviews
03.06.2025 - початок роботи з датасетом "top-rated-coffee-clean", посилання --> https://www.kaggle.com/datasets/asimmahmudov/top-rated-coffee?resource=download

Хоча в описі датасету вказано, що це очищений файл, на початку роботи з ним я виявила відсутні значення майже в кожній колонці, а також значення, які не були приведені до одного формату. 
Статистика відсутніх значень по кожній колонці: coffee_name - 0; total_score - 3 відсутніх значення; roaster_location - 0; coffee_origin - 6; roast_level - 23; est_price - 116; agtron_ground - 10; agtron_roast - 10; origin_country - 441; price_per_ounce - 259 пустих значень. В колонках total_score, coffee_origin, roast_level я замінила пусті значення на "UNKNOWN". 

В колонці coffee_origin я замінила знак ';' на ',' і за допомогою формули REGEXEXTRACT дістала назву країни з цієї колонки. Далі привела цю колонку до єдиного формату.
Колонку est_price покроково почала приводити до формату "ціна в доларах за 1 г". 
1.  Спершу витягла числову ціну за допомогою REGEXEXTRACT. !!!
2.  Далі витягла одиницю виміру (ounces або grams).
