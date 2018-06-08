## EKATТE

### Какво е EKATТE?
###### Списък на населените места в Р. България, сортирани по области и общини, по ЕКАТТЕ (Единен Класификатор на Административно-Териториалните и Териториалните Единици), във вид, удобен за използване в PHP среда.

### Кой би имал нужда от подобно нещо?
###### Разработчици, работещи върху системи за:
* Електронна търговия
* Застрахователни услуги
* Всичко останало, при разработката на което се налага работа с населени места (от потребители и/или администратори).

### Какво е необходимо, за да използвам EKATTE?
* php >= 5.3.0 - поради наличието на namespaces, closures и др.
* ext-zip - PHP Plug-in за работа със .zip архиви. Функцията update на EKATTE изтегля .zip архив от сайта на НСИ (Национален Статистически Институт), който трябва да бъде обработен.
* nuovo/spreadsheet-reader - PHP пакет, спомагащ за прочитане на файлове от Excel. В архива от НСИ се съдържат електронни таблици (spreadsheets) в .xls формат, които трябва да бъдат прочетени, след което, информацията от тях да бъде приведена в удобен за използване вид.
 

### Как да инсталирам EKATTE?
##### Ако разполагате с инсталиран composer глобално (сиреч, composer е инсталиран в системата Ви), просто изпълнете следната команда (в команден ред - Terminal, Konsole, Command Prompt) в папката на проекта (``` cd път/до/папката ``` или в Windows ``` cd път\до\папката ```), в който искате да използвате EKATTE:
```bash
    composer require abpam/ekatte
```

##### Ако работите с composer локално (composer архив във всеки отделен проект) - 
```bash
    composer.phar require abpam/ekatte 
```
##### в папката на проекта.

### Как да използвам EKATTE?
##### На първо време, трябва да обновите базата до последните данни на НСИ. Това става с помощта на файла vendor/abpam/ekatte/updater.php. През команден ред стигнете до папката ```път/до/проекта/vendor/abpam/ekatte``` и изпълнете:
``` php updater.php ```
##### Ако всичко е наред, резултатът трябва да е:
```
100%[==================================================>]

Обновено...
```




---
### _Забележка:_
_Ако по време на обновяването скриптът върне грешка, свързана с кодировка на файловете (encoding), това е проблем при прочитането на .xls файловете от пакета SpreadsheetReader на nuovo. Този проблем се разрешава като ред № 917 на файл /vendor/nuovo/spreadsheet-reader/php-excel-reader/excel_reader2.php бъде заменен от:_
```php
    $this->setUTFEncoder('iconv');
```

_на:_
```php
    $this->setUTFEncoder('mb');
```
---




##### Укажете на проекта си да използва composer файловете, ако вече не сте го направили:
```php 
<?php
    require_once __DIR__.'/vendor/autoload.php'; 
```
##### след което може да получим списък на всички области в Р. България:

```php
    use Ekatte\Oblast;

    $oblast = new Oblast();
    $oblastiList = $oblast->getOblastiList();

    var_dump($oblastiList);
```
##### Функцията getOblastiList() служи за извличане на списък с всички области. Връща двумерен масив. Първият индекс представлява отделните области, а вторият - параметрите на всяка област:
* 0 - трибуквен код на областта;
* 1 - номер по Екатте, на населеното място, което е център на дадената област;
* 2 - име на областта;
* 3 - код на района към който принадлежи областта;
* 4 - номер на документа, регистриращ последната административно-териториална промяна в областта; 
* 5 - пореден номер на областта по азбучен ред).


##### Функцията getOblast() служи за извличане на информация за определена област. Изисква трибуквения код на областта, подаден като аргумент на функцията, във вид на текстов низ (string) и връща едномерен масив, индексите на когото съответсват на горе посочените. Така, например, ако искаме да получим информация за област „Велико Търново“ е нужно да повикаме функцията по следния начин:
```php
    use Ekatte\Oblast;

    $oblast = new Oblast();
    $oblast = $oblast->getOblast('VTR');
    
    var_dump($oblast);
```

##### В долу-горе същия формат работят и функциите, касаещи общините. Ако имаме нужда от списък на всички общини в дадена област, ще трябва да си послужим с getObshtiniList(), като подадем като аргумент името на областта, списък на общините в която искаме да получим. Например, ако ни трябва списък на общините в област „Враца“:
```php
    use Ekatte\Obshtina;
    
    $obshtini = new Obshtina();
    $obshtini = $obshtini->getObshtiniList('VRC');
    
    var_dump($obshtini);
```

##### Функцията връща двумерен масив, първият индекс на когото показва определена община, а вторият - параметрите ѝ:
* 0 - 5-символен код на общината, състоящ се от трибуквеното означение на областта, в която се намира, заедно с двуцифрен номер, указващ поредността на общината в областта;
* 1 - код по ЕКАТТЕ на населеното място, център на общината;
* 2 - име на общината;
* 3 - категория на общината (критериите за определяне на категорията са описани в Закона за Административно-Териториалното Устройство на Р. България);
* 4 - номер на документа, регистриращ последната административно-териториална промяна в общината;
* 5 - пореден номер по азбучен ред;

##### По аналогия на функциите, касаещи областите, при общините също имаме функция, връщата информация за конкретна община - getObshtina(). Функцията приема един аргумент (5-символния код на общината) като текстов низ (string) и връща едномерен масив, индексът на когото съответства на втория индекс, върнат от getObshtiniList(): 
```php
    $obshtina = new Obshtina();
    $obshtina = $obshtina->getObshtina('LOV02');
    
    var_dump($obshtina);
```

#####  И не на последно място, да разясним функциите, касаещи селищата (градове и села). За да получим списък на селищата в дадена община, можем да си послужим с функцията getSelishtaList(), която приема два аргумента - трибуквен код на област, като текстов низ (string), и 5-символен код на община, също като текстов низ (string). Например, за да получим списък на всички населени места в общ. Свищов, обл. Велико Търново:
```php
    $selishta = new Selishte();
    $selishta = $selishta->getSelishtaList('VTR', 'VTR28');
    
    var_dump($selishta);
```

##### Трябва да се отбележи, че вторият аргумент на функцията не е задължителен. Сиреч, ако искаме да получим списък на всички населени места в дадена област (например Бургас), не е нужно да указваме община:

```php
    $selishta = new Selishte();
    $selishta = $selishta->getSelishtaList('BGS');
    
    var_dump($selishta);
```

##### Двете функции връщат масив, чиито индекси отговарят на номера на населеното място по ЕКАТТЕ, а двата елемента са, съответно името на селището и номер на кметството.


##### Ако искаме по-подробна информация за дадено селище, може да се послужим с функцията getSelishte(), която приема един аргумент (текстов низ - string) - идентификационния номер на селището по ЕКАТТЕ.

##### Искане за информация относно село Плешивец (Видинско) би изглеждало по следния начин:
```php
    $selishte = new Selishte();
    $selishte = $selishte->getSelishte('56753');
```

##### тъй като идентификационния номер по ЕКАТТЕ на село Плешивец, обл. Видин е 56753.



#### Това е базовата информация, нужна за работа с пакета. За допълнителни въпроси, пишете на e-mail avramov.emil@gmail.com или в Twitter на @ABPAM.