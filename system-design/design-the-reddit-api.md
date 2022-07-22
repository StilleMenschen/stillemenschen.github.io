# 设计：Reddit 论坛 API

API 包括以下 2 个实体：

- User | userId: string, ...
- SubReddit | subredditId: string, ...

这两个实体可能都有其他字段，但就本问题而言，不需要这些其他字段。

你的 API 应该支持 Reddit 上的 subreddit 的基本功能。

## 澄清要问的问题

- **问：为了确保我们在同一个页面上：subreddit 是一个在线社区，用户可以在其中撰写帖子、评论帖子、赞成/反对帖子、分享帖子、举报帖子、成为版主等。-- 这是正确的，我们是否正在设计所有这些功能？**

  答：是的，这是正确的，但让我们保持简单，只专注于撰写帖子、写评论和赞成/反对。您可以忘记所有辅助功能，例如分享、举报、审核等。

- **问：所以我们真的专注于 subreddit 非常狭窄但核心的方面：写帖子、评论和投票？**

  答：是的。

- **问：我正在考虑为位于 subreddit 中的主要实体定义模式，然后定义它们的 CRUD 操作——像 Create/Get/Edit/Delete/List\<Entity\>这样的方法 —— 这是否符合设计的要求**

  答：是的，并确保包含方法签名 —— 每个方法接受什么以及每个方法返回什么。还包括每个参数的类型。

- **问：我确定的实体是帖子、评论和投票（赞成和反对）。这看起来准确吗？**

  答：是的。这些是您应该定义的 3 个核心实体以及您正在设计的 API。

- **问：我们应该设计 subreddit 的其他功能吗？**

  答：是的，您还应该允许人们对帖子进行奖励。奖励是一种特殊的货币，可以用真钱购买并赠送给评论和帖子。用户可以用真金白银购买一定数量的奖励，并且可以对帖子和评论进行奖励（每帖子/评论一个奖励）。

## 1 收集要求

与任何 API 设计面试问题一样，我们要做的第一件事就是收集 API 需求；我们需要弄清楚我们正在构建什么 API。

我们正在设计 Reddit 上 subreddit 功能的核心用户流程。用户可以在 subreddits 上撰写帖子，他们可以评论帖子，他们可以投票/反对帖子和评论。

我们将定义三个主要实体：Posts、 Comments 和 Votes，以及它们各自的 CRUD 操作。

我们还将设计一个用于在 Reddit 上购买和奖励的 API。

## 2 制定计划

重要的是要将必要的信息组织起来，并就我们将如何处理我们的设计制定一个清晰的计划。我们的 API 中主要的、可能存在争议的部分是什么？我们为什么要做出某些设计决策？

第一个主要争论点是是否只存储评论和帖子的投票，并通过调用 EditComment 和 EditPost 方法进行投票，或者是否将它们存储为完全独立的实体 —— 可以说是帖子和评论的兄弟。将它们存储为单独的实体可以更直接地编辑或删除特定用户的投票（例如，只需调用 EditVote），因此我们将采用这种方法。

然后我们可以计划按此顺序处理帖子、评论和投票，因为它们可能会共享一些共同的结构或数据。

## 3 帖子

帖子将有一个 id、其创建者的 id（即编写它们的用户）、它们所在的 subreddit 的 id、描述和标题，以及它们创建时间的时间戳。

帖子还将统计他们的投票、评论和奖励，以显示在 UI 上。我们可以想象，当执行一些评论、投票和奖励 CRUD 操作时，一些后端服务将计算或更新这些数值。

最后，Posts 将具有可选的 deletedAt 和 currentVote 字段。Subreddits 显示已删除的帖子并带有特殊消息；我们可以使用 deletedAt 字段来完成此操作。currentVote 字段将用于向用户显示他们是否对帖子进行了投票。该字段可能会在获取帖子或投票时由后端填充。

- postId: string
- creatorId: string
- subredditId: string
- title: string
- description: string
- createdAt: timestamp
- votesCount: int
- commentsCount: int
- awardsCount: int
- deletedAt?: timestamp
- currentVote?: enum UP/DOWN

我们的 CreatePost、EditPost、GetPost 和 DeletePost 方法将非常简单。然而，需要注意的一点是，所有这些操作都将使用执行它们的用户的 userId；此 ID 可能包含身份验证信息，将用于 [ACL](/system-design/api-design?id=acl) 检查以查看执行操作的用户是否具有执行此操作所需的权限。

```
CreatePost(userId: string, subredditId: string, title: string, description: string)
  => Post

EditPost(userId: string, postId: string, title: string, description: string)
  => Post

GetPost(userId: string, postId: string)
  => Post

DeletePost(userId: string, postId: string)
  => Post
```

由于我们可以期望在给定的 subreddit 上有数百甚至数千个帖子，因此我们的 ListPosts 方法将不得不分页。该方法将采用可选的 pageSize 和 pageToken 参数，并将返回最多长度为 pageSize 的帖子列表以及 nextPageToken —— 将告知到该方法以检索下一页帖子的令牌。

```
ListPosts(userId: string, subredditId: string, pageSize?: int, pageToken?: string)
  => (Post[], nextPageToken?)
```

## 4 评论

评论将类似于帖子。他们将有一个 id、他们的创建者的 id（即编写他们的用户）、他们所在的帖子的 id、一个内容字符串以及与帖子相同的其他字段。唯一的区别是 Comments 还将有一个可选的 parentId 指向评论的父帖子或父评论。此 id 将允许 Reddit UI 创建评论树形组件以正确显示（缩进）评论。UI 还可以通过 createdAt 时间戳或 votesCount 对评论中的回复进行排序。

- commentId: string
- creatorId: string
- postId: string
- createdAt: timestamp
- content: string
- votesCount: int
- awardsCount: int
- parentId?: string
- deletedAt?: timestamp
- currentVote?: enum UP/DOWN

我们对 Comments 的 CRUD 操作将与 Posts 的操作非常相似，除了 CreateComment 方法还将接受一个可选的 parentId，指向它正在回复的评论（如果相关）。

```
CreateComment(userId: string, postId: string, content: string, parentId?: string)
  => Comment

EditComment(userId: string, commentId: string, content: string)
  => Comment

GetComment(userId: string, commentId: string)
  => Comment

DeleteComment(userId: string, commentId: string)
  => Comment

ListComments(userId: string, postId: string, pageSize?: int, pageToken?: string)
  => (Comment[], nextPageToken?)
```

## 5 投票

投票将有一个 id、其创建者的 id（即操作投票的用户）、他们的目标的 id（即他们所在的帖子或评论）和一个类型，这将是一个简单的 UP/DOWN 枚举。他们也可以有一个 createdAt 时间戳记录。

- voteId: string
- creatorId: string
- targetId: string
- type: enum UP/DOWN
- createdAt: timestamp

由于获得单次投票或列出投票似乎对我们的功能影响不是很大，因此我们将跳过设计这些接口（尽管它们很简单）。

我们的 CreateVote、EditVote 和 DeleteVote 方法将简单而有用。当用户对帖子或评论投新票时，将使用 CreateVote 方法；当用户已经对帖子或评论投了票并且对同一帖子或评论投反对票时，将使用 EditVote 方法；当用户已经对帖子或评论投了票并且只是删除了相同的投票时，将使用 DeleteVote 方法。

```
CreateVote(userId: string, targetId: string, type: enum UP/DOWN)
  => Vote

EditVote(userId: string, voteId: string, type: enum UP/DOWN)
  => Vote

DeleteVote(userId: string, voteId: string)
  => Vote
```

## 6 奖励

我们可以定义两个简单的接口来处理购买和授予奖励。购买奖励的接口将接收一个 paymentToken，它是一个字符串，包含处理支付所需的所有必要信息。授予奖励的接口将采用 targetId，这将是授予奖励的帖子或评论的 ID。

```
BuyAwards(userId: string, paymentToken: string, quantity: int)

GiveAward(userId: string, targetId: string)
```

Last Modified 2022-03-20
