---
name: dialog-replace
description: 使用 HTML `<dialog>` 元素建構現代化網頁互動介面。當使用者提及 alert、confirm、prompt、modal、popup、對話框、彈出視窗、訊息框、確認框、輸入框時自動使用。完全取代傳統的 window.alert()、window.confirm()、window.prompt() 及第三方 modal 套件。提供一致性、無障礙性的 UI 解決方案。
---

# Dialog Replace

使用原生 HTML `<dialog>` 元素取代傳統 JavaScript 對話框函數。

## When to use this skill

當使用者在 web 類 UI 或專案中提及以下關鍵詞時自動啟用：
- `alert`、`confirm`、`prompt`
- `dialog`、`modal`、`popup`
- 對話框、彈出視窗、訊息框、確認框、輸入框

## 核心原則

1. **完全取代傳統方法**：不使用 `window.alert()`、`window.confirm()`、`window.prompt()`
2. **不依賴第三方套件**：不使用 Bootstrap Modal、SweetAlert、等 modal 套件
3. **原生 `<dialog>` 優先**：使用 HTML5 原生 `<dialog>` 元素
4. **非阻塞式設計**：使用 Promise-based API，不阻塞主線程
5. **無障礙性**：符合 ARIA 規範，支援鍵盤導航

## 實作規範

### Alert 替代方案

```html
<dialog id="alert-dialog">
  <p class="message"></p>
  <button type="button" autofocus>確定</button>
</dialog>
```

```javascript
async function showAlert(message) {
  const dialog = document.getElementById('alert-dialog');
  dialog.querySelector('.message').textContent = message;
  dialog.showModal();
  return new Promise(resolve => {
    dialog.querySelector('button').onclick = () => {
      dialog.close();
      resolve();
    };
  });
}
```

### Confirm 替代方案

```html
<dialog id="confirm-dialog">
  <p class="message"></p>
  <div class="actions">
    <button type="button" value="cancel">取消</button>
    <button type="button" value="ok" autofocus>確定</button>
  </div>
</dialog>
```

```javascript
async function showConfirm(message) {
  const dialog = document.getElementById('confirm-dialog');
  dialog.querySelector('.message').textContent = message;
  dialog.showModal();
  return new Promise(resolve => {
    dialog.querySelectorAll('button').forEach(btn => {
      btn.onclick = () => {
        dialog.close();
        resolve(btn.value === 'ok');
      };
    });
  });
}
```

### Prompt 替代方案

```html
<dialog id="prompt-dialog">
  <form method="dialog">
    <label class="message"></label>
    <input type="text" name="input" autofocus>
    <div class="actions">
      <button type="button" value="cancel">取消</button>
      <button type="submit" value="ok">確定</button>
    </div>
  </form>
</dialog>
```

```javascript
async function showPrompt(message, defaultValue = '') {
  const dialog = document.getElementById('prompt-dialog');
  dialog.querySelector('.message').textContent = message;
  const input = dialog.querySelector('input');
  input.value = defaultValue;
  dialog.showModal();
  return new Promise(resolve => {
    dialog.querySelector('form').onsubmit = (e) => {
      e.preventDefault();
      dialog.close();
      resolve(input.value);
    };
    dialog.querySelector('[value="cancel"]').onclick = () => {
      dialog.close();
      resolve(null);
    };
  });
}
```

## 基礎樣式

```css
dialog {
  border: none;
  border-radius: 8px;
  padding: 1.5rem;
  max-width: 400px;
  box-shadow: 0 4px 24px rgba(0, 0, 0, 0.15);
}

dialog::backdrop {
  background: rgba(0, 0, 0, 0.5);
}

dialog .actions {
  display: flex;
  gap: 0.5rem;
  justify-content: flex-end;
  margin-top: 1rem;
}
```

## 注意事項

- 使用 `showModal()` 而非 `show()` 以獲得 backdrop 和模態行為
- 按 Escape 鍵會觸發 `cancel` 事件並關閉 dialog
- 確保 `autofocus` 屬性設置在適當的元素上
- 關閉 dialog 後應將焦點返回觸發元素
