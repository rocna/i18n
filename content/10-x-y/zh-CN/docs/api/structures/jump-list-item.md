# JumpListItem 对象

* `type` String (optional) - One of the following:
  * ` task `-任务将启动具有特定参数的应用程序。
  * ` separator`-可用于分隔标准 ` Tasks ` 类别中的项目。
  * ` file `-一个文件的链接将使用创建跳转列表的应用程序打开文件, 为此, 应用程序必须注册为该文件类型的默认程序 (尽管它并不需要成为默认程序)。
* ` path `字符串 (可选)-要打开的文件的路径, 只应在 ` type ` 为 ` file ` 时设置。
* ` program `字符串(可选)-要执行的程序的路径, 通常应该指定打开当前程序的 ` process.execPath `。 只应在 ` type ` 是 ` task ` 时设置。
* `arguments` 字符串(可选) - 给 `program` 这个程序执行时的命令行参数。 只应在 ` type ` 是 ` task ` 时设置。
* `title` 字符串(可选) - 该项目在跳转列表中显示的文本。 只应在 ` type ` 是 ` task ` 时设置。
* `description` 字符串(可选) - 任务描述(显示在鼠标悬浮提示中)。 只应在 ` type ` 是 ` task ` 时设置。
* `图标路径` 字符串 (可选项) - 在跳转列表中会显示一个使用绝对路径的图标，它可以是一个包含图标的任意资源文件 (e.g. `.ico`, `.exe`, `.dll`). 通常可以指定`process.execPath`值显示程序图标。
* `iconIndex` 数字(可选) - 图标在资源文件中的索引值。 如果资源文件包含多个图标，则可以使用此值指示从零开始的图标索引，用于此任务的显示。 如果资源文件只包含一个图标，则iconIndex必须设为0。
* `workingDirectory` 字符串(可选) - 当前工作目录。 默认值为空。