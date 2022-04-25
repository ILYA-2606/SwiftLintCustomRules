# Кастомные правила для SwiftLint
Подборка полезных дополнительных правил для SwiftLint. Не все правила подходят для всех архитектур и подходов, поэтому используйте только при необходимости. Правила совместимы со [Swift Guideline](https://github.com/ILYA-2606/SwiftGuideline)

## Использование кириллицы в названии файлов
Помогает найти случайную опечатку
```yml
custom_cyrillic_file_name:
  name: "Использование кириллицы в названии файлов запрещено"
  regex: "[A-Za-z]*[ЁёА-я]+[A-Za-z]*(\\.swift)$"
  message: "Проверь имя файла на наличие символов кириллицы"
  match_kinds:
    - comment
  severity: error
```
```diff
- // ВьюПользователя.swift
+ // UserView.swift
```

## Использование кириллицы вне комментариев, строк и документации
Помогает найти случайную опечатку
```yml
custom_cyrillic_code:
  name: "Использование кириллицы вне комментариев, строк и документации запрещено"
  regex: "[ЁёА-я]"
  message: "Проверь название на содержание символов кириллицы"
  match_kinds:
    - argument
    - identifier
    - keyword
    - parameter
    - typeidentifier
  severity: error 
```
```diff
- var пользователь = Пользователь()
+ var user = User()
```

## Словари должны именоваться с суффиксом `Map`
```yml
custom_map_name:
  name: "Словари должны именоваться с суффиксом Map"
  regex: "(?<!(Map)|(map))(:| =) \\[\\w+: \\w+]( |,|\\))"
  message: "Добавь суффикс Map"
  severity: error
```
```diff
- var usersInfo: [String: User]?
+ var usersMap: [String: User]?
```

## Множества должны именоваться с суффиксом `Set`
```yml
custom_set_name:
  name: "Множества должны именоваться с суффиксом Set"
  regex: "(?<!(Set)|(set))(((: Set<\\w+>(?!\\])([\\s]| *.)))| = Set<)"
  message: "Добавь суффикс Set"
  severity: error
```
```diff
- var users: Set<User>?
+ var usersSet: Set<User>?
```

## Идентификатор должен иметь суффикс `ID`
Все сокращения должны быть заглавными, если они используются в качестве суффикса. Исключение - DTO
```yml
custom_wrong_id:
  name: "Неверное наименование идентификатора"
  excluded: "path/to/DTO"
  regex: "(var|let) \\w*Id( |:)"
  message: "Используй ID"
  severity: error
```
```diff
- let userId: String? {
+ let userID: String? {
```

## Ссылка должна иметь суффикс `URL`
```yml
custom_wrong_url:
  name: "Неверное наименование URL"
  excluded: "path/to/DTO"
  regex: "(var|let) \\w*Url( |:)"
  message: "Используй URL"
  severity: error
```
```diff
- let photoUrl: URL? {
+ let photoURL: URL? {
```

## Модель DTO должна иметь суффикс `DTO`
Все сокращения должны быть заглавными, если они используются в качестве суффикса
```yml
custom_dto_suffix:
  name: "Модель DTO должна иметь суффикс 'DTO'"
  included: "path/to/DTO"
  regex: "(struct \\w*(?<!DTO):)|(struct (\\w*(?<!DTO)<))"
  message: "Модель DTO должна иметь суффикс 'DTO'"
  severity: error
```
```diff
- struct UserDto {
+ struct UserDTO {
```

## Enum в DTO
Зависит от используемого парсера моделей DTO. Как правило если в Enum приходит неизвестное значение, то парсинг завершится ошибкой. Поэтому мы рекомендуем Enum перенести в модель приложения. Исключение - CodingKeys
```yml
custom_enum_suffix:
  name: "Enum в DTO запрещены"
  included: "path/to/DTO"
  regex: "(enum (?!CodingKeys))"
  message: "Используй enum в моделях"
  severity: error
```
```diff
struct UserDTO {
-  enum Status {
-    case normal
-    case blocked
-  }

  let userStatus: Status
  
  enum CodingKeys: String, CodingKey {
    case userStatus = "user_status"
  }
}
```

## Использование пустого замыкания
```yml
custom_void_return:
  name: "Неверное использование Void"
  regex: "([(](Void)?[)] ?-> ?Void)"
  match_kinds:
    - typeidentifier
  message: "Используй '() -> ()'"
  severity: error
```
```diff
- let onTap: (Void) -> (Void)
+ let onTap: () -> ()
```

## Использование проверки на пустую строку
```yml
custom_empty_string:
  name: "Неверная проверка строки на пустоту"
  regex: "( == \"\")|(== \"\")|( ==\"\")|(==\"\")|( != \"\")|(!= \"\")|( !=\"\")|(!=\"\")"
  message: "Используй 'string.isEmpty'"
  severity: error
```
```diff
- if id == "" {
+ if id.isEmpty {
```

## Разворачивание `self`
```yml
custom_guard_self:
  name: "Неверное использование guard let self = self"
  regex: "guard\\s+let\\s+(((?!self).)\\S* = self[ ,]|`self` = self)"
  message: "Используй 'guard let self = self'"
  severity: error
```
```diff
- guard let `self` = self else {
+ guard let self = self else {
```

## Неверное создание массива
Мы рекомендуем создавать массивы в читаемом виде
```yml
custom_array_Type:
  name: "Неверное создание массива"
  regex: "(var|let) \\w* = \\[\\w*\\]\\(\\)"
  message: "var array: [Type] = []"
  severity: error
```
```diff
- var array = [Type]()
+ var array: [Type] = []
```

## Избыточное условие Optional chaining для `Bool`
```yml
custom_boolean_redundant_condition:
  name: "Optional chaining для Bool запрещен"
  regex: "(?? true)|(?? false)"
  message: "Используй == true или == false"
  severity: error
```
```diff
var isEnabled: Bool?
- if isEnabled ?? true {
+ if isEnabled == true {
```

## Избыточное условие для `Bool`
```yml
custom_boolean_clear_condition:
  name: "Переусложненное условие"
  regex: "^(?>(?!(as\\?)|(\\?.)).)*((!= true)|(!= false)).*$"
  message: "Используй == true или == false или саму перeменную в качестве условия"
  severity: error
```
```diff
var isEnabled = true
- if isEnabled != false {
+ if isEnabled {
```

## Неверный апостроф в документации
```yml
custom_documentation_ticks:
  name: "Неверный апостроф в документации"
  regex: "´"
  message: "Используй '`' вместо '´' для корректной работы документации."
  match_kinds:
    - comment
    - doccomment
  severity: error
```
```diff
- /// Класс использует ´URL´
+ /// Класс использует `URL`
```

## Избыточный возврат `Bool` из тернарного оператора
```yml
custom_boolean_in_ternar_return:
  name: "Возврат Bool из тернарного оператора"
  regex: "\\? (true : false)|(false : true)"
  message: "Не используй тернарный оператор для возврата Bool"
  severity: error
```
```diff
- return isEnabled ? true : false
+ return isEnabled
```

## Использование `unowned` запрещено
```yml
custom_unowned:
  name: "Использование unowned запрещено"
  regex: "\\[unowned "
  message: "Используй 'weak' вместо 'unowned'"
  severity: error
```
```diff
- updateUsers { [unowned self] in
+ updateUsers { [weak self] in
```

## Лишнее указание типа
```yml
custom_type_inferred_context:
  name: "Лишнее указание типа"
  regex: "^\\w* *((var)|(let)) \\w+\\:\\s(\\w+)\\s\\=\\s\\4"
  message: "var a = Type()"
  severity: error
```
```diff
- let user: User = User()
+ let user = User()
```

## Переносы при вызове функции
```yml
custom_func_line_breaks:
  name: "Переносы при вызове функции"
  regex: "\\w+\\((\\w+: |)[\\w.|\\[\\]\":$%]+,$"
  message: "Переносы строк необходимо делать согласно гайдлайну"
  severity: error
```
```diff
- func myMethod(parameter1: String
-               parameter2: String)
+ func myMethod(
+     parameter1: String
+     parameter2: String
+ )
```

## Переносы при многострочных массивах
```yml
custom_array_line_breaks:
  name: "Переносы при многострочных массивах"
  regex: "\\[[\\d\\w,.]+,$"
  message: "Переносы строк для многострочных массивов необходимо делать согласно гайдлайну"
  severity: error
```
```diff
- let array = [1,
-              2]
+ let array = [
+     1,
+     2
+ ]
```

## Замыкание должно иметь суффикс `Handler`
```yml
custom_handler_names:
  name: "Название замыкания не содержит суффикс Handler"
  regex: "(var|let) \\w+(Action|Block): (\\(|\\w+(Action|Handler))"
  message: "Замыкание должно иметь суффикс Handler"
  severity: error
```
```diff
- let tapAction: () -> ()
+ let tapHandler: () -> ()
```

## Force unwrapping для замыканий запрещен
```yml
custom_forced_handlers:
  name: "Force unwrapping для замыканий запрещен"
  regex: "(var|let) \\w+: (\\(*\\w*\\)->\\(\\w*\\)*|\\w+(Action|Handler))!"
  message: "Вместо var handler: Handler! используй var handler: Handler?"
  severity: error
```
```diff
- var handler: Handler!
+ var handler: Handler?
```

## Литералы цвета запрещены
```yml
custom_color_literal:
  name: "Литералы цвета запрещены"
  regex: "#colorLiteral"
  message: "Используй готовые цвета, добавленные в приложение"
  severity: error
```
```diff
- #colorLiteral(red: 1.0, green: 1.0, blue: 1.0, alpha: 1.0)
+ UIColor.background
```

## Неверное написание `@objc`
```yml
custom_objc_inline:
  name: "Неверное написание @objc"
  regex: "@objc\\n"
  message: "Пиши @objc в одну строку"
  severity: error
```
```diff
- @objc
- func myMethod()
+ @objc func myMethod()
```

## Использовать хардкод для текста в Presentation-слое запрещено
Требуется выделение Presentation-слоя
Правило подходит, если у вас вынесены текста. Мы рекомендуем использовать для этого [SwiftGen](https://github.com/SwiftGen/SwiftGen)
```yml
custom_texts:
  name: "Использовать хардкод для текста в Presentation запрещено"
  included: "path/to/Presentation"
  regex: "\"[a-zA-Zа-яА-Я]"
  message: "Используй структуру Texts"
  match_kinds:
    - string
  severity: error
```
```diff
- Text("Приветствую")
+ Text(Texts.greetings)
```

## Документация на неприватный интерфейс
Рекомендуется документировать неприватные части кода. Исключение - Canvas Preview
```yml
custom_doc:
  name: "Отсутствует документация на неприватный интерфейс двумя строками ниже"
  regex: "\\n\\n(public|open|struct|class|enum) (?!\\w+_Previews)"
  message: "Добавь документацию через комбинацию клавиш CMD+OPTION+/"
  severity: error
```
```diff
+ /// Документация
class MyClass {
```

## Использование методов создания `View` без параметров запрещено
```yml
custom_void_parameters_method:
  name: "Использование методов создания View без параметров запрещено"
  regex: "\\(\\) -> some View \\{"
  message: "Используй var вместо метода"
  severity: error
```
```diff
- func makeButton() -> some View {
+ var button: some View {
```

## Импорт по умолчанию запрещен
Требуется добавить файл Imports.swift с `@_exported import` стандартных библиотек
```yml
custom_imports:
  name: "Импорт по умолчанию запрещен"
  excluded: "Imports.swift"
  regex: "import (Foundation|CoreGraphics|UIKit)"
  message: "Не используй импорт по умолчанию"
  severity: error
```
```diff
- import UIKit

class UserView: UIView {
```

## GeometryProxy должен иметь суффикс `Proxy`
```yml
custom_geometryproxy_name:
  name: "GeometryProxy должен иметь суффикс Proxy"
  regex: "GeometryReader \\{ \\w*(?<!(proxy)|(Proxy)) "
  message: "Добавь суффикс Proxy"
  severity: error
```
```diff
- GeometryReader { reader in
+ GeometryReader { proxy in
```
