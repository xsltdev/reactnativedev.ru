

---
id: publishing-to-app-store
title: Publishing to Apple App Store
---

Процесс публикации такой же, как и у любого другого нативного приложения для iOS, с некоторыми дополнительными соображениями, которые необходимо принять во внимание.



:::info

Если вы используете Expo, прочитайте руководство Expo [Deploying to App Stores](https://docs.expo.dev/distribution/app-stores/) для создания и отправки вашего приложения в Apple App Store. Это руководство работает с любым приложением React Native для автоматизации процесса развертывания.

:::




### 1. Включите App Transport Security




App Transport Security - это функция безопасности, представленная в iOS 9, которая отклоняет все HTTP-запросы, отправленные не по HTTPS. Это может привести к блокировке HTTP-трафика, включая сервер разработчика React Native. ATS отключен для `localhost` по умолчанию в проектах React Native, чтобы упростить разработку.




Вы должны снова включить ATS перед сборкой приложения для производства, удалив запись `localhost` из словаря `NSExceptionDomains` и установив `NSAllowsArbitraryLoads` на `false` в файле `Info.plist` в папке `ios/`. Вы также можете повторно включить ATS из Xcode, открыв свойства вашей цели в панели Info и отредактировав запись App Transport Security Settings.




:::примечание

Если вашему приложению требуется доступ к HTTP-ресурсам на производстве, узнайте, как настроить ATS в вашем проекте.

:::




### 2. Настройка схемы выпуска




Создание приложения для распространения в App Store требует использования схемы `Release` в Xcode. Приложения, созданные по схеме `Release`, будут автоматически отключать внутриприкладное меню Dev Menu, что предотвратит случайный доступ пользователей к меню в продакшене. Кроме того, JavaScript будет собираться локально, так что вы сможете установить приложение на устройство и протестировать его, не подключаясь к компьютеру.




Чтобы настроить приложение на сборку по схеме `Release`, перейдите в **Product** → **Scheme** → **Edit Scheme**. Выберите вкладку **Run** на боковой панели, затем установите в выпадающем списке Build Configuration значение `Release`.




![](/docs/assets/ConfigureReleaseScheme.png)




#### Советы профессионалов

По мере увеличения размера вашего App Bundle вы можете начать видеть, как между заставкой и отображением представления корневого приложения появляется пустой экран. В этом случае вы можете добавить следующий код в `AppDelegate.m`, чтобы сохранить отображение заставки во время перехода.

```objectivec
  // Place this code after "[self.window makeKeyAndVisible]" and before "return YES;"
  UIStoryboard *sb = [UIStoryboard storyboardWithName:@"LaunchScreen" bundle:nil];
  UIViewController *vc = [sb instantiateInitialViewController];
  rootView.loadingView = vc.view;
```

Статический бандл создается каждый раз, когда вы нацеливаетесь на физическое устройство, даже в Debug. Если вы хотите сэкономить время, отключите генерацию пакета в Debug, добавив следующее в сценарий оболочки на этапе сборки Xcode `Bundle React Native code and images`:

```shell
 if [ "${CONFIGURATION}" == "Debug" ]; then
  export SKIP_BUNDLING=true
 fi
```

### 3. Build app for release

You can now build your app for release by tapping `⌘B` or selecting **Product** → **Build** from the menu bar. Once built for release, you'll be able to distribute the app to beta testers and submit the app to the App Store.

:::info
You can also use the `React Native CLI` to perform this operation using the option `--mode` with the value `Release` (e.g. `npx react-native run-ios --mode Release`).
:::

Once you are done with the testing and ready to publish to App Store, follow along with this guide.

- Launch your terminal, and navigate into the iOS folder of your app and type `open .`.
- Double click on YOUR_APP_NAME.xcworkspace. It should launch XCode.
- Click on `Product` → `Archive`. Make sure to set the device to "Any iOS Device (arm64)".

:::note
Check your Bundle Identifier and make sure it is exactly same as the one you have created in the Identifiers in Apple Developer Dashboard.
:::

- After the archive is completed, in the archive window, click on `Distribute App`.
- Click on `App Store Connect` now (if you want to publish in App Store).
- Click `Upload` → Make sure all the check boxes are selected, hit `Next`.
- Choose between `Automatically manage signing` and `Manually manage signing` based on your needs.
- Click on `Upload`.
- Now you can find it in the App Store Connect under TestFlight.

Now fill up the necessary information and in the Build Section, select the build of the app and click on `Save` → `Submit For Review`.

