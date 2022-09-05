Библиотека формирования и проверки электронной подписи для СМЭВ 2.х и 3.х.
=========
### Библиотека предназначена для создания и проверки ЭП СМЭВ 2.х и 3.х.     
   
В соответствии с МР СМЭВ 2.х для сообщений сущест следующие виды ЭП:
* **ЭП-СП** SOAP-сообщения **С** вложениями в формате PKCS#7. Для формирования и проверки **ЭП-СП** вложений SOAP-сообщения библтотека предоставляет утилитарный класс ru.smevx.crypto.smev2.cms.PKCS7Utils.
* **ЭП-СП** SOAP-сообщения **БЕЗ** вложений в формате XMLDSign. Для формирования и проверки **ЭП-СП** вложений библтотека предоставляет утилитарный класс ru.smevx.crypto.smev2.xmldsig.XmlDSignUtils.
* **ЭП-ОВ** SOAP-сообщения в формате WS-Security. Для формирования и проверки ЭП-ОВ библтотека предоставляет утилитарный класс ru.smevx.crypto.smev2.wss.WSSecurityUtils.

В соответствии с МР СМЭВ 3.х для сообщений сущест следующие виды ЭП:
* **ЭП-СП** (//AttachmentHeaderList/AttachmentHeader/SignaturePKCS7 или //RefAttachmentHeaderList/RefAttachmentHeader/SignaturePKCS7) вложений в формате PKCS#7. Для формирования и проверки ЭП-СП вложений сообщения формата СМЭВ 3.х библтотека предоставляет утилитарный класс ru.smevx.crypto.smev3.cms.PKCS7Utils.
* **ЭП-СП** (//SenderProvidedRequestData/PersonalSignature) и ЭП-ОВ (//CallerInformationSystemSignature) xml-элементов сообщений в формате ru.smevx.crypto.smev3.xmldsig.XmlDSignUtils. Для формирования и проверки ЭП-СП и ЭП-ОВ xml-элементов сообщения формата СМЭВ 3.х библтотека предоставляет утилитарный класс XmlDSignUtils.

Проверить корректность ЭП в соответствии с МР можно с помощью тестовых сервисов СМЭВ: 
* для СМЭВ 2.х  
 [Сервис проверки подписи](https://smev.gosuslugi.ru/portal/services-tools.jsp)  
 [SignatureTool](http://smev-mvf.test.gosuslugi.ru:7777/gateway/services/SID0003064/1.001)  
* для СМЭВ 3.х  
 [Проверка XML-сообщения на соответствие схемам сервиса СМЭВ](https://smev3.gosuslugi.ru/portal/checkxmlform.jsp)  

### 1. Необходимое ПО
* JDK 1.8.x;
* КриптоПро JCP версии 2.0.x.

### 2. Установка КриптоПро JCP версии 2.х
* Устанавливаем JDK 1.8.x, если она не установлена в системе.
* Скачиваем или получаем иным способом нужную версию КриптоПРО 2.0.х (библиотека сестировалась с КриптоПРО JCP версией **2.0.39267**) http://www.cryptopro.ru/.
* Распаковываем архив.
* Переходим в папку в которую распаковался архив.
* Запускаем setup_console.bat либо setup_console.sh, формат команды запуска:  
```
[скрипт-нисталлятор] [путь до jre] [ключ] [владелец лицензии]
sh sudo install.sh /opt/jdk-jcp/jre XXXXX-XXXXX-XXXXX-XXXXX-XXXXX "\"Название организации, на которую выдана лицензия\""
```
* Копируем все файлы из папки dependencies, внутри папки в которую распаковался архив, в патку [путь до jre]/lib/ext.
* Проверяем, что [путь до jre]/lib/ext находится библиотека xmlsec-1.5.х.jar (**не xmlsec-1.4.х.jar**).

### 3. Установка ключевых контейнеров 
* Установливаем ключи в хранилище с помощью панели управления Крипто-Про, которая находится в папке, в которую распаковался архив, ControlPane.sh / ControlPane.bat:  
-- в Linux, OSX можно скопировть папку контейнера /var/opt/cprocsp/keys/${USER};  
-- в Windows - %папка_пользователя\Local Settings\Application Data\Crypto Pro.  

##### Возникавшие проблемы
Ошибки при установке версии из-за того, что не хватало прав записи файлов в:
* папку с jre;
* /var/tmp;
* /var/opt/cprocsp.

Внятных сообщений об ошибке инсталлятор не выдает, выдает стек исключения с IOException. Кроме этого падал какой-то внутренний тест JCP, проверяющий корректность установки.

##### Действия при истечении лицензии:
* деинсталлировать КриптоПРО и убедиться, что все файлы КриптоПРО удалились из JRE;
* удалить ~/.java/.userPrefs/ru;
* удалить $jre/.systemPrefs/ru (где $jre - папка, в которой находится jre);
* установить Крипто-Про JCP.

### 4. Сборка библиотеки
Сборка осуществляется командой:
```
./gradlew clean build publishToMavenLocal
```
При сборке проекта выполнение тестов по-умолчанию **выключено**!

Для сборки проекта с тестами необходимо:  
1 Установить тестовый ключевой контейнер КритпроПРО в сооветствии с [п.3](#3.-установка-ключевых-контейнеров).  
2 В файле ./src/test/java/ru/smevx/crypto/test/SignVerifyTest.java
  в строках  
  ```
    privateKey = (PrivateKey) keyStore.getKey("Alias", "Password".toCharArray());
    cert = (X509Certificate) keyStore.getCertificate("Alias");
  ``` 
  заменить значения *Alias* и *Password* на параметры доступа к тестовому ключевому контейнеру.  

Сборка с выполнением тестов осуществляется командой:   
```
./gradlew clean build test publishToMavenLocal
```
 
После сборки в директории **./build/libs** должен появится файил **smevx-crypto-<версия проекта>.jar**.

### Полезные ссылки по теме:

[Форум КриптоПро](https://www.cryptopro.ru/forum2/default.aspx?g=posts&t=8840)  
[Apache CXF и ЭЦП для SOAP сообщений СМЭВ](http://oldcouncil.blogspot.ru/2013/03/apache-cxf-soap.html)  
[Побег из Крипто Про. Режиссерская версия, СМЭВ-edition](https://habrahabr.ru/post/282225/)  
[СМЭВ 3. Электронная подпись сообщений на Java и КриптоПро](https://habrahabr.ru/company/alfa/blog/350158/)  
[NET 4.x Интеграция с ГИС ЖКХ и подпись SOAP без Крипто .NET и stunnel](https://www.cyberforum.ru/web-services-wcf/thread1954969.html)
[Документация ALT Linux - не Debian](https://docs.altlinux.org/ru-RU/alt-workstation/10.1/html-single/alt-workstation/index.html#idm45893044104560)
[ГОСТ в OpenSSL - ALT Linux, CentOS и Ubuntu](https://www.altlinux.org/%D0%93%D0%9E%D0%A1%D0%A2_%D0%B2_OpenSSL)
[Установка для РуТокен - Аналогично](https://forum.rutoken.ru/topic/3173/)
[Установка CryptoPro libphpcades для Ubuntu 18.04 x64 и PHP 7.2.24 патч - не обязательно](https://docs.cryptopro.ru/cades/phpcades/phpcades-install)
