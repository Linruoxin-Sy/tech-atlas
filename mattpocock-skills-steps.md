## Small Feature

例如：

> Button 增加 loading 状态。

直接：

```
/implement
```

或者：

```
/ask-matt
```

让 Agent 判断。

------

## Medium Feature

例如：

> Todo 增加 Archive / Restore。

采用：

```
/grill-with-docs
        ↓
/to-spec
        ↓
/to-tickets
        ↓
/implement
        ↓
/code-review
```

日常使用的模式。

------

## Big Feature

例如：

> 给系统增加 Team / Organization。

采用：

```
/grill-with-docs
        ↓
/wayfinder
        ↓
decision tickets
        ↓
/to-spec
        ↓
/to-tickets
        ↓
/implement
        ↓
/code-review
```

大型需求应该采用的模式。

## Bug Fix

例如：

> 用户说：“登录之后偶尔跳回登录页面”

采用：

```
/diagnosing-bugs
```

它的流程是：

```
Bug
 ↓
Make it fail
 ↓
Minimise
 ↓
Hypothesise
 ↓
Instrument
 ↓
Fix
 ↓
Regression test
```

修复 Bug 应该采用的模式。