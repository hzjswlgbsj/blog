## Yjs 概念和名词解释

### Update

在 Yjs 中，`update` 是用于表示一组操作的数据结构，用于描述文档的修改。当一个客户端对文档进行编辑时，会生成一个 `update` 数据，其中包含了一系列操作，如插入、删除、格式修改等。这些 `update` 数据被广播给其他客户端，从而实现文档的协同编辑。`update` 数据通常是 `Uint8Array` 类型，用于高效地传输和处理协同编辑操作。

```javascript
const ydoc = new Y.Doc();
ydoc.on(
  "update",
  (update: Uint8Array, origin: any, doc: Y.Doc, tr: Transaction) => {
    console.log("文档内容发生改变", update, origin, doc, tr);
  }
);
```

上面这段代码，是对一篇文档的更新做了监听，这里可以看到回调函数里面第一个参数就是 `update` 这里的数据类型就是一个 `Uint8Array`它是可以直接被更新到任何 ydoc 的实例上的

```javascript
Y.applyUpdate(ydoc, update);
```

你可能注意到了最后一个参数 tr，它也是一种更新变动的描述。

### Transaction

`Transaction` 是 Yjs 中的一个概念，用于表示一次对文档的修改操作。`Transaction` 包含了一个或多个编辑操作，如插入、删除等。当一个客户端进行编辑时，它会创建一个 `Transaction` 对象，将编辑操作添加到其中。然后，Yjs 会将这个 `Transaction` 转换成对应的 `update` 数据，从而进行数据的同步。`Transaction` 对象的存在有助于对编辑操作进行合并、优化和管理，以实现更高效的协同编辑。

综合起来，`update` 数据和 `Transaction` 对象是 Yjs 实现协同编辑的关键组成部分。`update` 数据用于传输实际的编辑操作，而 `Transaction` 对象用于管理和合并这些操作，从而实现多个客户端之间的数据同步和协同编辑。它们共同构成了 Yjs 的核心机制，使得多个客户端能够实时共享和编辑同一份文档。

`update` 数据主要用于在客户端之间**传输 **编辑操作，确保数据的同步，而 `Transaction`数据则是更高界别是抽象，用于** 管理和处理**编辑操作，优化客户端的数据处理逻辑。

当客户端使用完 `Transaction` 处理合并和优化操作后，它会被转化为 `update` 数据在不同客户端中传输和同步。

```javascript
const Y = require("yjs");
require("y-xml")(Y);

const yDoc = new Y.Doc();
const text = yDoc.get("content", Y.XmlText);

// 创建 Transaction 对象
const transaction = yDoc.transact(() => {
  text.insert(0, "Hello, ");
  text.insert(7, "world!");
});

// 将 Transaction 转化为 update 数据
const updateData = Y.encodeStateAsUpdate(yDoc);
```

### Delta

`Delta` 是 Yjs 中更底层的概念，用于描述编辑操作变化，用于解决数据冲突以及在不同客户端之间进行数据同步。`Delta` 是一个表示编辑操作的数据结构，它包含了一系列操作步骤，如插入、删除、格式修改等。

`Delta` 跟 `Uint8Array` 数据和 `Transaction` 对象之间的关系是：
当一个客户端对文档进行编辑时，会生成一个包含编辑操作的 `Delta`，然后将其编码为 `Uint8Array` 格式的 `update` 数据。这些数据被广播给其他客户端，以实现数据的同步。

`Transaction` 是在客户端上创建和管理 `Delta` 的一种高级抽象。`Transaction` 包含了一个或多个编辑操作，形成一个编辑的序列。当编辑操作被执行后，它们会生成一个 `Delta`，然后通过 `Y.encodeStateAsUpdate()` 将其编码为 `Uint8Array` 格式的 `update` 数据，以进行数据传输和同步。

总结起来，`Delta` 是描述编辑操作变化的概念，`update` 数据（Uint8Array 数据）是用于传输编辑操作的二进制表示，而 `Transaction` 对象则是客户端上用于管理和应用编辑操作的高级抽象。这三者共同协作，实现了在不同客户端之间的协同编辑和数据同步。

## y-websocket 概念和名词解释
