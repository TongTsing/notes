在Go语言中，逻辑运算符用于处理布尔类型的表达式。这些运算符包括逻辑与、逻辑或和逻辑非。以下是Go语言中的逻辑运算符及其用法：

### 1. 逻辑与 (`&&`)
逻辑与运算符用于连接两个布尔表达式，只有当两个表达式都为 `true` 时，结果才为 `true`。

```go
package main

import (
    "fmt"
)

func main() {
    a := true
    b := false

    fmt.Println(a && b) // 输出: false
    fmt.Println(a && true) // 输出: true
    fmt.Println(b && false) // 输出: false
}
```

### 2. 逻辑或 (`||`)
逻辑或运算符用于连接两个布尔表达式，只要其中一个表达式为 `true`，结果就为 `true`。

```go
package main

import (
    "fmt"
)

func main() {
    a := true
    b := false

    fmt.Println(a || b) // 输出: true
    fmt.Println(a || true) // 输出: true
    fmt.Println(b || false) // 输出: false
}
```

### 3. 逻辑非 (`!`)
逻辑非运算符用于取反一个布尔表达式，如果表达式为 `true`，则结果为 `false`；反之亦然。

```go
package main

import (
    "fmt"
)

func main() {
    a := true
    b := false

    fmt.Println(!a) // 输出: false
    fmt.Println(!b) // 输出: true
}
```

### 示例：结合使用逻辑运算符

以下是一个示例，演示如何结合使用逻辑运算符进行复杂条件判断：

```go
package main

import (
    "fmt"
)

func main() {
    age := 25
    hasLicense := true

    if age >= 18 && hasLicense {
        fmt.Println("可以开车")
    } else {
        fmt.Println("不能开车")
    }

    isStudent := false
    hasDiscount := age < 18 || isStudent

    if hasDiscount {
        fmt.Println("可以享受折扣")
    } else {
        fmt.Println("不能享受折扣")
    }
}
```

在这个示例中：
- 使用 `&&` 检查年龄是否大于等于 18 且是否有驾照。
- 使用 `||` 检查年龄是否小于 18 或是否是学生。

### 注意事项
- 逻辑运算符的优先级较低，通常在复杂表达式中使用括号来明确运算顺序。
- 短路效应：在逻辑与 `&&` 运算中，如果左侧表达式为 `false`，右侧表达式将不会被计算；在逻辑或 `||` 运算中，如果左侧表达式为 `true`，右侧表达式将不会被计算。

这些逻辑运算符是编写条件判断和控制程序流的重要工具，在Go语言中广泛使用。