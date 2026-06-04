# テストしやすさを意識したインターフェース設計

良いインターフェースはテストを自然なものにします：

1. **依存は受け取る、自前で生成しない（Accept dependencies, don't create them）**

   ```typescript
   // Testable
   function processOrder(order, paymentGateway) {}

   // Hard to test
   function processOrder(order) {
     const gateway = new StripeGateway();
   }
   ```

2. **副作用を起こさず、結果を返す（Return results, don't produce side effects）**

   ```typescript
   // Testable
   function calculateDiscount(cart): Discount {}

   // Hard to test
   function applyDiscount(cart): void {
     cart.total -= discount;
   }
   ```

3. **外部への露出を減らす（Small surface area）**
   - メソッドが少ない = 必要なテストが少ない
   - 引数が少ない = テストの準備がシンプル