# Minio Basic

## 安装

```bash
pnpm add @aws-sdk/client-s3
```

如果需要预签名 URL：

```bash
pnpm add @aws-sdk/s3-request-presigner
```

------

# 1. 创建 S3Client

## AWS S3

```ts
import { S3Client } from '@aws-sdk/client-s3';

export const s3 = new S3Client({
  region: 'ap-east-1',
  credentials: {
    accessKeyId: process.env.S3_ACCESS_KEY!,
    secretAccessKey: process.env.S3_SECRET_KEY!,
  },
});
```

------

## MinIO

```ts
import { S3Client } from '@aws-sdk/client-s3';

export const s3 = new S3Client({
  region: 'us-east-1',

  endpoint: 'http://localhost:9000',

  credentials: {
    accessKeyId: 'minioadmin',
    secretAccessKey: 'minioadmin',
  },

  forcePathStyle: true,
});
```

### forcePathStyle

MinIO 推荐开启：

```text
http://localhost:9000/my-bucket/avatar.png
```

而不是：

```text
http://my-bucket.localhost:9000/avatar.png
```

------

# 2. 创建 Bucket

```ts
import { CreateBucketCommand } from '@aws-sdk/client-s3';

await s3.send(
  new CreateBucketCommand({
    Bucket: 'avatars',
  }),
);
```

------

# 3. 上传文件

最常用。

```ts
import { PutObjectCommand } from '@aws-sdk/client-s3';

await s3.send(
  new PutObjectCommand({
    Bucket: 'avatars',
    Key: 'users/1/avatar.jpg',
    Body: buffer,
    ContentType: 'image/jpeg',
  }),
);
```

------

## 上传字符串

```ts
await s3.send(
  new PutObjectCommand({
    Bucket: 'avatars',
    Key: 'hello.txt',
    Body: 'hello world',
  }),
);
```

------

## 上传 Stream

Node.js 中很常见：

```ts
import fs from 'node:fs';

await s3.send(
  new PutObjectCommand({
    Bucket: 'avatars',
    Key: 'video.mp4',
    Body: fs.createReadStream('./video.mp4'),
  }),
);
```

------

# 4. 下载文件

```ts
import { GetObjectCommand } from '@aws-sdk/client-s3';

const result = await s3.send(
  new GetObjectCommand({
    Bucket: 'avatars',
    Key: 'users/1/avatar.jpg',
  }),
);
```

返回：

```ts
{
  Body,
  ContentType,
  ContentLength,
}
```

------

## 转 Buffer

Node.js 18+：

```ts
const object = await s3.send(
  new GetObjectCommand({
    Bucket: 'avatars',
    Key: 'users/1/avatar.jpg',
  }),
);

const buffer = Buffer.from(
  await object.Body!.transformToByteArray(),
);
```

------

## 转字符串

```ts
const text = await object.Body!.transformToString();
```

------

# 5. 删除文件

```ts
import { DeleteObjectCommand } from '@aws-sdk/client-s3';

await s3.send(
  new DeleteObjectCommand({
    Bucket: 'avatars',
    Key: 'users/1/avatar.jpg',
  }),
);
```

------

# 6. 判断文件是否存在

使用 HEAD 请求。

```ts
import { HeadObjectCommand } from '@aws-sdk/client-s3';

try {
  await s3.send(
    new HeadObjectCommand({
      Bucket: 'avatars',
      Key: 'users/1/avatar.jpg',
    }),
  );

  return true;
} catch {
  return false;
}
```

------

# 7. 获取文件信息

```ts
const result = await s3.send(
  new HeadObjectCommand({
    Bucket: 'avatars',
    Key: 'users/1/avatar.jpg',
  }),
);

console.log(result.ContentLength);
console.log(result.ContentType);
console.log(result.LastModified);
```

------

# 8. 列出文件

```ts
import { ListObjectsV2Command } from '@aws-sdk/client-s3';

const result = await s3.send(
  new ListObjectsV2Command({
    Bucket: 'avatars',
  }),
);

console.log(result.Contents);
```

------

## 按目录查询

S3 实际没有目录。

只是 Key 前缀：

```text
users/1/avatar.jpg
users/1/banner.jpg
users/2/avatar.jpg
```

查询：

```ts
const result = await s3.send(
  new ListObjectsV2Command({
    Bucket: 'avatars',
    Prefix: 'users/1/',
  }),
);
```

------

# 9. 分页查询

```ts
let token: string | undefined;

do {
  const result = await s3.send(
    new ListObjectsV2Command({
      Bucket: 'avatars',
      ContinuationToken: token,
      MaxKeys: 100,
    }),
  );

  token = result.NextContinuationToken;
} while (token);
```

------

# 10. 生成下载 URL

```bash
pnpm add @aws-sdk/s3-request-presigner
import { getSignedUrl } from '@aws-sdk/s3-request-presigner';
import { GetObjectCommand } from '@aws-sdk/client-s3';

const url = await getSignedUrl(
  s3,
  new GetObjectCommand({
    Bucket: 'avatars',
    Key: 'users/1/avatar.jpg',
  }),
  {
    expiresIn: 3600,
  },
);
```

返回：

```text
https://...
```

用户可在 1 小时内下载。

------

# 11. 生成上传 URL（推荐）

头像上传最常见方案。

```ts
import { PutObjectCommand } from '@aws-sdk/client-s3';
import { getSignedUrl } from '@aws-sdk/s3-request-presigner';

const uploadUrl = await getSignedUrl(
  s3,
  new PutObjectCommand({
    Bucket: 'avatars',
    Key: 'users/1/avatar.jpg',
    ContentType: 'image/jpeg',
  }),
  {
    expiresIn: 300,
  },
);
```

返回给前端：

```json
{
  "uploadUrl": "...",
  "key": "users/1/avatar.jpg"
}
```

------

前端：

```ts
await fetch(uploadUrl, {
  method: 'PUT',
  body: file,
  headers: {
    'Content-Type': file.type,
  },
});
```

------

# 12. 批量删除

```ts
import { DeleteObjectsCommand } from '@aws-sdk/client-s3';

await s3.send(
  new DeleteObjectsCommand({
    Bucket: 'avatars',

    Delete: {
      Objects: [
        { Key: 'a.jpg' },
        { Key: 'b.jpg' },
        { Key: 'c.jpg' },
      ],
    },
  }),
);
```

# Minio Advanced

# 1. Multipart Upload（大文件上传）

普通上传：

```ts
PutObjectCommand
```

适合：

```text
头像
图片
小文件
< 20MB
```

对于：

```text
视频
录音
压缩包
100MB+
```

应该使用 Multipart Upload。

------

## 创建上传任务

```ts
import {
  CreateMultipartUploadCommand,
} from '@aws-sdk/client-s3';

const { UploadId } = await s3.send(
  new CreateMultipartUploadCommand({
    Bucket: 'files',
    Key: 'videos/demo.mp4',
  }),
);
```

获得：

```ts
{
  UploadId: 'xxxx'
}
```

------

## 上传分片

```ts
import {
  UploadPartCommand,
} from '@aws-sdk/client-s3';

const result = await s3.send(
  new UploadPartCommand({
    Bucket: 'files',
    Key: 'videos/demo.mp4',

    UploadId,
    PartNumber: 1,

    Body: chunk,
  }),
);
```

返回：

```ts
{
  ETag: '"abc123"'
}
```

需要保存。

------

## 合并分片

```ts
import {
  CompleteMultipartUploadCommand,
} from '@aws-sdk/client-s3';

await s3.send(
  new CompleteMultipartUploadCommand({
    Bucket: 'files',
    Key: 'videos/demo.mp4',

    UploadId,

    MultipartUpload: {
      Parts: [
        {
          PartNumber: 1,
          ETag: etag1,
        },
        {
          PartNumber: 2,
          ETag: etag2,
        },
      ],
    },
  }),
);
```

------

## 终止上传

防止垃圾数据堆积。

```ts
import {
  AbortMultipartUploadCommand,
} from '@aws-sdk/client-s3';

await s3.send(
  new AbortMultipartUploadCommand({
    Bucket: 'files',
    Key: 'videos/demo.mp4',
    UploadId,
  }),
);
```

------

# 2. 官方 Upload Helper

AWS SDK 提供高级封装。

安装：

```bash
pnpm add @aws-sdk/lib-storage
```

------

## 自动分片上传

```ts
import { Upload } from '@aws-sdk/lib-storage';

const upload = new Upload({
  client: s3,

  params: {
    Bucket: 'files',
    Key: 'movie.mp4',
    Body: fs.createReadStream('./movie.mp4'),
  },
});

await upload.done();
```

自动完成：

```text
CreateMultipartUpload
UploadPart
CompleteMultipartUpload
```

------

## 监听进度

```ts
upload.on(
  'httpUploadProgress',
  (progress) => {
    console.log(progress);
  },
);
```

------

# 3. CopyObject（服务端复制）

无需下载再上传。

```ts
import {
  CopyObjectCommand,
} from '@aws-sdk/client-s3';

await s3.send(
  new CopyObjectCommand({
    Bucket: 'avatars',

    Key: 'new/avatar.jpg',

    CopySource:
      'avatars/old/avatar.jpg',
  }),
);
```

------

## 用户头像转正

很多系统会：

```text
tmp/avatar.jpg

↓

users/1/avatar.jpg
```

直接复制即可。

------

# 4. Rename 文件

S3 没有 Rename。

本质：

```text
Copy
+
Delete
await copy();

await remove();
```

------

# 5. Bucket Policy

控制访问权限。

例如公开读取：

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": ["s3:GetObject"],
      "Resource": [
        "arn:aws:s3:::avatars/*"
      ]
    }
  ]
}
```

------

通过 API 设置：

```ts
import {
  PutBucketPolicyCommand,
} from '@aws-sdk/client-s3';

await s3.send(
  new PutBucketPolicyCommand({
    Bucket: 'avatars',
    Policy: JSON.stringify(policy),
  }),
);
```

------

# 6. Presigned POST

比 Presigned URL 更强。

可以限制：

```text
Content-Type
文件大小
Key
过期时间
```

------

后端

```ts
import { createPresignedPost }
  from '@aws-sdk/s3-presigned-post';

const result =
  await createPresignedPost(
    s3,
    {
      Bucket: 'avatars',

      Key: key,

      Conditions: [
        [
          'content-length-range',
          0,
          5 * 1024 * 1024,
        ],
      ],

      Expires: 300,
    },
  );
```

------

前端：

```html
<form>
  <input type="file" />
</form>
```

直接 POST。

------

# 7. 条件读取

利用 ETag。

```ts
import {
  GetObjectCommand,
} from '@aws-sdk/client-s3';

await s3.send(
  new GetObjectCommand({
    Bucket: 'avatars',
    Key: 'avatar.jpg',

    IfNoneMatch: etag,
  }),
);
```

未变化：

```http
304 Not Modified
```

------

# 8. 条件覆盖

防止并发写入。

```ts
await s3.send(
  new PutObjectCommand({
    Bucket: 'avatars',
    Key: 'avatar.jpg',

    Body: file,

    IfMatch: etag,
  }),
);
```

类似数据库：

```text
Optimistic Lock
```

------

# 9. 对象标签（Object Tagging）

给文件附加元数据。

```ts
import {
  PutObjectTaggingCommand,
} from '@aws-sdk/client-s3';

await s3.send(
  new PutObjectTaggingCommand({
    Bucket: 'avatars',
    Key: 'avatar.jpg',

    Tagging: {
      TagSet: [
        {
          Key: 'userId',
          Value: '123',
        },
      ],
    },
  }),
);
```

------

读取：

```ts
import {
  GetObjectTaggingCommand,
} from '@aws-sdk/client-s3';

const tags = await s3.send(
  new GetObjectTaggingCommand({
    Bucket: 'avatars',
    Key: 'avatar.jpg',
  }),
);
```

------

# 10. 自定义 Metadata

比 Tag 更常用。

上传：

```ts
await s3.send(
  new PutObjectCommand({
    Bucket: 'avatars',
    Key: 'avatar.jpg',

    Body: file,

    Metadata: {
      userId: '123',
      source: 'mobile',
    },
  }),
);
```

------

读取：

```ts
const info = await s3.send(
  new HeadObjectCommand({
    Bucket: 'avatars',
    Key: 'avatar.jpg',
  }),
);

console.log(info.Metadata);
```

------

# 11. Server-Side Encryption

服务端加密。

```ts
await s3.send(
  new PutObjectCommand({
    Bucket: 'files',
    Key: 'secret.pdf',

    Body: file,

    ServerSideEncryption: 'AES256',
  }),
);
```

------

# 12. 生命周期管理

自动删除临时文件。

例如：

```text
tmp/*
7天后自动删除
```

```ts
import {
  PutBucketLifecycleConfigurationCommand,
} from '@aws-sdk/client-s3';

await s3.send(
  new PutBucketLifecycleConfigurationCommand({
    Bucket: 'files',

    LifecycleConfiguration: {
      Rules: [
        {
          Status: 'Enabled',

          Filter: {
            Prefix: 'tmp/',
          },

          Expiration: {
            Days: 7,
          },

          ID: 'cleanup-temp',
        },
      ],
    },
  }),
);
```

------

# 实际项目最值得掌握的高级能力

按照优先级排序：

| 能力                   | 场景             | 优先级 |
| ---------------------- | ---------------- | ------ |
| Presigned URL          | 前端直传         | ⭐⭐⭐⭐⭐  |
| Presigned POST         | 文件大小限制     | ⭐⭐⭐⭐⭐  |
| Metadata               | 存储业务信息     | ⭐⭐⭐⭐⭐  |
| Multipart Upload       | 大文件上传       | ⭐⭐⭐⭐   |
| Upload Helper          | 简化上传         | ⭐⭐⭐⭐   |
| Lifecycle              | 自动清理临时文件 | ⭐⭐⭐⭐   |
| CopyObject             | 文件转正         | ⭐⭐⭐    |
| Object Tagging         | 文件分类         | ⭐⭐⭐    |
| Bucket Policy          | 权限管理         | ⭐⭐⭐    |
| Server-Side Encryption | 合规需求         | ⭐⭐     |

