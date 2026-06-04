# 良いテストと悪いテスト（Good and Bad Tests）

## 良いテスト（Good Tests）

**結合スタイル（Integration-style）**：内部部品のモックではなく、本物のインターフェースを通してテストする。

```typescript
// GOOD: Tests observable behavior
test("user can checkout with valid cart", async () => {
  const cart = createCart();
  cart.add(product);
  const result = await checkout(cart, paymentMethod);
  expect(result.status).toBe("confirmed");
});
```

特徴：

- ユーザー／呼び出し側が気にする振る舞いをテストする
- 公開 API のみを使う
- 内部のリファクタリングに耐える
- HOW ではなく WHAT を記述する
- 1 テストにつき論理的なアサーションは 1 つ

## 悪いテスト（Bad Tests）

**実装詳細のテスト（Implementation-detail tests）**：内部構造に結合している。

```typescript
// BAD: Tests implementation details
test("checkout calls paymentService.process", async () => {
  const mockPayment = jest.mock(paymentService);
  await checkout(cart, payment);
  expect(mockPayment.process).toHaveBeenCalledWith(cart.total);
});
```

危険信号（Red flags）：

- 内部の協働オブジェクトをモックしている
- private メソッドをテストしている
- 呼び出し回数／順序をアサートしている
- 振る舞いが変わっていないのにリファクタリングで壊れる
- テスト名が WHAT ではなく HOW を記述している
- インターフェースではなく外部手段を通して検証している

```typescript
// BAD: Bypasses interface to verify
test("createUser saves to database", async () => {
  await createUser({ name: "Alice" });
  const row = await db.query("SELECT * FROM users WHERE name = ?", ["Alice"]);
  expect(row).toBeDefined();
});

// GOOD: Verifies through interface
test("createUser makes user retrievable", async () => {
  const user = await createUser({ name: "Alice" });
  const retrieved = await getUser(user.id);
  expect(retrieved.name).toBe("Alice");
});
```
