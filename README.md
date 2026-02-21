# Social Saver

Social Saver is a bot that lets you save links from Instagram, YouTube, Twitter and blogs via WhatsApp, organize them with AI, and access them through a simple dashboanotepad ".\ .gitignore"rd. Send a URL to the Twilio sandbox number and it will scrape the page, classify or summarise it, and store it for you.

> Built for the **Hackathon Challenge – The Social Saver Bot**

[Demo video](https://drive.google.com/file/d/1OLI6xLgVeCM-0Xcjokk_0ZZcJSub_6uH/view)


## Table of contents

- [Features](#features)
- [System Architecture](#system-architecture)
- [Setup](#setup)
- [Usage](#usage)
- [Deployment](#deployment)
- [Tech stack](#tech-stack)
- [Contributing](#contributing)
- [License](#license)

## System Architecture

### 📊 System Flow Diagram
See the complete user journey from sending a WhatsApp message to viewing saved content:

**[View System Flow Diagram →](diagrams/SYSTEM_FLOW.md)**

### 🏗️ Architecture Diagram
Understand how all components interact:

**[View Architecture Diagram →](diagrams/ARCHITECTURE.md)**

## Features

- WhatsApp interface via Twilio sandbox (`whatsapp:+14155238886`)
- Scrapes Instagram, YouTube, Twitter, and blogs
- AI summarization & categorization using Google Generative AI / OpenAI
- Persistent storage with SQLite/PostgreSQL
- Web dashboard for managing saved links
- User authentication and session management

## Setup

### 1. Create a `.env` file

```
SECRET_KEY=your_random_secret_key
OPENAI_API_KEY=your_openai_api_key
TWILIO_ACCOUNT_SID=your_twilio_sid
TWILIO_AUTH_TOKEN=your_twilio_auth_token
TWILIO_WHATSAPP_NUMBER=whatsapp:+14155238886

# PostgreSQL — leave blank to use local SQLite
# DATABASE_URL=postgresql://avnadmin:<password>@<host>:<port>/defaultdb?sslmode=require
```

> **On Render**: add `DATABASE_URL` as an Environment Variable using your Aiven connection string.
> **Locally**: leave it unset — SQLite is used automatically.

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Start the app

```bash
uvicorn app.main:app --reload --port 8000
```

Open [http://localhost:8000](http://localhost:8000)

## Usage

After starting the app you can:

1. Point your browser to `http://localhost:8000` and register/login.
2. Send a link to the WhatsApp sandbox number.
3. View saved links on the dashboard, sorted and summarised by AI.

Example messages:

```
https://www.instagram.com/p/...
https://www.youtube.com/watch?v=dQw4w9WgXcQ
https://twitter.com/...
https://example-blog.com/my-article
```

## Deployment

The app can be deployed to services such as [Render](https://render.com). Add your environment variables (including `DATABASE_URL` for PostgreSQL) via the dashboard and point the web service to `uvicorn app.main:app`. SQLite is used automatically when `DATABASE_URL` is unset.

## Tech stack

See [TECH_STACK.md](TECH_STACK.md) for a full breakdown of libraries and architecture.

## Contributing

Feel free to open issues or submit pull requests. This project is a work in progress; suggestions and improvements are welcome.

## License

This repository is licensed under the [MIT License](LICENSE) — see the `LICENSE` file for details.
