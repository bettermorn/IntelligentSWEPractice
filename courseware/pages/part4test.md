---
layout: cover
class: text-center
background: '#5f1e3a'
---

# 第四部分
## 系统测试

第 11-13 周


---
layout: two-cols
---

# 第11周：理论

- 软件测试的方法

::right::

# 第11周：实践

- 编写软件作品的测试用例

📋 检查 确认测试用例

---
layout: cover
class: text-center
background: '#5f1e3a'
---

# 第11周
## 软件测试的方法

---

# 本周学习目标

- 理解测试的目的：**证明正确性** ≠ **降低缺陷风险**
- 掌握测试金字塔：单元/集成/系统/验收测试
- 区分**黑盒测试**（功能）与**白盒测试**（结构）
- 掌握**等价类划分、边界值分析**等经典设计方法
- 会写规范的测试用例文档
- 了解自动化测试工具：JUnit / pytest / Jest / Selenium


---

# 测试的目的：一个关键认知


### ❌ 常见误解

"测试是为了证明程序没错"

实际上：**测试永远无法证明没有 Bug**（Dijkstra 名言："程序测试只能证明 Bug 存在，不能证明 Bug 不存在"）





### ✅ 正确理解

测试是为了：

- **降低**缺陷流入生产环境的**风险**
- 给重构/修改提供**安全网**
- 驱动**设计和重构**（测试先行）
- 用**成本换质量**：越晚发现缺陷，修复成本越高


📈 缺陷发现越晚修复成本越高（需求阶段 1x → 发布后 100x）





---
layout: two-cols
---

# 测试金字塔



```mermaid
graph TD
    A[UI / E2E 测试
少量] --> B[集成测试
适量]
    B --> C[单元测试
大量]
    C --> D[基础层：多、快、便宜]
```

- **单元测试**：最多，最快，隔离小函数
- **集成测试**：验证模块配合
- **端到端（E2E）**：验证完整流程，慢，贵



::right::

### 比例建议

| 层级 | 数量比例 | 运行速度 |
|---|---|---|
| 单元 | ~70% | 毫秒级 |
| 集成 | ~20% | 秒级 |
| E2E | ~10% | 分钟级 |


⚠️ 常见误区："测试金字塔倒置"——全是 E2E，又慢又脆，改一处全崩


---

# 黑盒测试 与 白盒测试





### 黑盒（功能测试）
只看输入输出，不关心内部实现

```python
# 只测接口行为
def test_login_success(client):
    resp = client.post('/api/login',
        json={"username": "admin", "password": "123456"})
    assert resp.status_code == 200
    assert "token" in resp.json()
```





### 白盒（结构测试）
关注代码内部路径、分支、条件

```python
# 依据代码逻辑设计用例，覆盖每个分支
def test_calculate_positive(calc):
    assert calc.calculate(Operation.ADD, 10, 5) == 15

def test_calculate_divide_by_zero(calc):
    with pytest.raises(ZeroDivisionError):
        calc.calculate(Operation.DIVIDE, 10, 0)
```


💡 实际工作中两种结合：黑盒设计测试目标，白盒补充覆盖盲区（识别未测试的分支）


---
layout: two-cols
---

# 测试用例设计方法 1：等价类划分

将输入域划分成若干个**等价类**，每个类取一个代表值即可

**案例**：登录功能的"年龄"字段，合法范围 18-60

| 等价类类型 | 数据 | 预期结果 |
|---|---|---|
| 有效等价类 | 18、35、60 | 通过 |
| 无效等价类-下限 | 17 | 提示"年龄过小" |
| 无效等价类-上限 | 61 | 提示"年龄过大" |
| 无效等价类-非数字 | "abc" | 提示"格式错误" |
| 无效等价类-空值 | "" | 提示"必填" |

::right::

```python
import pytest

def validate_age(age: str) -> str:
    if not age:
        return "必填"
    if not age.isdigit():
        return "格式错误"
    a = int(age)
    if a < 18:
        return "年龄过小"
    if a > 60:
        return "年龄过大"
    return "通过"

@pytest.mark.parametrize("age,expected", [
    ("", "必填"), ("abc", "格式错误"),
    ("17", "年龄过小"), ("61", "年龄过大"),
    ("35", "通过"), ("18", "通过"), ("60", "通过"),
])
def test_validate_age(age, expected):
    assert validate_age(age) == expected
```

---

# 测试用例设计方法 2：边界值分析

**原理**：程序错误最容易发生在**边界附近**，所以对边界值及边界两侧的值都要测

**案例**：输入范围 [18, 60]

| 测试点 | 值 | 说明 |
|---|---|---|
| 下边界 | 18 | 恰好合法 |
| 下边界-1 | 17 | 非法 |
| 下边界+1 | 19 | 合法 |
| 上边界 | 60 | 恰好合法 |
| 上边界+1 | 61 | 非法 |
| 上边界-1 | 59 | 合法 |


🎯 经验：开发 80% 的 Bug 出在边界，边界值分析性价比最高


---

# 测试用例设计方法 3：判定表

适合**多条件组合**的场景，列出所有条件组合及对应动作

**案例**：图书借阅系统中，"是否能续借"判定

| 规则 | 是否逾期 | 是否欠费 | 续借次数<3 | 是否允许续借 |
|---|---|---|---|---|
| 1 | N | N | Y | ✅ 允许 |
| 2 | N | N | N | ❌ 不允许(超次数) |
| 3 | N | Y | Y | ❌ 不允许(有欠费) |
| 4 | N | Y | N | ❌ 不允许 |
| 5 | Y | N | Y | ❌ 不允许(逾期) |
| 6 | Y | Y | Y | ❌ 不允许 |
| 7 | Y | N | N | ❌ 不允许 |
| 8 | Y | Y | N | ❌ 不允许 |

```java
// 对应代码逻辑
public boolean canRenew(Loan loan) {
    if (loan.isOverdue()) return false;
    if (loan.hasUnpaidFine()) return false;
    if (loan.renewalCount() >= 3) return false;
    return true;
}
```


💡 用法：先画判定表找出所有分支，再据此写测试，能显著提高覆盖率


---

# 测试用例设计方法 4：因果图 & 场景法（简述）





### 因果图法
- 找"原因"（输入条件）与"结果"（输出动作）的依赖关系
- 用逻辑符（与/或/非）组合
- 适合输入条件相互影响的情况





### 场景法（面试常考）
- 按**业务流程**组织用例，走完整流程
- 基本流：正常完成任务
- 备选流：异常分支、边界处理
- 适合电商下单、登录、支付等业务


用"购物→下单→支付→发货"串多条场景，
比孤立测每个按钮更能发现流程缺陷





---
layout: two-cols
---

# JS 测试示例：Jest

```javascript
// product.js
export function calculateTotal(items) {
  return items.reduce((sum, item) =>
    sum + item.price * item.quantity, 0);
}
```

```javascript
// product.test.js
import { calculateTotal } from './product.js';

describe('calculateTotal', () => {
  test('多个商品正确求和', () => {
    const items = [
      { price: 10, quantity: 2 },  // 20
      { price: 5, quantity: 3 },   // 15
    ];
    expect(calculateTotal(items)).toBe(35);
  });

  test('空购物车返回0', () => {
    expect(calculateTotal([])).toBe(0);
  });
});
```

::right::

# 接口测试示例：Postman / REST

```http
### 登录接口 - 正常
POST /api/login
Content-Type: application/json

{ "username": "admin", "password": "123456" }

> {% client.test("登录成功", function() {
    client.assert(response.status === 200, "状态码200");
    client.assert(response.json().token !== undefined, "返回token");
}); %}

### 登录接口 - 密码错误
POST /api/login
Content-Type: application/json

{ "username": "admin", "password": "wrong" }
```


💡 还可用 Postman / curl / Katalon 做 API 测试，或 Selenium / Playwright 做 E2E UI 测试


---

# 自动化测试工具速查表

| 语言/场景 | 单元测试 | 覆盖率 | E2E |
|---|---|---|---|
| Java | JUnit 5 | JaCoCo | Selenium |
| Python | pytest | coverage.py | Selenium / Playwright |
| JS/TS | Jest / Vitest | NYC | Playwright / Cypress |
| Go | testing | go test -cover | Selenium |
| 接口 | Postman / REST | — | — |

---

# 编写测试用例文档（规范模板）



| 用例编号 | 用例名称 | 前置条件 | 测试步骤 | 输入数据 | 预期结果 | 优先级 |
|---|---|---|---|---|---|---|
| TC-001 | 正常登录 | 用户已注册 | 1.打开登录页
2.输入账号密码
3.点击登录 | 正确账号+密码 | 登录成功跳转首页 | 高 |
| TC-002 | 密码错误 | 用户已注册 | 同上 | 错误密码 | 提示"密码错误" | 高 |
| TC-003 | 账号不存在 | 已入注册页 | 同上 | 不存在的账号 | 提示"用户不存在" | 中 |
| TC-004 | 密码为空 | 已入注册页 | 1.输入账号
2.密码留空
3.点击登录 | 空密码 | 提示"请输入密码" | 高 |




📋 本周作业：为你们的软件作品编写至少 10 个测试用例（覆盖等价类、边界值、场景法），包含预期结果


---
layout: center
---

# 本周实践任务



1. 为作品功能的**核心逻辑**编写单元测试（JUnit / pytest / Jest）
2. 用**等价类 + 边界值**为至少一个输入字段设计测试用例
3. 用**场景法**为一条关键业务流程编写端到端测试
4. 统计覆盖率，确保核心逻辑 ≥ 60%（第12周还会继续测）




📋 本周检查：确认学生的测试用例（提交测试用例文档 + 测试代码）


---

# 第12周

— 无新理论内容 —



## 实践
- 用测试用例测试作品
- 修改作品缺陷


📋 检查：确认测试用例


---
layout: two-cols
---

# 第13周：理论

- 软件产品原型测试方法

::right::

# 第13周：实践

- 小组交叉测试软件作品原型
---

# 软件产品原型测试方法
- 实际效果与用户期望
- 产品的可用性
- 竞品比较
- 功能设计
- 用户体验

# 软件产品原型测试方法

结对测试产品原型。从实际效果与用户期望、产品的可用性、竞品比较、功能设计和用户体验等方面回答问题，并给出建议。

## 本小组名称

## 被测试小组名称

## 回答问题
给出问题的答案。

产品原型测试

| 问题                                                         | 参考目标           | 答案 |
| :----------------------------------------------------------- | ------------------ | ---- |
| 能否从首页看出产品能解决怎样的问题，首页哪些地方有吸引力（有价值） | 实际效果与用户期望 |    |
| 是否使用过同类产品或者网站？                                 | 竞品比较           |    |
| 是否比常用产品好？                                           | 产品的可用性       |   |
| 有多大可能性向同行推荐这款产品？                             | 产品的可用性       |  |
| 除了此产品，是否有其他解决办法？                             | 竞品比较           |   |
| 产品的功能是否能够满足需要？                                 | 功能设计           |   |
| 是否能快速找到自己想要的功能？                               | 用户体验           |    |
| 能否看到每个UI要求用户完成的最重要的任务？                   | 用户体验           |   |
| 每个UI用到的方案是否最简单？                                 | 用户体验           |    |
| UI的信息是否组织得当？                                       | 用户体验           |    |
| 设计是否易用且一目了然？                                     | 用户体验           |    |
| 设计标准是否一致？                                           | 用户体验           |   |
| 是否能减少用户点击次数？                                     | 用户体验           |    |



## 建议
（在这里分条给出给作品的建议）

