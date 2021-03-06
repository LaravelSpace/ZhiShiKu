```json
{
  "updated_by": "KelipuTe",
  "updated_at": "2020-08-03",
  "tags": "极客时间,GeekBang,后端存储实践课"
}
```

---

## 课前必读

### 后端存储系统概述

**存储是系统中最核心、最重要、最关键的组成部分，没有之一。**实际开发的各种业务系统，几乎都是MIS系统（管理信息系统）。管理信息系统就是管理信息的系统，其实就是管理数据的系统。不管系统业务是什么样的，系统的业务功能最终的结果就是数据。只要这个数据是正确的，剩下的都是小问题。数据错了、丢了、处理不及时，这些都是大问题。**凡是特别难解决的问题，付出巨大代价的问题，几乎都和存储脱不了干系。**

我们日常常用的存储系统有很多，有单机的也有分布式的，有数据库系统也有文件系统，还有一些介于二者之间的系统。但是，无论是什么样的存储，比如MySQL、Redis、ElasticSearch等等，它们都有几个共同的特点。

- 1、难用。拿MySQL举例，我要存取对象，必须把对象转换成MySQL表中的行，还得写SQL语句才能存取。
- 2、慢。一个优化过的业务系统，它的性能瓶颈一定是存储。
- 3、杂。存储这块不像其他的成熟的技术领域，基本上都是一两种方案包打天下。MySQL、Redis、ElasticSearch、MongoDB、RocksDB等等，这些存储谁都替代不了谁，都有擅长的地方，有它适用的场景，也有突出的短板。

这个课程以电商作为场景，讲解不同规模的存储系统应该如何构建。首先，电商系统覆盖面足够广泛。特别是在互联网行业，几乎所有的互联网公司都在做两个事情：电商和社交。其次，用电商系统作为案例，直接就能学以致用。即使面对的业务和电商关系不大，但是因为电商的系统足够复杂，在其他业务中可能遇到的技术问题，大多数在电商系统中基本都会遇到。

另外，即使是同样一个电商系统，不同的规模，它需要解决的问题也不一样。不少做技术的同学崇尚于海量数据和高并发，认为只有大厂那些海量数据和高并发的核心或者是底层存储系统，才是真正有技术含量的系统，能胜任这样系统的开发者，才是真正的技术大牛。

这其实是一个技术认知误区。因为，并不是规模小的系统就简单，规模大的系统才有难度。不同规模的系统，在技术上没有高低贵贱之分，它们的建设目标不一样，面临的挑战不一样，需要解决的问题也不一样，对于存储系统的选择、架构设计也是不一样的。

### 设计电商系统

在需求还不太明确的情况下，比较可行的方式就是，先把那些不太会变化的核心系统搭建出来，尽量简单地实现出一个最小化的系统，然后再逐步迭代完善。不要一上来就设计功能，而是先要回答下面这两个问题：**这个系统是给哪些人用的？这些人使用这个系统来解决什么问题？**这两个问题的答案，就是业务需求。一般来说，业务需求和我们要设计的系统关系不大。做业务需求的主要目的，是理清楚业务场景是什么样的。

业务需求最终转化为业务流程，而模块的划分依赖于业务流程的细化。作为系统设计者，如果系统业务是复杂而多变的，尽量识别出这部分复杂业务的边界，将复杂的部分封禁在一个模块的内部，避免复杂度扩散到整个系统中去。可行的做法是，把一个模块与其他模块的接口设计的相对简单和固定，这样系统的其他模块就不会因为一个模块的变化而改变。

#### 电商系统的核心流程

第一个问题，电商系统给哪些人用？首先得有买东西的人即用户，还得有卖东西的人即运营人员。还有老板，千万不要把老板或者是领导给忘了。第二个问题：用户、运营和老板，分别用电商系统来干什么？用户用系统来买东西，运营用系统来卖东西，老板需要在系统中看到他赚了多少钱。这两个问题的答案，或者说是业务需求，稍加细化后，可以用UML（统一建模语言）中的用例图（Use Case）来表述。用例图是需求分析的时候你需要画的第一张图。它回答的就是业务需求中的那两个关键的问题。

然后我们要分析电商系统的流程。显然，一个电商系统最主要的业务流程，一定是购物这个流程。流程从用户选购商品开始，用户先从你的App中浏览商品；找到心仪的商品之后，把商品添加到购物车里面；都选好了之后，打开购物车，下一个订单；下单结算之后，就可以支付了；支付成功后，运营人员接下来会给每个已经支付的订单发货；邮寄商品给用户之后，用户确认收货，到这里一个完整的购物流程就结束了。根据流程我们就可以设计系统功能和划分模块了。

#### 电商系统的技术选型

技术选型本身没有好与坏，更多的是选择合适的技术。对于编程语言和技术栈的选择，一般需要从两方面考虑，一方面就是团队的人员配置，尽量选择大家熟悉的技术，第二个方面就是要考察选择的技术它的生态是不是够完善。

