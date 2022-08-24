# Quality_of_pre-workout_warm-up
Визначення якості розминки перед тренуванням, по даним з акселерометра на руці, за допомогою машинного навчання та аналізу даних.
## Зміст

- [Постановка задачі](#постановка-задачі)
- [Про файли](#про-файли)
- [Використані дані](#використані-дані)
- [Як використовувати](#як-використовувати)

## Постановка задачі
Моєю цілю було створити та реалізувати структуру моделі, яка б могла допомогти спортсменам визначити якість їх розминки перед тренуванням.
### Основна ідея виглядає так:
1) У спортсмена на правій руці є смарт годинник з акселерометром, на початку тренування він повинен зайти в додаток та розпочати запис.
2) Після закінчення розминки, людина закінчує запис.
3) Потім алгоритм розділяє розминку на окремі вправи та класифікує їх, і після цього видає звіт розминки.
4) У звіті написаний список вправ, у порядку їх виконання, яких виконав спортсмен, час їх виконання та сумарний результат, таблиця з запланованими вправами та часом виділеним на кожну вправу сумарно під час розминки.
5) По результатам сумарної таблиці, алгоритм видає висновок: людина зробила все правильно та може продовжувати тренування, чи потрібно зробити додаткові вправи для розминки.

Так як це просто структура, то в якості розминки у нас буде короткий список вправ - присідання, махи руками в плечовому суглобі та удари правою рукою по груші.
P.S. Вправи вибирались так, щоб я міг самостійно зібрати достатній дата сет і не сильно виснажуватись під час цього.

В якості алгоритму визначення якості розминки виступає умова "Для того щоб гарно розігріти групу м'язів, потрібно виконувати певну вправу мінімум 12 сек", я її придумав сам, тому не судіть жорстоко, для більш якісних умов потрібно проконсультуватися з тренерами.

## Про файли
Файли:
- fitness_workbook.ipynb, Це мій робочий ноутбук в якому записаний процес аналізу даних, підбору фільтрів, процес вибору фічей для моделей, підбор моделей та аргументація їх використання, також там є графіки, на які я дивився під час роботи, та основні думки, які в мене виникали. Нажаль, так як це робочий ноутбук, то він не дуже чисто оформлений(
- fitness_final.ipynb, Це готовий ноутбук, який забирає останній файл з тестової папки (неначе це тільки що отриманий файл), проводить його перевірку на коректність, проводить фільтрацію, розбиває сигнал на окремі вправи, класифікує їх та видає кінцеві звіти.
- fitness_final_one.ipynb, Це той самий fitness_final.ipynb, але зібраний в одній клітині та без коментарів, він створений для того щоб вибрати файл, натиснути одну кнопку та отримати звіти.

Папки:
- Fitness report, Папка в яку потрапляють звіти тренувань після виконання коду. (В ній вже є 8 звітів, для 8 тестових файлів з тестової папки, вони з підписом old)
- Error report, Папка в яку потрапляють звіти помилок, якщо файл поданий в модель не відповідає певним умовам. (Аналогічно там вже є файл приклад з підписом old)
- Graphs, Папка з візуалізаціями предіктів моделі до тестових даних.
- test_file, Папка з тестовими даними
- models, Папка з моделями, які я отримав під час роботи.
- train_file, Папка з файлами, які використовувались для навчання моделі.
- error_file, Папка з зіпсованими фалами, їх відловила система пошуку помилок під час тренування моделі.
- Error report train, Папка з звітами до файлів зазначених в error_file.

## Використані дані
### Збір даних
У моєму випадку збір даних виконувався телефоном прив'язаним до руку в місці, де повинен бути браслет (пробував тримати телефон в долоні екраном в руку, все продовжувало працювати).
Дані записувались за допомогою додатку Accelerometer Meter (ви його можете знайти в Google Play) на налаштуваннях (в розділі Graph) позначених на малюнку.

<img src="app_settings.jpg" width="350" height="300" />

Тобто модель використовує значення датчика в 	$m/s^{2}$ та при частоті 50 Гц (я пробував змінювати частоти (там довга історія з спробами переписати цей додаток для автоматизації збору даних) в діапазоні від 5 - 300 Гц, якщо частота менше 30 Гц алгоритм починає працювати погано, а при частотах більше 100 Гц потрібно змінити розмір вікна на етапі виділення активних зон і алгоритм продовжить працювати гарно, в іншому випадку будуть помилки).

### Train data
У зборі тренувального дата сета брали участь 4 молоді та здорові людини різних спортивних категорій, 10 + 9 + 8 + 5 в цілому 33 записи. 
По технічній частині збору даних описано вище.
Тренувальний запис мав такий формат:
1. 5 секунд спокою
2. 15 секунд махів руками
3. 10 сек відпочинку
4. 15 сек присідань
5. 10 сек відпочинку
6. 15 сек ударів руками
7. 5 сек спокою
У половині файлів під час спокою та відпочинку потрібно було тримати руки майже нерухомо, у половині можна було спокійно рухатись та виконувати інші дії (почесати спину та тд).
### Test data
У нас є 8 спеціально відзнятих файли для тесту (те що модель ні одного разу на них не промахнулась, це просто співпало), щоб перевірити всі можливі варіанти звіту та роботу алгоритму.
1. test1_standard - формат дій як і в трейні, виконаний людиною, дані якої є в трейні. (15 сек махів (в майбут. 15 м), 15 сек присідань (15 п), 15 сек ударів (15 у)).
2. test2_standard_other - формат дій як і в трейні, виконаний людиною, даних якої не має трейні. (15 м, 15 п, 15 у).
3. test3_stress - великий набір вправ у хаотичному порядку ранодомного часу. (10п, 20у, 15м, 10м, 8м, 10у, 12п, 10у, 10м), людини не має в трейні.
4. test4_not_full_squatting - не виконані всі присідання (15м, 15у, 5п), можна побачити, як алгоритм робить помилки, але основну задачу все таки виконує.
5. test5_only_hits - присутні тільки удари в повному обсязі (15у).
6. test6_not_squatting - повні удари, не всі махи, немає присідань (15у, 5м).
7. test7_only_not_full_rotation - тільки не повні махи (10м).
8. test8_error_file - файл з пошкодженою частою та середнім прискоренням, для перевірки частини пошуку помилок, (7м, 7у).

P.S. Відхилення часу дій в звітах від зазначених здебільшого помилка людини, яка виконувала дію невірний час, а не помилка алгоритму.

## Як використовувати
Мною передбачалось, що ви захочете перевірити отримані мною звіти та отримати Їх самостійно, чи подивитись на дані під час певних стадій їх обробки, тому за допомогою файлів test_file та fitness_final.ipynb чи fitness_final_one.ipynb (у цьому файлі графіків не має), ви можете це зробити.
Просто запустіть код вибравши файл з тестової папки.
Вибір файлу можна зробити 2 способами:
- Змінити номер фалу який зчитає алгоритм у строчці (обережно тут зворотна нумерація):
file_name = os.listdir("test_file")[**-1**]
- Чи змінивши назву файлу, так щоб він став на останнє місце в алфавітному порядку.

**Також ви можете спробувати алгоритм на свої даних**, для цього запишіть файл, як сказано в [використаних даних](#використані-дані), додайте його в test_file та виберіть його. Алгоритм досить гарно відпрацьовував на даних людей, яких не було в трейні, але це були молоді спортивні люди.

На цьому в мене все, приємного дня та користування моїм проектом))
Слава Україні!!!
