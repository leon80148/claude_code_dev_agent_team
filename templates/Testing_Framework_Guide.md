# 🧪 測試框架指南 - 快速實效的現代測試方法

## 🎯 測試哲學

### 核心原則
1. **自動化優先** - 能自動化的絕不手動
2. **快速反饋** - 30分鐘內開始測試，5分鐘內反饋結果
3. **實用導向** - 專注解決實際問題，避免過度設計
4. **工具驅動** - 善用現代工具提升效率

### 測試金字塔（簡化版）
```
   🔺 E2E測試（關鍵路徑）- 20%
  🔻🔻 API測試（業務邏輯）- 50%  
 🔻🔻🔻 單元測試（函數邏輯）- 30%
```

## 🛠 推薦工具棧

### 必備工具
- **E2E 測試**：Playwright（首選）、Cypress
- **API 測試**：REST Client、Playwright Request
- **CI/CD**：GitHub Actions
- **問題追蹤**：GitHub Issues

### 選用工具
- **性能測試**：Lighthouse、K6
- **視覺測試**：Playwright Screenshots
- **負載測試**：簡單壓力測試

## ⚡ 5分鐘快速開始

### 1. 環境設置
```bash
# Playwright 快速設置
npm init playwright@latest
npx playwright install

# 第一個測試
npx playwright test --headed
```

### 2. 基本測試結構
```typescript
// tests/example.spec.ts
import { test, expect } from '@playwright/test';

test('基本功能測試', async ({ page }) => {
  await page.goto('/');
  await expect(page).toHaveTitle(/首頁/);
});
```

### 3. 運行測試
```bash
# 運行所有測試
npx playwright test

# 運行特定測試
npx playwright test login

# UI 模式
npx playwright test --ui
```

## 📝 測試設計模式

### 1. Page Object Pattern（推薦）
```typescript
// pages/LoginPage.ts
export class LoginPage {
  constructor(private page: Page) {}
  
  async login(email: string, password: string) {
    await this.page.fill('[data-testid="email"]', email);
    await this.page.fill('[data-testid="password"]', password);
    await this.page.click('[data-testid="login-btn"]');
  }
}

// 使用
const loginPage = new LoginPage(page);
await loginPage.login('test@example.com', 'password');
```

### 2. 測試數據管理
```typescript
// fixtures/testData.ts
export const TestUsers = {
  admin: { email: 'admin@test.com', password: 'admin123' },
  user: { email: 'user@test.com', password: 'user123' }
};
```

### 3. 選擇器策略
```typescript
// ✅ 推薦：使用 data-testid
await page.click('[data-testid="submit-button"]');

// ❌ 避免：CSS 類名（容易變動）
await page.click('.btn-primary');

// ✅ 可選：語義化選擇器
await page.click('button:has-text("提交")');
```

## 🧪 測試類型與範例

### E2E 測試（關鍵路徑）
```typescript
test.describe('用戶核心流程', () => {
  test('完整註冊登錄流程', async ({ page }) => {
    // 註冊
    await page.goto('/register');
    await page.fill('[data-testid="email"]', 'new@test.com');
    await page.fill('[data-testid="password"]', 'password123');
    await page.click('[data-testid="register-btn"]');
    
    // 登錄
    await page.goto('/login');
    await page.fill('[data-testid="email"]', 'new@test.com');
    await page.fill('[data-testid="password"]', 'password123');
    await page.click('[data-testid="login-btn"]');
    
    // 驗證
    await expect(page).toHaveURL('/dashboard');
  });
});
```

### API 測試
```typescript
test.describe('API 測試', () => {
  test('用戶認證 API', async ({ request }) => {
    const response = await request.post('/api/auth/login', {
      data: {
        email: 'test@example.com',
        password: 'password123'
      }
    });
    
    expect(response.status()).toBe(200);
    const data = await response.json();
    expect(data.token).toBeDefined();
  });
});
```

### 視覺回歸測試
```typescript
test('頁面視覺檢查', async ({ page }) => {
  await page.goto('/dashboard');
  await page.waitForLoadState('networkidle');
  
  // 隱藏動態內容
  await page.addStyleTag({
    content: '[data-testid="timestamp"] { visibility: hidden; }'
  });
  
  await expect(page).toHaveScreenshot('dashboard.png');
});
```

## 🚀 CI/CD 整合範例

### GitHub Actions 最小配置
```yaml
# .github/workflows/tests.yml
name: Tests
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
      
      - run: npm ci
      - run: npx playwright install --with-deps
      - run: npx playwright test
      
      - uses: actions/upload-artifact@v4
        if: always()
        with:
          name: playwright-report
          path: playwright-report/
```

## 🔧 常見問題速解

### 1. 測試不穩定
```typescript
// 添加適當等待
await page.waitForLoadState('networkidle');
await expect(element).toBeVisible({ timeout: 10000 });

// 處理異步操作
await page.waitForResponse(response => 
  response.url().includes('/api/data')
);
```

### 2. 元素找不到
```typescript
// 使用更可靠的選擇器
await page.locator('[data-testid="button"]').click();

// 等待元素出現
await page.locator('#dynamic-content').waitFor();
```

### 3. 測試環境問題
```typescript
// 環境檢查
test.beforeEach(async ({ page }) => {
  // 確認服務可用
  const response = await page.request.get('/api/health');
  expect(response.status()).toBe(200);
});
```

## 📊 品質檢查清單

### 測試設計檢查
- [ ] 覆蓋核心用戶路徑
- [ ] 使用穩定的選擇器（data-testid）
- [ ] 包含正面和負面測試案例
- [ ] 測試隔離且可重複執行

### 自動化檢查
- [ ] E2E 測試覆蓋率 ≥ 80%
- [ ] API 測試覆蓋率 ≥ 90%
- [ ] 測試執行時間 ≤ 10分鐘
- [ ] CI/CD 整合正常

### 維護檢查
- [ ] 測試失敗時易於除錯
- [ ] 測試代碼簡潔易懂
- [ ] 測試資料管理良好
- [ ] 文檔和註釋充足

## 📋 實施步驟

### 第一週：基礎建設
1. 設置 Playwright 環境
2. 創建第一個 E2E 測試
3. 建立 Page Object 基礎架構
4. 配置 CI/CD 基本流程

### 第二週：核心測試
1. 實現關鍵用戶路徑測試
2. 添加 API 測試
3. 建立測試資料管理
4. 完善錯誤處理

### 第三週：優化完善
1. 添加視覺回歸測試
2. 性能測試基準
3. 測試報告優化
4. 團隊培訓和文檔

## 🎯 成功指標

### 技術指標
- **啟動時間**：≤ 30分鐘
- **執行時間**：≤ 10分鐘
- **自動化率**：≥ 80%
- **通過率**：≥ 95%

### 業務指標
- **問題發現**：開發階段 80% 問題被發現
- **修復效率**：95% 問題一次修復成功
- **發布信心**：團隊對發布品質有信心
- **用戶滿意**：生產環境問題減少 50%

---

**記住：最好的測試是能快速發現問題並易於維護的測試！** 🚀