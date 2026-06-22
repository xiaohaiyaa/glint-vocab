# 前后端接口契约 (API Contract)

> 文档版本：v0.1 | 编写目的：锁定前后端数据格式
---

## 1. 接口列表（v0.1 仅 3 个接口）

| 接口名称 | 方法 | 路径 | 说明 |
| :--- | :--- | :--- | :--- |
| 录入单词 | POST | `/api/words` | 保存用户录入的单词和例句 |
| 获取今日复习 | GET | `/api/review/today` | 获取今日待复习的单词列表（含例句） |
| 提交复习反馈 | POST | `/api/review/feedback` | 用户点击“陌生/模糊/掌握”后提交 |

---

## 2. 接口详情

### 2.1 录入单词

**请求** POST

```json
POST /api/words
Content-Type: application/json

{
  "word": "serendipity",
  "sentence": "She found it by serendipity."
}
```

**响应（成功）** -

```json
{
  "code": 0,
  "data": {
    "wordId": "uuid-xxx-xxx",
    "message": "录入成功"
  }
}
```

**响应（失败）** -

```json
{
  "code": 1001,
  "message": "单词已存在"
}
```

**错误码** -

| 错误码 | 说明 |
| :--- | :--- |
| 1001 | 单词已存在 |
| 1002 | 例句为空 |
| 1003 | 翻译 API 调用失败 |

---

### 2.2 获取今日复习列表

**请求** GET

```json
GET /api/review/today
```

**响应（成功）** -

```json
{
  "code": 0,
  "data": [
    {
      "wordId": "uuid-xxx-1",
      "word": "serendipity",
      "translation": "意外发现珍奇事物的本领",
      "sentenceId": "uuid-yyy-1",
      "sentence": "She found it by serendipity.",
      "nextReviewDate": "2026-06-22"
    },
    {
      "wordId": "uuid-xxx-2",
      "word": "ephemeral",
      "translation": "短暂的，瞬息的",
      "sentenceId": "uuid-yyy-2",
      "sentence": "Fame is ephemeral.",
      "nextReviewDate": "2026-06-21"
    }
  ]
}
```

**响应（空列表）** -

```json
{
  "code": 0,
  "data": []
}
```

---

### 2.3 提交复习反馈

**请求** POST

```json
POST /api/review/feedback
Content-Type: application/json

{
  "wordId": "uuid-xxx-1",
  "sentenceId": "uuid-yyy-1",
  "rating": 2
}
```

> `rating` 取值：`1`=陌生，`2`=模糊，`3`=掌握

**响应（成功）** -

```json
{
  "code": 0,
  "data": {
    "nextReviewDate": "2026-06-25"
  }
}
```

**响应（失败）** -

```json
{
  "code": 2001,
  "message": "单词不存在"
}
```

**错误码** -

| 错误码 | 说明 |
| :--- | :--- |
| 2001 | 单词不存在 |
| 2002 | 例句不存在 |
| 2003 | rating 取值无效（必须为 1/2/3） |

---

## 3. 前端 Mock 数据

前端在未接入真实后端时，使用以下数据模拟接口：

```javascript
// src/mocks/api.js

// Mock 录入
export const mockAddWord = (word, sentence) => {
  return {
    code: 0,
    data: { wordId: 'mock-uuid', message: '录入成功' }
  };
};

// Mock 今日复习列表
export const mockGetTodayReview = () => {
  return {
    code: 0,
    data: [
      {
        wordId: 'mock-1',
        word: 'serendipity',
        translation: '意外发现珍奇事物的本领',
        sentenceId: 'mock-s-1',
        sentence: 'She found it by serendipity.',
        nextReviewDate: '2026-06-22'
      }
    ]
  };
};

// Mock 提交反馈
export const mockSubmitFeedback = (wordId, sentenceId, rating) => {
  const days = rating === 1 ? 1 : rating === 2 ? 3 : 7;
  const nextDate = new Date();
  nextDate.setDate(nextDate.getDate() + days);
  return {
    code: 0,
    data: { nextReviewDate: nextDate.toISOString().split('T')[0] }
  };
};
```

---

## 4. 接口变更记录

| 版本 | 变更内容 | 日期 |
| :--- | :--- | :--- |
| v0.1 | 文档约束建立 | 2026-06-21 |
