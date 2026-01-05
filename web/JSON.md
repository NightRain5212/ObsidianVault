
## JSON文件

- JSON: **J**ava**S**cript **O**bject **N**otation,JavaScript 对象表示法
- `json`是**存储和交换文本信息**的语法

## JSON格式

- JSON 的格式基于两种核心结构：
	1. **对象 (Object)**：一个无序的“键/值”对集合。
	2. **数组 (Array)**：一个有序的值列表。
- 这些结构可以相互嵌套，形成复杂的数据模型。

- 基本数据类型
	- `String`：`"你好"`，`"hello world!"`，只能使用双引号
	- `Number`：整数或浮点数，不需要引号。`3.14`，`-24`
	- `Boolean`：布尔值`true`，`false`
	- `Array`：数组用`[]`包围，内部由一个或多个值组成，值之间用逗号 `,` 分隔。
		- 数组中的值可以是任何有效的`JSON` 数据类型，并且类型可以不同。
		- 如：`[ "Google", "Runoob", "Taobao", 1, null ]`
	- `null`： 表示“无”或“空”值。必须是小写 `null`，不加引号。
	- `Object(对象)`：用`{}`括起来的键值对组成，键值用`:`分隔，键值对用`,`分隔
		- **键 (Key)** 必须是**双引号**引起来的字符串。
		- **值 (Value)** 可以是任何有效的 `JSON` 数据类型（字符串、数字、布尔值、数组、或另一个对象）。
		- `{key1 : value1, key2 : value2, ... keyN : valueN }`
- `json`文件后缀为`.json`
- `JSON` 文本的 `MIME` 类型是 `application/json`
```json
{
  "name": "张三",
  "age": 30,
  "isMember": true,
  "address": {
    "street": "人民路123号",
    "city": "上海",
    "postalCode": "200000"
  },
  "phoneNumbers": [
    {
      "type": "家庭",
      "number": "021-88888888"
    },
    {
      "type": "手机",
      "number": "13800138000"
    }
  ],
  "spouse": null
}
```

