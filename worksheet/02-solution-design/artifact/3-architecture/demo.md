---
artifact: 3 — Demo kiến trúc dữ liệu
format: Mermaid diagram + bảng routing
---

# demo.md — Kiến trúc SkyBot v1

Sơ đồ kiến trúc luồng xử lý trọng tâm vào giải pháp escalation failure (T-03).

---

## 1. Main Pipeline — Luồng xử lý chính

```mermaid
flowchart TD
    User([Người dùng\nWebsite / App / Zalo])
    
    subgraph ingress [Ingress Layer]
        Gateway[API Gateway\n+ Rate Limiter]
        AuthCheck{Đã xác thực?}
        SessionCtx[Session Context\nLịch sử 5 tin nhắn gần nhất]
    end

    subgraph classifier [Intent Classifier Layer]
        IC[Intent Classifier\nModel nhỏ - lightweight]
        Router{Nhãn phân loại}
    end

    subgraph llm_path [LLM Path - Xử lý bình thường]
        RAG[RAG Retriever\nPolicy DB]
        LLM[SkyBot LLM\nSystem Prompt v1]
        ResponseGen[Response Generator]
    end

    subgraph escalate_path [Escalation Path]
        HumanQueue[Human Agent Queue]
        AgentDash[Agent Dashboard\nNhân viên CSKH]
        NotifyAgent[Push Notification\ncho nhân viên]
    end

    subgraph ui_signal [UI Signal]
        BannerTrigger[Trigger Banner UI\nLớp 1 - Giao diện]
    end

    subgraph logging [Logging Pipeline]
        EventLog[Event Logger]
        Analytics[Analytics DB\nAnonymized]
        AlertEngine[Alert Engine]
        OpsTeam([Ops Team])
    end

    User --> Gateway
    Gateway --> AuthCheck
    AuthCheck -->|"Chưa xác thực"| SessionCtx
    AuthCheck -->|"Đã xác thực"| SessionCtx
    SessionCtx --> IC

    IC --> Router

    Router -->|"NORMAL"| RAG
    Router -->|"FRUSTRATED"| BannerTrigger
    Router -->|"FRUSTRATED"| RAG
    Router -->|"ESCALATE"| HumanQueue

    BannerTrigger -->|"Kích hoạt banner\ntrên giao diện"| User

    RAG --> LLM
    LLM --> ResponseGen
    ResponseGen --> User

    HumanQueue --> NotifyAgent
    NotifyAgent --> AgentDash
    AgentDash -->|"Nhân viên trả lời"| User

    Router -->|"Ghi log"| EventLog
    EventLog --> Analytics
    Analytics --> AlertEngine
    AlertEngine -->|"Alert khi ESCALATE > threshold"| OpsTeam
```

---

## 2. Intent Classifier — Chi tiết phân loại

```mermaid
flowchart LR
    Input([Tin nhắn người dùng\n+ Lịch sử hội thoại])
    
    subgraph features [Feature Extraction]
        F1[F1: Lặp lại vấn đề\ntrong hội thoại]
        F2[F2: Từ ngữ bức xúc\ntheo từ điển]
        F3[F3: Yêu cầu\nngười thật]
        F4[F4: Tin ngắn\nliên tiếp]
        F5[F5: Hậu quả cá nhân\nnghiêm trọng]
        SPECIAL[Special Rules:\nXe lăn, Y tế\nThai phụ, UM]
    end

    subgraph scoring [Scoring]
        Counter{Đếm tín hiệu\nphát hiện}
    end

    subgraph output [Phân loại]
        NORMAL[NORMAL\n0-1 tín hiệu]
        FRUSTRATED[FRUSTRATED\n2 tín hiệu]
        ESCALATE_F[ESCALATE\n≥3 tín hiệu\nhoặc F3 rõ]
        ESCALATE_S[ESCALATE\nSpecial Rules\nbất kể tín hiệu khác]
    end

    Input --> F1
    Input --> F2
    Input --> F3
    Input --> F4
    Input --> F5
    Input --> SPECIAL

    F1 --> Counter
    F2 --> Counter
    F3 --> Counter
    F4 --> Counter
    F5 --> Counter

    Counter -->|"0-1"| NORMAL
    Counter -->|"2"| FRUSTRATED
    Counter -->|"≥3 hoặc F3 rõ"| ESCALATE_F
    SPECIAL --> ESCALATE_S
```

---

## 3. Human Agent Queue — Luồng nhân viên tiếp nhận

```mermaid
flowchart TD
    EscalateIn([Case ESCALATE\ntừ Classifier])
    
    subgraph queue [Queue Management]
        Enqueue[Enqueue case\nvới priority score]
        PriorityCalc{Priority?\nDựa trên:\n- Số tín hiệu bức xúc\n- Thời gian chờ\n- Loại yêu cầu}
    end

    subgraph agent [Agent Dashboard]
        Dashboard[Agent Dashboard\nDanh sách case]
        ConvContext[Conversation Context\nToàn bộ lịch sử chat\n+ nhãn tín hiệu phát hiện]
        AgentReply[Nhân viên phản hồi\ntrực tiếp qua chat]
    end

    subgraph user_side [Phía người dùng]
        WaitScreen[Màn hình chờ\nMã case + thời gian ước tính]
        ConnectedChat[Chat với nhân viên thật]
    end

    subgraph fallback [Fallback nếu queue đầy]
        QueueFull{Queue > capacity?}
        Hotline[Chuyển sang Hotline\n1900-xxxx]
    end

    EscalateIn --> Enqueue
    Enqueue --> PriorityCalc
    PriorityCalc --> Dashboard
    Dashboard --> ConvContext
    ConvContext --> AgentReply
    AgentReply --> ConnectedChat

    Enqueue --> WaitScreen
    WaitScreen --> ConnectedChat

    Enqueue --> QueueFull
    QueueFull -->|"Có"| Hotline
    QueueFull -->|"Không"| Dashboard
```

---

## 4. Logging & Monitoring Pipeline

```mermaid
flowchart LR
    Events([Events:\nNORMAL / FRUSTRATED / ESCALATE])
    
    subgraph log [Event Logger]
        Anonymize[Anonymize PII\nXóa tên, mã vé, SĐT]
        LogEntry[Log Entry:\n- Timestamp\n- Session ID ẩn danh\n- Intent label\n- Tín hiệu phát hiện\n- Thời gian xử lý]
    end

    subgraph storage [Analytics DB]
        DB[(Analytics DB\nRetention 30 ngày)]
    end

    subgraph monitoring [Monitoring]
        Dashboard2[Ops Dashboard\nReal-time metrics]
        AlertRules{Alert Rules}
        EscalateAlert[Alert:\nESCALATE rate > 15%\ntrong 1h]
        FrustratedAlert[Alert:\nFRUSTRATED rate tăng\n>2x so với baseline]
        OpsTeam2([Ops Team / QA])
    end

    subgraph feedback [Feedback Loop]
        WeeklyReview[Weekly Review\nPattern analysis]
        ClassifierUpdate[Cập nhật\nClassifier thresholds]
        PromptUpdate[Cập nhật\nPrompt rules]
    end

    Events --> Anonymize
    Anonymize --> LogEntry
    LogEntry --> DB
    DB --> Dashboard2
    DB --> AlertRules
    AlertRules --> EscalateAlert
    AlertRules --> FrustratedAlert
    EscalateAlert --> OpsTeam2
    FrustratedAlert --> OpsTeam2
    DB --> WeeklyReview
    WeeklyReview --> ClassifierUpdate
    WeeklyReview --> PromptUpdate
```

---

## 5. Bảng Routing — Tóm tắt

| Nhãn classifier | Điều kiện | LLM xử lý? | UI thay đổi? | Nhân viên? | Log? |
|---|---|---|---|---|---|
| NORMAL | 0-1 tín hiệu | Có | Không | Không | Có (metadata) |
| FRUSTRATED | 2 tín hiệu | Có | Banner cam xuất hiện | Không (chưa) | Có (đầy đủ) |
| ESCALATE | ≥3 tín hiệu / F3 rõ / Special | Không | Banner đỏ + chuyển | Có, ngay lập tức | Có (đầy đủ + priority) |

---

## 6. Data Sources cho RAG (T-07, T-09)

| Nguồn | Loại dữ liệu | Tần suất cập nhật | Dùng cho |
|---|---|---|---|
| Policy DB chính thức | Chính sách vé, hành lý, đổi/hoàn | Cập nhật khi hãng thay đổi chính sách | T-07 (đổi vé), T-08 (Thông tư 14) |
| Flight DB | Trạng thái chuyến bay, lịch bay | Real-time sync từ hệ thống hãng | T-01 (delay), T-04 |
| Special Assistance Rules | Quy định hỗ trợ đặc biệt | Cập nhật theo IATA / Cục HKVN | T-05 (xe lăn), T-09 (trẻ em) |
| Security Rules | Quy định an ninh cabin (chất lỏng, v.v.) | Theo IATA / Bộ GTVT VN | T-07 (hành lý) |

Quy tắc xử lý khi RAG thiếu dữ liệu:
- Nếu Policy DB không có dữ liệu cho câu hỏi → SkyBot KHÔNG tự đoán → trả lời "Tôi chưa có thông tin chính xác về vấn đề này, để tôi kết nối bạn với nhân viên hoặc dẫn bạn đến trang chính sách chính thức."
- Nếu Flight DB offline → không cung cấp thông tin trạng thái chuyến → thông báo và hướng sang hotline

---

## 7. Kiểm tra nhanh

- [x] Sơ đồ cho thấy dữ liệu đi từ User → Classifier → Router → LLM / Human Queue.
- [x] Có bước kiểm tra intent trước khi AI trả lời (Classifier là bước đó).
- [x] Có cách xử lý khi phân loại ESCALATE (Human Agent Queue).
- [x] Có cách biết lỗi có đang lặp lại không (Logging + Alert Engine).
- [x] 3 lớp phối hợp rõ ràng: Classifier → trigger Lớp 1 banner + cho Lớp 2 prompt xử lý.
