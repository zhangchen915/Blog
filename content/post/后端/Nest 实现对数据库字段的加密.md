---
title: "Nest 使用 typeorm 对数据库字段进行加密"
categories: ["Nest.js"]
tags: []
draft: false
slug: "typeorm-encrypted"
date: "2022-05-05 14:31:45"
---

`typeorm-encrypted` 可以通过 2 种方式调用：转换器或订阅者。
在下面的示例中，Key和IV因算法而异。

### 使用转换器

```typescript
 @Column({
    type: "varchar",
    nullable: false,
    transformer: new EncryptionTransformer({
      key: 'e41c966f21f9e1577802463f8924e6a3fe3e9751f201304213b2f845d8841d61',
      algorithm: 'aes-256-cbc',
      ivLength: 16,
      iv: 'ff5ac19190424b1d88f9419ef949ae56'
    })
  })
  secret: string;
```

key 是 algorithm 使用的原始密钥，iv 是初始化向量。 两个参数都必须是 'utf8' 编码的字符串，如果加密不需要初始化向量，则 iv 可以是 null。
注意：nodejs原生库IV不是16字节，就会报错

### Subscribers
以下示例分别在保存/获取时自动加密/解密字段。
```ts
import { BaseEntity, Entity, Column, createConnection } from "typeorm";
import { ExtendedColumnOptions, AutoEncryptSubscriber } from "typeorm-encrypted";

@Entity()
class User extends BaseEntity {
    @Column(<ExtendedColumnOptions>{
        type: "varchar",
        nullable: false,
        encrypt: {
            key: "d85117047fd06d3afa79b6e44ee3a52eb426fc24c3a2e3667732e8da0342b4da",
            algorithm: "aes-256-cbc",
            ivLength: 16
        }
    })
    secret: string;
}

let connection = createConnection({
    entities: [ User ],
    subscribers: [ AutoEncryptSubscriber ]
});
```

#### 常见问题

- IV 长度无效
  - 列定义错误。可能是密钥或 IV 的问题。
  - 数据库中有没有经过加密的数据，需要先进行数据迁移。
  - 可能需要清除缓存 typeorm cache:clear

- 如何将加密列添加到包含数据的表中？
  - 将新列（col B）添加到表中。配置加密的列。从（col A）中要添加的新列。
  - 将一个脚本来表中的所有条目。查询将 col B 的值设置为 col A。
  - 保存所有记录。
  - 手动将col A重命名为其他名称。
  - 手动将 col B 重命名为 col A 的原始名称。
  - 删除 col A 的 typeorm 配置。
  - 将 col B 的 typeorm 配置重命名为 col A 的名称。
  - 手动从表中删除 col A（未加密列）。
