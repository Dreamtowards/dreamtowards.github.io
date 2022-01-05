

## Wek9. ERDs 对 Relational Model 关系模型的进阶映射

### 学习目标
- 修正 - 依赖的类型
- 对依赖时间对数据 建模
- 约束和引用集成
- ERDs
  - 定义实体
  - 强和弱实体
  - 关系实体
- 学习案例 - 批发鞋店

### 在标准化过程中修正依赖

#### 1NF -> 2NF 识别和纠正局部依赖 Partial Dependencies

```
Payment (EmpNum, Date, Rate, Hours, Pay, EName)
->
  Employee (EmpNum, Name)
  Payment (EmpNum, Date, Rate, Hours, Pay)
```


#### 2NF -> 3NF 识别和纠正传递性依赖 Transitive Dependencies

```
Staff (StaffName, RoomNum, Department)
->
  Staff(StaffName, RoomNum*)
  Room(RoomNum, Department)
```

#### 3NF -> BCNF 识别和纠正BCNF依赖 BCNF Dependencies


```
// 不是很能理解例子的要干。似乎不是个成功的例子 subject目标重叠/模糊.
StudentSubject (StudentName, SubjectLecturer, SubjectName)
-> 
  StudentSubjects(StudentName, SubjectLecturer)
  Lecturer(SubjectLecturer, SubjectName)
```




```
1.
我不确定是不是这样的，我要看一个示范答案才知道。
他说要列举关系，但是关系可以有很多种 有很多无意义的关系，这里的关系表述了结构

AdventureCardId -> (ExpiryDate)
BookingId -> (ArrivalTime, DepatureTime, AdultsNum, ChildrenNum, PaymentId*, TotalPrice, TotalPaymentAmount)
ParkId -> (Address, Name, Phone, ManagerId*)
SiteId -> (ConcreteSlabFlag, EnsuiteFlag, PoweredFlag, MaxCaravanLength, MaxPeopleAllowed, Category)
CustId -> (Address, Email, Name, Phone)
ManagerId -> (Phone, Name)
PaymentId -> (Amount, Time, Type)


2.

2.1.1 
BookingID -> (AdventureCardNum, ArrivalDate, CustID, DepartureDate, PaymentID, TotalPaymentAmount, TotalPrice, SiteNum)
AdventureCardNum -> AdventCardExpiryDt
CustID -> (CustAddress, CustEmail, CustName, CustPhone)
PaymentID -> (PaymentAmount, PaymentDate, PaymentTypeFlag)
SiteNum -> (...)

2.1.2 


3.

AdventureCard {
  id,
  expire_time
}

Booking {
  id,
  site_id*,
  arrival_time,
  departure_time,
  num_adults,
  num_children,
  payment_id*,
  total_price,
  total_payment_amount
}

Park {
  id,
  address,
  name,
  phone,
  park_manager_id*
}

ParkSite {
  id,
  park_id*,
  has_concrete_slab,
  has_ensuite,
  has_powered,
  max_caravan_length,
  max_people_allowed,
  category
}

ParkManager {
 id,
 name,
 phone
}

Customer {
  id,
  address,
  email,
  name,
  phone
}

Payment {
  id,
  amount,
  time,
  type
}

```
