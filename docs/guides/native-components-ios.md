# Нативные UI компоненты iOS

!!!info ""

    Native Module и Native Components — это наши стабильные технологии, используемые в унаследованной архитектуре. Они будут устаревшими в будущем, когда новая архитектура станет стабильной. Новая архитектура использует [Turbo Native Module](the-new-architecture-pillars-turbomodules.md) и [Fabric Native Components](the-new-architecture-pillars-fabric-components.md) для достижения аналогичных результатов.

Существует масса нативных виджетов пользовательского интерфейса, готовых к использованию в новейших приложениях — некоторые из них являются частью платформы, другие доступны в виде библиотек сторонних разработчиков, а еще больше могут использоваться в вашем собственном портфолио. В React Native уже обернуто несколько наиболее важных компонентов платформы, таких как `ScrollView` и `TextInput`, но не все из них, и уж точно не те, которые вы могли написать сами для предыдущего приложения. К счастью, мы можем обернуть эти существующие компоненты для бесшовной интеграции с вашим приложением React Native.

Как и руководство по нативным модулям, это более продвинутое руководство, которое предполагает, что вы немного знакомы с программированием для iOS. Это руководство покажет вам, как создать нативный компонент пользовательского интерфейса, проведя вас через реализацию подмножества существующего компонента `MapView`, доступного в основной библиотеке React Native.

## Пример iOS MapView

Допустим, мы хотим добавить интерактивную карту в наше приложение — с тем же успехом можно использовать [`MKMapView`](https://developer.apple.com/library/prerelease/mac/documentation/MapKit/Reference/MKMapView_Class/index.html), нам нужно только сделать ее пригодной для использования из JavaScript.

Нативные представления создаются и управляются подклассами `RCTViewManager`. Эти подклассы похожи по функциям на контроллеры представлений, но по сути являются синглтонами — мост создает только один экземпляр каждого из них. Они открывают собственные представления для `RCTUIManager`, который делегирует им обратную связь для установки и обновления свойств представлений по мере необходимости. Обычно `RCTViewManager` также является делегатом для представлений, посылая события обратно в JavaScript через мост.

Чтобы открыть представление, вы можете:

-   Подкласс `RCTViewManager` для создания менеджера для вашего компонента.
-   Добавить маркерный макрос `RCT_EXPORT_MODULE()`.
-   Реализовать метод `-(UIView *)view`.

```objectivec title='RNTMapManager.m'
#import <MapKit/MapKit.h>

#import <React/RCTViewManager.h>

@interface RNTMapManager : RCTViewManager
@end

@implementation RNTMapManager

RCT_EXPORT_MODULE(RNTMap)

- (UIView *)view
{
  return [[MKMapView alloc] init];
}

@end
```

!!!note ""

    Не пытайтесь установить свойства `frame` или `backgroundColor` для экземпляра `UIView`, который вы открываете через метод `-view`.

    React Native перезапишет значения, установленные вашим пользовательским классом, чтобы соответствовать реквизитам компоновки вашего JavaScript-компонента.

    Если вам нужна такая степень контроля, возможно, лучше обернуть экземпляр `UIView`, который вы хотите стилизовать, в другой `UIView` и вернуть оберточный `UIView` вместо него.

    Дополнительную информацию см. в [Issue 2948](https://github.com/facebook/react-native/issues/2948).

!!!info ""

    В приведенном выше примере мы добавили к имени нашего класса префикс `RNT`. Префиксы используются для того, чтобы избежать столкновения имен с другими фреймворками.

    Фреймворки Apple используют двухбуквенные префиксы, а React Native использует в качестве префикса `RCT`. Чтобы избежать коллизии имен, мы рекомендуем использовать в собственных классах трехбуквенный префикс, отличный от `RCT`.

Затем вам понадобится немного JavaScript, чтобы сделать этот компонент пригодным для использования в React:

```ts title="MapView.tsx"
import { requireNativeComponent } from 'react-native';

// requireNativeComponent automatically resolves 'RNTMap' to 'RNTMapManager'
module.exports = requireNativeComponent('RNTMap');
```

```ts title="MyApp.tsx"
import MapView from './MapView.js';

// ...

render() {
  return <MapView style={{flex: 1}} />;
}
```

Убедитесь, что здесь используется `RNTMap`. Мы хотим потребовать здесь менеджера, который откроет представление нашего менеджера для использования в JavaScript.

!!!note ""

    При рендеринге не забудьте растянуть представление, иначе вы будете смотреть на пустой экран.

```ts
render() {
    return <MapView style={{flex: 1}} />;
}
```

Теперь это полностью функционирующий компонент просмотра карты на JavaScript, с поддержкой pinch-zoom и других жестов. Однако мы пока не можем управлять им из JavaScript :(

## Свойства

Первое, что мы можем сделать, чтобы сделать этот компонент более удобным для использования, это передать некоторые свойства. Допустим, мы хотим иметь возможность отключить масштабирование и указать видимую область. Отключение масштабирования — это булево значение, поэтому мы добавим одну строчку:

```objectivec title='RNTMapManager.m'
RCT_EXPORT_VIEW_PROPERTY(zoomEnabled, BOOL)
```

Обратите внимание, что мы явно указываем тип как `BOOL` — React Native использует `RCTConvert` под капотом для преобразования всевозможных типов данных при общении через мост, и плохие значения будут показывать удобные ошибки "RedBox", чтобы дать вам знать о проблеме как можно скорее. Когда все так просто, о реализации за вас позаботится этот макрос.

Теперь, чтобы фактически отключить масштабирование, мы устанавливаем свойство в JS:

```ts title="MyApp.tsx"
<MapView zoomEnabled={false} style={{ flex: 1 }} />
```

Чтобы документировать свойства (и какие значения они принимают) нашего компонента MapView, мы добавим компонент-обертку и задокументируем интерфейс с помощью React `PropTypes`:

```ts title="MapView.tsx"
import PropTypes from 'prop-types';
import React from 'react';
import { requireNativeComponent } from 'react-native';

class MapView extends React.Component {
    render() {
        return <RNTMap {...this.props} />;
    }
}

MapView.propTypes = {
    /**
     * A Boolean value that determines whether the user may use pinch
     * gestures to zoom in and out of the map.
     */
    zoomEnabled: PropTypes.bool,
};

var RNTMap = requireNativeComponent('RNTMap');

module.exports = MapView;
```

Теперь у нас есть хорошо документированный компонент-обертка для работы.

Далее добавим более сложный реквизит `region`. Начнем с добавления собственного кода:

```objectivec title='RNTMapManager.m'
RCT_CUSTOM_VIEW_PROPERTY(region, MKCoordinateRegion, MKMapView)
{
  [view setRegion:json ? [RCTConvert MKCoordinateRegion:json] : defaultView.region animated:YES];
}
```

Ок, это более сложный случай, чем `BOOL`, который мы имели раньше. Теперь у нас есть тип `MKCoordinateRegion`, которому нужна функция преобразования, и у нас есть пользовательский код, чтобы представление анимировалось, когда мы устанавливаем регион из JS. В теле функции, которое мы предоставляем, `json` означает необработанное значение, которое было передано из JS. Есть также переменная `view`, которая дает нам доступ к экземпляру представления менеджера, и `defaultView`, которую мы используем для возврата свойства к значению по умолчанию, если JS посылает нам null sentinel.

Вы можете написать любую функцию преобразования, какую захотите, для вашего представления — вот реализация для `MKCoordinateRegion` через категорию `RCTConvert`. Она использует уже существующую категорию ReactNative `RCTConvert+CoreLocation`:

```objectivec title='RNTMapManager.m'
#import "RCTConvert+Mapkit.h"
```

```objectivec title='RCTConvert+Mapkit.h'
#import <MapKit/MapKit.h>
#import <React/RCTConvert.h>
#import <CoreLocation/CoreLocation.h>
#import <React/RCTConvert+CoreLocation.h>

@interface RCTConvert (Mapkit)

+ (MKCoordinateSpan)MKCoordinateSpan:(id)json;
+ (MKCoordinateRegion)MKCoordinateRegion:(id)json;

@end

@implementation RCTConvert(MapKit)

+ (MKCoordinateSpan)MKCoordinateSpan:(id)json
{
  json = [self NSDictionary:json];
  return (MKCoordinateSpan){
    [self CLLocationDegrees:json[@"latitudeDelta"]],
    [self CLLocationDegrees:json[@"longitudeDelta"]]
  };
}

+ (MKCoordinateRegion)MKCoordinateRegion:(id)json
{
  return (MKCoordinateRegion){
    [self CLLocationCoordinate2D:json],
    [self MKCoordinateSpan:json]
  };
}

@end
```

Эти функции преобразования предназначены для безопасной обработки любого JSON, который JS может бросить в них, отображая ошибки "RedBox" и возвращая стандартные значения инициализации, когда встречаются отсутствующие ключи или другие ошибки разработчика.

Чтобы завершить поддержку реквизита `region`, нам нужно задокументировать его в `propTypes`:

```ts title="MapView.tsx"
MapView.propTypes = {
    /**
     * A Boolean value that determines whether the user may use pinch
     * gestures to zoom in and out of the map.
     */
    zoomEnabled: PropTypes.bool,

    /**
     * The region to be displayed by the map.
     *
     * The region is defined by the center coordinates and the span of
     * coordinates to display.
     */
    region: PropTypes.shape({
        /**
         * Coordinates for the center of the map.
         */
        latitude: PropTypes.number.isRequired,
        longitude: PropTypes.number.isRequired,

        /**
         * Distance between the minimum and the maximum latitude/longitude
         * to be displayed.
         */
        latitudeDelta: PropTypes.number.isRequired,
        longitudeDelta: PropTypes.number.isRequired,
    }),
};
```

```ts title="MyApp.tsx"
render() {
  var region = {
    latitude: 37.48,
    longitude: -122.16,
    latitudeDelta: 0.1,
    longitudeDelta: 0.1,
  };
  return (
    <MapView
      region={region}
      zoomEnabled={false}
      style={{flex: 1}}
    />
  );
}
```

Здесь вы можете видеть, что форма области явно указана в документации JS.

## События

Итак, теперь у нас есть собственный компонент карты, которым мы можем свободно управлять из JS, но как нам быть с событиями от пользователя, такими как пинч-зум или панорамирование для изменения видимой области?

До сих пор мы только возвращали экземпляр `MKMapView` из метода `-(UIView *)view` нашего менеджера. Мы не можем добавить новые свойства в `MKMapView`, поэтому нам придется создать новый подкласс от `MKMapView`, который мы используем для нашего представления. Затем мы можем добавить обратный вызов `onRegionChange` для этого подкласса:

```objectivec title='RNTMapView.h'
#import <MapKit/MapKit.h>

#import <React/RCTComponent.h>

@interface RNTMapView: MKMapView

@property (nonatomic, copy) RCTBubblingEventBlock onRegionChange;

@end
```

```objectivec title='RNTMapView.m'
#import "RNTMapView.h"

@implementation RNTMapView

@end
```

Обратите внимание, что все `RCTBubblingEventBlock` должны иметь префикс `on`. Далее объявите свойство обработчика событий на `RNTMapManager`, сделайте его делегатом для всех представлений, которые он открывает, и передавайте события в JS, вызывая блок обработчика событий из родного представления.

```objectivec title='RNTMapManager.m'
#import <MapKit/MapKit.h>
#import <React/RCTViewManager.h>

#import "RNTMapView.h"
#import "RCTConvert+Mapkit.h"

@interface RNTMapManager : RCTViewManager <MKMapViewDelegate>
@end

@implementation RNTMapManager

RCT_EXPORT_MODULE()

RCT_EXPORT_VIEW_PROPERTY(zoomEnabled, BOOL)
RCT_EXPORT_VIEW_PROPERTY(onRegionChange, RCTBubblingEventBlock)

RCT_CUSTOM_VIEW_PROPERTY(region, MKCoordinateRegion, MKMapView)
{
	[view setRegion:json ? [RCTConvert MKCoordinateRegion:json] : defaultView.region animated:YES];
}

- (UIView *)view
{
  RNTMapView *map = [RNTMapView new];
  map.delegate = self;
  return map;
}

#pragma mark MKMapViewDelegate

- (void)mapView:(RNTMapView *)mapView regionDidChangeAnimated:(BOOL)animated
{
  if (!mapView.onRegionChange) {
    return;
  }

  MKCoordinateRegion region = mapView.region;
  mapView.onRegionChange(@{
    @"region": @{
      @"latitude": @(region.center.latitude),
      @"longitude": @(region.center.longitude),
      @"latitudeDelta": @(region.span.latitudeDelta),
      @"longitudeDelta": @(region.span.longitudeDelta),
    }
  });
}
@end
```

В методе делегата `-mapView:regionDidChangeAnimated:` вызывается блок обработчика события на соответствующем представлении с данными региона. Вызов блока обработчика события `onRegionChange` приводит к вызову того же самого реквизита обратного вызова в JavaScript. Этот обратный вызов вызывается с необработанным событием, которое мы обычно обрабатываем в компоненте-обертке для упрощения API:

```ts title="MapView.tsx"
class MapView extends React.Component {
  _onRegionChange = event => {
    if (!this.props.onRegionChange) {
      return;
    }

    // process raw event...
    this.props.onRegionChange(event.nativeEvent);
  };
  render() {
    return (
      <RNTMap
        {...this.props}
        onRegionChange={this._onRegionChange}
      />
    );
  }
}
MapView.propTypes = {
  /**
   * Callback that is called continuously when the user is dragging the map.
   */
  onRegionChange: PropTypes.func,
  ...
};
```

```ts title="MyApp.tsx"
class MyApp extends React.Component {
    onRegionChange(event) {
        // Do stuff with event.region.latitude, etc.
    }

    render() {
        var region = {
            latitude: 37.48,
            longitude: -122.16,
            latitudeDelta: 0.1,
            longitudeDelta: 0.1,
        };
        return (
            <MapView
                region={region}
                zoomEnabled={false}
                onRegionChange={this.onRegionChange}
            />
        );
    }
}
```

## Работа с несколькими нативными представлениями

Представление React Native может иметь более одного дочернего представления в дереве представлений, например.

```ts
<View>
    <MyNativeView />
    <MyNativeView />
    <Button />
</View>
```

В этом примере класс `MyNativeView` является оберткой для `NativeComponent` и раскрывает методы, которые будут вызываться на платформе iOS. Класс `MyNativeView` определен в `MyNativeView.ios.js` и содержит прокси-методы `NativeComponent`.

Когда пользователь взаимодействует с компонентом, например, нажимает на кнопку, `backgroundColor` `MyNativeView` меняется. В этом случае `UIManager` не будет знать, какой `MyNativeView` должен быть обработан и какой из них должен изменить `backgroundColor`. Ниже вы найдете решение этой проблемы:

```ts
<View>
    <MyNativeView ref={this.myNativeReference} />
    <MyNativeView ref={this.myNativeReference2} />
    <Button
        onPress={() => {
            this.myNativeReference.callNativeMethod();
        }}
    />
</View>
```

Теперь вышеуказанный компонент имеет ссылку на определенный `MyNativeView`, что позволяет нам использовать конкретный экземпляр `MyNativeView`. Теперь кнопка может контролировать, какой `MyNativeView` должен изменить свой `backgroundColor`. В этом примере предположим, что `callNativeMethod` изменяет `backgroundColor`.

```ts title="MyNativeView.ios.tsx"
class MyNativeView extends React.Component {
    callNativeMethod = () => {
        UIManager.dispatchViewManagerCommand(
            ReactNative.findNodeHandle(this),
            UIManager.getViewManagerConfig(
                'RNCMyNativeView'
            ).Commands.callNativeMethod,
            []
        );
    };

    render() {
        return (
            <NativeComponent ref={NATIVE_COMPONENT_REF} />
        );
    }
}
```

`callNativeMethod` — это наш пользовательский метод iOS, который, например, изменяет `backgroundColor`, отображаемый через `MyNativeView`. Этот метод использует `UIManager.dispatchViewManagerCommand`, который требует 3 параметра:

-   `(nonnull NSNumber \*)reactTag` — id представления react.
-   `commandID:(NSInteger)commandID` — Id нативного метода, который должен быть вызван
-   `commandArgs:(NSArray<id>\*)commandArgs` — Args нативного метода, которые мы можем передать из JS в native.

```objectivec title='RNCMyNativeViewManager.m'
#import <React/RCTViewManager.h>
#import <React/RCTUIManager.h>
#import <React/RCTLog.h>

RCT_EXPORT_METHOD(callNativeMethod:(nonnull NSNumber*) reactTag) {
    [self.bridge.uiManager addUIBlock:^(RCTUIManager *uiManager, NSDictionary<NSNumber *,UIView *> *viewRegistry) {
        NativeView *view = viewRegistry[reactTag];
        if (!view || ![view isKindOfClass:[NativeView class]]) {
            RCTLogError(@"Cannot find NativeView with tag #%@", reactTag);
            return;
        }
        [view callNativeMethod];
    }];

}
```

Здесь `callNativeMethod` определен в файле `RNCMyNativeViewManager.m` и содержит только один параметр, которым является `(nonnull NSNumber*) reactTag`. Эта экспортируемая функция находит определенное представление с помощью `addUIBlock`, который содержит параметр `viewRegistry`, и возвращает компонент на основе `reactTag`, позволяя вызвать метод на нужном компоненте.

## Стили

Поскольку все наши родные представления react являются подклассами `UIView`, большинство атрибутов стиля будут работать так, как вы ожидаете. Однако некоторые компоненты будут иметь стиль по умолчанию, например, `UIDatePicker`, который имеет фиксированный размер. Этот стиль по умолчанию важен для того, чтобы алгоритм компоновки работал так, как ожидается, но мы также хотим иметь возможность переопределять стиль по умолчанию при использовании компонента. `DatePickerIOS` делает это, оборачивая родной компонент в дополнительное представление, которое имеет гибкую стилизацию, и используя фиксированный стиль (который генерируется с помощью констант, передаваемых из родного компонента) для внутреннего родного компонента:

```ts title="DatePickerIOS.ios.tsx"
import {UIManager} from 'react-native';
var RCTDatePickerIOSConsts = UIManager.RCTDatePicker.Constants;
// ...
  render: function() {
    return (
      <View style={this.props.style}>
        <RCTDatePickerIOS
          ref={DATEPICKER}
          style={styles.rkDatePickerIOS}
          ...
        />
      </View>
    );
  }
});

var styles = StyleSheet.create({
  rkDatePickerIOS: {
    height: RCTDatePickerIOSConsts.ComponentHeight,
    width: RCTDatePickerIOSConsts.ComponentWidth,
  },
});
```

Константы `RCTDatePickerIOSConsts` экспортируются из native путем захвата фактического кадра native компонента следующим образом:

```objectivec title='RCTDatePickerManager.m'
- (NSDictionary *)constantsToExport
{
  UIDatePicker *dp = [[UIDatePicker alloc] init];
  [dp layoutIfNeeded];

  return @{
    @"ComponentHeight": @(CGRectGetHeight(dp.frame)),
    @"ComponentWidth": @(CGRectGetWidth(dp.frame)),
    @"DatePickerModes": @{
      @"time": @(UIDatePickerModeTime),
      @"date": @(UIDatePickerModeDate),
      @"datetime": @(UIDatePickerModeDateAndTime),
    }
  };
}
```

В этом руководстве рассмотрены многие аспекты создания мостов с использованием собственных нативных компонентов, но есть еще больше аспектов, которые вам, возможно, придется рассмотреть, например, пользовательские крючки для вставки и компоновки вложенных представлений. Если вы хотите углубиться еще больше, посмотрите [исходный код](https://github.com/facebook/react-native/tree/main/packages/react-native/React/Views) некоторых реализованных компонентов.
