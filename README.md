## Кейс 1
<details>
  <summary>Условия задачи</summary>

На игровом проекте компании в начале прошлого месяца была проведена монетизационная акция. Она длилась 3 дня.
В рамках акции, игрокам предлагалось купить игровые ресурсы по более выгодным, чем обычно, ценам.
При этом стоимость акционного предложения была выше среднего чека по проекту. Перед Вами стоит задача провести анализ этой акции.
</details>


Анализ данных будет включать в себя несколько основных этапов:

1. Сбор данных
2. Очистка данных и быстрый анализ
3. Расчет основных показателей и метрик
4. Визуализация данных


### 1. Сбор данных
Данные в базе, скорее всего, хранятся по типу ивента, поэтому выгрузку будем производить поэтапно.
Можно через СУБД запросить селектом нужные таблицы, загрузить и затем уже обращаться к ним через Python.
А можно, напрямую загружать данные через API, например. Лучше выбирать второй способ, так как настроив
загрузку один раз, можно автоматизировать процесс, избежать лишних трудозатрат и ошибок, связанных с человеческим фактором.

Для анализа нам могут понадобиться:
* Транзакционные данные (id пользователя, дата транзакции, число покупок, например, за день,
   тип покупки (обычная/акционная покупка);
* Данные о пользователях (возраст аккаунта, регион/страна);
* Активности игроков (дата сессии, длительность сессии, уровень в игре);
* Данные отслеживания поведения (например, клики на предложение, взаимодействие с магазином).


### 2. Очистка данных
Далее обязательным этапом будет знакомство с данными и их очистка. Библиотека **Pandas Python** обладает широкими возможностями для работы с датафреймами.
Поэтому для корректной обработки можно сначала преобразовать таблицы в датафреймы:
* Преобразуем данные в датафрейм (`pd.DataFrame()`)
* Удаляем дубликаты (`df.drop_dublicates()`)
* Проверяем, есть ли пропуски и заполняем их (`df.isnull().sum()`, `df.fillna(0)`), либо удаляем пропущенные значения, в зависимости от задчи
* Проверяем формат колонок и, если нужно, преобразуем str дату в формат даты (`df.dtypes, pd.to_datetime(df[date'])`)
* Проверяем ошибки в данных, например, `amount < 0`
* Оцениваем выбросы, можно, например посмотреть аномально большие суммы покупок

После очистки можно провести быстрый анализ, посчитать, например, количество покупок по дням, посмотреть, есть ли киты и сколько их,
сгруппировав покупки по юзерам, можно посмотреть распределение покупок, используя метод `.hist(bins=50)`.


### 3. Расчет метрик
Здесь хотелось бы отметить один момент насчет сегментации пользователей. Я бы сначала посмотрела динамику основных показателей в общем, по всем пользователям,
а затем перешла бы к сегментации для более глубокого анализа. Расчет базовых показателей поможет понять, была ли акция вообще успешной.

Можно рассчитать:
* **Выручку** (общую, в динамике по дням);
* **Средний чек**;
* **Конверсию в покупку**;
* **Retention**;
* **Число платежей**;
* **Баланс хард валюты** (внутриигровой).

Далее для оценки эффективности акции можно провести сравнение с показателями за схожий период без акции.
Так как акция длилась всего три дня, здесь еще важно оценить, в какие дни недели она проводилась и сравнивать с такими же днями,
так как, вероятнее всего, в выходные активность игроков выше.

Сегментацию игроков можно сделать по нескольким параметрам:
* По платежеспособности (не покупают, киты и остальные средне платящие);
* По активности (новые игроки, спящие (вернулись ради акции), активные);
* По времени покупки (купили во время акции, купили после акции);
* Еще можно посмотреть активность пользователей по платформам OS, Android, Windows и т.д.);

Такая сегментация поможет понять, как акция повлияла на разные типы игроков. С точки зрения маркетинга это поможет в будущем распределению затрат на рекламу,
кому стоит показывать больше и чаще, а для какого эффекта будет меньше. Для когорт игроков важно будет оценить **ARPPU**, **долю игроков, купивших только акционный товар**,
поведение китов (**whales**) – увеличили ли они траты. Сегментация также поможет определить, есть ли **каннибализация**.

### 5. Визуализация данных
Визуализация важна и нужна, так как копаться в данных любят только аналитики!:)😊
Показатели в динамике (**Revenue**, **ARPPU**, **количество покупок** и т.д.) лучше отображать **линейным графиком**.
**Bar chart** удобно использовать для сравнения общих показателей для разных когорт пользователей.
Например, доля выручки для каждой когорты от общей revenue. Количество пользователей в когортах.

Как итог анализа можно сделать презентацию или дашборд, отвечающие на основные вопросы:
* Был ли всплеск дохода во время акции?
* Увеличился ли средний чек?
* Повлияла ли акция на новых или спящих игроков?
* Были ли негативные эффекты?


## Кейс 2
<details>
<summary>Условия задачи</summary>
  
Допустим, Вам были предоставлены данные о монетизационной акции, описанной в Кейсе 1.
Данные содержат информацию об активности пользователей, заходивших в приложение за неделю до акции, во время акции,
а также за неделю после окончания акции. Данные предоставлены в следующем формате.

Группирующие колонки:  
  **Date** - дата, за которую производится наблюдение;  
  **Platform** - платформа, на которой играет пользователь (IOS, Android, Windows и т.д.);  
  **User_ID** - уникальный идентификатор пользователя;  
  **Reg_Date** - дата регистрации пользователя в приложении.  

Расчетные колонки:  
  **Revenue** - сколько центов заплатил пользователь за рассматриваемую дату;  
  **Payments** - сколько платежей совершил пользователь за рассматриваемую дату;  
  **Sessions** - сколько сессий было у пользователя за рассматриваемую дату;  
  **Playing_Time** - сколько времени пользователь провел в игре за рассматриваемую дату.  
</details>


Данные, предоставленные мне для оценки монетизационной акции позволяют посчитать немалое количество метрик разного типа.
Ключевыми можно считать следующие группы, характеризующие базовые финансовые показатели, вовлеченность и поведение пользователей:  
* Revenue;
* Средний чек;
* ARPPU;
* Количество уникальных платящих пользователей (Paying Users);
* Конверсия в покупку (Conversion Rate);
* DAU (Daily Active Users);
* Среднее количество сессий на пользователя;
* Среднее время в игре (Avg Playing Time;
* Retention Rate.

Для понимания влияния акции на разные категории пользователей важно оценить поведение метрик по сегментам:  
* Платформы iOS vs Android vs Windows;
* новички vs ветераны;
* F2P vs Paying Users.

Если цель акции – увеличить выручку, важно смотреть **Revenue**, **ARPPU** и **Paying Users**.  
Если цель – повысить вовлеченность, то **DAU**, **Playing Time** и **Retention Rate**.  
Если акция должна привлечь новых плательщиков, то **конверсия в покупку** и сегментация **новичков**.

Можно построить динамику **Revenue**, **ARPPU** по дням для понимания общей картины изменения показателей.
Если агрегировать данные по периодам до, во время и после акции, то я бы выделила в данных три когорты по три совпадающих дня недели:  
до, во время и после монетизационной акции.
Тогда сравнение сагрегированных основных показателей и метрик будет корректным с учетом сезонности по дням недели.

Что касается расчета **Retention**, это классический способ оценки возврата пользователя после события.
Можно посчитать **Retention**, например, на **1**, **3**, **7** дни после акции.
Мне кажется, наиболее эффективным будет показатель именно на 3 день, так как возврат в первый день нам ни о чем не скажет,
пользователь может еще надеяться на продолжение акции, а вот возврат на третий день после акции уже будет более осознанным.

При визуализации можно использовать следующие графики:  
* **Барчарты** – сравнение Revenue / ARPPU сравнение платежей по платформам / сегментам
* **Линейные графики** – динамика платежей до/во время/после акции по сегментам.

При анализе на Python мне бы пригодились библиотеки:  
* Requests (для загрузки данных через API);
* Pandas (для работы с датафреймами);
* Возможно, NumPy (для работы с массивами);
* Matplotlib, seaborn (для визуализации данных).
Ниже приведу пример кода на Python деления на когорты, расчета некоторых метрик, в том числе Retention 3 дня.
Удобно будет создать датафрейм по трем периодам, куда можно записать, например, сагрегированные Revenue, ARPPU, Payments, Paying_Users и т.д.

```python
import pandas as pd

# Преобразуем дату в формат даты
df['date'] = pd.to_datetime(df['date'])

#Фиксируем даты акции
promo_day_start = pd.Timestamp('2025-02-01')
promo_day_end = pd.Timestamp('2025-02-03')
print(promo_day_start, promo_day_end)

#Дни недели акции dt.weekday (0 - пн, 1 - вт итд)
promo_weekdays = df[(df['date'] >= promo_day_start) & (df['date'] <= promo_day_end)]['date'].dt.weekday.unique()

# Определяем дни недели promo до акции
before_promo_weekdays = df[(df['date'] >= promo_day_start - pd.Timedelta(days=7)) & 
                           (df['date'] < promo_day_start) &
                           (df['date'].dt.weekday.isin(promo_weekdays))]

# Определяем дни недели promo после акции
after_promo_weekdays = df[(df['date'] > promo_day_end) &
                          (df['date'] <= promo_day_end + pd.Timedelta(days=7)) &
                          (df['date'].dt.weekday.isin(promo_weekdays))]

# Определяем дни недели promo
promo_weekdays = df[(df['date'] >= promo_day_start) & (df['date'] <= promo_day_end)]

# Посчитаем доход за каждый период
revenue_comparison = pd.DataFrame({'Period': ['before_promo', 'promo', 'after_promo'],
                                   'Revenue': [before_promo_weekdays['Revenue'].sum(),
                                               promo_weekdays['Revenue'].sum(),
                                               after_promo_weekdays['Revenue'].sum()]})

revenue_comparison['ARPPU'] = [before_promo_weekdays['Revenue'].sum() / 
                              before_promo_weekdays[before_promo_weekdays['Revenue'] > 0]['User_ID'].nunique(),
                              promo_weekdays['Revenue'].sum() / 
                              promo_weekdays[promo_weekdays['Revenue'] > 0]['User_ID'].nunique(),
                              after_promo_weekdays['Revenue'].sum() / 
                              after_promo_weekdays[after_promo_weekdays['Revenue'] > 0]['User_ID'].nunique()]

revenue_comparison['Payments_count'] = [before_promo_weekdays['Payments'].sum(),
                                        promo_weekdays['Payments'].sum(),
                                        after_promo_weekdays['Payments'].sum()]
    

revenue_comparison['Paying_users'] = [before_promo_weekdays[before_promo_weekdays['Revenue'] > 0]['User_ID'].nunique(),
                                      promo_weekdays[promo_weekdays['Revenue'] > 0]['User_ID'].nunique(),
                                      after_promo_weekdays[after_promo_weekdays['Revenue'] > 0]['User_ID'].nunique()]


# Расчитаем Retention 3 дня,смотрим любую активность после возврата
promo_users = promo_weekdays[promo_weekdays['Revenue'] > 0]['User_ID'].unique()
retented_3day_users = df[(df['date'] == promo_day_end + pd.Timedelta(days=3)) & (df['date'].isin(promo_users))]['User_ID'].nunique()
Retention_3day = retented_3day_users*100.00 / len(promo_users)
```


## Кейс 3
<details>
  <summary>Условия задачи</summary>
Теперь представим другую ситуацию. К Вам обратился отдел, занимающийся закупкой
  рекламного трафика для наших приложений (User Acquisition).
Для планирования своих расходов и построения KPI, им необходимо иметь возможность прогнозировать
доходы по когортам закупаемого трафика. Конкретно, их интересует прогноз накопительного
LTV (Lifetime Value) за первые 60 дней жизни когорты.
Крайне желательно предоставить им прогноз на каждый день (с 1 по 60), а не только на 60-й.
В базе данных имеется информация о том, по какой реферальной ссылке пользователь пришел в игру, на
какой платформе он играет, в какой стране находится, а также многие другие факторы, которые
можно использовать при прогнозировании LTV. Ответьте в свободной форме и в любом порядке на
следующие вопросы:  
  
1. Опишите, как бы Вы рассчитывали такой прогноз.
2. Какими инструментами Вы пользовались бы 
(Excel/R/Python/Other)?
3. Какие математические подходы Вы применили бы
для решения данной задачи?
</details>


LTV (Lifetime Value) – это показатель дохода от одного пользователя за время его жизни, то есть доход до момента времени,
когда пользователь перестанет пользоваться приложением.
В одной статье встретила забавный мем на тему расчета LTV по методу Шарикова!😊. 

![](/figures_for_test/1.jpg)

Шутки шутками, а этот способ действительно существует, как простой и грубый метод расчета LTV по историческим данным.
Но эффективность его с точки зрения прогнозирования практически нулевая, так как оперировать мы можем только с историческими данными.
К тому же метод имеет ряд отрицательных качеств, связанных с неучетом деталей развития продукта и сегментации.

`LTV = total revenue (за период) / total users (за период)`

В оправдание использования этого метода хочу отметить один момент.
Если мы действительно имеем исторические данные для расчета **LTV**, мы можем построить прогноз, например, в том же **Excel**.
Оперируя этим инструментом, можно построить линию тренда по выручке, посмотреть коэффициент детерминации,
для поиска наиболее подходящего тренда и на основе математически сформулированного уравнения построить прогноз.
Предварительно можно посчитать коэффициент корреляции исторических данных,
который отвечает именно за линейный тренд и регрессию, если он высокий, построить прогноз линейного тренда.
Этот метод будет топорным и трудозатратным, и скорее всего, эффективным на короткий временной прогноз.

Наиболее точным и эффективным, конечно, будет прогноз кумулятивного **LTV** с применением **ML** на **Python**.
Для простой аппроксимации можно использовать логарифмические модели, так называемые, кривые роста.
Известно, что накопительный **LTV**, как и накопительный **APRU** сначала растет быстро,
а потом его рост замедляется и стремится к своей асимптоте, пределу. Самое сложно, подобрать уравнение кривой так,
чтобы оно максимально близко продолжало кривую исторических **ARPU** и достигало наиболее точно спрогнозированного **LTV**. Такой подход носит название **Top Down**.

![](/figures_for_test/2.png)

Этапы этого подхода включают следующие шаги:
Допустим, у вас есть новая когорта игроков, отобранная по какому-либо критерию.
Замеряя, сколько игрок потратил в средней в первый день, второй, вы можете сформировать накопительный **ARPU** в начале кривой.
На основе этих данных можно построить математическую модель. Часто рассматривается кривая с коэффициентами модели A и B вида:  

F(t) = A * ln(t + B)

Коэффициенты А и В подбираются на основе уже имеющихся данных, так, чтобы минимизировать сумму квадратов отклонений фактических значений функций от смоделированных,
то есть методом наименьших квадратов. Подобрав значения коэффициентом, подставляем нужное t и получаем значение прогнозируемого **ARPU**.

Степенная функция для расчета накопительного **LTV** может иметь такой вид:  

LTV(t) = A * (1−e<sup>−B*t</sup>)

Оптимальные значения коэффициентов также подбираются на основе метода наименьших квадратов. Используя метод `curve_fit` из библиотеки **scipy Python**,
можно задать вид уравнения и подобрать оптимальные коэффициенты на основе имеющихся данных. Приведу простой пример на **Python**.

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.optimize import curve_fit

# Данные: накопительный ARPU за несколько дней
days = np.array([1, 3, 7, 14])
cumulative_arpu = np.array([0.5, 1.2, 2.5, 4.0])  # Примерные значения

#LTV-модель
def ltv_function(t, A, B):
    return A * (1 - np.exp(-B * t))

# Подбор параметров A и B
params, mat_cov = curve_fit(ltv_function, days, cumulative_arpu)

# Прогнозируем LTV на 60 дней
future_days = np.arange(1, 61)
predicted_ltv = ltv_function(future_days, params[0], params[1])

# Визуализация
plt.plot(days, cumulative_arpu, 'o', label='Фактические данные')
plt.plot(future_days, predicted_ltv, '-', label='Прогноз LTV')
plt.xlabel('Дни')
plt.ylabel('Кумулятивный LTV')
plt.legend()
plt.show()
```

![](/figures_for_test/3.png)

Строив модель по сегментам, например, по платформам, получаем несколько кривых прогноза накопительного LTV.

Для одновременного учета нескольких сегментов и более точного прогноза можно обучить модель **ML**,
используя градиентный бустинг **XGBoost** (**Extreme Gradient Boosting**).
Этот алгоритм отлично подходит для прогнозирования **LTV** с одновременным учетом нескольких факторов (например, источник трафика, страна и платформа).
Метод градиентного бустинга строит дерево решений одно за другим, исправляя ошибки предыдущего.
Таким образом каждое новое дерево улучшает предсказание предыдущего.

Прогноз на Python будет включать следующие шаги:  
* Импортируем библиотеки `pandas`, `xgboost`, `sklearn`;
* Преобразуем в датафрейм имеющиеся данные (значения **LTV** и даты (желательно уже в днях, для построения прогноза на 60 дней), источник трафика, страну и платформу);
* Кодируем факторы, влияющие на прогноз (источник трафика, страна, платформа), так как машина не понимает текст, преобразуем его в биномиальные значения с определенным названием столбцов (модуль `OneHotEncoder` из `sklearn`);
* Преобразованные в массив чисел данные сохраняем в датафрейм и объединяем с известными днями и **LTV**;
* Разделяем данные на обучающую и тестовую группы (`train_test_split` из `sklearn`);
* Задаем модель и обучаем ее (`XGBRegressor()`);
* Формируем датафрейм с датами прогноза и закодированными факторами;

Предсказываем значения по построенной модели!











