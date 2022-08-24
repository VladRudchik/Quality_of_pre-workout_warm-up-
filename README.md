# Quality_of_pre-workout_warm-up
Визначення якості розминки перед тренуванням, по даним з акселерометра на руці, за допомогою машиного навчання та анлізу даних.

## Зміст

- [Постановка задачі](#постановка-задачі)
- [Про файли](#про-файли)
- [Використані дані](#використані-дані)
- [Як використовувати](#як-використовувати)

## Постановка задачі

Моєю цілю було створити та реалізувати структуру моделі, яка б могла допомогти спортсменам визначити якісь їх розминки перед тренуваням.
### Основна ідея виглядає так:
1) У спортсмена на правій руці є смарт годинику з акселерометром, на початку тренування він повинен зайти в додаток та розпочати запис.
2) Після закінчення розминки, людина закінчує запис.
3) Потім алгоритм розділяє розминку на окремі вправи та класифікує їх, і після цього видає звіт розминки.
4) У звіті написаний список вправ, у порядку їх виконання, яких виконав спортсмен, час їх виконання та сумарний результат, таблиця з запланованими вправами та часом виділеним на кожну вправу сумарно під час розминки.
5) По результатам сумарної таблиці, алгорит видає висновок: людина зробила все правильно та може продовжувати тренування, чи потрібно зробити додаткові вправи для розминки.

Так як це просто структура, то в якості розминки у нас буде короткий список вправ - присідання, махи руками в плечевому суставі та удари правою рукою по груші.
P.S. Впарви вибирались так, щоб я міг самостійн зібрати достятній дата сет і не сильно виснажуватись під час цього.

В якості алгоритма визначення якості розминки виступає умова "Для того щоб гарно розігріти групу м'язів, потрібно виконувати певну вправу мінімум 12 сек", я її придумав сам, тому не судіть жорстоко, для більш якісних умов потрібно проконсультуватися з тренерами.

## Про файли

Файли:
- fitness_workbook.ipynb, Це мій робочий ноутбук в якому записаний поцес аналізу даних, підбору фільтрів, процес вибору фічей для моделей, підбор моделей та аргументація їх використання, також там є графіки, на які я дивився під час роботи, та основні думки, які в мене виникали. Нажаль,так як це робочий ноутбук, то він не дуже чисто оформлений(
- fitness_final.ipynb, Це готовий ноутбук, який забирає останній файл з тестової папки (неначе це тільки що отриманий файл), проводить його перевірку на коректність, проводить фільтрацію, розбиває на сигнал на окремі вправи, класифікує їх та видає кінцеві звіти.
- fitness_final_one.ipynb, Це той самий fitness_final.ipynb, але собраний в одній клітині та без коментарів, він створений для того щоб вибрати файл, нажати одну кнопку та отримати звіти.

Папки:
- Fitness report, Папка в яку потрапляють звіти тренувань після виконання коду. (В ній вже є 8 звітів, для 8 тестових файлів з тестової папки, вони з підписом old)
- Error report, Папка в яку потрапляють звіти помилок, якщо файл поданий в модель не відповідає певним умовам. (Аналогічно там вже є файл приклад з підписом old)
- Graphs, Папка з візуалізаціями предіктів моделі до тестових даних.
- test_file, Папка з тестовими даними
- models, Папка з моделями, які я отримав під час роботи.
- train_file, Папка з файлами, які використовувались для навчання моделі
- error_file, Папка з зіпсованими фалами, їх відловила система пошуку помилок під час тренування моделі.
- Error report train, Папка з звітами до фалів до файлів зазначених в error_file.

## Використані дані

### Сбір даних
У моєму випадку збір даних виконувався телефоном прив'язаним до руку в місці, де повинен бути браслет (пробував тримати телефон в лодоні екраном в руку, все продовжувало працювати).
Дані записувались за допомогою додатку Accelerometer Meter (ви його можете занайти в Google Play) на налаштуваннях позначених на малюнку.

<img src="app_settings.jpg" style="width=200; height=200" />

Тобто модель використоє значення датчика в 	$m/s^{2}$ та при частоті 50 Гц (я пробував змінювати частоти (там довга історія з спробами переписати цей додаток для автоматизації збору даних) в діапазоні від 5 - 300 Гц, якщо частота менше 30 Гц алгоритм починає працювати погано, а при частотах більше 100 Гц потрібно зманити розмір вікна на етапі виділення активних зон і алгоритм продовжить працювати гарно, в іншому випадку будуть помилки).


## Як використовувати

Мною передбачалось, що ви захочите перевірити отримані мною звіти та отримати іх самостійно, чи подивитись на дані під час первних стадій їх обробки, тому за допомогою файлів test_file та fitness_final.ipynb чи fitness_final_one.ipynb (у цьому файлі графіків не має) ви можете це зробити.
Просто запустіть код вибравши файл з в тестовій папці.
Вибір файлу можно зробити 2 способами:
- Змінити номер фалу який зчитає алгоритм у строці (обережно тут зворотня номерація):
file_name = os.listdir("test_file")[**-1**]
- Чи змінивши назву файлу, так щоб він став на останнє місце в алфавітному порядку.

**Також ви можете спробувати алгоритм на свої даних**, для цього запишіть файл, як сказано в [використаних даних](#використані-дані), додайте його в train_file та виберіть його. Алгоритм досить гарно відпрацьовував на даних людей, яких не було в трейні, але це були молоді спортивні люди.

На цьому в мене все, приємного дня та користування моїм проєктом))
Слава Україні!!!
