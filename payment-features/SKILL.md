---
name: payment-features
description: 实现支付功能，包括自助付款、二维码生成、支付状态轮询和交易记录。当开发支付流程、充值功能、二维码展示或支付历史页面时使用。
---

# Payment Features

## Overview

Payment features handle self-service payment (自助付款) and transaction record management (交易记录) for enterprise accounts.

## Self-Payment Flow

Location: `src/pages/SelfPayment/index.tsx`

### Payment Amount
- **Range**: 0.01 - 20000 (supports decimals)
- **Input**: Number input with step 0.01
- **Validation**: Minimum 0.01, maximum 20000
- **Formatting**: Use `formatAmount()` from `src/utils/format.ts` for display (thousands separator)

### Payment Process

1. **Enter Amount**: User inputs payment amount (0.01-20000)
2. **Agreement Check**: User must check "业务合作协议" checkbox
3. **Generate QR Code**: Click "确定" button
4. **Polling**: System polls payment status every 2s for up to 10 minutes
5. **Success**: On payment success, clear amount and agreement checkbox

### QR Code States

- **Loading**: "正在生成付款二维码..."
- **Ready**: QR code displayed with payment amount
- **Expired**: After 10 minutes, shows "二维码已过期" with refresh option
- **Success**: Shows "支付成功" with green checkmark overlay

### Payment Methods

Display icons for:
- Alipay (支付宝): `icon-Vector-1` (blue #1677ff)
- WeChat Pay (微信): `icon-Vector` (green #07c160)

## API Endpoints

**Recharge (Generate QR Code):**
```typescript
API_recharge({
  amount: number // 0.01-20000
})
// Returns: { payUrl: string, paymentOrderNo: string }
```

**Get Payment Order:**
```typescript
API_getPaymentOrder({
  paymentOrderNo: string
})
// Returns: { payStatus: number } // 2 = success
```

**Get Offline Payment Info:**
```typescript
API_getOfflinePaymentInfo()
// Returns: Bank account info for offline payment
```

**Agreement Preview:**
```typescript
API_agreementPreview({
  amount: number
})
// Returns: base64 PDF string
```

## Payment Status Polling

**Implementation:**
- Poll interval: 2 seconds (`POLL_INTERVAL_MS = 2000`)
- Timeout: 10 minutes (`POLL_TIMEOUT_MS = 600000`)
- Success status: `payStatus === 2`

**Polling Logic:**
```typescript
startPolling(paymentOrderNo: string) {
  // Poll every 2s
  // On success: handlePaymentSuccess()
  // On timeout: setQrExpired(true)
}
```

## Transaction Record

Location: `src/pages/TransactionRecord/index.tsx`

**Display:**
- Payment order number (支付订单号)
- Payment channel (类型): 1=支付宝, 2=微信
- Third-party order number (支付通道订单号)
- Amount (金额): Formatted with thousands separator
- Payment time (发生时间)

**API:**
```typescript
API_paymentOrderPage({
  payStatusList: [2], // Only show successful payments
  current: number,
  pageSize: number,
  // ... other filters
})
```

## Amount Formatting

Use `formatAmount()` utility:
```typescript
import { formatAmount } from '@/utils/format';

// Format with thousands separator
formatAmount(1000) // "1,000.00"
formatAmount(1234.56) // "1,234.56"
formatAmount(0.01) // "0.01"
```

## Agreement Modal

Location: `src/pages/SelfPayment/AgreementModal.tsx`

**Features:**
- Displays PDF agreement
- Uses react-pdf for rendering
- Page separators: Grey bars between PDF pages
- Base64 PDF source from `API_agreementPreview`

## Common Patterns

### Reset Payment Form
After successful payment or clicking reset:
- Clear amount input
- Uncheck agreement checkbox
- Clear QR code state
- Stop polling

### QR Code Component
Uses `QRCode` component with:
- `value`: Payment URL from API
- `status`: "active", "expired", or "scanned"
- `onRefresh`: Regenerate QR code
- Error level: "H" (high)

### Payment Success Callback
Global callback via `window.__selfPaymentOnSuccess`:
- Called when payment succeeds
- Clears form and stops polling
- Shows success state
