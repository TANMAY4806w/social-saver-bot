# Social Saver Bot - Architecture Diagram

This diagram shows the system architecture with all components and how they interact.

```mermaid
graph LR
    subgraph "messaging"["📱 Messaging Layer"]
        WA["WhatsApp<br/>User"]
        TW["Twilio SDK<br/>Sandbox"]
    end
    
    subgraph "backend"["⚙️ Backend - FastAPI"]
        WEBHOOK["Webhook Handler<br/>/webhook/whatsapp"]
        AUTH["Auth Router<br/>Login/Register"]
        DASH["Dashboard Router<br/>Search/Filter"]
        CHAT["Chat Router<br/>Manual Entry"]
    end
    
    subgraph "scraping"["🔍 Scraping Layer"]
        DETECT["Platform Detector<br/>Instagram/YouTube/Twitter/Blog"]
        IG["Instagram oEmbed<br/>+ Facebook UA"]
        YT["YouTube OG<br/>Metadata"]
        TW2["Twitter oEmbed<br/>+ Facebook UA"]
        BLOG["Blog HTML<br/>Parser"]
    end
    
    subgraph "ai"["🤖 AI Layer"]
        GEMINI["Gemini API<br/>Primary"]
        GROQ["Groq API<br/>Fallback"]
        KEYWORD["Keyword Parser<br/>Backup"]
    end
    
    subgraph "storage"["💾 Database Layer"]
        DB["SQLite/PostgreSQL<br/>Users, Links, Metadata"]
        CACHE["Session Store<br/>MCQ Pending"]
    end
    
    subgraph "frontend"["🎨 Frontend - Web"]
        LOGIN["Login/Register<br/>Pages"]
        DASHBOARD["Dashboard<br/>Cards Grid"]
        SEARCH["Search & Filter<br/>by Tags/Category"]
    end
    
    WA -->|Send Link| TW
    TW -->|HTTP POST| WEBHOOK
    WEBHOOK --> DETECT
    
    DETECT -->|Instagram URL| IG
    DETECT -->|YouTube URL| YT
    DETECT -->|Twitter URL| TW2
    DETECT -->|Blog URL| BLOG
    
    IG --> AI{Scraping<br/>Success?}
    YT --> AI
    TW2 --> AI
    BLOG --> AI
    
    AI -->|Got Text| GEMINI
    AI -->|No Text| MCQ["❓ Send MCQ<br/>Back to User"]
    
    GEMINI -->|Success| STORE["Store Result"]
    GEMINI -->|Fail| GROQ
    GROQ -->|Success| STORE
    GROQ -->|Fail| KEYWORD
    KEYWORD --> STORE
    
    STORE --> DB
    MCQ --> CACHE
    CACHE --> DB
    
    AUTH --> DB
    DASH --> DB
    CHAT --> DB
    
    WEBHOOK -->|Reply| TW
    TW -->|WhatsApp Message| WA
    
    DASHBOARD --> SEARCH
    SEARCH --> DB
    LOGIN --> DASHBOARD
```

## Architecture Layers

### **📱 Messaging Layer**
- WhatsApp integration via Twilio Sandbox
- Webhook endpoint receives and sends messages

### **⚙️ Backend - FastAPI**
- **Webhook Handler**: Processes incoming messages from Twilio
- **Auth Router**: User login/registration with session cookies
- **Dashboard Router**: Search and filter saved links
- **Chat Router**: Manual link submission

### **🔍 Scraping Layer**
- **Platform Detection**: Identifies content source
- **Instagram**: Uses oEmbed API + Facebook's crawler UA
- **YouTube**: Extracts OG metadata (title, description, thumbnail)
- **Twitter/X**: Uses oEmbed API + Facebook crawler UA
- **Blog**: HTML parsing with BeautifulSoup

### **🤖 AI Layer**
- **Primary**: Google Gemini API (gemini-2.0-flash)
- **Secondary**: Groq API with Llama model
- **Tertiary**: Keyword-based fallback parser

### **💾 Database Layer**
- **SQLite** (local development)
- **PostgreSQL** (production via Render)
- Stores: Users, Links, Metadata, Tags
- **Session Store**: In-memory MCQ pending links

### **🎨 Frontend - Web**
- **Login/Register**: User authentication
- **Dashboard**: Card-based layout with filters
- **Search**: Full-text search with tag matching and category filters
