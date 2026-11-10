# Rust Demo — Equipment Fix

Проект Godot 4.5.1, восстановленный из предоставленного `Rust_Demo_v1_9_partial.apk`.
Восстановлены все 50 GDScript-файлов (включая тесты) и 533 импортированных ресурса.
Комментарии исходных скриптов и качество ресурсов до сжатия APK восстановить нельзя.
Архив `DRAGORUST.rar` содержит Windows-сборку Unity с ресурсами, а не проект редактора Unity.

## Запуск

Откройте `project.godot` в Godot **4.5.1** и дождитесь импорта ресурсов, затем запустите проект.
Каталог `.godot` создаётся автоматически и не нужен в архиве исходников.

- `WASD` — движение, мышь — обзор, `Space` — прыжок, `Shift` — бег.
- `V` — переключение первого/третьего лица.
- `Tab` — инвентарь; базука, каменный топор и кирка изначально находятся в рюкзаке.
- `1`–`6` — слоты пояса, ЛКМ — использование, ПКМ — прицеливание, `R` — перезарядка.
- На Android используются экранные кнопки и сенсорный инвентарь.

## Сборка Android

Нужен редактор Godot 4.5.1. Скрипт настройки устанавливает OpenJDK 17.0.21 из Ubuntu,
Android Command-line Tools 15859902, Platform-Tools 35.0.0, SDK/Build-Tools 35 и официальный
debug-шаблон экспорта Godot 4.5.1. SDK и шаблон хранятся в игнорируемом каталоге `.tools`.
Автоматическая установка JDK рассчитана на Ubuntu 24.04; в другой системе установите JDK 17
самостоятельно и задайте `JAVA_HOME`.

```sh
bash tools/setup_android.sh
GODOT=/path/to/godot-4.5.1 bash tools/export_android.sh
```

Результат: `build/Rust_Demo_Equipment_Fix.apk`, ARMv7 + ARM64, Android 7.0 и новее.
Это debug-сборка с отдельным пакетом `org.godot.rustdemo.equipmentfix`: она устанавливается
рядом с исходной игрой и не заменяет её данные. Закрытый ключ исходного APK не предоставлялся;
локальный тестовый ключ создаётся в `.tools` и не входит в исходники.

## Проверки

```sh
godot --headless --path . --script res://tests/rocket_shoulder_test.gd
godot --headless --path . --script res://tests/firstperson_tools_test.gd
godot --headless --path . --script res://tests/rocket_avatar_test.gd
godot --headless --path . --script res://tests/hammer_test.gd
```

В восстановленной исходной версии также воспроизводятся старые ошибки
`grip_regression.gd:22` и `character_layers_test.gd:9`. Эти проверки не отключались
и их условия не ослаблялись. Проверка на физическом Android-устройстве не выполнялась.