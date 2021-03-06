---
title: "线程安全集合 | Microsoft Docs"
ms.custom: 
ms.date: 03/30/2017
ms.prod: .net
ms.reviewer: 
ms.suite: 
ms.technology: dotnet-standard
ms.tgt_pltfrm: 
ms.topic: article
helpviewer_keywords:
- thread-safe collections, overview
ms.assetid: 2e7ca21f-786c-4367-96be-0cf3f3dcc6bd
caps.latest.revision: 24
author: mairaw
ms.author: mairaw
manager: wpickett
ms.translationtype: Human Translation
ms.sourcegitcommit: c50b3e328998b65ec47efe6d7457b36116813c77
ms.openlocfilehash: b35c47a7d77038cc6a213b221e0081a4d83c8dae
ms.contentlocale: zh-cn
ms.lasthandoff: 04/08/2017

---
# <a name="thread-safe-collections"></a>线程安全集合
[!INCLUDE[net_v40_short](../../../../includes/net-v40-short-md.md)] 引入了 <xref:System.Collections.Concurrent?displayProperty=fullName> 命名空间，其中包含多个线程安全且可缩放的集合类。 多个线程可以安全高效地从这些集合添加或删除项，而无需在用户代码中进行其他同步。 编写新代码时，只要将集合同时写入多个线程中，就使用并发集合类。 如果仅从共享集合进行读取，则可使用 <xref:System.Collections.Generic?displayProperty=fullName> 命名空间中的类。 建议不要使用 1.0 集合类，除非需要定位 .NET Framework 1.1 或更低版本运行时。  
  
## <a name="thread-synchronization-in-the-net-framework-10-and-20-collections"></a>.NET Framework 1.0 和 2.0 集合中的线程同步  
 .NET Framework 1.0 中引入的集合位于 <xref:System.Collections?displayProperty=fullName> 命名空间中。 这些集合（包括常用的 <xref:System.Collections.ArrayList> 和 <xref:System.Collections.Hashtable>）通过 `Synchronized` 属性提供一些线程安全性，此属性返回线程安全的集合包装器。 该包装器通过对每个添加或删除操作锁定整个集合进行工作。 因此，每个尝试访问集合的线程必须等待，直到轮到它获取锁定。 这不可缩放，并且可能导致大型集合的性能显著下降。 此外，这一设计并不能完全防止争用情况的出现。 有关详细信息，请参阅 MSDN 网站上的[泛型集合中的同步](http://go.microsoft.com/fwlink/?LinkID=161130)。  
  
 .NET Framework 2.0 中引入的集合类位于 <xref:System.Collections.Generic?displayProperty=fullName> 命名空间中。 这些包括 <xref:System.Collections.Generic.List%601>、<xref:System.Collections.Generic.Dictionary%602> 等。 与 .NET Framework 1.0 类相比，这些类提升了类型安全性和性能。 不过，.NET Framework 2.0 集合类不提供任何线程同步；多线程同时添加或删除项时，用户代码必须提供所有同步。  
  
 建议使用 [!INCLUDE[net_v40_short](../../../../includes/net-v40-short-md.md)] 中的并发集合类，因为它们不仅能够提供 .NET Framework 2.0 集合类的类型安全性，而且能够比 [!INCLUDE[net_v10_short](../../../../includes/net-v10-short-md.md)] 集合更高效完整地提供线程安全性。  
  
## <a name="fine-grained-locking-and-lock-free-mechanisms"></a>细粒度锁定和无锁机制  
 一些并发集合类型使用轻型同步机制，如 <xref:System.Threading.SpinLock>、<xref:System.Threading.SpinWait>、<xref:System.Threading.SemaphoreSlim> 和 <xref:System.Threading.CountdownEvent>，这些机制是 [!INCLUDE[net_v40_short](../../../../includes/net-v40-short-md.md)] 中的新增功能。 这些同步类型通常在将线程真正置于等待状态之前，会在短时间内使用*忙旋转*。 预计等待时间非常短时，旋转比等待所消耗的计算资源少得多，因为后者涉及资源消耗量大的内核转换。 对于使用旋转的集合类，这种效率意味着多个线程能够以非常快的速率添加和删除项。 有关旋转与锁定的详细信息，请参阅 [SpinLock](../../../../docs/standard/threading/spinlock.md) 和 [SpinWait](../../../../docs/standard/threading/spinwait.md)。  
  
 <xref:System.Collections.Concurrent.ConcurrentQueue%601> 和 <xref:System.Collections.Concurrent.ConcurrentStack%601> 类完全不使用锁定。 相反，它们依赖 <xref:System.Threading.Interlocked> 操作来实现线程安全性。  
  
> [!NOTE]
>  由于并发集合类支持 <xref:System.Collections.ICollection>，因此它们将提供 <xref:System.Collections.ICollection.IsSynchronized%2A> 和 <xref:System.Collections.ICollection.SyncRoot%2A> 属性的实现代码，尽管这些属性并不相关。 `IsSynchronized` 始终返回 `false`，而 `SyncRoot` 始终为 `null`（在 Visual Basic 中是 `Nothing`）。  
  
 下表列出了 <xref:System.Collections.Concurrent?displayProperty=fullName> 命名空间中的集合类型。  
  
|类型|说明|  
|----------|-----------------|  
|<xref:System.Collections.Concurrent.BlockingCollection%601>|为实现 <xref:System.Collections.Concurrent.IProducerConsumerCollection%601> 的所有类型提供控制和锁定功能。 有关详细信息，请参阅 [BlockingCollection 概述](../../../../docs/standard/collections/thread-safe/blockingcollection-overview.md)。|  
|<xref:System.Collections.Concurrent.ConcurrentDictionary%602>|键值对字典的线程安全实现。|  
|<xref:System.Collections.Concurrent.ConcurrentQueue%601>|FIFO（先进先出）队列的线程安全实现。|  
|<xref:System.Collections.Concurrent.ConcurrentStack%601>|LIFO（后进先出）堆栈的线程安全实现。|  
|<xref:System.Collections.Concurrent.ConcurrentBag%601>|无序元素集合的线程安全实现。|  
|<xref:System.Collections.Concurrent.IProducerConsumerCollection%601>|类型必须实现以在 `BlockingCollection` 中使用的接口。|  
  
## <a name="related-topics"></a>相关主题  
  
|标题|描述|  
|-----------|-----------------|  
|[BlockingCollection 概述](../../../../docs/standard/collections/thread-safe/blockingcollection-overview.md)|介绍了 <xref:System.Collections.Concurrent.BlockingCollection%601> 类型提供的功能。|  
|[如何：在 ConcurrentDictionary 中添加和移除项](../../../../docs/standard/collections/thread-safe/how-to-add-and-remove-items.md)|介绍了如何在 <xref:System.Collections.Concurrent.ConcurrentDictionary%602> 中添加和删除元素|  
|[如何：在 BlockingCollection 中逐个添加和取出项](../../../../docs/standard/collections/thread-safe/how-to-add-and-take-items.md)|描述如何在不使用只读枚举器的情况下，从阻止的集合添加和检索项。|  
|[如何：向集合添加限制和阻塞功能](../../../../docs/standard/collections/thread-safe/how-to-add-bounding-and-blocking.md)|介绍了如何将任意集合类用作 <xref:System.Collections.Concurrent.IProducerConsumerCollection%601> 集合的基础存储机制。|  
|[如何：使用 ForEach 移除 BlockingCollection 中的项](../../../../docs/standard/collections/thread-safe/how-to-use-foreach-to-remove.md)|介绍了如何使用 `foreach`（在 Visual Basic 中是 `For Each`）在锁定集合中删除所有项。|  
|[如何：在管道中使用阻塞集合的数组](../../../../docs/standard/collections/thread-safe/how-to-use-arrays-of-blockingcollections.md)|描述如何同时使用多个阻塞集合来实现一个管道。|  
|[如何：使用 ConcurrentBag 创建目标池](../../../../docs/standard/collections/thread-safe/how-to-create-an-object-pool.md)|演示如何使用并发包在可重用对象（而不是继续创建新对象）的情况下改进性能。|  
  
## <a name="reference"></a>参考  
 <xref:System.Collections.Concurrent?displayProperty=fullName>
