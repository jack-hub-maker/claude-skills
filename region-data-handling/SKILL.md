---
name: region-data-handling
description: 处理省市区级联数据。当开发地区选择、地址表单或在地区代码与名称之间转换时使用。注意：本项目仅使用省/市两级（不含区）。
---

# Region Data Handling

## Overview

Region data handling manages province and city selection for enterprise addresses. **Important**: This project only uses **province/city level** (not district/区 level).

## Data Source

**Location**: `src/constants/zone.js`

**Format**: pca-code format (province/city/district structure)
- Province: 2-digit code (e.g., "11" for 北京市)
- City: 4-digit code (e.g., "1101" for 市辖区)
- District: 6-digit code (e.g., "110101" for 东城区)

**Note**: Data includes district level, but we only use province/city.

## Conversion Function

**Location**: `src/pages/Overview/components/ApplyForm.tsx`

```typescript
function pcaToRegionOptions(nodes: PcaNode[]): RegionOption[] {
  return nodes.map((node) => ({
    value: node.code ?? node.name ?? node.text ?? '',
    label: node.name ?? node.text ?? '',
    children: node.children?.length
      ? node.children.map((child) => ({
          value: child.code ?? child.name ?? child.text ?? '',
          label: child.name ?? child.text ?? '',
          // No further recursion - only province/city level
        }))
      : undefined,
  }));
}
```

## Cascader Configuration

**Component**: Ant Design Cascader

**Props:**
```typescript
<Cascader
  options={REGION_OPTIONS}
  placeholder="请选择省/市"
  displayRender={(labels) => labels.join(' / ')}
  // Only 2 levels: province and city
/>
```

**Display Format**: "省 / 市" (e.g., "北京市 / 市辖区")

## API Integration

**Submission:**
- `provinceId`: Province code (number)
- `cityId`: City code (number)
- `districtId`: Set to `cityId` value (since we don't use district)

**Example:**
```typescript
{
  provinceId: 11,      // 北京市
  cityId: 1101,        // 市辖区
  districtId: 1101     // Same as cityId
}
```

## Data Retrieval

**From API Response:**
```typescript
// API returns: provinceId, cityId, districtId
// Convert to regionCodes for form display
const regionCodes: [string, string] = [
  String(d.provinceId),
  String(d.cityId)
  // Ignore districtId
];
```

**For Form Initialization:**
```typescript
const regionInitial = initialValues?.regionCodes?.slice(0, 2) || 
  findRegionCodesByNames(regionData, province, city);
```

## Finding Codes by Names

**Function**: `findRegionCodesByNames()`

Converts province/city names to codes:
```typescript
findRegionCodesByNames(
  regionData,
  '北京市',    // province name
  '市辖区'     // city name
  // district optional, ignored if provided
)
// Returns: ['11', '1101']
```

## Common Patterns

### Display Region Text
```typescript
const cityText = [province, city].filter(Boolean).join('');
// Example: "北京市市辖区"
```

### Form Validation
- Region is required
- Must select both province and city
- Validation message: "请选择省/市"

### Region Codes Array
- Length: Always 2 elements `[provinceCode, cityCode]`
- Type: `[string, string]`
- Used for form `initialValues.regionCodes`

## Important Notes

1. **No District Level**: Always ignore district data, even if present
2. **districtId Submission**: Set `districtId = cityId` when submitting
3. **Display**: Only show province and city in UI
4. **Validation**: Ensure exactly 2 levels selected
