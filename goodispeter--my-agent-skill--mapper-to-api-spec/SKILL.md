---
name: mapper-to-api-spec
description: 分析 SQL Server Mapper（MyBatis/JPA 等）並生成簡潔的 API 規格文件。產出三個核心檔案：API-SPEC.md (主表格)、input.md (Input DTO)、output.md (Output DTO)。觸發詞："生成 API 規格"、"Mapper 轉 API"、"分析 Mapper"。 Use when this capability is needed.
metadata:
  author: goodispeter
---

# Mapper to API Specification Generator

將 SQL Server 的 Mapper 層分析並產出簡潔的 API 規格文件。

## 核心目標

從 MyBatis Mapper、JPA Repository 或其他資料存取層分析並產出：

- **API-SPEC.md**: 主要表格，列出所有 Mapper 方法與其基本資訊
- **input.md**: 所有 Input DTO 定義（可被 API-SPEC.md 連結）
- **output.md**: 所有 Output DTO 定義（可被 API-SPEC.md 連結）
- **{api_Name}.md**: 複雜 API 的獨立詳細說明檔案

## 設計原則

1. **不自動命名 API 或設定 endpoint** - 只分析 Mapper 方法，不做 RESTful 轉換
2. **簡單的保持簡單** - 簡單查詢直接填在表格中
3. **複雜的獨立說明** - 複雜邏輯產出獨立的 `{api_Name}.md` 檔案
4. **集中管理 DTO** - 所有 Input/Output 定義集中在 input.md / output.md

## 架構流程

```
┌──────────────────────────────────────────────────────────────┐
│  Phase 1: Mapper Discovery                                   │
│  → 掃描目標路徑，識別 Mapper 檔案                            │
│  → 識別 Mapper 類型（MyBatis XML/Java/JPA）                  │
│  → 統計方法數量                                              │
│  → Output: mapper-inventory.json                             │
├──────────────────────────────────────────────────────────────┤
│  Phase 2: Method Analysis                                    │
│  → 分析每個 Mapper 方法                                      │
│  → 解析方法簽章、參數、返回類型                             │
│  → 解析 SQL 語句（SELECT/INSERT/UPDATE/DELETE）             │
│  → 判斷複雜度（簡單/複雜）                                   │
│  → 提取 Input/Output DTO 結構                                │
│  → Output: _analysis/{mapper-name}.json                      │
├──────────────────────────────────────────────────────────────┤
│  Phase 3: Generate Specification Files                       │
│  → 生成 API-SPEC.md（主表格）                                │
│    - 簡單方法：直接填在表格中                               │
│    - 複雜方法：產生獨立 {api_Name}.md 並在表格中連結        │
│  → 生成 input.md（所有 Input DTO 定義）                      │
│  → 生成 output.md（所有 Output DTO 定義）                    │
│  → 生成複雜方法的獨立檔案（如需要）                         │
│  → Output: API-SPEC.md, input.md, output.md, {api}.md       │
└──────────────────────────────────────────────────────────────┘
```

## 執行階段詳細說明

### Phase 1: Mapper Discovery

**輸入**:

- Mapper 根目錄路徑

**執行**:

```bash
# 搜尋所有 Mapper 檔案
glob **/*Mapper.java
glob **/*Mapper.xml
glob **/*Repository.java

# 分析檔案類型
grep @Mapper
grep @Repository
grep <select|insert|update|delete>
```

**輸出**: `mapper-inventory.json`

```json
{
  "total_mappers": 15,
  "mappers": [
    {
      "name": "StudentMapper",
      "type": "MyBatis_XML",
      "file_path": "dal_products/StudentMapper.java",
      "xml_path": "dal_products/StudentMapper.xml",
      "method_count": 8
    }
  ]
}
```

---

### Phase 2: Method Analysis

**目標**: 分析每個 Mapper 方法的詳細資訊

**分析內容**:

1. **方法基本資訊**
   - 方法名稱
   - 參數類型與名稱
   - 返回類型（單筆/列表/分頁/數量）

2. **SQL 分析**
   - SQL 類型（SELECT/INSERT/UPDATE/DELETE）
   - 查詢欄位
   - WHERE 條件
   - JOIN 關聯

3. **複雜度判斷**
   - **簡單**: 單表 CRUD、簡單條件查詢
   - **複雜**: 多表 JOIN、複雜邏輯、批次操作、統計聚合

4. **API 方法合併分析** ⚠️ **重要**

   **目標**: 將可用可選參數實現的方法合併為單一 API，避免產生過多 endpoint。

   **合併規則**:

   | 模式                           | 範例方法                                                    | 合併後 API 名稱                | 合併策略                               |
   | ------------------------------ | ----------------------------------------------------------- | ------------------------------ | -------------------------------------- |
   | selectAll + selectByX          | `selectAll()` + `selectByLevelNo(String)`                   | `queryActivityRuleDetails`     | levelNo 改為可選參數                   |
   | selectByX1 + selectByX2        | `selectByWebSite(String)` + `selectBySn(Integer)`           | `querySessionTypes`            | webSite 和 sn 都改為可選，支援同時傳入 |
   | selectByPrimaryKey + selectByX | `selectByPrimaryKey(Integer)` + `selectByClientSn(Integer)` | `queryClientTemporalContracts` | sn 和 clientSn 改為可選，至少傳一個    |

   **判斷邏輯**:
   1. **識別候選方法**: 同一個 Mapper 中，返回類型相同或兼容的查詢方法
   2. **檢查參數差異**:
      - 若方法 A 無參數，方法 B 有參數 → 可合併（A 是全查詢，B 是條件查詢）
      - 若方法 A/B 參數不同但返回類型相同 → 可合併（條件查詢的不同維度）
   3. **生成合併 API**:
      - **API 名稱**: 使用語義化的查詢名稱（如 `queryActivityRuleDetails`）
      - **Input DTO**: 建立新的 Query Input，所有參數都設為可選（✗）
      - **Output DTO**: 維持原有輸出類型
      - **記錄合併來源**: 在 API-SPEC.md 的「合併的Mapper方法」欄位記錄原始方法名

   **範例 1: selectAll + selectByX**

   ```
   原始方法:
   - ActivityRuleDetailMapper.selectAll() → List<ActivityRuleDetail>
   - ActivityRuleDetailMapper.selectByLevelNo(String levelNo) → List<ActivityRuleDetail>

   合併為:
   - API 名稱: queryActivityRuleDetails
   - Input: ActivityRuleDetailQueryInput { levelNo?: String }
   - Output: List<ActivityRuleDetail>
   - 合併的Mapper方法: selectAll, selectByLevelNo
   ```

   **範例 2: 多參數查詢**

   ```
   原始方法:
   - SessionTypeMapper.selectAll() → List<SessionType>
   - SessionTypeMapper.selectByWebSite(String webSite) → List<SessionType>
   - SessionTypeMapper.selectBySn(Integer sourceSn) → List<SessionType>

   合併為:
   - API 名稱: querySessionTypes
   - Input: SessionTypeQueryInput { webSite?: String, sn?: Integer }
   - Output: List<SessionType>
   - 合併的Mapper方法: selectAll, selectByWebSite, selectBySn
   - 注意: webSite 和 sn 可同時傳入，進行 AND 查詢
   ```

   **範例 3: 主鍵查詢 + 其他維度查詢**

   ```
   原始方法:
   - ClientTemporalContractMapper.selectByPrimaryKey(Integer sn) → ClientTemporalContract
   - ClientTemporalContractMapper.selectByClientSn(Integer clientSn) → List<ClientTemporalContract>

   合併為:
   - API 名稱: queryClientTemporalContracts
   - Input: ClientTemporalContractQueryInput { sn?: Integer, clientSn?: Integer }
   - Output: List<ClientTemporalContract> (統一為 List，sn 查詢返回單元素陣列)
   - 合併的Mapper方法: selectByPrimaryKey, selectByClientSn
   - 注意: sn 和 clientSn 至少傳入一個
   ```

   **不應合併的情況**:
   - 返回類型完全不同（如一個返回 Entity，一個返回 VO）
   - SQL 語句邏輯差異過大（如一個是統計，一個是明細）
   - 業務語義不同（如 selectActive vs selectDeleted）
   - Example 查詢（通常保留獨立方法以保持靈活性）

5. **DTO 結構提取**
   - Input 參數物件結構（含合併 API 的 Query Input）
   - Output 返回物件結構

**輸出**: `_analysis/{MapperName}.json`

```json
{
  "mapper": "StudentMapper",
  "database": "ProductDB",
  "methods": [
    {
      "name": "selectById",
      "sql_type": "SELECT",
      "complexity": "SIMPLE",
      "params": [{ "name": "id", "type": "Long" }],
      "return_type": "Student",
      "input_dto": "StudentIdInput",
      "output_dto": "StudentOutput",
      "description": "根據 ID 查詢單一學生"
    },
    {
      "name": "selectStudentWithCourses",
      "sql_type": "SELECT",
      "complexity": "COMPLEX",
      "params": [
        { "name": "studentId", "type": "Long" },
        { "name": "includeInactive", "type": "Boolean" }
      ],
      "return_type": "List<StudentCourseVO>",
      "input_dto": "StudentCourseQueryInput",
      "output_dto": "StudentCourseOutput",
      "description": "查詢學生及其選課資訊（含多表 JOIN）",
      "needs_detail_file": true
    }
  ]
}
```

---

### Phase 3: Generate Specification Files

**目標**: 產出三個核心檔案 + 複雜方法的獨立檔案

#### 3.1 生成 API-SPEC.md

**表格欄位**:

| Mapper | API名稱 | 連線DB | Input | Output | 合併的Mapper方法 | Desc | Detail |
| ------ | ------- | ------ | ----- | ------ | ---------------- | ---- | ------ |

**說明**:

- **Mapper**: Mapper 類別名稱
- **API名稱**: 語義化的 API 名稱（對於合併的方法，使用 `query{Entity}` 命名）
- **連線DB**: 資料庫連線名稱
- **Input**: Input DTO 名稱（連結到 input.md）
- **Output**: Output DTO 名稱（連結到 output.md）
- **合併的Mapper方法**: 原始 Mapper 方法名稱（多個方法用逗號分隔）
- **Desc**: 簡短描述
- **Detail**: 複雜方法的詳細說明文件連結

**簡單方法範例**（未合併）:

| Mapper        | API名稱    | 連線DB    | Input                             | Output                          | 合併的Mapper方法 | Desc                 | Detail |
| ------------- | ---------- | --------- | --------------------------------- | ------------------------------- | ---------------- | -------------------- | ------ |
| StudentMapper | selectById | ProductDB | [StudentIdInput](#studentidinput) | [StudentOutput](#studentoutput) | selectById       | 根據 ID 查詢單一學生 | -      |

**合併方法範例**（selectAll + selectByX）:

| Mapper                   | API名稱                  | 連線DB   | Input                                                         | Output                                                | 合併的Mapper方法           | Desc                                 | Detail |
| ------------------------ | ------------------------ | -------- | ------------------------------------------------------------- | ----------------------------------------------------- | -------------------------- | ------------------------------------ | ------ |
| ActivityRuleDetailMapper | queryActivityRuleDetails | TutorERP | [ActivityRuleDetailQueryInput](#activityruledetailqueryinput) | [ActivityRuleDetailOutput](#activityruledetailoutput) | selectAll, selectByLevelNo | 查詢活動規則明細（可選擇性過濾等級） | -      |

**複雜方法範例**（連結到獨立檔案）:

| Mapper               | API名稱           | 連線DB   | Input                                             | Output                                    | 合併的Mapper方法  | Desc                   | Detail                           |
| -------------------- | ----------------- | -------- | ------------------------------------------------- | ----------------------------------------- | ----------------- | ---------------------- | -------------------------------- |
| ActivityRecordMapper | updateScholarShip | TutorERP | [UpdateScholarshipInput](#updatescholarshipinput) | [UpdateResultOutput](#updateresultoutput) | updateScholarShip | 批次更新獎學金計算結果 | [查看詳情](updateScholarShip.md) |

#### 3.2 生成 input.md

集中定義所有 Input DTO，提供錨點供 API-SPEC.md 連結

````markdown
# Input DTO 定義

## StudentIdInput

| 欄位 | 類型 | 必填 | 說明    |
| ---- | ---- | ---- | ------- |
| id   | Long | ✓    | 學生 ID |

**JSON 範例**:

```json
{
  "id": 1001
}
```

---

## ActivityRuleDetailQueryInput

查詢活動規則明細的輸入參數（合併 `selectAll` + `selectByLevelNo`）。

| 欄位    | 類型   | 必填 | 說明                       |
| ------- | ------ | ---- | -------------------------- |
| levelNo | String | ✗    | 等級編號（不傳則查詢全部） |

**JSON 範例**:

查詢特定等級：

```json
{
  "levelNo": "L001"
}
```

查詢全部：

```json
{}
```

**Mapper 方法還原**:

- `selectAll()` → 不傳任何參數或傳空物件
- `selectByLevelNo("L001")` → `{"levelNo": "L001"}`

---

## SessionTypeQueryInput

查詢課程類型的輸入參數（合併 `selectAll` + `selectByWebSite` + `selectBySn`）。

| 欄位    | 類型    | 必填 | 說明           |
| ------- | ------- | ---- | -------------- |
| webSite | String  | ✗    | 網站名稱或代碼 |
| sn      | Integer | ✗    | 來源序號       |

**JSON 範例**:

查詢全部：

```json
{}
```

根據網站查詢：

```json
{
  "webSite": "TutorABC"
}
```

根據序號查詢：

```json
{
  "sn": 301
}
```

同時使用兩個條件（AND 查詢）：

```json
{
  "webSite": "TutorABC",
  "sn": 301
}
```

**Mapper 方法還原**:

- `selectAll()` → 不傳任何參數或傳空物件
- `selectByWebSite("TutorABC")` → `{"webSite": "TutorABC"}`
- `selectBySn(301)` → `{"sn": 301}`

**注意**: `webSite` 和 `sn` 可以同時傳入，會進行 AND 條件查詢。

---

## StudentCourseQueryInput

| 欄位            | 類型    | 必填 | 說明                            |
| --------------- | ------- | ---- | ------------------------------- |
| studentId       | Long    | ✓    | 學生 ID                         |
| includeInactive | Boolean | ✗    | 是否包含停用課程（預設: false） |

**JSON 範例**:

```json
{
  "studentId": 1001,
  "includeInactive": false
}
```
````

````

#### 3.3 生成 output.md

集中定義所有 Output DTO，提供錨點供 API-SPEC.md 連結

```markdown
# Output DTO 定義

## StudentOutput

| 欄位 | 類型 | 說明 |
|------|------|------|
| id | Long | 學生 ID |
| name | String | 姓名 |
| grade | String | 年級 |
| email | String | Email |

**JSON 範例**:
```json
{
  "id": 1001,
  "name": "張三",
  "grade": "五年級",
  "email": "zhangsan@example.com"
}
````

## StudentCourseOutput

| 欄位        | 類型                    | 說明     |
| ----------- | ----------------------- | -------- |
| studentId   | Long                    | 學生 ID  |
| studentName | String                  | 學生姓名 |
| courses     | Array&lt;CourseInfo&gt; | 選課清單 |

**JSON 範例**:

```json
{
  "studentId": 1001,
  "studentName": "張三",
  "courses": [
    {
      "courseId": 201,
      "courseName": "數學",
      "status": "active"
    }
  ]
}
```

````

#### 3.4 生成複雜方法獨立檔案

對於複雜方法，產出獨立的 `{api_Name}.md`，檔名使用描述性命名（例如：`selectStudentWithCourses.md`）

```markdown
# selectStudentWithCourses

## 方法資訊

- **Mapper**: StudentMapper
- **方法名稱**: selectStudentWithCourses
- **連線DB**: ProductDB
- **複雜度**: 複雜（多表 JOIN）

## 說明

查詢學生基本資訊及其所有選課記錄，包含課程名稱與狀態。

## Input

參考: [input.md - StudentCourseQueryInput](input.md#studentcoursequeryinput)

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| studentId | Long | ✓ | 學生 ID |
| includeInactive | Boolean | ✗ | 是否包含停用課程（預設: false） |

**範例**:
```json
{
  "studentId": 1001,
  "includeInactive": false
}
````

## Output

參考: [output.md - StudentCourseOutput](output.md#studentcourseoutput)

| 欄位        | 類型                    | 說明     |
| ----------- | ----------------------- | -------- |
| studentId   | Long                    | 學生 ID  |
| studentName | String                  | 學生姓名 |
| courses     | Array&lt;CourseInfo&gt; | 選課清單 |

**範例**:

```json
{
  "studentId": 1001,
  "studentName": "張三",
  "courses": [
    {
      "courseId": 201,
      "courseName": "數學",
      "status": "active"
    },
    {
      "courseId": 202,
      "courseName": "英文",
      "status": "active"
    }
  ]
}
```

## SQL 邏輯

```sql
SELECT
  s.id AS studentId,
  s.name AS studentName,
  c.id AS courseId,
  c.name AS courseName,
  e.status AS status
FROM students s
INNER JOIN enrollments e ON s.id = e.student_id
INNER JOIN courses c ON e.course_id = c.id
WHERE s.id = #{studentId}
  AND (#{includeInactive} = true OR e.status = 'active')
```

## 注意事項

- 使用 INNER JOIN 關聯學生、選課、課程三張表
- `includeInactive` 參數控制是否過濾停用課程
- 返回結果需要在應用層組裝成巢狀結構

`````

---

#### 3.5 真實案例：多 Mapper 方法整合為單一 API

**案例背景**:

在實際專案中，一個業務功能可能會使用到多個 Mapper 方法，並且包含複雜的業務邏輯與外部 API 呼叫。此時應該將這些 Mapper 方法整合為單一 API 規格，並產生獨立的詳細說明檔案。

**範例：updateScholarShip（獎學金計算更新）**

- **來源檔案**: `ScholarshipBulkServiceImpl.java`
- **使用的 Mapper**: `ActivityRecordMapper`
- **Mapper 方法**:
  1. `selectbySn(Integer from, Integer to)` - 查詢獎學金記錄
  2. `updateCalculateResult(List<ActivityRecordEntity> records)` - 批次更新計算結果

雖然使用了 2 個 Mapper 方法，但從 API 角度只需要一個 `updateScholarShip` API。

**API-SPEC.md 中的表格項目**:

| Mapper | Method | 連線DB | Input | Output | Desc | Detail |
|--------|--------|--------|-------|--------|------|--------|
| ActivityRecordMapper | updateScholarShip | TutorERP | [UpdateScholarshipInput](input.md#updatescholarshipinput) | [UpdateScholarshipOutput](output.md#updatescholarshipoutput) | 計算並更新獎學金發放資格 | [詳情](updateScholarShip.md) |

**獨立檔案：updateScholarShip.md**

````markdown
# updateScholarShip

## 方法資訊

- **Mapper**: ActivityRecordMapper
- **使用的 Mapper 方法**:
  - `selectbySn(Integer from, Integer to)` - 查詢獎學金記錄
  - `updateCalculateResult(List<ActivityRecordEntity> records)` - 批次更新計算結果
- **連線DB**: TutorERP
- **複雜度**: 複雜（多 Mapper 整合、外部 API 呼叫、批次處理）

## 說明

根據合約序號範圍，查詢獎學金活動記錄，並透過呼叫外部 API 取得預約記錄，計算學員是否符合獎學金發放資格。

支援兩種計算類型：
- **Type 1**: 根據總點數計算應上課堂數，檢查出席率是否達標（90%）
- **Type 2**: 按月份統計，檢查每月出席次數是否達標（每月至少10堂）

## Input

參考: [input.md - UpdateScholarshipInput](input.md#updatescholarshipinput)

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| fromId | Integer | ✓ | 起始合約序號 |
| toId | Integer | ✓ | 結束合約序號 |
| brandId | Integer | ✓ | 品牌 ID（用於呼叫外部 API） |

**JSON 範例**:
```json
{
  "fromId": 1001,
  "toId": 1100,
  "brandId": 2
}
`````

## Output

參考: [output.md - UpdateScholarshipOutput](output.md#updatescholarshipoutput)

| 欄位    | 類型    | 說明     |
| ------- | ------- | -------- |
| success | Boolean | 執行結果 |
| message | String  | 訊息     |
| data    | Integer | 更新筆數 |

**JSON 範例**:

```json
{
  "success": true,
  "message": "執行成功",
  "data": 95
}
```

## 處理流程

```
1. 查詢獎學金記錄
   └─ Mapper: selectbySn(fromId, toId)

2. 分批處理（每 10 筆一組）
   └─ 使用 Java 8 Stream Partition

3. 平行處理每批資料
   ├─ 呼叫外部 API 取得預約記錄
   │  └─ TutorGroupAPI.postBookingRecordLite()
   │
   ├─ 根據活動類型計算資格
   │  ├─ Type 1: checkResultType1()
   │  │  └─ 計算出席率 = 已出席堂數 / 應上堂數
   │  │  └─ 判斷是否 >= 90%
   │  │
   │  └─ Type 2: checkResultType2()
   │     └─ 按月份統計出席次數
   │     └─ 判斷每月是否 >= 10 堂
   │
   └─ 更新計算結果
      └─ Mapper: updateCalculateResult(records)
```

## 外部 API 呼叫

### TutorGroupAPI - postBookingRecordLite

**用途**: 查詢學員的預約（上課）記錄

**請求參數**:

```json
{
  "clientSn": [1001, 1002, 1003],
  "contractSn": [5001, 5002, 5003],
  "tutorBrand": "2"
}
```

**回應欄位**:

- `contractSn`: 合約序號
- `sessionStartTime`: 課程開始時間
- `status`: 課程狀態（0: 待上課, 1+: 已完成）
- `refundStatus`: 退費狀態（1: 已退費）
- `useSessions`: 使用堂數

## Type 1 計算邏輯

```java
// 計算應上課堂數
shouldRecordSession = (totalPoint / 65) * 9 / 10

// 統計已出席堂數（排除已退費、超過合約結束日期後的課程）
bookedSession = sum(record.useSessions where status > 0 and refundStatus != 1)

// 計算出席率
percent = bookedSession * 100 / shouldRecordSession

// 判斷結果
result = bookedSession >= shouldRecordSession ? "True-100%" : "False-{percent}%"

// 產生說明文字
note = "{result}-應使用:{shouldRecordSession}-已出席:{bookedSession}-歸還:{alreadyRefundSession}"
```

**範例輸出**:

- 符合資格：`"True-100%-應使用 : 90-已出席 : 92-歸還 : 0"`
- 不符資格：`"False-87%-應使用 : 90-已出席 : 78-歸還 : 2"`

## Type 2 計算邏輯

```java
// 將課程按合約開始日期後的天數分配到 6 個月
// 每個月計算出席次數（排除已退費的課程）

for each month (0-5):
  used[month] = count(records where status > 0 and refundStatus != 1)

// 統計不達標的月份
unSatisfiedMonth = count(month where used[month] < 10)
unSatisfiedCount = sum(10 - used[month] where used[month] < 10)

// 判斷結果
result = unSatisfiedMonth == 0 ? "True" : "False-少{unSatisfiedMonth}個月-少{unSatisfiedCount}堂課"

// 產生說明文字
note = "{result}-出席次數 {used[0]} / {used[1]} / {used[2]} / {used[3]} / {used[4]} / {used[5]} /"
```

**範例輸出**:

- 符合資格：`"True-出席次數 12 / 11 / 13 / 10 / 12 / 11 /"`
- 不符資格：`"False-少2個月-少8堂課-出席次數 12 / 8 / 9 / 10 / 12 / 11 /"`

## 批次與平行處理

```java
// 將查詢結果分批（每批 10 筆）
Collection<List<ActivityRecordEntity>> multipleRecords =
    Java8StreamPartition.partition(records, 10);

// 平行處理每批資料
multipleRecords.parallelStream().forEach(batch -> {
    // 1. 呼叫外部 API 取得預約記錄
    List<BookingRecordDto> bookingRecords = getSessions(batch, brandId);

    // 2. 計算每筆記錄的資格
    batch.forEach(record -> {
        // 篩選該合約的預約記錄
        List<BookingRecordDto> subList = bookingRecords.stream()
            .filter(br -> br.getContractSn().equals(record.getContractSn()))
            .collect(Collectors.toList());

        // 根據類型計算
        String note = record.getType() == 1
            ? checkResultType1(record, subList)
            : checkResultType2(record, subList);

        record.setNote(note);
    });

    // 3. 批次更新計算結果
    mapper.updateCalculateResult(batch);
});
```

## 注意事項

### 效能考量

- 使用批次處理（每 10 筆一組）避免單次查詢過多資料
- 使用平行處理（parallelStream）提升處理速度
- 外部 API 呼叫有批次限制（最多 10 筆 clientSn/contractSn）

### 錯誤處理

- 外部 API 呼叫失敗時返回空列表，不中斷整體處理
- 記錄錯誤訊息到 log（`logger.info`）

### 資料一致性

- 每批資料在平行處理中獨立執行
- 每批資料的更新是原子性的（單次 `updateCalculateResult` 呼叫）

### 日期處理

- Type 1: 過濾合約結束日期後的課程
- Type 2: 按合約開始日期後的天數分配到 6 個月區間
  - 0-30 天：第 1 個月
  - 31-61 天：第 2 個月
  - ...以此類推

## 實作 Mapper 方法

### ActivityRecordMapper.java

```java
public interface ActivityRecordMapper {
    // 查詢獎學金記錄（根據序號範圍）
    List<ActivityRecordEntity> selectbySn(
        @Param("from") Integer from,
        @Param("to") Integer to
    );

    // 批次更新計算結果
    Integer updateCalculateResult(List<ActivityRecordEntity> records);
}
```

### XML Mapper

```xml
<!-- 查詢獎學金記錄 -->
<select id="selectbySn" resultType="ActivityRecordEntity">
    SELECT
        sn, client_sn, contract_sn, type,
        total_point, contract_s_date, contract_e_date
    FROM activity_records
    WHERE sn BETWEEN #{from} AND #{to}
    ORDER BY sn
</select>

<!-- 批次更新計算結果 -->
<update id="updateCalculateResult">
    <foreach collection="list" item="item" separator=";">
        UPDATE activity_records
        SET note = #{item.note},
            update_time = NOW()
        WHERE sn = #{item.sn}
    </foreach>
</update>
```

## 總結

這個案例展示了：

1. **多個 Mapper 方法整合為單一 API**
   - `selectbySn` + `updateCalculateResult` → `updateScholarShip` API

2. **複雜業務邏輯需要詳細說明**
   - Type 1 / Type 2 不同的計算邏輯
   - 批次與平行處理流程
   - 外部 API 整合

3. **獨立檔案的價值**
   - 完整的處理流程說明
   - 計算邏輯範例
   - 效能與錯誤處理注意事項
   - 實作程式碼參考

這樣的文件可以幫助開發人員快速理解複雜業務邏輯，並正確實作對應的 API 服務。

````

---

## Skill 執行範例

### 使用者觸發

```

請幫我分析 D:\workspace\oms\OMS_StudyProgress\dal_products\DBMapper
這個路徑的所有 Mapper，並生成 API 規格

```

### Agent 回應

```

Phase 1: 掃描 Mapper 檔案...
發現 15 個 Mapper (8 個 MyBatis, 7 個 JPA Repository)
共 47 個方法

Phase 2: 分析方法...
簡單方法: 32 個（直接填入表格）
複雜方法: 15 個（產生獨立檔案）

Phase 3: 生成規格文件...
✓ API-SPEC.md (主表格，47 個方法)
✓ input.md (28 個 Input DTO 定義)
✓ output.md (22 個 Output DTO 定義)
✓ 15 個複雜方法獨立檔案: - selectStudentWithCourses.md - updateScholarship.md - batchInsertEnrollments.md - calculateGradeStatistics.md - ...

所有檔案已輸出至: history/2026-02-26/

```

---

## 輸出結構

```

mapper-to-api-spec/
├── history/
│ ├── 2026-02-26/
│ │ ├── API-SPEC.md # 🎯 主表格
│ │ ├── input.md # 🎯 所有 Input DTO 定義
│ │ ├── output.md # 🎯 所有 Output DTO 定義
│ │ ├── selectStudentWithCourses.md # 複雜方法 1
│ │ ├── updateScholarship.md # 複雜方法 2
│ │ ├── batchInsertEnrollments.md # 複雜方法 3
│ │ ├── ... # 其他複雜方法
│ │ ├── mapper-inventory.json # Phase 1 產出（內部）
│ │ └── \_analysis/ # Phase 2 產出（內部）
│ │ ├── StudentMapper.json
│ │ ├── CourseMapper.json
│ │ └── ...
│ └── 2026-02-25/
│ └── ...
└── SKILL.md # 本文件

````

### 檔案說明

**三個核心檔案**（使用者主要關注）:

1. **API-SPEC.md**: 主表格，列出所有 Mapper 方法
   - 簡單方法直接在表格中顯示完整資訊
   - 複雜方法在 Detail 欄位連結到獨立檔案

2. **input.md**: 所有 Input DTO 集中定義
   - 提供錨點讓 API-SPEC.md 連結
   - 包含欄位說明、型別、必填資訊、JSON 範例

3. **output.md**: 所有 Output DTO 集中定義
   - 提供錨點讓 API-SPEC.md 連結
   - 包含欄位說明、型別、JSON 範例

**複雜方法獨立檔案**（按需產出）:

- **{api_Name}.md**: 複雜方法的詳細說明
  - 檔名使用描述性命名（如：updateScholarship.md）
  - 包含完整 Input/Output、SQL 邏輯、注意事項

**內部檔案**（分析過程產出，供參考）:

- **mapper-inventory.json**: Mapper 清單
- **\_analysis/\*.json**: 詳細分析資料

---

## 完整範例：API-SPEC.md

```markdown
# API 規格文件

## 文件資訊

- **產生日期**: 2026-02-26
- **來源路徑**: D:\workspace\oms\OMS_StudyProgress\dal_products\DBMapper
- **Mapper 總數**: 15
- **方法總數**: 47

---

## API 列表

| Mapper            | Method                   | 連線DB    | Input                                                       | Output                                               | Desc                 | Detail                              |
| ----------------- | ------------------------ | --------- | ----------------------------------------------------------- | ---------------------------------------------------- | -------------------- | ----------------------------------- |
| StudentMapper     | selectById               | ProductDB | [StudentIdInput](input.md#studentidinput)                   | [StudentOutput](output.md#studentoutput)             | 根據 ID 查詢單一學生 | -                                   |
| StudentMapper     | selectAll                | ProductDB | -                                                           | [StudentOutput](output.md#studentoutput)[]           | 查詢所有學生         | -                                   |
| StudentMapper     | selectByGrade            | ProductDB | [GradeInput](input.md#gradeinput)                           | [StudentOutput](output.md#studentoutput)[]           | 根據年級查詢學生     | -                                   |
| StudentMapper     | selectStudentWithCourses | ProductDB | [StudentCourseQueryInput](input.md#studentcoursequeryinput) | [StudentCourseOutput](output.md#studentcourseoutput) | 查詢學生及選課資訊   | [詳情](selectStudentWithCourses.md) |
| StudentMapper     | insert                   | ProductDB | [CreateStudentInput](input.md#createstudentinput)           | Integer                                              | 新增學生             | -                                   |
| StudentMapper     | update                   | ProductDB | [UpdateStudentInput](input.md#updatestudentinput)           | Integer                                              | 更新學生資訊         | -                                   |
| StudentMapper     | deleteById               | ProductDB | [StudentIdInput](input.md#studentidinput)                   | Integer                                              | 刪除學生             | -                                   |
| CourseMapper      | selectById               | ProductDB | [CourseIdInput](input.md#courseidinput)                     | [CourseOutput](output.md#courseoutput)               | 根據 ID 查詢課程     | -                                   |
| CourseMapper      | selectAll                | ProductDB | -                                                           | [CourseOutput](output.md#courseoutput)[]             | 查詢所有課程         | -                                   |
| EnrollmentMapper  | batchInsert              | ProductDB | [BatchEnrollmentInput](input.md#batchenrollmentinput)       | Integer                                              | 批次新增選課記錄     | [詳情](batchInsertEnrollments.md)   |
| ScholarshipMapper | updateScholarship        | ProductDB | [ScholarshipUpdateInput](input.md#scholarshipupdateinput)   | Integer                                              | 更新獎學金資訊       | [詳情](updateScholarship.md)        |
| StatisticsMapper  | calculateGradeStats      | ProductDB | [GradeStatsQueryInput](input.md#gradestatsqueryinput)       | [GradeStatsOutput](output.md#gradestatsoutput)       | 計算年級統計資料     | [詳情](calculateGradeStatistics.md) |
| ...               | ...                      | ...       | ...                                                         | ...                                                  | ...                  | ...                                 |

---

## 補充說明

- **Input**: 點擊連結查看 [input.md](input.md) 中的完整定義
- **Output**: 點擊連結查看 [output.md](output.md) 中的完整定義
- **Detail**: 複雜方法會產生獨立檔案，點擊連結查看詳細說明
```

---

## 品質標準

### 分析準確性

- 正確解析 Mapper 方法簽章與返回類型
- 準確提取 SQL 語句與邏輯
- 正確判斷複雜度（簡單/複雜）

### 文件完整性

- API-SPEC.md 表格包含所有方法
- input.md / output.md 包含所有 DTO 定義
- 複雜方法獨立檔案包含完整說明（Input/Output/SQL/注意事項）
- 所有連結正確可用

### DTO 定義清晰度

- 欄位名稱、型別、必填資訊完整
- 提供實際 JSON 範例
- 複雜物件有巢狀結構說明

---

## 限制與注意事項

1. **不做 API 命名與 endpoint 設計**
   - 只分析 Mapper 方法，不轉換為 RESTful API
   - 不自動設計 HTTP 方法與路由

2. **複雜度判斷標準**
   - 簡單：單表 CRUD、簡單條件查詢（< 3 個參數）
   - 複雜：多表 JOIN、複雜邏輯、批次操作、統計聚合、動態 SQL

3. **DTO 命名**
   - Input DTO 以方法參數物件為準（若無物件則建立簡單 DTO）
   - Output DTO 以返回類型為準
   - 複雜方法獨立檔案檔名使用描述性命名（如：updateScholarship.md）

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/goodispeter) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
