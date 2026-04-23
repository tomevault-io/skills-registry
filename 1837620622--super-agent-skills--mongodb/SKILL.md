---
name: mongodb
description: 使用最佳实践操作 MongoDB 数据库。适用于设计模式、编写查询、构建聚合管道或优化性能。触发关键词：MongoDB, Mongoose, NoSQL, aggregation pipeline, document database, MongoDB Atlas, 文档数据库。 Use when this capability is needed.
metadata:
  author: 1837620622
---

# MongoDB 与 Mongoose

使用 MongoDB 的最佳实践进行模式设计、查询和性能优化。

## 快速开始

```bash
npm install mongodb mongoose
```

### 原生驱动
```typescript
import { MongoClient, ObjectId } from 'mongodb';

const client = new MongoClient(process.env.MONGODB_URI!);
const db = client.db('myapp');
const users = db.collection('users');

// 连接
await client.connect();

// CRUD 操作
await users.insertOne({ name: 'Alice', email: 'alice@example.com' });
const user = await users.findOne({ email: 'alice@example.com' });
await users.updateOne({ _id: user._id }, { $set: { name: 'Alice Smith' } });
await users.deleteOne({ _id: user._id });
```

### Mongoose 设置
```typescript
import mongoose from 'mongoose';

await mongoose.connect(process.env.MONGODB_URI!, {
  maxPoolSize: 10,
  serverSelectionTimeoutMS: 5000,
  socketTimeoutMS: 45000,
});

// 连接事件
mongoose.connection.on('connected', () => console.log('MongoDB connected'));
mongoose.connection.on('error', (err) => console.error('MongoDB error:', err));
mongoose.connection.on('disconnected', () => console.log('MongoDB disconnected'));

// 优雅关闭
process.on('SIGINT', async () => {
  await mongoose.connection.close();
  process.exit(0);
});
```

## Schema 设计

### 基础 Schema
```typescript
import mongoose, { Schema, Document, Model } from 'mongoose';

interface IUser extends Document {
  email: string;
  name: string;
  password: string;
  role: 'user' | 'admin';
  profile: {
    avatar?: string;
    bio?: string;
  };
  createdAt: Date;
  updatedAt: Date;
}

const userSchema = new Schema<IUser>({
  email: {
    type: String,
    required: [true, 'Email is required'],
    unique: true,
    lowercase: true,
    trim: true,
    match: [/^\S+@\S+\.\S+$/, 'Invalid email format'],
  },
  name: {
    type: String,
    required: true,
    trim: true,
    minlength: 2,
    maxlength: 100,
  },
  password: {
    type: String,
    required: true,
    select: false,  // Never return password by default
  },
  role: {
    type: String,
    enum: ['user', 'admin'],
    default: 'user',
  },
  profile: {
    avatar: String,
    bio: { type: String, maxlength: 500 },
  },
}, {
  timestamps: true,  // Adds createdAt, updatedAt
  toJSON: {
    transform(doc, ret) {
      delete ret.password;
      delete ret.__v;
      return ret;
    },
  },
});

// 索引
userSchema.index({ email: 1 });
userSchema.index({ createdAt: -1 });
userSchema.index({ name: 'text', 'profile.bio': 'text' });  // Text search

const User: Model<IUser> = mongoose.model('User', userSchema);
```

### 嵌入文档 vs 引用

```typescript
// ✅ Embed when: Data is read together, doesn't grow unbounded
const orderSchema = new Schema({
  customer: {
    name: String,
    email: String,
    address: {
      street: String,
      city: String,
      country: String,
    },
  },
  items: [{
    product: String,
    quantity: Number,
    price: Number,
  }],
  total: Number,
});

// ✅ Reference when: Data is large, shared, or changes independently
const postSchema = new Schema({
  title: String,
  content: String,
  author: {
    type: Schema.Types.ObjectId,
    ref: 'User',
    required: true,
  },
  comments: [{
    type: Schema.Types.ObjectId,
    ref: 'Comment',
  }],
});

// 填充引用
const post = await Post.findById(id)
  .populate('author', 'name email')  // Select specific fields
  .populate({
    path: 'comments',
    populate: { path: 'author', select: 'name' },  // Nested populate
  });
```

### 虚拟字段
```typescript
const userSchema = new Schema({
  firstName: String,
  lastName: String,
});

// 虚拟字段（不存储在数据库中）
userSchema.virtual('fullName').get(function() {
  return `${this.firstName} ${this.lastName}`;
});

// 虚拟填充（用于反向引用）
userSchema.virtual('posts', {
  ref: 'Post',
  localField: '_id',
  foreignField: 'author',
});

// 在 JSON 中启用虚拟字段
userSchema.set('toJSON', { virtuals: true });
userSchema.set('toObject', { virtuals: true });
```

## 查询操作

### 查找操作
```typescript
// 带过滤条件查找
const users = await User.find({
  role: 'user',
  createdAt: { $gte: new Date('2024-01-01') },
});

// 查询构建器
const results = await User.find()
  .where('role').equals('user')
  .where('createdAt').gte(new Date('2024-01-01'))
  .select('name email')
  .sort({ createdAt: -1 })
  .limit(10)
  .skip(20)
  .lean();  // Return plain objects (faster)

// 查找单个
const user = await User.findOne({ email: 'alice@example.com' });
const userById = await User.findById(id);

// 存在检查
const exists = await User.exists({ email: 'alice@example.com' });

// 计数
const count = await User.countDocuments({ role: 'admin' });
```

### 查询操作符
```typescript
// 比较操作符
await User.find({ age: { $eq: 25 } });      // Equal
await User.find({ age: { $ne: 25 } });      // Not equal
await User.find({ age: { $gt: 25 } });      // Greater than
await User.find({ age: { $gte: 25 } });     // Greater or equal
await User.find({ age: { $lt: 25 } });      // Less than
await User.find({ age: { $lte: 25 } });     // Less or equal
await User.find({ age: { $in: [20, 25, 30] } });   // In array
await User.find({ age: { $nin: [20, 25] } });      // Not in array

// 逻辑操作符
await User.find({
  $and: [{ age: { $gte: 18 } }, { role: 'user' }],
});
await User.find({
  $or: [{ role: 'admin' }, { isVerified: true }],
});
await User.find({ age: { $not: { $lt: 18 } } });

// 元素操作符
await User.find({ avatar: { $exists: true } });
await User.find({ score: { $type: 'number' } });

// 数组操作符
await User.find({ tags: 'nodejs' });  // Array contains value
await User.find({ tags: { $all: ['nodejs', 'mongodb'] } });  // Contains all
await User.find({ tags: { $size: 3 } });  // Array length
await User.find({ 'items.0.price': { $gt: 100 } });  // Array index

// 文本搜索
await User.find({ $text: { $search: 'mongodb developer' } });

// 正则表达式
await User.find({ name: { $regex: /^john/i } });
```

### 更新操作
```typescript
// 更新单个
await User.updateOne(
  { _id: userId },
  { $set: { name: 'New Name' } }
);

// 更新多个
await User.updateMany(
  { role: 'user' },
  { $set: { isVerified: true } }
);

// 查找并更新（返回文档）
const updated = await User.findByIdAndUpdate(
  userId,
  { $set: { name: 'New Name' } },
  { new: true, runValidators: true }  // Return updated doc, run validators
);

// 更新操作符
await User.updateOne({ _id: userId }, {
  $set: { name: 'New Name' },          // Set field
  $unset: { tempField: '' },           // Remove field
  $inc: { loginCount: 1 },             // Increment
  $mul: { score: 1.5 },                // Multiply
  $min: { lowScore: 50 },              // Set if less than
  $max: { highScore: 100 },            // Set if greater than
  $push: { tags: 'new-tag' },          // Add to array
  $pull: { tags: 'old-tag' },          // Remove from array
  $addToSet: { tags: 'unique-tag' },   // Add if not exists
});

// Upsert（不存在则插入）
await User.updateOne(
  { email: 'new@example.com' },
  { $set: { name: 'New User' } },
  { upsert: true }
);
```

## 聚合管道

### 基础聚合
```typescript
const results = await Order.aggregate([
  // 阶段 1: 匹配
  { $match: { status: 'completed' } },
  
  // 阶段 2: 分组
  { $group: {
    _id: '$customerId',
    totalOrders: { $sum: 1 },
    totalSpent: { $sum: '$total' },
    avgOrder: { $avg: '$total' },
  }},
  
  // 阶段 3: 排序
  { $sort: { totalSpent: -1 } },
  
  // 阶段 4: 限制
  { $limit: 10 },
]);
```

### 管道阶段
```typescript
const pipeline = [
  // $match - Filter documents
  { $match: { createdAt: { $gte: new Date('2024-01-01') } } },
  
  // $project - Shape output
  { $project: {
    name: 1,
    email: 1,
    yearJoined: { $year: '$createdAt' },
    fullName: { $concat: ['$firstName', ' ', '$lastName'] },
  }},
  
  // $lookup - Join collections
  { $lookup: {
    from: 'orders',
    localField: '_id',
    foreignField: 'userId',
    as: 'orders',
  }},
  
  // $unwind - Flatten arrays
  { $unwind: { path: '$orders', preserveNullAndEmptyArrays: true } },
  
  // $group - Aggregate
  { $group: {
    _id: '$_id',
    name: { $first: '$name' },
    orderCount: { $sum: 1 },
    orders: { $push: '$orders' },
  }},
  
  // $addFields - Add computed fields
  { $addFields: {
    hasOrders: { $gt: ['$orderCount', 0] },
  }},
  
  // $facet - Multiple pipelines
  { $facet: {
    topCustomers: [{ $sort: { orderCount: -1 } }, { $limit: 5 }],
    stats: [{ $group: { _id: null, avgOrders: { $avg: '$orderCount' } } }],
  }},
];
```

### 分析示例
```typescript
// 按月销售额
const salesByMonth = await Order.aggregate([
  { $match: { status: 'completed' } },
  { $group: {
    _id: {
      year: { $year: '$createdAt' },
      month: { $month: '$createdAt' },
    },
    totalSales: { $sum: '$total' },
    orderCount: { $sum: 1 },
  }},
  { $sort: { '_id.year': -1, '_id.month': -1 } },
]);

// 热销产品
const topProducts = await Order.aggregate([
  { $unwind: '$items' },
  { $group: {
    _id: '$items.productId',
    totalQuantity: { $sum: '$items.quantity' },
    totalRevenue: { $sum: { $multiply: ['$items.price', '$items.quantity'] } },
  }},
  { $lookup: {
    from: 'products',
    localField: '_id',
    foreignField: '_id',
    as: 'product',
  }},
  { $unwind: '$product' },
  { $project: {
    name: '$product.name',
    totalQuantity: 1,
    totalRevenue: 1,
  }},
  { $sort: { totalRevenue: -1 } },
  { $limit: 10 },
]);
```

## 中间件（钩子）

```typescript
// 保存前中间件
userSchema.pre('save', async function(next) {
  if (this.isModified('password')) {
    this.password = await bcrypt.hash(this.password, 12);
  }
  next();
});

// 保存后中间件
userSchema.post('save', function(doc) {
  console.log('User saved:', doc._id);
});

// 查找前中间件
userSchema.pre(/^find/, function(next) {
  // Exclude deleted users by default
  this.find({ isDeleted: { $ne: true } });
  next();
});

// 聚合前中间件
userSchema.pre('aggregate', function(next) {
  // 在所有聚合中添加匹配阶段
  this.pipeline().unshift({ $match: { isDeleted: { $ne: true } } });
  next();
});
```

## 事务

```typescript
const session = await mongoose.startSession();

try {
  session.startTransaction();
  
  // 事务中的所有操作
  const user = await User.create([{ name: 'Alice' }], { session });
  await Account.create([{ userId: user[0]._id, balance: 0 }], { session });
  await Order.updateOne({ _id: orderId }, { $set: { status: 'paid' } }, { session });
  
  await session.commitTransaction();
} catch (error) {
  await session.abortTransaction();
  throw error;
} finally {
  session.endSession();
}

// 使用回调
await mongoose.connection.transaction(async (session) => {
  await User.create([{ name: 'Alice' }], { session });
  await Account.create([{ userId: user._id }], { session });
});
```

## 索引

```typescript
// 单字段索引
userSchema.index({ email: 1 });

// 复合索引
userSchema.index({ role: 1, createdAt: -1 });

// 唯一索引
userSchema.index({ email: 1 }, { unique: true });

// 部分索引
userSchema.index(
  { email: 1 },
  { partialFilterExpression: { isActive: true } }
);

// TTL 索引（自动过期删除）
sessionSchema.index({ createdAt: 1 }, { expireAfterSeconds: 3600 });

// 文本索引（用于搜索）
postSchema.index({ title: 'text', content: 'text' });

// 地理空间索引
locationSchema.index({ coordinates: '2dsphere' });

// 检查索引
const indexes = await User.collection.getIndexes();
```

## 性能优化技巧

```typescript
// 对只读查询使用 lean()
const users = await User.find().lean();

// 只选择需要的字段
const users = await User.find().select('name email');

// 对大数据集使用游标
const cursor = User.find().cursor();
for await (const user of cursor) {
  // 逐个处理
}

// 批量操作
const bulkOps = [
  { insertOne: { document: { name: 'User 1' } } },
  { updateOne: { filter: { _id: id1 }, update: { $set: { name: 'Updated' } } } },
  { deleteOne: { filter: { _id: id2 } } },
];
await User.bulkWrite(bulkOps);

// 解释查询
const explanation = await User.find({ role: 'admin' }).explain('executionStats');
```

## MongoDB Atlas

```typescript
// Atlas 连接字符串
const uri = 'mongodb+srv://user:password@cluster.mongodb.net/dbname?retryWrites=true&w=majority';

// Atlas Search（全文搜索）
const results = await Product.aggregate([
  { $search: {
    index: 'default',
    text: {
      query: 'wireless headphones',
      path: ['name', 'description'],
      fuzzy: { maxEdits: 1 },
    },
  }},
  { $project: {
    name: 1,
    score: { $meta: 'searchScore' },
  }},
]);

// Atlas 向量搜索
const results = await Product.aggregate([
  { $vectorSearch: {
    index: 'vector_index',
    path: 'embedding',
    queryVector: [0.1, 0.2, ...],
    numCandidates: 100,
    limit: 10,
  }},
]);
```

## 虚拟填充（Virtual Populate）

```typescript
// 定义 Author Schema
const authorSchema = new mongoose.Schema({
  name: String,
  email: String,
}, {
  toJSON: { virtuals: true },   // JSON.stringify() 时包含虚拟字段
  toObject: { virtuals: true }  // console.log() 时包含虚拟字段
});

// 定义虚拟字段 - 关联 BlogPost
authorSchema.virtual('posts', {
  ref: 'BlogPost',        // 关联的模型
  localField: '_id',      // 本地字段
  foreignField: 'author', // 外部字段
  // justOne: false,      // 默认返回数组
});

const Author = mongoose.model('Author', authorSchema);

// 定义 BlogPost Schema
const blogPostSchema = new mongoose.Schema({
  title: String,
  content: String,
  author: { type: mongoose.Schema.Types.ObjectId, ref: 'Author' },
});

const BlogPost = mongoose.model('BlogPost', blogPostSchema);

// 使用虚拟填充
const authorWithPosts = await Author.findById(authorId).populate('posts');
console.log(authorWithPosts.posts); // 该作者的所有文章

// 单个文档的虚拟字段
const bookSchema = new mongoose.Schema({
  title: String,
  authorId: { type: mongoose.Schema.Types.ObjectId, ref: 'Author' }
});

bookSchema.virtual('authorDetails', {
  ref: 'Author',
  localField: 'authorId',
  foreignField: '_id',
  justOne: true  // 只返回一个文档而非数组
});

const Book = mongoose.model('Book', bookSchema);

// 填充单个作者详情
const bookWithAuthor = await Book.findById(bookId).populate('authorDetails');
console.log(bookWithAuthor.authorDetails.name);
```

### 计算虚拟字段

```typescript
const userSchema = new mongoose.Schema({
  firstName: String,
  lastName: String,
}, { toJSON: { virtuals: true } });

// 定义计算虚拟字段
userSchema.virtual('fullName').get(function() {
  return `${this.firstName} ${this.lastName}`;
});

// 可设置的虚拟字段
userSchema.virtual('fullName')
  .get(function() {
    return `${this.firstName} ${this.lastName}`;
  })
  .set(function(value: string) {
    const [first, last] = value.split(' ');
    this.firstName = first;
    this.lastName = last;
  });

const User = mongoose.model('User', userSchema);

const user = new User({ firstName: 'John', lastName: 'Doe' });
console.log(user.fullName); // "John Doe"

user.fullName = 'Jane Smith';
console.log(user.firstName); // "Jane"
console.log(user.lastName);  // "Smith"
```

## 相关资源

- **MongoDB 文档**: https://www.mongodb.com/docs/
- **Mongoose 文档**: https://mongoosejs.com/docs/
- **Mongoose 虚拟字段**: https://mongoosejs.com/docs/tutorials/virtuals.html
- **MongoDB 大学**: https://learn.mongodb.com/
- **Atlas 文档**: https://www.mongodb.com/docs/atlas/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/1837620622) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
