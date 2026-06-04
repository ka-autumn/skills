# モックするタイミング（When to Mock）

**システム境界（system boundaries）** でのみモックしてください：

- 外部 API（決済、メールなど）
- データベース（場合による — テスト用 DB を優先）
- 時刻／乱数
- ファイルシステム（場合による）

モックしないもの：

- 自分のクラス／モジュール
- 内部の協働オブジェクト（internal collaborators）
- 自分が制御できるもの

## モックしやすさを意識した設計（Designing for Mockability）

システム境界では、モックしやすいインターフェースを設計してください：

**1. 依存性注入（dependency injection）を使う**

外部依存は内部で生成せず、外から渡してください：

```typescript
// Easy to mock
function processPayment(order, paymentClient) {
  return paymentClient.charge(order.total);
}

// Hard to mock
function processPayment(order) {
  const client = new StripeClient(process.env.STRIPE_KEY);
  return client.charge(order.total);
}
```

**2. 汎用フェッチャーより SDK スタイルのインターフェースを優先する**

条件分岐を持つ 1 つの汎用関数ではなく、外部操作ごとに専用の関数を作ってください：

```typescript
// GOOD: Each function is independently mockable
const api = {
  getUser: (id) => fetch(`/users/${id}`),
  getOrders: (userId) => fetch(`/users/${userId}/orders`),
  createOrder: (data) => fetch('/orders', { method: 'POST', body: data }),
};

// BAD: Mocking requires conditional logic inside the mock
const api = {
  fetch: (endpoint, options) => fetch(endpoint, options),
};
```

SDK 方式では次のようになります：
- 各モックは 1 つの決まった結果を返す
- テストの準備に条件分岐が不要
- そのテストがどのエンドポイントを使うか分かりやすい
- エンドポイントごとの型安全性
