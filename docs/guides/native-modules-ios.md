# Нативные модули iOS

!!!info ""

    Native Module и Native Components - это наши стабильные технологии, используемые в унаследованной архитектуре. Они будут устаревшими в будущем, когда новая архитектура станет стабильной. Новая архитектура использует [Turbo Native Module](the-new-architecture-pillars-turbomodules.md) и [Fabric Native Components](the-new-architecture-pillars-fabric-components.md) для достижения аналогичных результатов.

Добро пожаловать в раздел "Нативные модули для iOS". Пожалуйста, начните с чтения [Native Modules Intro](native-modules-intro.md), чтобы узнать, что такое нативные модули.

## Создание нативного модуля календаря

В следующем руководстве вы создадите нативный модуль `CalendarModule`, который позволит вам получить доступ к API календаря Apple из JavaScript. В конце вы сможете вызывать `CalendarModule.createCalendarEvent('Dinner Party', 'My House');` из JavaScript, вызывая нативный метод, который создает событие календаря.

### Установка

Чтобы начать работу, откройте проект iOS в вашем приложении React Native в Xcode. Вы можете найти свой проект iOS в приложении React Native здесь:

<figure markdown>
![Установка](native-modules-ios-open-project.png)
<figcaption>Изображение места, где можно найти ваш проект iOS</figcaption>
</figure>

Мы рекомендуем использовать Xcode для написания нативного кода. Xcode создан для разработки iOS, и его использование поможет вам быстро устранить мелкие ошибки, такие как синтаксис кода.

### Создание файлов пользовательского нативного модуля

Первым шагом будет создание заголовка и файлов реализации нашего основного пользовательского нативного модуля. Создайте новый файл `RCTCalendarModule.h`.

<figure markdown>
![Создание файлов пользовательского нативного модуля](native-modules-ios-add-class.png)
<figcaption>Образ создания файла пользовательского нативного модуля в той же папке, что и AppDelegate</figcaption>
</figure>

и добавьте к нему следующее:

```objectivec
//  RCTCalendarModule.h
#import <React/RCTBridgeModule.h>
@interface RCTCalendarModule : NSObject <RCTBridgeModule>
@end
```

Вы можете использовать любое имя, которое подходит для создаваемого вами нативного модуля. Назовите класс `RCTCalendarModule`, поскольку вы создаете нативный модуль календаря. Поскольку в ObjC нет поддержки пространств имен на уровне языка, как в Java или C++, принято добавлять к имени класса подстроку. Это может быть аббревиатура имени вашего приложения или имени вашей инфры. RCT, в данном примере, означает React.

Как вы можете видеть ниже, класс CalendarModule реализует протокол `RCTBridgeModule`. Нативный модуль - это класс Objective-C, который реализует протокол `RCTBridgeModule`.

Далее приступим к реализации нативного модуля. Создайте соответствующий файл реализации, `RCTCalendarModule.m`, в той же папке и включите в него следующее содержимое:

```objectivec
// RCTCalendarModule.m
#import "RCTCalendarModule.h"

@implementation RCTCalendarModule

// To export a module named RCTCalendarModule
RCT_EXPORT_MODULE();

@end
```

### Имя модуля

Пока что ваш нативный модуль `RCTCalendarModule.m` включает только макрос `RCT_EXPORT_MODULE`, который экспортирует и регистрирует класс нативного модуля в React Native. Макрос `RCT_EXPORT_MODULE` также принимает необязательный аргумент, который указывает имя, под которым модуль будет доступен в вашем JavaScript-коде.

Этот аргумент не является строковым литералом. В примере ниже передается `RCT_EXPORT_MODULE(CalendarModuleFoo)`, а не `RCT_EXPORT_MODULE("CalendarModuleFoo")`.

```objectivec
// To export a module named CalendarModuleFoo
RCT_EXPORT_MODULE(CalendarModuleFoo);
```

Затем к нативному модулю можно получить доступ в JS следующим образом:

```ts
const { CalendarModuleFoo } = ReactNative.NativeModules;
```

Если вы не укажете имя, имя модуля JavaScript будет соответствовать имени класса Objective-C, с удаленными префиксами "RCT" или "RK".

Давайте последуем приведенному ниже примеру и вызовем `RCT_EXPORT_MODULE` без каких-либо аргументов. В результате модуль будет открыт для React Native под именем `CalendarModule`, так как это имя класса Objective-C с удаленным RCT.

```objectivec
// Without passing in a name this will export the native module name as the Objective-C class name with “RCT” removed
RCT_EXPORT_MODULE();
```

Затем к нативному модулю можно получить доступ в JS следующим образом:

```ts
const { CalendarModule } = ReactNative.NativeModules;
```

### Экспорт нативного метода в JavaScript

React Native не будет экспортировать методы нативного модуля в JavaScript, если это не указано явно. Это можно сделать с помощью макроса `RCT_EXPORT_METHOD`. Методы, написанные в макросе `RCT_EXPORT_METHOD`, являются асинхронными, поэтому тип возврата всегда void. Чтобы передать результат от метода `RCT_EXPORT_METHOD` в JavaScript, вы можете использовать обратные вызовы или эмитировать события (об этом ниже). Давайте продолжим и создадим нативный метод для нашего нативного модуля `CalendarModule` с помощью макроса `RCT_EXPORT_METHOD`. Назовем его `createCalendarEvent()` и пока что пусть он принимает аргументы name и location в виде строк. Опции типа аргументов будут рассмотрены в ближайшее время.

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)name location:(NSString *)location)
{
}
```

> Обратите внимание, что макрос `RCT_EXPORT_METHOD` не нужен в TurboModules, если только ваш метод не использует преобразование аргументов RCT (см. типы аргументов ниже). В конечном итоге React Native удалит `RCT_EXPORT_MACRO,` поэтому мы не рекомендуем использовать `RCTConvert`. Вместо этого вы можете выполнить преобразование аргументов в теле метода.

Прежде чем вы создадите функциональность метода `createCalendarEvent()`, добавьте консольный лог в метод, чтобы вы могли подтвердить, что он был вызван из JavaScript в вашем приложении React Native. Используйте API `RCTLog` из React. Давайте импортируем этот заголовок в верхней части вашего файла, а затем добавим вызов журнала.

```objectivec
#import <React/RCTLog.h>
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)name location:(NSString *)location)
{
 RCTLogInfo(@"Pretending to create an event %@ at %@", name, location);
}
```

### Синхронные методы

Вы можете использовать `RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD` для создания синхронного нативного метода.

```objectivec
RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD(getName)
{
return [[UIDevice currentDevice] name];
}
```

Возвращаемый тип этого метода должен быть объектного типа (id) и должен быть сериализуемым в JSON. Это означает, что хук может возвращать только значения nil или JSON (например, NSNumber, NSString, NSArray, NSDictionary).

На данный момент мы не рекомендуем использовать синхронные методы, поскольку синхронный вызов методов может привести к значительным потерям производительности и внести ошибки, связанные с потоками, в ваши родные модули. Кроме того, обратите внимание, что если вы решите использовать `RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD`, ваше приложение больше не сможет использовать отладчик Google Chrome. Это связано с тем, что синхронные методы требуют, чтобы виртуальная машина JS делила память с приложением. Для отладчика Google Chrome React Native работает внутри JS VM в Google Chrome и асинхронно взаимодействует с мобильными устройствами через WebSockets.

### Протестируйте то, что вы создали

На данном этапе вы создали базовый каркас для вашего нативного модуля в iOS. Проверьте это, обратившись к родному модулю и вызвав его экспортированный метод в JavaScript.

Найдите место в вашем приложении, где вы хотели бы добавить вызов метода `createCalendarEvent()` родного модуля. Ниже приведен пример компонента `NewModuleButton`, который вы можете добавить в свое приложение. Вы можете вызвать нативный модуль внутри функции `NewModuleButton` `onPress()`.

```ts
import React from 'react';
import { NativeModules, Button } from 'react-native';

const NewModuleButton = () => {
    const onPress = () => {
        console.log(
            'We will invoke the native module here!'
        );
    };

    return (
        <Button
            title="Click to invoke your native module!"
            color="#841584"
            onPress={onPress}
        />
    );
};

export default NewModuleButton;
```

Чтобы получить доступ к нативному модулю из JavaScript, сначала нужно импортировать `NativeModules` из React Native:

```ts
import { NativeModules } from 'react-native';
```

Затем вы можете получить доступ к нативному модулю `CalendarModule` из `NativeModules`.

```ts
const { CalendarModule } = NativeModules;
```

Теперь, когда у вас есть нативный модуль CalendarModule, вы можете вызвать нативный метод `createCalendarEvent()`. Ниже он добавлен в метод `onPress()` в `NewModuleButton`:

```ts
const onPress = () => {
    CalendarModule.createCalendarEvent(
        'testName',
        'testLocation'
    );
};
```

Последний шаг - пересобрать приложение React Native, чтобы у вас был доступен самый свежий нативный код (с вашим новым нативным модулем!). В командной строке, где находится приложение react native, выполните следующее :

```shell
npx react-native run-ios
```

### Создание по мере итерации

По мере того, как вы будете работать по этим руководствам и итерировать свой нативный модуль, вам нужно будет перестроить свое приложение, чтобы получить доступ к последним изменениям из JavaScript. Это связано с тем, что код, который вы пишете, находится в нативной части вашего приложения. В то время как metro bundler React Native может следить за изменениями в JavaScript и перестраивать JS bundle на лету для вас, он не будет делать этого для нативного кода. Поэтому, если вы хотите протестировать последние изменения в нативном коде, вам нужно пересобрать его с помощью команды `npx react-native run-ios`.

### Recap✨

Теперь вы должны быть в состоянии вызвать метод `createCalendarEvent()` вашего нативного модуля на JavaScript. Поскольку вы используете `RCTLog` в функции, вы можете убедиться, что ваш родной метод вызывается, включив [режим отладки в вашем приложении](https://reactnative.dev/docs/debugging#chrome-developer-tools) и посмотрев на консоль JS в Chrome или отладчике мобильных приложений Flipper. Вы должны увидеть сообщение `RCTLogInfo(@"Pretending to create an event %@ at %@", name, location);` при каждом вызове метода нативного модуля.

<figure markdown>
![Recap✨](native-modules-ios-logs.png)
<figcaption>Изображение журналов iOS в Flipper</figcaption>
</figure>

На данном этапе вы создали нативный модуль iOS и вызвали его метод из JavaScript в вашем приложении React Native. Вы можете прочитать дальше, чтобы узнать больше о таких вещах, как типы аргументов, которые принимает метод вашего нативного модуля, и как настроить обратные вызовы и обещания в вашем нативном модуле.

## За пределами нативного модуля календаря

### Улучшенный экспорт нативного модуля

Импорт нативного модуля путем извлечения его из `NativeModules`, как описано выше, немного неудобен.

Чтобы избавить потребителей вашего нативного модуля от необходимости делать это каждый раз, когда они хотят получить доступ к вашему нативному модулю, вы можете создать JavaScript-обертку для модуля. Создайте новый файл JavaScript с именем `NativeCalendarModule.js` со следующим содержимым:

```ts
/**
* This exposes the native CalendarModule module as a JS module. This has a
* function 'createCalendarEvent' which takes the following parameters:

* 1. String name: A string representing the name of the event
* 2. String location: A string representing the location of the event
*/
import { NativeModules } from 'react-native';
const { CalendarModule } = NativeModules;
export default CalendarModule;
```

Этот файл JavaScript также становится хорошим местом для добавления любой функциональности на стороне JavaScript. Например, если вы используете систему типов, такую как TypeScript, вы можете добавить сюда аннотации типов для вашего нативного модуля. Хотя React Native еще не поддерживает безопасность типов Native to JS, с этими аннотациями типов весь ваш JS-код будет безопасен для типов. Эти аннотации также облегчат вам переход на безопасные для типов нативные модули в будущем. Ниже приведен пример добавления безопасности типов в модуль `Calendar`:

```ts
/**
 * This exposes the native CalendarModule module as a JS module. This has a
 * function 'createCalendarEvent' which takes the following parameters:
 *
 * 1. String name: A string representing the name of the event
 * 2. String location: A string representing the location of the event
 */
import { NativeModules } from 'react-native';
const { CalendarModule } = NativeModules;
interface CalendarInterface {
    createCalendarEvent(
        name: string,
        location: string
    ): void;
}
export default CalendarModule as CalendarInterface;
```

В других файлах JavaScript вы можете обратиться к родному модулю и вызвать его метод следующим образом:

```ts
import NativeCalendarModule from './NativeCalendarModule';
NativeCalendarModule.createCalendarEvent('foo', 'bar');
```

!indo ""

    Обратите внимание, это предполагает, что место, куда вы импортируете `CalendarModule`, находится в той же иерархии, что и `CalendarModule.js`. Пожалуйста, обновите относительный импорт при необходимости.

### Типы аргументов

Когда метод нативного модуля вызывается на JavaScript, React Native преобразует аргументы из JS-объектов в их объектные аналоги Objective-C/Swift. Так, например, если метод нативного модуля Objective-C принимает NSNumber, в JS вам нужно вызвать метод с числом. React Native выполнит преобразование за вас. Ниже приведен список типов аргументов, поддерживаемых методами нативного модуля, и их JavaScript-эквивалентов.

| Objective-C                                   | JavaScript         |
| --------------------------------------------- | ------------------ |
| NSString                                      | string, ?string    |
| BOOL                                          | boolean            |
| NSNumber                                      | ?boolean           |
| double                                        | number             |
| NSNumber                                      | ?number            |
| NSArray                                       | Array, ?Array      |
| NSDictionary                                  | Object, ?Object    |
| RCTResponseSenderBlock                        | Function (success) |
| RCTResponseSenderBlock, RCTResponseErrorBlock | Function (failure) |
| RCTPromiseResolveBlock, RCTPromiseRejectBlock | Promise            |

!!!info ""

    Следующие типы поддерживаются в настоящее время, но не будут поддерживаться в TurboModules. Пожалуйста, избегайте их использования.

    -   Функция (отказ) -> RCTResponseErrorBlock
    -   Число -> NSInteger
    -   Число -> CGFloat
    -   Число -> float

Для iOS вы также можете писать собственные методы модуля с любым типом аргументов, который поддерживается классом `RCTConvert` (смотрите [RCTConvert](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTConvert.h) для подробностей о том, что поддерживается). Все вспомогательные функции RCTConvert принимают на вход значение JSON и отображают его в собственный тип или класс Objective-C.

### Экспорт констант

Родной модуль может экспортировать константы, переопределив родной метод `constantsToExport()`. Ниже `constantsToExport()` переопределяется и возвращает Dictionary, содержащий свойство имени события по умолчанию, к которому можно получить доступ в JavaScript следующим образом:

```objectivec
- (NSDictionary *)constantsToExport
{
 return @{ @"DEFAULT_EVENT_NAME": @"New Event" };
}
```

Затем к константе можно получить доступ, вызвав `getConstants()` на родном модуле в JS следующим образом:

```ts
const {
    DEFAULT_EVENT_NAME,
} = CalendarModule.getConstants();
console.log(DEFAULT_EVENT_NAME);
```

Технически, можно получить доступ к константам, экспортируемым в `constantsToExport()` непосредственно из объекта `NativeModule`. Это больше не будет поддерживаться в TurboModules, поэтому мы призываем сообщество перейти на описанный выше подход, чтобы избежать необходимой миграции в будущем.

!!!info ""

    Обратите внимание, что константы экспортируются только во время инициализации, поэтому если вы измените значения `constantsToExport()` во время выполнения, это не повлияет на среду JavaScript.

Для iOS, если вы переопределите `constantsToExport()`, вам также следует реализовать `+ requiresMainQueueSetup`, чтобы сообщить React Native, что ваш модуль должен быть инициализирован в главном потоке, до выполнения любого кода JavaScript. В противном случае вы увидите предупреждение, что в будущем ваш модуль может быть инициализирован в фоновом потоке, если вы явно не откажетесь от этого с помощью `+ requiresMainQueueSetup:`. Если вашему модулю не требуется доступ к UIKit, то на `+ requiresMainQueueSetup` следует ответить NO.

### Обратные вызовы

Нативные модули также поддерживают уникальный вид аргумента - обратный вызов. Обратные вызовы используются для передачи данных из Objective-C в JavaScript для асинхронных методов. Они также могут быть использованы для асинхронного выполнения JS с нативной стороны.

Для iOS обратные вызовы реализованы с помощью типа `RCTResponseSenderBlock`. Ниже параметр обратного вызова `myCallBack` добавлен в `createCalendarEventMethod()`:

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title
                location:(NSString *)location
                myCallback:(RCTResponseSenderBlock)callback)

```

Затем вы можете вызвать обратный вызов в вашей собственной функции, предоставив любой результат, который вы хотите передать JavaScript в массиве. Обратите внимание, что `RCTResponseSenderBlock` принимает только один аргумент - массив параметров для передачи в обратный вызов JavaScript. Ниже вы передадите обратно ID события, созданного в предыдущем вызове.

!!!info ""

    Важно отметить, что обратный вызов не вызывается сразу после завершения родной функции - помните, что связь асинхронная.

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title location:(NSString *)location callback: (RCTResponseSenderBlock)callback)
{
 NSInteger eventId = ...
 callback(@[@(eventId)]);

 RCTLogInfo(@"Pretending to create an event %@ at %@", title, location);
}

```

Затем к этому методу можно получить доступ в JavaScript, используя следующее:

```ts
const onSubmit = () => {
    CalendarModule.createCalendarEvent(
        'Party',
        '04-12-2020',
        (eventId) => {
            console.log(
                `Created a new event with id ${eventId}`
            );
        }
    );
};
```

Родной модуль должен вызывать свой обратный вызов только один раз. Однако он может хранить обратный вызов и вызывать его позже. Этот паттерн часто используется для обертывания iOS API, требующих делегатов - см. пример в [`RCTAlertManager`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/CoreModules/RCTAlertManager.mm). Если обратный вызов так и не будет вызван, произойдет утечка памяти.

Существует два подхода к обработке ошибок с помощью обратных вызовов. Первый - следовать соглашению Node и рассматривать первый аргумент, переданный в массив обратного вызова, как объект ошибки.

```objectivec
RCT_EXPORT_METHOD(createCalendarEventCallback:(NSString *)title location:(NSString *)location callback: (RCTResponseSenderBlock)callback)
{
  NSNumber *eventId = [NSNumber numberWithInt:123];
  callback(@[[NSNull null], eventId]);
}
```

В JavaScript вы можете проверить первый аргумент, чтобы узнать, была ли передана ошибка:

```ts
const onPress = () => {
    CalendarModule.createCalendarEventCallback(
        'testName',
        'testLocation',
        (error, eventId) => {
            if (error) {
                console.error(`Error found! ${error}`);
            }
            console.log(`event id ${eventId} returned`);
        }
    );
};
```

Другой вариант - использовать два отдельных обратных вызова: onFailure и onSuccess.

```objectivec
RCT_EXPORT_METHOD(createCalendarEventCallback:(NSString *)title
                  location:(NSString *)location
                  errorCallback: (RCTResponseSenderBlock)errorCallback
                  successCallback: (RCTResponseSenderBlock)successCallback)
{
  @try {
    NSNumber *eventId = [NSNumber numberWithInt:123];
    successCallback(@[eventId]);
  }

  @catch ( NSException *e ) {
    errorCallback(@[e]);
  }
}
```

Затем в JavaScript вы можете добавить отдельный обратный вызов для ответов об ошибке и успехе:

```ts
const onPress = () => {
    CalendarModule.createCalendarEventCallback(
        'testName',
        'testLocation',
        (error) => {
            console.error(`Error found! ${error}`);
        },
        (eventId) => {
            console.log(`event id ${eventId} returned`);
        }
    );
};
```

Если вы хотите передавать JavaScript объекты, похожие на ошибки, используйте `RCTMakeError` из [`RCTUtils.h.`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTUtils.h) Сейчас это только передача JavaScript словаря в форме ошибки, но в будущем React Native планирует автоматически генерировать настоящие объекты JavaScript Error. Вы также можете предоставить аргумент `RCTResponseErrorBlock`, который используется для обратных вызовов ошибок и принимает `NSError \* object`. Обратите внимание, что этот тип аргумента не будет поддерживаться в TurboModules.

### Промисы

Нативные модули также могут выполнять обещания, что может упростить ваш JavaScript, особенно при использовании синтаксиса `async/await` в ES2016. Когда последним параметром метода нативного модуля является `RCTPromiseResolveBlock` и `RCTPromiseRejectBlock`, соответствующий JS-метод вернет объект JS Promise.

Рефакторинг приведенного выше кода для использования обещания вместо обратных вызовов выглядит следующим образом:

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title
                 location:(NSString *)location
                 resolver:(RCTPromiseResolveBlock)resolve
                 rejecter:(RCTPromiseRejectBlock)reject)
{
 NSInteger eventId = createCalendarEvent();
 if (eventId) {
    resolve(@(eventId));
  } else {
    reject(@"event_failure", @"no event id returned", nil);
  }
}

```

JavaScript-аналог этого метода возвращает Promise. Это означает, что вы можете использовать ключевое слово `await` в асинхронной функции для вызова этого метода и ожидания его результата:

```ts
const onSubmit = async () => {
    try {
        const eventId = await CalendarModule.createCalendarEvent(
            'Party',
            'my house'
        );
        console.log(
            `Created a new event with id ${eventId}`
        );
    } catch (e) {
        console.error(e);
    }
};
```

### Отправка событий на JavaScript

Нативные модули могут сигнализировать JavaScript о событиях, не вызывая их напрямую. Например, вы можете захотеть передать на JavaScript напоминание о том, что скоро произойдет событие календаря из родного приложения календаря iOS. Предпочтительным способом сделать это является подкласс `RCTEventEmitter`, реализовать `supportedEvents` и вызвать self `sendEventWithName`:

Обновите свой заголовочный класс, чтобы импортировать `RCTEventEmitter` и подкласс `RCTEventEmitter`:

```objectivec
//  CalendarModule.h

#import <React/RCTBridgeModule.h>
#import <React/RCTEventEmitter.h>

@interface CalendarModule : RCTEventEmitter <RCTBridgeModule>
@end

```

Код JavaScript может подписаться на эти события, создав новый экземпляр `NativeEventEmitter` вокруг вашего модуля.

Вы получите предупреждение, если будете тратить ресурсы без необходимости, испуская событие, пока нет слушателей. Чтобы избежать этого и оптимизировать нагрузку на ваш модуль (например, отписываясь от уведомлений, поступающих сверху, или приостанавливая фоновые задачи), вы можете переопределить `startObserving` и `stopObserving` в вашем подклассе `RCTEventEmitter`.

```objectivec
@implementation CalendarManager
{
  bool hasListeners;
}

// Will be called when this module's first listener is added.
-(void)startObserving {
    hasListeners = YES;
    // Set up any upstream listeners or background tasks as necessary
}

// Will be called when this module's last listener is removed, or on dealloc.
-(void)stopObserving {
    hasListeners = NO;
    // Remove upstream listeners, stop unnecessary background tasks
}

- (void)calendarEventReminderReceived:(NSNotification *)notification
{
  NSString *eventName = notification.userInfo[@"name"];
  if (hasListeners) {// Only send events if anyone is listening
    [self sendEventWithName:@"EventReminder" body:@{@"name": eventName}];
  }
}

```

### Threading

Если нативный модуль не предоставляет собственную очередь методов, он не должен делать никаких предположений о том, в каком потоке его вызывают. В настоящее время, если нативный модуль не предоставляет очередь методов, React Native создаст для него отдельную очередь GCD и вызовет его методы в ней. Обратите внимание, что это деталь реализации и может измениться. Если вы хотите явно предоставить очередь методов для нативного модуля, переопределите метод `(dispatch_queue_t) methodQueue` в нативном модуле. Например, если модуль должен использовать API iOS только для основного потока, он должен указать это через:

```objectivec
- (dispatch_queue_t)methodQueue
{
  return dispatch_get_main_queue();
}
```

Аналогично, если выполнение операции может занять много времени, нативный модуль может указать свою собственную очередь для выполнения операций. Опять же, в настоящее время React Native предоставляет отдельную очередь методов для вашего нативного модуля, но это деталь реализации, на которую не стоит полагаться. Если вы не предоставите собственную очередь методов, то в будущем долго выполняемые операции вашего нативного модуля могут заблокировать асинхронные вызовы, выполняемые в других несвязанных нативных модулях. Модуль `RCTAsyncLocalStorage` здесь, например, создает свою собственную очередь, чтобы очередь React не блокировалась в ожидании потенциально медленного доступа к диску.

```objectivec
- (dispatch_queue_t)methodQueue
{
 return dispatch_queue_create("com.facebook.React.AsyncLocalStorageQueue", DISPATCH_QUEUE_SERIAL);
}
```

Указанная `methodQueue` будет общей для всех методов вашего модуля. Если только один из ваших методов работает долго (или по какой-то причине должен выполняться в другой очереди, чем остальные), вы можете использовать `dispatch_async` внутри метода, чтобы выполнить код этого конкретного метода в другой очереди, не затрагивая остальные:

```objectivec
RCT_EXPORT_METHOD(doSomethingExpensive:(NSString *)param callback:(RCTResponseSenderBlock)callback)
{
 dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
   // Call long-running code on background thread
   ...
   // You can invoke callback from any thread/queue
   callback(@[...]);
 });
}

```

!!!info ""

    Совместное использование очередей диспетчеризации между модулями

    Метод `methodQueue` будет вызван один раз при инициализации модуля, а затем сохранен React Native, поэтому нет необходимости хранить ссылку на очередь самостоятельно, если вы не хотите использовать ее внутри своего модуля. Однако если вы хотите использовать одну и ту же очередь в нескольких модулях, вам нужно убедиться, что вы сохраняете и возвращаете один и тот же экземпляр очереди для каждого из них.

### Инъекция зависимостей

React Native автоматически создает и инициализирует все зарегистрированные нативные модули. Однако вы можете захотеть создать и инициализировать свои собственные экземпляры модулей, чтобы, например, внедрить зависимости.

Вы можете сделать это, создав класс, реализующий протокол `RCTBridgeDelegate`, инициализируя `RCTBridge` с делегатом в качестве аргумента и инициализируя `RCTRootView` с инициализированным мостом.

```objectivec
id<RCTBridgeDelegate> moduleInitialiser = [[classThatImplementsRCTBridgeDelegate alloc] init];

RCTBridge *bridge = [[RCTBridge alloc] initWithDelegate:moduleInitialiser launchOptions:nil];

RCTRootView *rootView = [[RCTRootView alloc]
                        initWithBridge:bridge
                            moduleName:kModuleName
                     initialProperties:nil];
```

### Экспорт Swift

В Swift нет поддержки макросов, поэтому экспортирование нативных модулей и их методов в JavaScript внутри React Native требует немного больше настроек. Тем не менее, это работает относительно одинаково. Допустим, у вас есть тот же `CalendarModule`, но в виде Swift-класса:

```swift
// CalendarManager.swift

@objc(CalendarManager)
class CalendarManager: NSObject {

 @objc(addEvent:location:date:)
 func addEvent(_ name: String, location: String, date: NSNumber) -> Void {
   // Date is ready to use!
 }

 @objc
 func constantsToExport() -> [String: Any]! {
   return ["someKey": "someValue"]
 }

}
```

!!!info ""

    Важно использовать модификаторы `@objc`, чтобы обеспечить правильный экспорт класса и функций в среду выполнения Objective-C.

Затем создайте частный файл реализации, который будет регистрировать необходимую информацию в React Native:

```objectivec
// CalendarManagerBridge.m
#import <React/RCTBridgeModule.h>

@interface RCT_EXTERN_MODULE(CalendarManager, NSObject)

RCT_EXTERN_METHOD(addEvent:(NSString *)name location:(NSString *)location date:(nonnull NSNumber *)date)

@end
```

Для новичков в Swift и Objective-C, всякий раз, когда вы [смешиваете два языка в проекте iOS](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html), вам также понадобится дополнительный связующий файл, известный как связующий заголовок, чтобы открыть файлы Objective-C для Swift. Xcode предложит вам создать этот заголовочный файл, если вы добавите ваш Swift-файл в приложение с помощью опции меню Xcode `File>New File`. Вам нужно будет импортировать `RCTBridgeModule.h` в этот заголовочный файл.

```objectivec
// CalendarManager-Bridging-Header.h
#import <React/RCTBridgeModule.h>
```

Вы также можете использовать `RCT_EXTERN_REMAP_MODULE` и `_RCT_EXTERN_REMAP_METHOD` для изменения JavaScript-имени экспортируемого модуля или методов. Для получения дополнительной информации смотрите [`RCTBridgeModule`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTBridgeModule.h).

!!!info ""

    Важно при создании модулей сторонних разработчиков: Статические библиотеки с Swift поддерживаются только в Xcode 9 и более поздних версиях. Чтобы проект Xcode собрался при использовании Swift в статической библиотеке iOS, которую вы включаете в модуль, ваш основной проект приложения должен содержать код Swift и сам мостовой заголовок. Если ваш проект приложения не содержит кода Swift, обходным решением может быть один пустой файл .swift и пустой связующий заголовок.

### Зарезервированные имена методов

#### invalidate()

Нативные модули могут соответствовать протоколу [RCTInvalidating](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTInvalidating.h) на iOS, реализуя метод `invalidate()`. Этот метод [может быть вызван](https://github.com/facebook/react-native/blob/0.62-stable/ReactCommon/turbomodule/core/platform/ios/RCTTurboModuleManager.mm#L456), когда нативный мост становится недействительным (т.е. при перезагрузке devmode). Пожалуйста, используйте этот механизм по мере необходимости, чтобы выполнить требуемую очистку для вашего нативного модуля.
