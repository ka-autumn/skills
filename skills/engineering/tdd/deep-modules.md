# 深いモジュール（Deep Modules）

『A Philosophy of Software Design』より：

**深いモジュール（deep module）** = 小さいインターフェース + 多くの実装

```
┌─────────────────────┐
│   Small Interface   │  ← Few methods, simple params
├─────────────────────┤
│                     │
│                     │
│  Deep Implementation│  ← Complex logic hidden
│                     │
│                     │
└─────────────────────┘
```

**浅いモジュール（shallow module）** = 大きいインターフェース + 少ない実装（避けるべき）

```
┌─────────────────────────────────┐
│       Large Interface           │  ← Many methods, complex params
├─────────────────────────────────┤
│  Thin Implementation            │  ← Just passes through
└─────────────────────────────────┘
```

インターフェースを設計するときは、次を自問してください：

- メソッドの数を減らせないか？
- 引数を簡潔にできないか？
- もっと多くの複雑さを内部に隠せないか？
