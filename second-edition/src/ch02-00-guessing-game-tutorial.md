# Гра "відгадай число"

Розпочнемо вивчення Rust зі спільної розробки проекта! Цей розділ ознайомить вас
із кількома поширеними концепціями Rust, показавши вам, як вони використовуються
у реальній програмі. Ви дізнаєтеся про `let`, `match`, методи, асоційовані 
функції, використання зовнішніх ящиків (crate), і навіть більше! Наступні 
розділи розкриють більше деталей цих ідей. У цьому розділі, ви займатиметеся
основами.

Ми розв'язуватимемо класичну задачу для програмістів-початківців: гру "відгадай
число". Умови такі: програма генерує випадкове ціле число між 1 та 100. Потім
пропонує гравцю відгадати. Ввівши спробу, вона скаже, чи число більше або менше
за неї. Якщо відгадано правильно, гра виведе вітання і припиниться.

## Початок нового проекту

Щоб розпочати новий проект, перейдіть до папки *projects*, яку ви створили у 
Розділі 1, і створіть новий проект за допомогою Cargo, ось так:

```text
$ cargo new guessing_game --bin
$ cd guessing_game
```

Перша команда, `cargo new`, приймає першим параметром ім'я проекту 
(`guessing_game`). Прапорець `--bin` каже Cargo зробити двійковий проект, так
само, як і в Розділі 1. Друга команда переходить до теки нового проекту.

Переглянемо щойно створений файл *Cargo.toml*:

<span class="filename">Filename: Cargo.toml</span>

```toml
[package]
name = "guessing_game"
version = "0.1.0"
authors = ["Ваше ім'я <адреса@поштовий.сайт>"]

[dependencies]
```

Якщо інформація про автора, отримана Cargo з вашого середовища, неправильна,
виправіть це і знову збережіть файл.

Як ви вже бачили у Розділі 1, `cargo new` створює програму "Hello, world!". 
Подивимося, що міститься у файлі *src/main.rs*:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    println!("Hello, world!");
}
```

Скомпілюймо цю програму “Hello, world!” і запустимо її за один крок за допомогою
команди `cargo run`: 

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
     Running `target/debug/guessing_game`
Hello, world!
```

Команда `run` стає в нагоді, коли треба швидко розвивати проект, і ця гра є
якраз таким проектом: ви хочемо швидко тестувати кожну ітерацію перед тим, як
переходити до наступної.

Знову відкрийте файл *src/main.rs*. Весь код ви будете писати у ньому.

## Обробляємо здогадку

Перша частина програми буде запитувати у користувача ввести здогадку, обробляти
те, що він увів, і перевіряти, чи ввів він дані у потрібній формі. Для початку,
дозволимо користувачеві ввести здогадку. Введіть код з Роздруку 2-1 до 
*src/main.rs*.

<figure>
<span class="filename">Filename: src/main.rs</span>

```rust,ignore
use std::io;

fn main() {
    println!("Відгадай число!");

    println!("Введіть здогадку.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Не вдалося прочитати рядок");

    println!("Ваша здогадка: {}", guess);
}
```

<figcaption>

Роздрук 2-1: Код, що отримує здогадку у користувача і виводить її

</figcaption>
</figure>

Цей код містить багато інформації, тому розбиратимемо його шматок за шматком. 
Щоб отримати, що ввів користувач, і вивести результат, нам треба ввести 
бібліотеку `io` (ввід/вивід, англ. input/output) в область видимості. Бібліотека
`io` входить до стандартної бібліотеки, яка зветься `std`.

```rust,ignore
use std::io;
```

Типово, Rust вводить в область видимості тільки декілька типів у 
[*прелюдії* (*prelude*)][prelude]<!-- ignore -->. Якщо типу, який ви хочете 
використати, нема у прелюдії, вам доведеться явно вносити цей тип у область 
видимості за допомогою виразу `use`.  Використання бібліотеки `std::io` надає 
вам ряд корисних речей, пов'язаних із введенням-виведенням, включно з 
функціональністю для користувацького вводу.

[prelude]: https://doc.rust-lang.org/std/prelude/

Як ви бачили у Розділі 1, функція `main` - це точка входу в програму:

```rust,ignore
fn main() {
```

Синтаксична конструкція `fn` проголошує нову функцію, `()` показує, що вона не 
має параметрів, і `{` починає тіло функції.

Як ви вже знаєте з Розділу 1, `println!` - це макрос, що виводить стрічку на
екран:

```rust,ignore
println!("Відгадай число!");

println!("Введіть здогадку.");
```

Цей код просто виводить повідомлення, що це за гра і запит введення у 
користувача.

### Зберігання значень у змінних

Тепер створімо місце для зберігання вводу користувача:

```rust,ignore
let mut guess = String::new();
```

Тепер програма стає цікавішою! В цьому коротенькому рядку відбувається багато
всього. Зверніть увагу на оператор `let`, що використовується для створення 
*змінних* (*variable*). Ось інший приклад:

```rust,ignore
let foo = bar;
```

Цей рядок створить нову змінну з назвою `foo` і зв'яже (bind) її зі значенням 
`bar`. У Rust, змінні типово є сталими (immutable). Наступний 
приклад показує, як використовується `mut` перед іменем змінної, зробити її 
несталою (mutable):

```rust
let foo = 5;     // стала
let mut bar = 5; // нестала
```

> Зверніть увагу: синаксична конструкція `//` починає коментар, що продовжується
> до кінця рядка. Rust ігнорує весь вміст коментаря.

Тепео ви знаєте, що `let mut guess` створить несталу змінну, на ім'я `guess`. З 
іншого боку знаку рівності `=` знаходиться значення, з яким зв'язується `guess`, 
а саме - результат виклику `String::new`, функції, що повертає новий екземпляр 
(instance) стрічки String. [`String`][string]<!-- ignore --> - це тип стрічки, 
що надається стандартною бібліотекою; це кодовані в UTF-8 шматки тексту, які 
можна нарощувати.

[string]: ../std/string/struct.String.html

Синаксична конструкція `::` в рядку `::new` позначає, що `new` - це *асоційована 
функція* типу `String`. Асоційована функція є втіленою (implemented) на типі, в
цьому випадку `String`, а не на конкретному екземплярі `String`. В деяких мовах
це зветься *статичним методом*.

Ця функція `new` створює нову, пусту `String`. Функція `new` зустрінеться вам у
багатьох типах, оскільки це звичайна назва функції, що створює нове значення
певного виду.

Підсумуємо: рядок `let mut guess = String::new();` створив несталу змінну, зараз
прив'язану до нового, пустого екземпляру `String`. Уф!

Згадаймо, що ми додали функціональність введення/виведення зі стандартної 
бібліотеки за допогою `use std::io;` у першому рядку програми. Тепер ми 
викличемо асоційовану функцію, `stdin`, з `io`.

```rust,ignore
io::stdin().read_line(&mut guess)
    .expect("Не вдалося прочитати рядок.");
```

Якби на початку програми не було рядка `use std::io`, ми могли б записати цей
виклик функції як `std::io::stdin`. Функциія `stdin` повертає екземпляр
[`std::io::Stdin`][iostdin]<!-- ignore -->; цей тип репрезентує дескриптор
(handle) стандартного потоку введення терміналу.

[iostdin]: ../std/io/struct.Stdin.html

Наступна частина коду, `.read_line(&mut guess)`, викликає метод
[`read_line`][read_line]<!-- ignore --> на дескрипторі стандартного потоку 
введення, щоб отримати, що ввів користувач. Також ми передаємо `read_line` один 
параметр: `&mut guess`.

[read_line]: ../std/io/struct.Stdin.html#method.read_line

`read_line` бере все, що користувач вводить у стандартний потік введення, і 
розміщує це в стрічці, тому приймає цю стрічку аргументом. Цей аргумент мусить
бути несталим, щоб метод змінив вміст стрічки, додавши те, що ввів користувач.

`&` позначає, що цей аргумент - *посилання* (*reference*), що дає вам можливість
надати кільком частинам вашого коду доступ до одного фрагменту даних без 
кількаразового копіювання цих даних. Посилання - складна тема, але одна з 
основних переваг Rust полягає в безпеці і легкості використання посилань. Для
завершення цієї програми вам не знадобляться особливо детальні знання про 
посилання; в Розділі 4 будуть надані докладніші пояснення. Поки що, все, що вам
треба знати - що посилання, як і зміні, типово є сталими. Тому необхідно писати 
`&mut guess`, а не просто `&guess`, щоб зробити її несталою.

Ми ще не закінчили розбиратися із цим рядком коду. Хоча це один рядок тексту, це
лише перша частина єдиного логічного рядка коду. Друга частина - це ось цей 
метод:

```rust,ignore
.expect("Не вдалося прочитати рядок");
```

Коли ви викликаєте метод за допомогою синтаксичної конструкції `.foo()` часто 
має сенс розпочати новий рядок і додати відступи, щоб розділити довгі рядки. Ми
могли б написати цей код так:

```rust,ignore
io::stdin().read_line(&mut guess).expect("Не вдалося прочитати рядок");
```

Але довгий рядок важко читати, тому краще поділити його на два рядки для виклику
двох методів. Тепер розглянемо, що цей рядок робить.

### Керування потенційною невдачую за допомогою типу `Result`

Як вже було сказано, `read_line` виводить те, що ввів користувач, у стрічку, яку
ми їй передали, але також повертає значення - в цьому випадку, 
[`io::Result`][ioresult]<!-- ignore -->. В стандартній бібліотеці Rust є кілька 
типів, що звуться Result: як звичайний [`Result`][result]<!-- ignore -->, так і
спеціалізовані версії в підмодулях, як-от `io::Result`.

[ioresult]: ../std/io/type.Result.html
[result]: ../std/result/enum.Result.html

Типи `Result` - це [*переліки* (*enumeration*)][enums]<!-- ignore -->, які часто
звуть просто *енум*. Перелік - це тип, який може набувати значення з визначеного
набору, і ці значення звуться *варіантами* переліку. Розділ 6 детальніше розкриє
роботу енумів.

[enums]: ch06-00-enums.html

`Result` має варіанти `Ok` та `Err`. `Ok` показує, що операція була вдалою, і
всередині варіанту `Ok` знаходиться успішно згенероване значення. `Err` позначає
відмову, і містить інформацію, як і чому операція була невдалою.

Призначення типів `Result` - кодувати інформацію про обробку помилок. Значення 
типу `Result`, які інших типів, мають методи, визначені для них. Екземпляр 
`io::Result` має [метод `expect`][expect]<!-- ignore -->, який можна викликати.
Якщо цей екземпляр `io::Result` має значення `Err`, то `expect` викличе аварійне
завершення програми і виведе повідомлення, яке ви передали параметром `expect`.
Якщо метод `read_line` поверне `Err`, це, швидше за все, буде результатом 
помилки в операційній системі. Якщо цей екземлпяр `io::Result` має значення 
`Ok`, `expect` візьме значення, яке знаходиться в `Ok`, і поверне тільки це 
значення, щоб їм можна було скористатися. В цьому випадку це значення - 
кількість байтів, введених користувачем до стандартного потоку.

[expect]: ../std/result/enum.Result.html#method.expect

Якщо ми не викличемо `expect`, програма скомпілюється, проте ми отримаємо
попередження:

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
src/main.rs:10:5: 10:39 warning: unused result which must be used,
#[warn(unused_must_use)] on by default
src/main.rs:10     io::stdin().read_line(&mut guess);
                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```

Rust попереджає, що ми не використали значення `Result`, повернуте з 
`read_line`, що означає, що програма не обробила можливу помилку. Правильний 
спосіб пригнітити попередження - власне, обробити помилку, але оскільки ми 
просто хочемо, щоб програма аварійно завершилася, якщо виникне проблема, можна
скористатися `expect`. Ви дізнаєтеся про те, як відновити роботу програми при
помилці, у Розділі 9.

### Вивід значень за допомогою заповнювачів `println!`

Якщо не враховувати закриваючої фігурної дужки, поки щолишився лише один рядок,
який ми ще не обговорили, а саме:

```rust,ignore
println!("Ваша здогадка: {}", guess);
```

Цей рядок виводить стрічку, в якій ми зберегли те, що ввів користувач. Символи
`{}` - це заповнювач, який замінюється значенням. Ви можете вивести більше 
одного значення за допомогою `{}`: перший набір `{}` замінюється першим 
значенням після форматної стрічки, другий набір - другим значенням і так далі.
Вивід багатьох значень за один виклик `println!` виглядатиме так:

```rust
let x = 5;
let y = 10;

println!("x = {} і y = {}", x, y);
```

Цей код виведе `x = 5 і y = 10`.

### Тестування першої частини

Протестуймо першу частину гри "відгадай число". Запустіть її за допомогою
`cargo run`:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
     Running `target/debug/guessing_game`
Відгадай число!
Введіть здогадку.     
6
Ваша здогадка: 6
```

На цей момент, перша частина гри завершена: ви отримуємо дані з клавіатури і
виводимо їх.

## Генерація таємного числа

Тепер нам треба згенерувати таємне число, яке користувач пробуватиме відгадати.
Таємне число має бути різним кожного разу, щоб у гру було цікаво грати більше
одного разу. Використаймо випадкове число від 1 до 100, щоб гра була не надто
складною. Rust поки що не має функціональності для генерації випадкових чисел у
стандартній бібліотеці; натомість команда Rust надає [ящик `rand`][randcrate].

[randcrate]: https://crates.io/crates/rand

### Використання ящика для отримання додаткової функціональності

Згадаймо, що *ящик* - це пакет коду на Rust. Проект, який ми робимо - 
*двійковий ящик* і є виконанним. Ящик `rand` - *бібліотечний ящик*, і містить
код, призначений для використання в інших програмах.

Використання зовнішніх ящиків - найсильніший бік Cargo. Перед тим, як писати 
код, що використовує `rand`, ми маємо модифікувати файл *Cargo.toml*, додавши 
туди ящик `rand` як залежність. Відкрийте цей файл і додайте такий рядок униз,
під заголовком секції `[dependencies]` ("залежності"), яку Cargo створив для 
вас:

<span class="filename">Файл: Cargo.toml</span>

```toml
[dependencies]

rand = "0.3.14"
```

У файлі *Cargo.toml* все, що йде після заголовку - частина секції, що 
продовжується до початку нової секції. У секції `[dependencies]` ви повідомляєте
Cargo, від яких зовнішніх ящиків залежить і які версії цих ящиків вам потрібні. 
У цьому випадку, ми зазначаємо ящик `rand` зі семантичним версіонуванням 
`0.3.14`. Cargo розуміє [семантичне версіонування][semver]<!-- ignore --> 
(також зване *SemVer*), що є стандартом для запису номеру версії. Запис `0.3.14`
насправді є скороченням для `^0.3.14`, що означає "будь-яка версія, що має 
публічний API, сумісний із версією 0.3.14".

[semver]: http://semver.org

Тепер, не змінюючи коду, побудуємо проект, як показано в Роздруку 2-2:

<figure>

```text
$ cargo build
    Updating registry `https://github.com/rust-lang/crates.io-index`
 Downloading rand v0.3.14
 Downloading libc v0.2.14
   Compiling libc v0.2.14
   Compiling rand v0.3.14
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
```

<figcaption>

Роздрук 2-2: Вивід команди `cargo build` після додавання ящика rand як
залежність.

</figcaption>
</figure>

Ви можете побачити інші номери версій (але вони будуть сумісні з кодом завдяки
SemVer!), і рядки можуть бути в іншому порядку.

Тепер, коли ми маємо зовнішню залежність, Cargo витягає останні версії всього,
що нам треба, з *реєстру*, тобто копії даних з [Crates.io][cratesio]. На 
crates.io в екосистемі Rust люди викладають свої проекти з відкритим кодом, щоб
ними могли скористатися інші.

[cratesio]: https://crates.io

Після оновлення реєстру, Cargo перевіряє розділ `[dependencies]` і завантажує 
ті, яких у вас бракує. В цьому випадку, хоча ми вказали тільки залежність від
`rand`, Cargo також завантажив копію `libc`, тому що `rand` залежить від `libc`.
Після завантаження, Rust компілює їх і потім компілює проект.

Якщо ви знову запустите `cargo build`, не зрозбивши жодних змін, ви не отримаєте
ніякої відповіді. Cargo знає, що він вже завантажив і скомпілював залежності, а
ви не змінили нічого у своєму коді, тому він теж не буде перекомпільований.
Оскільки роботи у Cargo немає, він просто завершує роботу. Якщо ви відкриєте
файл *src/main.rs*, зробите тривіальну зміну, збережете і знову побудуєте, то
побачите тільки один рядок виводу:

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
```

Цей рядок показує, що Cargo обробив тільки вашу дрібну зміну до файлу 
*src/main.rs*. Залежності не змінилися, і Cargi знає, що може заново використати
те, що він вже завантажив і скомпілював. Він перебудовує тільки вашу частину 
коду.

#### Файл *Cargo.lock* гарантує відтворюваність побудови

Cargo має механізм, що гарантує, що гарантує однаковість побудувати проекту 
кожного разу, коли ви чи хтось інший будує ваш код: Cargo використає тільки ті
версії залежностей, які ви зазначили, доки ви не вкажете інші. Наприклад, якщо
наступного тижня вийде `rand` версії `0.3.15`, що міститиме важливе виправлення
вади, але також регресію, що зіпсує ваш код?

Відповідь на цю задачу - файл *Cargo.lock*, що створюється при першому запуску
`cargo build` і розміщується у теці *guessing_game*. Коли ви збираєте проект
вперше, Cargo визначає всі версії залежностей, що відповідають критерію, і 
записує їх у файл *Cargo.lock*. Коли ви пізніше збиратимете проект, Cargo 
побачить, що файл *Cargo.lock* існує, і використає версії, зазначені там, а не
буде наново визначати версії. Це дозволяє вам автоматично мати відтворювану 
збірку. Іншими словами, ваш проект залишиться на версії `0.3.14`, доки ви самі
не захочете оновити її, завдяки файлу *Cargo.lock*.

#### Оновлення ящика для отримання нової версії

Коли ви *хочете* оновити ящик, Cargo надає іншу команду, `update`, яка:
1. Ігнорує файл *Cargo.lock* і визначає всі останні версії, що відповідають
специфікаціям в *Cargo.toml*.
1. Якщо це вдалося, Cargo напише ці версії до файлу *Cargo.lock*.

Але типово Cargo шукатиме тільки  версії, більші за `0.3.0` і менші `0.4.0`. 
Якщо ящик `rand` вийшов у двох нових версіях, `0.3.15` та `0.4.0`, ви побачите
таке, запустивши `cargo update`:

```text
$ cargo update
    Updating registry `https://github.com/rust-lang/crates.io-index`
    Updating rand v0.3.14 -> v0.3.15
```

Також можна звернути увагу на зміну у файлі *Cargo.lock* - версія ящика `rand`,
яку ви використовуєте, тепер `0.3.15`.

Якщо вам потрібен `rand` версії `0.4.0` чи будь-якої версії у гілці `0.4.x`, вам
доведеться оновити файл *Cargo.toml*, щоб він мав такий рядок:
```toml
[dependencies]

rand = "0.4.0"
```

Наступного разу, коли ви запустите `cargo build`, Cargo оновить реєстр доступних
ящиків і переоцінить вимоги до `rand` відповідно до вказаної вами нової версії.

Можна багато сказати про [Cargo][doccargo]<!-- ignore --> і 
[його екосистему][doccratesio]<!-- ignore -->, яка обговорюється у Розділі 14,
але поки що цього знати достатньо. Cargo робить використання бібліотек дуже 
простим, що дозволяє растацеанцям писати менші проекти, зібрані з кількох 
пакетів.

[doccargo]: http://doc.crates.io
[doccratesio]: http://doc.crates.io/crates-io.html

### Генерація випадкового числа

Почнемо *використовувати* `rand`. Наступний крок - оновити *src/main.rs*, як
показано в Роздруку 2-3:

<figure>
<span class="filename">Файл: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use rand::Rng;

fn main() {
    println!("Відгадай число!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("Таємне число: {}", secret_number);

    println!("Будь ласка, введіть здогадку:");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Не вдалося прочитати рядок");

    println!("Ваша здогадка: {}", guess);
}
```

<figcaption>

Роздрук 2-3: Зміни в коді, необхідні для генерації випадкового числа

</figcaption>
</figure>

Ми додаємо рядок `extern crate rand;` на початок, що дає Rust знати, що ми 
будемо використовувати зовнішню залежність. Це, на додачу, виконує функцію,
еквівалентну `use rand`, так що тепер ми можемо викликати будь-що з ящика 
`rand`, додавши префікс `rand::`.

Далі ми додаємо ще один рядок із `use` - `use rand::Rng`. `Rng` - це риса, що 
визначає методи, втілені генераторами випадкових чисел, і ця риса має бути в 
області видимості, щоб можна було використовувати ці методи. Риси детальніше
розкриваються у Розділі 10.

Також, ми додали ще два рядки всередині. Функція `rand::thread_rng` дає нам
конкретний генератор випадкових чисел, який ми будемо використовувати: локальний
для чинного потоку виконання і ініціалізований операційною системою. Далі, ви
викликаємо метод `gen_range` цього генератора. Цей метод визначений рисою `Rng`,
яку ми внесли до області видимості за допомогою оператора `use rand::Rng`. Метод
`gen_range` приймає два числа параметрами і генерує випадкове число між ними,
включно з нижньою межею, але виключаючи верхню, тому треба вказувати `1` та 
`101`, щоб отримати число між 1 та 100 включно.

Знання, які риси використати і які функції та методи викликати з ящика не є 
чимось таким, що треба знати напам'ять. Інструкції з використання ящика є в 
документації цього ящика. Ще одна корисна особливість Cargo полягає в тому, що
запуск команди `cargo doc --open` побудує на вашому комп'ютері документацію, 
надану всіма залежностями, і відкриє її у вашому переглядачі. Якщо вам цікава
інша функціональність ящика `rand`, запустіть `cargo doc --open` і клацніть
`rand` на боковій панелі ліворуч.

Другий рядок, який ми додали до коду, виводить таємне число. Це корисно, коли
ми розробляємо програму, щоб можна було перевірити її роботу, але ми видалимо її
у фінальній версії. Буде не дуже цікаво, якщо програма виводитиме відповідь \
одразу по запуску!

Спробуємо запустити програму кілька разів:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
     Running `target/debug/guessing_game`
Відгадай число!
Таємне число: 7
Будь ласка, введіть здогадку:
4
Ваша здогадка: 4
$ cargo run
     Running `target/debug/guessing_game`
Відгадай число!
Таємне число: 83
Будь ласка, введіть здогадку:
5
Ваша здогадка: 5
```

Ви маєте побачити різні випадкові числа, і вони мають бути між 1 та 100. Чудово!

## Порівняння здогадки з таємним числом

Тепер, коли ми маємо введене користувачем і випадкове числа, ми можемо їх 
порівняти. Цей крок показано в Роздруку 2-4:

<figure>
<span class="filename">Файл: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("Таємне число: {}", secret_number);

    println!("Будь ласка, введіть здогадку:");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Не вдалося прочитати рядок");

    println!("Ваша здогадка: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less    => println!("Замало!"),
        Ordering::Greater => println!("Забагато!"),
        Ordering::Equal   => println!("Ви перемогли!"),
    }
}
```

<figcaption>

Роздрук 2-4: Різні дії в залежності від порівняння двох чисел

</figcaption>
</figure>

Перше нововведення - ще один `use`, який вводить тип `std::cmp::Ordering` зі
стандартної бібліотеки до області видимості. `Ordering` ("впорядкування") - це 
ще один енум, як і `Result`, але варіанти `Ordering` такі: `Less` ("менше"),
`Greater` ("більше"), and `Equal` ("рівно"). Це три можливі результати при
порівнянні двох чисел.

Потім ми додємо п'ять нових рядків
Then we add five new lines at the bottom that use the `Ordering` type:

```rust,ignore
match guess.cmp(&secret_number) {
    Ordering::Less    => println!("Замало!"),
    Ordering::Greater => println!("Забагато!"),
    Ordering::Equal   => println!("Ви перемогли!"),
}
```

Метод `cmp` порівнює два значення і може бути викликаний для всього, що можна
порівнювати. Він приймає параметром посилання на те, що ви хочете порівнювати із
ним: тут він порівнює `guess` із `secret_number`. `cmp` повертає варіант з енуму
`Ordering`, який ми внесли у область видимості за допомогою оператора `use`. Ми
скористалися виразом [`match`][match]<!-- ignore -->, щоб визначити, що робити
далі залежно від варіанту `Ordering`, що його повернув виклик `cmp` зі 
значеннями `guess` та `secret_number`.

[match]: ch06-02-match.html

Вираз `match` збирається з *рукавів*. Рукав складається зі *зразка* (*pattern*) 
та коду, який буде виконано, якщо значення, передане виразу `match`, відповідає
зразку цього рукава. Rust бере значення, передане `match`, і по черзі переверіє
зразки рукавів. Конструкція `match` і зразки - потужні засоби мови Rust, які
дозволяють вам виражати різноманітні ситуації, які можуть трапитися вам при 
програмуванні, і допомагають переконатися, що ви обробили їх усіх. Детально ці
можливості будуть розглянуті в Розділах 5 і 18, відповідно.

Let’s walk through an example of what would happen with the `match` expression
used here. Say that the user has guessed 50, and the randomly generated secret
number this time is 38. When the code compares 50 to 38, the `cmp` method will
return `Ordering::Greater`, because 50 is greater than 38. `Ordering::Greater`
is the value that the `match` expression gets. It looks at the first arm’s
pattern, `Ordering::Less`, but the value `Ordering::Greater` does not match
`Ordering::Less`. So it ignores the code in that arm and moves to the next arm.
The next arm’s pattern, `Ordering::Greater`, *does* match
`Ordering::Greater`! The associated code in that arm will execute and print
`Забагато!` to the screen. The `match` expression ends because it has no need to
look at the last arm in this particular scenario.

However, the code in Listing 2-4 won’t compile yet. Let’s try it:

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
error[E0308]: mismatched types
  --> src/main.rs:23:21
   |
23 |     match guess.cmp(&secret_number) {
   |                     ^^^^^^^^^^^^^^ expected struct `std::string::String`, found integral variable
   |
   = note: expected type `&std::string::String`
   = note:    found type `&{integer}`

error: aborting due to previous error
Could not compile `guessing_game`.
```

The core of the error states that there are *mismatched types*. Rust has a
strong, static type system. However, it also has type inference. When we wrote
`let guess = String::new()`, Rust was able to infer that `guess` should be a
`String` and didn’t make us write the type. The `secret_number`, on the other
hand, is a number type. A few number types can have a value between 1 and 100:
`i32`, a 32-bit number; `u32`, an unsigned 32-bit number; `i64`, a 64-bit
number; as well as others. Rust defaults to an `i32`, which is the type of
`secret_number` unless we add type information elsewhere that would cause Rust
to infer a different numerical type. The reason for the error is that Rust will
not compare a string and a number type.

Ultimately, we want to convert the `String` the program reads as input into a
real number type so we can compare it to the guess numerically. We can do
that by adding the following two lines to the `main` function body:

<span class="filename">Файл: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("Таємне число: {}", secret_number);

    println!("Будь ласка, введіть здогадку:");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Не вдалося прочитати рядок");

    let guess: u32 = guess.trim().parse()
        .expect("Please type a number!");

    println!("Ваша здогадка: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less    => println!("Замало!"),
        Ordering::Greater => println!("Забагато!"),
        Ordering::Equal   => println!("Ви перемогли!"),
    }
}
```

The two new lines are:

```rust,ignore
let guess: u32 = guess.trim().parse()
    .expect("Please type a number!");
```

We create a variable named `guess`. But wait, doesn’t the program
already have a variable named `guess`? It does, but Rust allows us to
*shadow* the previous value of `guess` with a new one. This feature is often
used in similar situations in which you want to convert a value from one type
to another type. Shadowing lets us reuse the `guess` variable name rather than
forcing us to create two unique variables, like `guess_str` and `guess` for
example. (Chapter 3 covers shadowing in more detail.)

We bind `guess` to the expression `guess.trim().parse()`. The `guess` in the
expression refers to the original `guess` that was a `String` with the input in
it. The `trim` method on a `String` instance will eliminate any whitespace at
the beginning and end. `u32` can only contain numerical characters, but the
user must press the Return key to satisfy `read_line`. When the user presses
Return, a newline character is added to the string. For example, if the user
types 5 and presses return, `guess` looks like this: `5\n`. The `\n` represents
“newline,” the return key. The `trim` method eliminates `\n`, resulting in just
`5`.

The [`parse` method on strings][parse]<!-- ignore --> parses a string into some
kind of number. Because this method can parse a variety of number types, we
need to tell Rust the exact number type we want by using `let guess: u32`. The
colon (`:`) after `guess` tells Rust we’ll annotate the variable’s type. Rust
has a few built-in number types; the `u32` seen here is an unsigned, 32-bit
integer. It’s a good default choice for a small positive number. You’ll learn
about other number types in Chapter 3. Additionally, the `u32` annotation in
this example program and the comparison with `secret_number` means that Rust
will infer that `secret_number` should be a `u32` as well. So now the
comparison will be between two values of the same type!

[parse]: ../std/primitive.str.html#method.parse

The call to `parse` could easily cause an error. If, for example, the string
contained `A👍%`, there would be no way to convert that to a number. Because it
might fail, the `parse` method returns a `Result` type, much like the
`read_line` method does as discussed earlier in “Handling Potential Failure
with the Result Type” on page XX. We’ll treat this `Result` the same way by
using the `expect` method again. If `parse` returns an `Err` `Result` variant
because it couldn’t create a number from the string, the `expect` call will
crash the game and print the message we give it. If `parse` can successfully
convert the string to a number, it will return the `Ok` variant of `Result`,
and `expect` will return the number that we want from the `Ok` value.

Let’s run the program now!

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
     Running `target/guessing_game`
Guess the number!
Таємне число: 58
Будь ласка, введіть здогадку:
  76
Ваша здогадка: 76
Забагато!
```

Nice! Even though spaces were added before the guess, the program still figured
out that the user guessed 76. Run the program a few times to verify the
different behavior with different kinds of input: guess the number correctly,
guess a number that is too high, and guess a number that is too low.

We have most of the game working now, but the user can make only one guess.
Let’s change that by adding a loop!

## Allowing Multiple Guesses with Looping

The `loop` keyword gives us an infinite loop. Add that now to give users more
chances at guessing the number:

<span class="filename">Файл: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("Таємне число: {}", secret_number);

    loop {
        println!("Будь ласка, введіть здогадку:");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("Не вдалося прочитати рядок");

        let guess: u32 = guess.trim().parse()
            .expect("Please type a number!");

        println!("Ваша здогадка: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Замало!"),
            Ordering::Greater => println!("Забагато!"),
            Ordering::Equal   => println!("Ви перемогли!"),
        }
    }
}
```

As you can see, we’ve moved everything into a loop from the guess input prompt
onward. Be sure to indent those lines another four spaces each, and run the
program again. Notice that there is a new problem because the program is doing
exactly what we told it to do: ask for another guess forever! It doesn’t seem
like the user can quit!

The user could always halt the program by using the keyboard shortcut `Ctrl-C`.
But there’s another way to escape this insatiable monster that we mentioned in
the `parse` discussion in “Comparing the Guesses” on page XX: if the user
enters a non-number answer, the program will crash. The user can take advantage
of that in order to quit, as shown here:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
     Running `target/guessing_game`
Guess the number!
Таємне число: 59
Будь ласка, введіть здогадку:
45
Ваша здогадка: 45
Замало!
Будь ласка, введіть здогадку:
60
Ваша здогадка: 60
Забагато!
Будь ласка, введіть здогадку:
59
Ваша здогадка: 59
Ви перемогли!
Будь ласка, введіть здогадку:
quit
thread 'main' panicked at 'Please type a number!: ParseIntError { kind: InvalidDigit }', src/libcore/result.rs:785
note: Run with `RUST_BACKTRACE=1` for a backtrace.
error: Process didn't exit successfully: `target/debug/guess` (exit code: 101)
```

Typing `quit` actually quits the game, but so will any other non-number input.
However, this is suboptimal to say the least. We want the game to automatically
stop when the correct number is guessed.

### Quitting After a Correct Guess

Let’s program the game to quit when the user wins by adding a `break`:

<span class="filename">Файл: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("Таємне число: {}", secret_number);

    loop {
        println!("Будь ласка, введіть здогадку:");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("Не вдалося прочитати рядок");

        let guess: u32 = guess.trim().parse()
            .expect("Please type a number!");

        println!("Ваша здогадка: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Замало!"),
            Ordering::Greater => println!("Забагато!"),
            Ordering::Equal   => {
                println!("Ви перемогли!");
                break;
            }
        }
    }
}
```

By adding the `break` line after `Ви перемогли!`, the program will exit the loop
when the user guesses the secret number correctly. Exiting the loop also means
exiting the program, because the loop is the last part of `main`.

### Handling Invalid Input

To further refine the game’s behavior, rather than crashing the program when
the user inputs a non-number, let’s make the game ignore a non-number so the
user can continue guessing. We can do that by altering the line where `guess` is
converted from a `String` to a `u32`:

```rust,ignore
let guess: u32 = match guess.trim().parse() {
    Ok(num) => num,
    Err(_) => continue,
};
```

Switching from an `expect` call to a `match` expression is how you generally
move from crash on error to actually handling the error. Remember that `parse`
returns a `Result` type, and `Result` is an enum that has the variants `Ok` or
`Err`. We’re using a `match` expression here, like we did with the `Ordering`
result of the `cmp` method.

If `parse` is able to successfully turn the string into a number, it will return
an `Ok` value that contains the resulting number. That `Ok` value will match the
first arm’s pattern, and the `match` expression will just return the `num` value
that `parse` produced and put inside the `Ok` value. That number will end up
right where we want it in the new `guess` variable we’re creating.

If `parse` is *not* able to turn the string into a number, it will return an
`Err` value that contains more information about the error. The `Err` value
does not match the `Ok(num)` pattern in the first `match` arm, but it does match
the `Err(_)` pattern in the second arm. The `_` is a catchall value; in this
example, we’re saying we want to match all `Err` values, no matter what
information they have inside them. So the program will execute the second arm’s
code, `continue`, which means to go to the next iteration of the `loop` and ask
for another guess. So effectively, the program ignores all errors that `parse`
might encounter!

Now everything in the program should work as expected. Let’s try it by running
`cargo run`:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
     Running `target/guessing_game`
Guess the number!
Таємне число: 61
Будь ласка, введіть здогадку:
10
Ваша здогадка: 10
Замало!
Будь ласка, введіть здогадку:
99
Ваша здогадка: 99
Забагато!
Будь ласка, введіть здогадку:
foo
Будь ласка, введіть здогадку:
61
Ваша здогадка: 61
Ви перемогли!
```

Awesome! With one tiny final tweak, we will finish the guessing game: recall
that the program is still printing out the secret number. That worked well for
testing, but it ruins the game. Let’s delete the `println!` that outputs the
secret number. Listing 2-5 shows the final code:

<figure>
<span class="filename">Filename: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    loop {
        println!("Будь ласка, введіть здогадку:");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("Не вдалося прочитати рядок");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("Ваша здогадка: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Замало!"),
            Ordering::Greater => println!("Забагато!"),
            Ordering::Equal   => {
                println!("Ви перемогли!");
                break;
            }
        }
    }
}
```

<figcaption>

Listing 2-5: Complete code of the guessing game

</figcaption>
</figure>

## Summary

At this point, you’ve successfully built the guessing game! Congratulations!

This project was a hands-on way to introduce you to many new Rust concepts:
`let`, `match`, methods, associated functions, using external crates, and more.
In the next few chapters, you’ll learn about these concepts in more detail.
Chapter 3 covers concepts that most programming languages have, such as
variables, data types, and functions, and shows how to use them in Rust.
Chapter 4 explores ownership, which is a Rust feature that is most different
from other languages. Chapter 5 discusses structs and method syntax, and
Chapter 6 endeavors to explain enums.
