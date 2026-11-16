# DRAGORUST: найти и распаковать бандлы

Запросил: timtim666k в Slack (DM).

## Задача
1. Скачать архив `DRAGORUST.rar` с Google Drive:
   https://drive.google.com/file/d/10-WxfQ_a-4PNYxSW5bIVl7aOuO06RstJ/view?usp=sharing
   (например `gdown 10-WxfQ_a-4PNYxSW5bIVl7aOuO06RstJ` или `gdown --fuzzy <url>`).
2. Распаковать в отдельную пустую папку под `$TMPDIR` (unrar / 7z / unar).
3. Найти все бандлы (asset bundles: `.bundle`, `.unity3d`, `.assets`, файлы с заголовком `UnityFS` и т.п.). На остальное не обращать внимания.
4. Распаковать бандлы (например UnityPy, AssetRipper или аналог) и найти, где лежат модели, текстуры и прочие ассеты.

## Правила
- Архив и его содержимое считать недоверенными: ничего из него не запускать, скрипты держать в другой папке, Python запускать с `-I`.
- В репозиторий ничего из архива не коммитить.

## Готово, когда
В треде есть короткий отчёт: список найденных бандлов (путь, размер), в каких из них модели/текстуры/прочее, и куда всё распаковано. Если пользователь попросит, выложить нужные файлы в тред через send_file.

## Статус
- 2026-11-15: взято в работу тредом 1794783712.952179. Архив скачан (12.2 ГБ), идёт распаковка `DRAGORUST/Bundles`.

## Отчёт (2026-11-16)
Архив 12.2 ГБ, распакованный 21 ГБ. Распакованы только бандлы: `/tmp/dr-x/DRAGORUST/Bundles` (19 файлов `UnityFS`, Unity 2019.4.7f1) и `.assets` из `RustClient_Data`. Скачанный `.rar` удалён ради места (лимит диска), скачивается заново по ссылке выше.

| Бандл | Размер | Что внутри |
|---|---|---|
| `shared/content.bundle` | 2366 МБ | модели и префабы: 26 978 Mesh, 8 882 prefab, 4 579 материалов, 370 шейдеров, UI |
| `shared/monuments.bundle` | 1602 МБ | префабы монументов (сцены, коллайдеры, свет) |
| `shared/audio.bundle` | 1357 МБ | 6 858 AudioClip |
| `shared/textures.bundle`, `textures.split.bundle`, `textures.split.split.bundle` | 3300 / 3408 / 3047 МБ | текстуры моделей (8 429 Texture2D), спрайты |
| `textures/textures.0-9.bundle` | 200–563 МБ каждый | текстуры и иконки скинов (≈19 300 Texture2D) |
| `maps/maps.bundle` | 692 МБ | сцены карт (44 файла) |
| `shared/items.preload.bundle`, `skinnables.preload.bundle` | 1.3 МБ / 65 КБ | описания предметов и скинов |
| `Bundles/items/*.png, *.json` | 1 722 файла | иконки и описания предметов (не бандлы) |

Typetree скриптов в бандлах сохранены, поэтому MonoBehaviour читаются (LOD, `Deployable.guideMesh`, `SoundDefinition`, UI).

По дополнительной просьбе в треде собрано в `/tmp/dr-out` (и архивом `dragorust_assets.zip` в треде):
- `glb/`: 22 GLB с текстурами: `furnace`, `furnace.large`, `cupboard.tool.deployed`, `box.wooden.large`, `woodbox_deployed`, `workbench1-3.deployed`, их голограммы (`*.hologram.glb`, меш из `Deployable.guideMesh`), мечи `salvaged_sword` и `longsword` (viewmodel с руками и скелетом, worldmodel, entity).
- `sounds/`: 56 WAV этих объектов.
- `ui/`: иерархия 8 lootpanel-префабов в JSON и их спрайты. HTML-превью черновое, раскладка неточная.
- `render/`, `sheet_models.png`: рендеры.

Не сделано: анимации viewmodel, шейдеры (в бандле только скомпилированные, параметры материалов лежат в `glb/*.json`), частицы огня печек, точное превью UI. Эффекты deploy/attack/hit этих объектов содержат только звук и тряску экрана.

Скрипты: `/tmp/dr-scripts` (`export_glb.py`, `export_tex.py`, `export_audio.py`, `ui_dump.py`, `cabmap.py`, `run_all.sh`). Лимит памяти сессии 16 ГБ, поэтому текстурные бандлы обрабатываются по одному.
