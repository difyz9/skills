---
name: nextjs-expert
description: Next.js 14+ App Router 开发专家,提供最佳实践、性能优化和现代化开发指导
---

# Next.js 开发专家技能

为 Next.js 项目提供全面的开发指导。

## App Router 最佳实践

- 使用 Server Components 作为默认选项
- 仅在需要交互时使用 Client Components (`'use client'`)
- 利用 `loading.tsx` 和 `error.tsx` 提供更好的用户体验
- 使用 `generateMetadata` 进行动态 SEO

## 数据获取策略

```typescript
// Server Component - 服务端数据获取
async function ProductPage({ params }: { params: { id:  string } }) {
  const product = await fetch(`https://api.example.com/products/${params.id}`, {
    next: { revalidate: 3600 } // ISR:  每小时重新验证
  }).then(res => res.json())
  
  return <ProductDisplay product={product} />
}

// Client Component - 客户端交互
'use client'
import { useState } from 'react'

export function AddToCart() {
  const [count, setCount] = useState(0)
  return <button onClick={() => setCount(count + 1)}>添加 {count}</button>
}
```

## 性能优化

- 使用 `next/image` 自动优化图片
- 使用 `next/font` 优化字体加载
- 使用动态导入 `next/dynamic` 进行代码分割
- 启用 Turbopack (Next.js 14+)

## 项目结构

```
app/
├── (auth)/          # 路由组
│   ├── login/
│   └── register/
├── api/             # API 路由
├── components/      # 共享组件
├── lib/            # 工具函数
└── layout.tsx      # 根布局
```

## TypeScript 配置

- 使用严格模式
- 为 Server Actions 定义明确的类型
- 使用 Zod 进行运行时验证

## 示例输出

提供完整的、可运行的 Next.js 组件代码,包含类型定义和错误处理。