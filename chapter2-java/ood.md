---
description: Design a Parking Lot
---

# OOD

## Design a Parking Lot

### Step 1: Understand / Analyze the Use Case \(明确这个程序/系统是做什么的\)

> 在问题领域 用自然语言描述问题是什么

Use Case: Describe the parking lot building. Vehicle monitoring? What kind of parking lot？

* One level or multiple levels?
* Parking-spot / vehicle sizes?
* Need to track the location?
* Fee? 

### Step 2: Classes and their relationships

> 用程序语言 通过建模来描述问题

每出现一个事物的种类，就定义一个class，一个类只做一件事，把这一件事做好。

class relationships 区别 是否可以独立存在: 

* **Composition**: \(has a\) ownership relationship between two classes 手足
* **Aggregation**: holds a reference of another, no real ownership 男女朋友
* **Association**: an activity between 2 classes

> inheritance \(is a\)

对于parking lot的语境，上级hold下级的reference

* level无法脱离parking lot存在，所以是composition
* parking spot无法脱离level存在，也是composition
* vehicle和parking spot是aggregation
* vehicle和car、truck是inheritance的关系
* parking lot和vehicle是association、aggregation

#### 方案比较 有level vs 没有level

1）Parking Lot -- Level -- Parking Spot  
2\)   Parking Lot -- Parking Spot

1的好处是easy to evolve, 未来不但能扩展楼层数，还能specify每一层的功能 2的好处是方便prototyping  

### Step 3: For complicated designs, firs focus on public methods 如何调用

1. Basic functionality: tell whether or not there is available spot in the parking lot
2. Possible Extensions: provide available spot locations, assign spot to the vehicle

```java
public class ParkingLot{
    List<Level> level;
    
    Ticket park(Vehicle v); //开着车拿ticket走
    Vehicle leave(Ticket t); //拿ticket取车
    
    public b
}
```

