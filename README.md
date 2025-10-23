# flutter_xgen

> 🚀 **VSCode + Flutter 开发效率小工具**
> 一站式集成资源管理、热重载、国际化、图标生成、重命名等核心能力，告别重复劳动，让开发更流畅高效。

---

## ✨ 核心特性

### 1. 🧭 统一功能面板

支持通过 `fxg.yaml` 配置 **主项目（main project）** 与 **子项目（sub projects）**

⚠️ 注意：如果项目中使用了 `fxg.yaml` 文件配置面板，则 `fxg_xxx_yyy` 等功能会优先读取 `fxg.yaml` 中的配置。

#### 配置示例

```
fxg:
  main_project:
    dir: "" # 相对路径
    fxg_app_icon:
      # 主项目图标配置
      ...
    fxg_app_rename:
      # 主项目重命名配置
      ...
    fxg_assets_gen:
      # 主项目资源生成配置
      ...

  sub_projects:
    - dir: "" # 相对路径
      fxg_assets_gen:
        # 子项目资源生成配置
        ...
```

#### 核心特点

- 📌 **统一入口**：侧边栏聚合所有核心功能入口，操作直观
- ⚡ **快速切换**：支持功能模块与配置项快速切换
- 🔄 **实时生效**：所有配置修改即时生效，无需重启插件或项目

---

### 2. ⚡ 跨编辑器自动热重载

* 🌟 即使在 **Emacs、Vim、WebStorm** 等外部编辑器中修改 `.dart` 文件，也能自动触发 **VS Code 调试会话中的热重载**
* 🔍 当项目以 `flutter run` 或 **VS Code 调试（F5）** 启动时，插件会监听文件系统变更并自动执行 `flutter hot reload`
* 🚀 **保存即刷新**，实时预览修改结果，无需切回 VS Code 或手动输入命令

---

### 3. 🧩 类型安全的资源管理（`assets.dart`）

自动扫描项目资源并生成类型安全的 Dart 类，彻底避免硬编码与路径拼写错误。
✅ 支持图片预览、SVGA 动画预览、文件大小显示、TinyPNG 压缩等功能。

#### 🔑 核心能力

* 📸 悬停预览图片或动画
* 📊 文件大小可视化（KB / MB / GB）
* 🎨 一键 TinyPNG 无损压缩
* 📁 文件新增/删除实时同步生成
* 🔒 静态类型检查 + 智能补全
* 🧱 多模块隔离管理，避免命名冲突

#### 🔧 配置方式（`fxg.yaml` 或 `pubspec.yaml`）

##### ✅ 单模块示例

```yaml
fxg_assets_gen:
  output_dir: lib/generated
  named_with_parent: true
  output_filename: assets
  class_name: Assets
  filename_split_pattern: "[-_]"
  leading_with_package_name: false
  uniqify_name: true
  exclude_paths:
    - assets/temp/
    - assets/icons/unused/
  include_paths:
    - assets/images/
    - assets/sounds/
```

##### 🧩 多模块示例

```yaml
fxg_assets_gen:
  - output_dir: lib/generated
    output_filename: assets_main
    class_name: AssetsMain
    include_paths:
      - assets/main/
  - output_dir: lib/generated
    output_filename: assets_store
    class_name: AssetsStore
    include_paths:
      - assets/store/
```

#### ⚙️ 参数说明

| 参数名                      | 类型     | 默认值          | 说明                                                     |
| :-------------------------- | :------- | :-------------- | :------------------------------------------------------- |
| `output_dir`                | string   | `lib/generated` | 输出目录                                                 |
| `named_with_parent`         | bool     | `true`          | 是否在变量名中包含上级目录名                             |
| `output_filename`           | string   | `assets`        | 生成文件名（不含 `.dart`）                               |
| `class_name`                | string   | `Assets`        | 生成类名                                                 |
| `filename_split_pattern`    | string   | `"[-_]"`        | 文件名分隔符正则（例：`dog-icon → dogIcon`）             |
| `leading_with_package_name` | bool     | `false`         | 是否添加包名前缀                                         |
| `uniqify_name`              | bool     | `true`          | 重名文件自动加后缀                                       |
| `include_paths`             | string[] | 无              | 包含路径（默认扫描所有。支持 glob 语法，⚠️ 不建议太复杂） |
| `exclude_paths`             | string[] | 无              | 排除路径                                                 |

#### 🧾 示例

```
assets/
 ├─ images/
 │   ├─ cat.png
 │   └─ dog-icon.jpg
 └─ sounds/
     └─ meow.mp3
```

生成：

```dart
class Assets {
  static const imagesCat = "assets/images/cat.png";
  static const imagesDogIcon = "assets/images/dog-icon.jpg";
  static const soundsMeow = "assets/sounds/meow.mp3";
}
```

使用：

```dart
Image.asset(Assets.imagesCat);
AudioPlayer.play(Assets.soundsMeow);
```

---

### 4. 🌍 文本国际化

兼容 **Flutter Intl** / **Intl Utils**，支持 ARB 多语言文案管理，简化国际化流程。

📘 配置参考：
🔗 [https://github.com/localizely/intl_utils#configure-package](https://github.com/localizely/intl_utils#configure-package)

> ⚠️ 不支持 `localizely_api_key` 云端配置。

---

### 5. 🏷️ 应用重命名

自动批量修改 App 名称、包名、Bundle ID 等跨平台配置。
支持单 target 与多 target 的 iOS 工程结构。

```yaml
fxg_app_rename:
  app_name: "MyApp" # 若子项中包含 app_name，该项失效
  flutter:
    app_name: "MyApp"
  android:
    enable: true
    package: "com.company.myapp"
  ios:
    # --- 写法一：单 target（Map 结构） ---
    app_name: "MyApp"
    bundle_name: "MyApp"
    target_name: "Runner"
    bundle_id: "com.company.myapp"

    # --- 写法二：多 target（List 结构）每一项的 target_name 不能为空 ---
    # - app_name: "MyApp"
    #   bundle_name: "MyApp"
    #   target_name: "Runner"
    #   bundle_id: "com.company.myapp"
    # - enable: false
    #   target_name: "RunnerTest"
    #   bundle_ids:
    #     debug: "com.company.myapp.test.debug"
    #     release: "com.company.myapp.test.release"
  macos:
    enable: false
    app_name: "MacOSApp"
    bundle_name: "MacOSAppBN"
    bundle_id: "com.app.macos"
    copyright: "flutter_xgen@2025"
```

#### ⚙️ 参数说明

| 参数名              | 类型   | 默认值  | 说明                                  |
| :------------------ | :----- | :------ | :------------------------------------ |
| `app_name`          | string | 无      | 全局应用名称（可被子项覆盖）          |
| `flutter.app_name`  | string | 无      | Flutter 层应用名（任务栏显示名称）    |
| `android.enable`    | bool   | `true`  | 是否启用 Android 修改                 |
| `android.package`   | string | 无      | Android 包名                          |
| `ios[].enable`      | bool   | `true`  | 是否启用该 iOS target                 |
| `ios[].target_name` | string | 无      | iOS target 名称                       |
| `ios[].bundle_id`   | string | 无      | iOS Bundle ID（支持分 debug/release） |
| `macos.enable`      | bool   | `false` | 是否启用 macOS 修改                   |
| `macos.bundle_id`   | string | 无      | macOS Bundle ID                       |
| `macos.copyright`   | string | 无      | macOS 版权信息                        |

---

### 6. 🖼️ 应用图标生成

自动生成多平台图标资源，无需手动切图。

```yaml
fxg_app_icon:
  app_icon: "assets/icons/app.png" # 要求尺寸: 1024x1024
  android:
    enable: true
    app_icon: "assets/icons/_android_app.png"
    enable_circle_icon: true
    firebase_notification_icon: "assets/icons/notification.png"
  ios:
    enable: false
    launch_image: "assets/icons/launch.png"
    enable_pad: true
    enable_macos: true
    enable_watch: true
  macos:
    enable: false
    enable_round_icon: false
```

#### ⚙️ 参数说明

| 参数名                               | 类型   | 默认值  | 说明                            |
| :----------------------------------- | :----- | :------ | :------------------------------ |
| `app_icon`                           | string | 无      | 主图标文件路径（1024x1024 PNG） |
| `android.enable`                     | bool   | `true`  | 是否启用 Android 图标生成       |
| `android.app_icon`                   | string | 无      | Android 图标文件路径            |
| `android.enable_circle_icon`         | bool   | `false` | 是否生成圆形图标（Android 8+）  |
| `android.firebase_notification_icon` | string | 无      | Firebase 通知图标路径           |
| `ios.enable`                         | bool   | `true`  | 是否启用 iOS 图标生成           |
| `ios.launch_image`                   | string | 无      | 启动图路径                      |
| `ios.enable_pad`                     | bool   | `false` | 是否生成 iPad 图标              |
| `ios.enable_macos`                   | bool   | `false` | 是否生成 macOS 图标（共用资源） |
| `ios.enable_watch`                   | bool   | `false` | 是否生成 watchOS 图标           |
| `macos.enable`                       | bool   | `false` | 是否启用 macOS 图标生成         |
| `macos.enable_round_icon`            | bool   | `false` | 是否生成圆形 macOS 图标         |

---

### 7. 📚 Import 排序工具

自动排序 `import`，保持代码结构统一、风格整洁。

```yaml
fxg_import_sort:
  exclude_paths: [lib/generated/**] # 支持 glob 语法
  include_paths: [lib/**]
  sort_by: length        # 可选：'length' | 'alphabetical'
  sort_order: asc        # 可选：'asc' | 'desc'
```

#### ⚙️ 参数说明

| 参数名          | 类型     | 默认值       | 说明                                                    |
| :-------------- | :------- | :----------- | :------------------------------------------------------ |
| `exclude_paths` | string[] | 无           | 排除的路径（支持 glob 语法）                            |
| `include_paths` | string[] | `["lib/**"]` | 包含的路径（默认扫描整个 lib 目录）                     |
| `sort_by`       | string   | `"length"`   | 排序依据：`length`（按长度）或 `alphabetical`（按字母） |
| `sort_order`    | string   | `"asc"`      | 排序方向：`asc` 或 `desc`                               |

---

## ❓ 常见问题

**Q：跨编辑器热重载不生效？**
✅ 请检查以下几点：

1. 使用 `flutter run` 或 F5 启动项目
2. 启用 VS Code 自动保存（File → Auto Save）
3. 外部编辑器已正确保存文件
4. 打开「输出 → FXGLog」查看日志

---

## 📮 反馈与贡献

💌 提交 Issue：
👉 [flutter_xgen_feedback](https://github.com/ivine/flutter_xgen_feedback/issues)
