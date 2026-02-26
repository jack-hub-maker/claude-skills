---
name: enterprise-application-workflow
description: 处理企业入驻申请流程，包括提交、审核、通过、驳回等状态管理。当开发企业入驻功能、申请表单、状态管理或企业概览页面时使用。
---

# Enterprise Application Workflow

## Overview

The enterprise application workflow manages the complete lifecycle of enterprise onboarding, from initial submission through review to final approval or rejection.

## Application States

The workflow has five main states:

1. **未提交 (Unsubmit)** - `/overview/unsubmit` - User hasn't submitted application
2. **审核中 (Checking)** - `/overview/checking` - Application submitted, under review
3. **已通过 (Pass)** - `/overview/pass` - Application approved
4. **已驳回 (Reject)** - `/overview/reject` - Application rejected
5. **申请中 (Apply)** - `/overview/apply` - Active application form

## Key Components

### ApplyForm Component
Location: `src/pages/Overview/components/ApplyForm.tsx`

**Props:**
- `initialValues`: Pre-filled form data
- `readonly`: Display-only mode (for viewing submitted applications)
- `isUpdate`: Update mode (re-application after rejection)
- `corporationApplyRecordId`: Required for updates

**Form Fields:**
- Company name (企业名称)
- Company type (企业类型): 1=网吧, 2=酒店, 3=其他
- Region (所在城市): Province/City only (not district)
- Address (详细地址)
- Invoice title (开票名称)
- Tax number (税号)
- Email (电子邮箱)

**Validation:**
- Invoice title: 2-100 chars, Chinese/English/numbers/parentheses/hyphens
- Tax number: 15-20 chars, uppercase letters/numbers
- Region: Must select province and city

### Status Components

- `Checking.tsx`: Shows pending review state with "查看详情" and "撤销申请" buttons
- `Pass.tsx`: Shows approved state with enterprise information display
- `Reject.tsx`: Shows rejected state with rejection reason
- `UnSubmit.tsx`: Shows unsubmitted state with "提交申请" button

## API Endpoints

**Get Latest Application:**
```typescript
API_corporationApplyGetLatest() 
// Returns: GetLatestApplyResponse with current application status
```

**Submit Application:**
```typescript
API_corporationApplySubmit({
  corporationName: string,
  corporationType: number, // 1, 2, or 3
  provinceId: number,
  cityId: number,
  districtId: number, // Same as cityId (only province/city level)
  address?: string,
  invoiceTitle?: string,
  invoiceTaxNo?: string,
  contactEmail?: string,
  optUser: string
})
```

**Update Application:**
```typescript
API_corporationApplyUpdate({
  corporationApplyRecordId: number,
  // ... same fields as submit
})
```

**Cancel Application:**
```typescript
API_corporationApplyCancel({
  optUser: string,
  corporationApplyRecordId: number
})
```

## Region Handling

**Important**: Region selection is **province/city only** (not district level).

- Data source: `src/constants/zone.js` (pca-code format)
- Conversion function: `pcaToRegionOptions()` converts to Cascader format
- Display: Shows as "省 / 市" (e.g., "北京市 / 市辖区")
- Submission: `districtId` should be set to `cityId` value

## Common Patterns

### Redirect on Reload
Use `useRedirectOverviewOnReload` hook to redirect to appropriate state page based on application status.

### Form Initialization
Convert API response to form initialValues:
- `provinceId/cityId/districtId` → `regionCodes: [provinceCode, cityCode]`
- `corporationType` number → form value
- Handle both code-based and name-based region data

### Readonly Mode
When `readonly=true`:
- All form fields disabled
- Show close button (bottom-right) to return to checking page
- No submit buttons displayed

## Example: Adding New Status

1. Create component in `src/pages/Overview/components/`
2. Add route in `src/router/routes.ts`
3. Update status logic in `src/pages/Overview/index.tsx`
4. Add corresponding API types in `src/services/models/overview/typing.d.ts`
