---
name: iconfont-usage
description: 在项目中使用 IconFont 图标。当需要在菜单、按钮、表单或任何 UI 组件中添加图标时使用。必要时用 IconFont 图标替换 Ant Design 图标。
---

# IconFont Usage

## Overview

This project uses custom IconFont icons instead of Ant Design icons for consistent branding and design.

## IconFont Setup

**Location**: `src/assets/fonts/`

**Files:**
- `iconfont.css` - Icon styles
- `iconfont.js` - Icon definitions
- Font files: `.ttf`, `.woff`, `.woff2`

**Import**: Already imported in `src/app.tsx`:
```typescript
import './assets/fonts/iconfont.js';
```

## Usage Pattern

**Basic Usage:**
```tsx
<span className="iconfont icon-iconname" />
```

**With Styling:**
```tsx
<span className={`iconfont icon-iconname ${styles.customClass}`} />
```

## Common Icons

### Menu Icons
- `icon-qiyegailan` - 企业概览 (Enterprise Overview)
- `icon-zizhufukuan` - 自助付款 (Self-Payment)
- `icon-jiaoyijilu` - 交易记录 (Transaction Record)

### Payment Icons
- `icon-Vector-1` - 支付宝 (Alipay) - Blue (#1677ff)
- `icon-Vector` - 微信 (WeChat Pay) - Green (#07c160)
- `icon-zizhufukuan` - Payment amount prefix

### Enterprise Type Icons
- `icon-a-Frame427318627` - 网吧 (Internet Cafe, type=1)
- `icon-Frame-1` - 酒店 (Hotel, type=2)
- `icon-Frame1` - 其他 (Other, type=3)

### Form Icons
- `icon-chengshi` - 所在城市 (City)
- `icon-youxiang` - 邮箱地址 (Email)
- `icon-dizhi` - 详细地址 (Address)
- `icon-piao` - 开票名称/税号 (Invoice)

### Other Icons
- `icon-yijianfuzhi` - Copy icon

## Menu Icon Configuration

**In Routes** (`src/router/routes.ts`):
```typescript
{
  name: '企业概览',
  path: '/overview',
  icon: 'icon-qiyegailan', // IconFont class name
}
```

**Rendering** (`src/app.tsx`):
```typescript
if (typeof route.icon === 'string' && route.icon.startsWith('icon-')) {
  route.icon = (
    <span className={`iconfont ${route.icon}`}></span>
  );
}
```

## Icon Styling

**Size**: Typically 16px
```css
.iconfont {
  font-size: 16px;
}
```

**Color**: Inherits from parent or set explicitly
```tsx
<span 
  className="iconfont icon-Vector-1" 
  style={{ color: '#1677ff' }} 
/>
```

**In Menu Items**:
- Default: Grey (`rgba(0, 0, 0, 0.45)`)
- Selected: Blue (`#1664ff`)
- Disabled: Light grey (`rgba(0, 0, 0, 0.25)`)

## Replacing Ant Design Icons

**Before (Ant Design):**
```tsx
import { AlipayCircleOutlined } from '@ant-design/icons';
<AlipayCircleOutlined />
```

**After (IconFont):**
```tsx
<span className="iconfont icon-Vector-1" />
```

## Dynamic Icon Selection

**Example - Enterprise Type Icons:**
```typescript
const getTypeIcon = () => {
  const type = Number(companyType);
  if (type === 1) return 'icon-a-Frame427318627';
  if (type === 2) return 'icon-Frame-1';
  if (type === 3) return 'icon-Frame1';
  return 'icon-a-Frame427318627';
};

<span className={`iconfont ${getTypeIcon()}`} />
```

## Best Practices

1. **Always use `iconfont` class**: Required for proper rendering
2. **Check icon names**: Verify icon exists in `iconfont.json` or demo file
3. **Consistent sizing**: Use 16px for most UI elements
4. **Color inheritance**: Icons inherit text color, override when needed
5. **Accessibility**: Include `alt` text or `aria-label` for images, use semantic HTML for icon spans

## Finding Available Icons

Check `src/assets/fonts/demo_index.html` or `iconfont.json` for complete list of available icons.
