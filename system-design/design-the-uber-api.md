# 设计：优步 API

## 澄清要问的问题

- **问：Uber 有很多不同的服务：有核心的出租车服务 Uber，有 UberEats，有 UberPool —— 我们是为所有这些服务设计 API，还是只为其中一个服务设计 API？**

  答：让我们只设计核心出租车服务 API —— 而不是 UberEats 或 UberPool。

- **问：乍一看，我们似乎既需要面向乘客的 API 也需要面向驾驶员的 API —— 这有意义吗？如果是，我们应该同时设计这两者吗？**

  答：是的，这完全有道理。是的，让我们设计两者，从面向乘客的 API 开始。

- **问：为了确保我们在同一个页面上，我设想这个 API 将支持以下功能：他们与司机匹配；然后他们可以跟踪他们的行程，直到他们到达目的地，此时行程完成。在整个过程中，还有更多功能需要支持，例如能够在乘客被接走之前跟踪乘客的司机在哪里，也许能够取消乘车等。这是否符合您的大部分想法?**

  答：是的，这正是我的想法。您可以在开始设计 API 时逐步提炼出细节。

- **问：我们是否需要处理诸如创建优步账户、设置支付偏好、联系优步等事情？诸如给司机打分、给司机小费之类的事情呢？**

  答：现在，让我们跳过这些，真正关注核心的出租车服务。

- **问：为了确认一下，您希望我为各种 API 写出函数签名，包括参数、它们的类型、返回值等，对吗？**

  答：是的，没错。

## 1 收集要求

与任何 API 设计面试问题一样，我们要做的第一件事就是收集 API 需求；我们需要弄清楚我们正在构建什么 API。

我们正在设计优步提供的核心出租车服务。乘客可以通过手机预订行程，然后与司机匹配；他们可以在整个行程中跟踪司机的位置，直到行程结束或取消；他们还可以查看行程的价格以及整个行程中到达目的地的预计时间等。

优步提供的核心出租车服务有面向乘客的一面和面向司机的一面；我们将为双方设计 API。

## 2 制定计划

重要的是要将必要的信息组织起来，并就我们将如何处理我们的设计制定一个清晰的计划。我们的 API 中主要的、可能存在争议的部分是什么？我们为什么要做出某些设计决策？

我们将围绕 Ride 实体集中我们的 API；每个 Uber 行程都会有一个关联的行程，其中包含有关行程的信息，包括有关乘客和司机的信息。

由于普通的 Uber 乘车只能有一名乘客（一个乘客帐户——叫车的那个）和一名司机，我们将通过乘客和司机 ID 巧妙地处理与乘车操作相关的所有许可。换句话说，GetRide 和 EditRide 之类的操作将完全依赖传递的 userId，即调用它们的乘客或驾驶员的 userId，以返回与该乘客或驾驶员相关的适当乘车。

我们将首先定义 Ride 实体，然后再设计面向乘客的 API，然后是面向驾驶员的 API。

## 3 实体

Ride 实体将具有唯一的 ID、有关其乘客和驾驶员的信息、状态以及有关乘车的其他详细信息。

- Ride

  ```
  rideId: string
  passengerInfo: PassengerInfo（乘客信息）
  driverInfo?: DriverInfo（司机信息）
  rideStatus: RideStatus - enum CREATED/MATCHED/STARTED/FINISHED/CANCELED（状态）
  start: GeoLocation（起点地理位置）
  destination: GeoLocation（终点地理位置）
  createdAt: timestamp（创建时间）
  startTime: timestamp（出发时间）
  estimatedPrice: int（最终价格）
  timeToDestination: int（预计剩余时间）
  ```

当我们进入到 API 设计时，我们将解释为什么 driverInfo 是可选的。

- PassengerInfo

  ```
  id: string
  name: string
  rating: int（评分）
  ```

- DriverInfo

  ```
  id: string
  name: string
  rating: int（评分）
  ridesCount: int（完成订单数）
  vehicleInfo: VehicleInfo（汽车信息）
  ```

- VehicleInfo

  ```
  licensePlate: string（车牌照）
  description: string（描述）
  ```

## 4 乘客 API

面向乘客的 API 将相当简单。它将包括围绕 Ride 实体的简单 CRUD 操作，以及在整个乘车过程中实时调用获取驾驶员位置的接口。

```
CreateRide(userId: string, pickup: Geolocation, destination: Geolocation)
  => Ride
```

用法：当乘客预订乘车时调用；一个 Ride 是在没有 DriverInfo 和 `CREATED` RideStatus 的情况下创建的；Uber 后端调用一个内部 FindDriver API，该 API 使用一种算法来寻找最合适的司机；一旦找到司机并接受乘车，后端就会使用司机的信息和 `MATCHED` RideStatus 调用 EditRide。

```
GetRide(userId: string)
  => Ride
```

用法：在创建租车订单后每隔几秒轮询一次，直到订单的状态为 `MATCHED`；之后，在整个行程中每 20-90 秒进行一次轮询，以更新行程的预估价格、到达目的地的时间或者司机取消此次行程变更 RideStatus 等等。

```
EditRide(userId: string, [...params?: Ride 对象上需要编辑的所有属性])
  => Ride
```

```
CancelRide(userId: string)
  => void
```

EditRide 的封装 —— 有效地调用 EditRide(userId: string, rideStatus: `CANCELLED`)。

```
StreamDriverLocation(userId: string)
  => Geolocation
```

用法：通过持久连接的 websocket 连接连续流式传输司机的位置；其位置被流式传输的司机是与绑定到传递的 userId 的 Ride 相关联的司机。

## 5 司机 API

面向司机的 API 将依赖于 Ride 实体的一些相同的 CRUD 操作，并且它还将具有一个 SetDriverStatus 接口以及一个将司机的位置推送给正在实时获取司机位置的乘客的接口。

```
SetDriverStatus(userId: string, driverStatus: DriverStatus)
  => void

DriverStatus: enum UNAVAILABLE/IN RIDE/STANDBY
```

用法：当司机想搜索订单、正在执行订单或当天结束工作时调用；当使用 STANDBY 调用时，Uber 后端调用内部 FindRide API，该 API 使用一种算法将司机排入等待乘车的司机队列中并找到最合适的订单；一旦找到订单，该订单会在内部对司机锁定 30 秒，在此期间，司机可以接受或拒绝订单；一旦司机接受了订单，内部后端使用司机的信息和 `MATCHED` RideStatus 调用 EditRide。

```
GetRide(userId: string)
  => Ride
```

用法：在整个行程中每 20-90 秒轮询一次，以更新行程的预估价格、到达目的地的时间、订单是否被取消等。

```
EditRide(userId: string, [...params?: Ride 对象上需要编辑的所有属性])
  => Ride
```

```
AcceptRide(userId: string)
  => void
```

调用 EditRide(userId, `MATCHED`) 和 SetDriverStatus(userId, `IN_RIDE`)。

```
CancelRide(userId: string)
  => void
```

EditRide 的封装 —— 有效地调用 EditRide(userId, `CANCELLED`)。

```
PushLocation(userId: string, location: Geolocation)
  => void
```

用法：在整个乘车过程中被司机使用的移动终端持续调用；将司机的位置推送给正在流式传输该司机位置的相关乘客；乘客是与绑定到传递的 userId 的 Ride 相关联的乘客。

## 6 优步拼车

作为一个延伸目标，您的面试官可能会要求您考虑如何扩展您的设计以处理 UberPool 乘车。

优步拼车允许多名乘客（不同的优步帐户）以更便宜的价格共享优步乘车。

处理 UberPool 乘车的一种方法是允许 Ride 对象具有多个乘客信息。在这种情况下，Rides 还必须维护一个列表，其中列出了该行程将停靠的所有目的地，以及个别乘客的相关最终目的地。

处理 UberPool 乘车的一种更简洁的方法可能是引入一个全新的实体，即 PoolRide 实体，该实体将附加一个 Ride 列表 。乘客在预订 UberPool 乘车时仍会调用 CreateRide 接口，因此他们仍将拥有自己的正常 Ride 实体，但该实体将与 UberPool 乘车信息的其余部分 一起附加到 PoolRide 实体。

司机可能会有一个额外的 DriverStatus 值来指示他们是否在执行订单（载客中）但仍接受新的 UberPool 乘客。

大多数其他功能将保持不变；乘客和司机仍将不断地轮询 GetRide 接口以获取有关乘车的最新信息，乘客仍将实时获取其司机的位置，乘客仍将能够取消他们的个人行程（订单）等。

Last Modified 2022-04-05
