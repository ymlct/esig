#### Приложение под Android для работы с ЭП  

##### Функции, ожидаемые от приложения  

- возможность создания ЭП для различных файлов, хранящихся в памяти устройства; созданная ЭП должна соответствовать ГОСТ  
- возможность работы с разными ключевыми носителями (кроме носителей, не   поддерживаемых криптопровайдером)  
- возможность установки, просмотра, удаления сертификатов (корневых, промежуточных)  

***

##### Использованные источники информации  

- ЖТЯИ.00101-02 96 02. Руководство программиста. JavaCSP.pdf  
- Форум КриптоПро
- Java Cryptography Architecture (JCA) Reference Guide  
- примеры функций из приложения ACSPclient.apk 
- примеры функций из библиотеки samples-2.0.41971-release-210521.jar 

***

##### Компоненты, необходимые  для работы приложения:  

###### Криптопровайдер  
Криптопровайдером для приложения является [КриптоПро JavaCSP](https://www.cryptopro.ru/products/csp/jcsp) .  Его бибилиотеки добавлены в проект (директория libs/) и являются зависимостью для кода приложения.  

######  Приложение ACSP  
Панель управления в виде отдельного приложения ( ACSP.apk ). Является поставщиком криптосервисов предоставляя необходимый функционал. Также необходимо для работы с ключевыми носителями.  

<img width="657" alt="Pasted image 20230208035416" src="https://user-images.githubusercontent.com/59499236/217384910-d2c6c61d-296d-4e7f-a988-31fa83d7d19a.png">


###### Приложения для работы с некоторыми марками ключевых носителей  

- [JaCarta Service](https://play.google.com/store/apps/details?id=ru.aladdin.jacarta_service&hl=en_US&gl=US)  - необходимо для работы с ключевыми носителями JaCarta2 
- [Панель управления Рутокен](https://play.google.com/store/apps/details?id=ru.rutoken&hl=en_US&gl=US) - необходимо для работы с ключевыми носителями Рутокен  

***

##### Ход работы  

Что сделано в ходе выполнения задания:  
- реализована функция формирования открепленной ЭП по хэшу данных (с добавлением значения подписи от хэша и сертификата подписанта, но без цепочки сертификатов, с помощью которых проверяется подпись получателем документа). Подпись, сформированная приложением, сравнивалась с подписью, сформированной проверенным средством формирования ЭП, с помощью [ASN.1 декодера](http://lapo.it/asn1js/), - разница только в отсутствующих в составе подписи промежуточных сертификатах. Код с коментариями в файле `PKS7.kt `   
- реализованы функции работы (чтение, запись) с хранилищем сертификатов для работы приложения (в т.ч. для построения цепочки при формировании ЭП). Код  в файле `CertStoreUtil.kt 
- реализован GUI структуры MVVM. Поведение View компонентов определяется состояниями, которые формирует ViewModel. В рамках некоторых состояний передаются данные, необходимые View для их отображения  

Что осталось сделать:  
- добавить функционал удаления сертификатов из хранилища
- GUI: вынести параметры view (из xml) в styles, привести компоненты к одному стилю оформления, добавить locales; на основном экране (про подпись) связать кнопки из 3й секции с функциями ViewModel и реализовать эти функции; реализовать отображение установленных сертификатов разными списками
- применить DI (Hilt)  
- задачи, указанные ниже

Задачи, для которых пока не нашлось решения:  
- при формировании ЭП с помощью реализованной функции необходимо добавлять промежуточные сертификаты в один раздел структуры ЭП, а корневые сертификаты в другой (TrustAnchor). При этом на данном этапе удалось наладить взаимодействие только с одним хранилищем сертификатов. Если сложить все сертификаты (корневые и промежуточные) в это хранилище, возникнет вопрос - как при извлечении сертификатов из хранилища узнать какой промежуточный, а какой корневой? Поэтому, с целью обхода данной ситуации, необходимо создать второе хранилище. Информация как это реализовать была, но проверить пока не успел.  
- при построении цепочки сертификатов во время формирования ЭП бросается исключение, указывающее на невозможность проверить всю цепь. Возможно ошибка связана с первой проблемой. 
