# API 设计

## ACL

访问控制列表（Access- Control List）的缩写。该术语通常用于指代许可模型：系统中的哪些用户可以执行哪些操作。例如，API 通常与 ACLS 一起定义哪些用户可以删除、编辑或查看某些实体。

## 分页

当一个网络请求可能需要一个非常大的响应时，相关的 API 可能被设计为只返回该响应的一个分页（即响应的有限部分），并伴随着一个标识符或令牌供客户端请求下一个需要的分页。

设计列表端点时经常使用分页。例如，在 YouTube 趋势页面上列出视频的端点可能会返回大量视频列表。由于可能存在网络速度较低的情况，影响在移动设备上的加载速度，且根本不是最佳选择，
因为大多数用户只会滚动浏览前十或二十个视频。因此，API 可以设计为仅响应该列表的前几个视频；在这种情况下，我们会说 API 响应是分页的。

## CRUD 操作

代表创建（Create）、读取（Read）、更新（Update）、删除（Delete）操作。这四个操作通常作为功能系统的基石，也可以发现它们是许多 API 设计的核心。
CRUD 这个词很可能在 API 设计面试中出现。

## Demo

api

```markdown
# API Definition

## Entity Definitions

### Charge:

- id: uuid
- customer_id: uuid
- amount: integer
- currency: string (or currency-code enum)
- status: enum ["succeeded", "pending", "failed"]

### Customer:

- id: uuid
- name: string
- address: string
- email: string
- card: Card

### Card

## Endpoint Definitions

### Charges

CreateCharge(charge: Charge)
=> Charge
GetCharge(id: uuid)
=> Charge
UpdateCharge(id: uuid, updatedCharge: Charge )
=> Charge
ListCharges(offset: integer, limit: integer)
=> Charge[]
CaptureCharge(id: uuid)
=> Charge

### Customers

CreateCustomer (customer: Customer)
=> Customer
GetCustomer(id: uuid )
=> Customer
UpdateCustomer(id: uuid, updatedCustomer: Customer)
=> Customer
DeleteCustomer(id: uuid)
=> Customer
ListCustomers(offset: integer, limit: integer)
=> Customer[]
```

charge.json

```json
{
  "id": "73a6fa7e-96e2-11ec-b909-0242ac120002",
  "customer_id": "859925cc-96e2-11ec-b909-0242ac120002",
  "amount": 10000,
  "currency": "usd",
  "status": "pending"
}
```

customer.json

```json
{
  "id": "67471260-9f58-4780-9ef6-8d2722ffb823",
  "name": "Cersei Lannister",
  "address": "1 King's Landing",
  "email": "cersei.lannister@gmai1.com",
  "card": {}
}
```

Last Modified 2022-02-26
