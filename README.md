Denarixx WhatsApp AI
Denarixx WhatsApp AI is a production-ready WhatsApp AI Business Bot SaaS platform. It auto-detects the user's language, guides them through smart multi-step conversation flows with full input validation, captures structured leads, bookings, and support tickets, and surfaces everything in a premium gold/black React admin dashboard.

Built for digital agencies, SaaS companies, and any business that wants an always-on WhatsApp assistant.

Features
WhatsApp Webhook — Receives Twilio messages and replies with valid TwiML XML
13-Language Support — Auto-detected from greeting words (EN, DE, FR, ES, IT, PT, RU, TR, AR, NL, PL, ZH, HI); language persists for the full session
Multi-step Conversation Flows — Services, Booking (with callback phone), Pricing, Support (structured ticket), Business Hours
Full Input Validation — Every step validates name, email, phone, date, and time with polite multilingual error messages
Lead Capture — Saves contacts with full details (name, business, email, description, plan, lead type)
Appointment Booking — Collects type, name, date, time, and callback phone; user can type "same" to reuse WhatsApp number
Structured Support Tickets — Collects name, email, and issue description
Session Management — Tracks each user's conversation state in PostgreSQL (jsonb)
AI Fallback — Optional OpenAI fallback for unrecognised messages; returns null gracefully if key not set
Twilio Signature Validation — Enforced in production, skipped automatically in dev
Admin Dashboard — Premium React dashboard: 4 stat cards, sub-stats row, 3-column recent tables, leads/bookings/support pages with export
Tech Stack
Layer	Technology
Runtime	Node.js 24
API Framework	Express 5
Database	PostgreSQL + Drizzle ORM
WhatsApp	Twilio WhatsApp API (TwiML)
AI Fallback	OpenAI gpt-3.5-turbo (optional)
Frontend	React + Vite + Tailwind CSS + Framer Motion
Type Safety	TypeScript + Zod
API Contract	OpenAPI 3.1 + Orval codegen
Package Manager	pnpm workspaces
Project Structure
workspace/
├── artifacts/
│   ├── api-server/                    # Express API + WhatsApp webhook
│   │   └── src/
│   │       ├── config/
│   │       │   └── businessConfig.ts  # All services, plans, booking types (edit to customise)
│   │       ├── routes/
│   │       │   ├── webhook.ts         # POST /api/webhook/whatsapp
│   │       │   └── admin.ts           # GET /api/admin/stats|leads|bookings|support + exports
│   │       └── services/
│   │           ├── botFlowService.ts  # Full multilingual state machine with validation
│   │           ├── i18n.ts            # 13-language translations + greeting detection
│   │           ├── aiService.ts       # Optional OpenAI fallback
│   │           ├── twilioService.ts   # TwiML builder + signature validation
│   │           ├── leadService.ts     # Lead CRUD
│   │           └── bookingService.ts  # Booking CRUD
│   └── denarixx-dashboard/            # React admin dashboard (served at /)
│       └── src/
│           ├── pages/
│           │   ├── dashboard.tsx      # Overview: stats + 3 recent tables
│           │   ├── leads.tsx          # All leads with export
│           │   ├── bookings.tsx       # All bookings + callbackPhone
│           │   └── support.tsx        # Support tickets with email links + export
│           └── hooks/
│               └── use-export.ts      # Download hooks for leads, bookings, support
├── lib/
│   ├── api-spec/openapi.yaml          # OpenAPI 3.1 contract
│   ├── api-client-react/              # Generated React Query hooks (Orval)
│   ├── api-zod/                       # Generated Zod schemas (Orval)
│   └── db/src/schema/
│       ├── leads.ts                   # Leads table (id, phone, fullName, businessName, email, description, leadType, plan)
│       ├── bookings.ts                # Bookings table (id, phone, fullName, bookingType, preferredDate, preferredTime, callbackPhone)
│       └── sessions.ts                # Conversation sessions (phone pk, step, data jsonb)
├── .env.example                       # Copy to .env and fill in values
└── README.md
Environment Variables
Copy .env.example to .env:

# Required
DATABASE_URL=your_postgres_connection_string
PORT=8080
# Twilio (required for production, skipped in dev)
TWILIO_ACCOUNT_SID=your_twilio_account_sid
TWILIO_AUTH_TOKEN=your_twilio_auth_token
TWILIO_WHATSAPP_NUMBER=whatsapp:+14155238886
TWILIO_WEBHOOK_BASE_URL=https://your-domain.com   # Used for signature validation
# Optional — enables AI fallback for unrecognised messages
OPENAI_API_KEY=sk-...
Installation & Local Setup
pnpm install
pnpm --filter @workspace/db run push        # Sync schema to PostgreSQL
pnpm --filter @workspace/api-server run dev  # API on port 8080
pnpm --filter @workspace/denarixx-dashboard run dev  # Dashboard
Running on Replit
Fork or import this project into Replit
The PostgreSQL database is automatically provisioned
Click Run — both workflows start automatically
View the dashboard in the preview pane
Connecting Twilio WhatsApp
Sandbox (Testing)
Go to Twilio Console → Messaging → Try WhatsApp
Follow the sandbox join instructions
Set the When a message comes in webhook to:
https://your-replit-domain/api/webhook/whatsapp
Method: POST | Content-Type: application/x-www-form-urlencoded

Production
Get a WhatsApp-enabled Twilio number
Set the webhook URL and add all Twilio env vars
Twilio signature validation is enforced automatically
Bot Conversation Flows
Send "Hi" (or any greeting in any of the 13 supported languages) to start.

Language Detection
The bot auto-detects language from the opening word:

Language	Trigger words
English	hi, hello, hey, start, menu
German	hallo, guten tag, moin, servus
French	bonjour, salut, bonsoir
Spanish	hola, buenas, buenos días
Italian	ciao, buongiorno, salve
Portuguese	olá, oi, bom dia
Russian	привет, здравствуйте
Turkish	merhaba, selam
Arabic	مرحبا, أهلاً
Dutch	hoi, goedendag
Polish	cześć, dzień dobry
Chinese	你好, 您好
Hindi	नमस्ते, हेलो
Flow 1 — Services
User: 1 → Service menu (Website / AI Automation / WhatsApp Bot / Mobile App)
User: 2 → Selected: AI Automation
Bot:  ← Ask: Full name
Bot:  ← Ask: Business name
Bot:  ← Ask: Email address (validated)
Bot:  ← Ask: Project description
Bot:  ← ✅ Lead saved (leadType=service)
Flow 2 — Booking
User: 2 → Booking type (Demo Call / Consultation / Technical Support)
User: 1 → Selected: Demo Call
Bot:  ← Ask: Full name (validated ≥2 chars)
Bot:  ← Ask: Preferred date
Bot:  ← Ask: Preferred time (validated)
Bot:  ← Ask: Callback phone (type "same" to reuse WhatsApp number)
Bot:  ← ✅ Booking saved with callbackPhone
Flow 3 — Pricing
User: 3 → Plans: Starter €49 / Business €99 / Enterprise €199 / Talk to Sales
User: 2 → Selected: Business (€99/month)
Bot:  ← Ask: Full name
Bot:  ← Ask: Business name
Bot:  ← Ask: Email (validated)
Bot:  ← ✅ Lead saved (leadType=pricing, plan=Business)
Flow 4 — Support
User: 4 → Support intro
Bot:  ← Ask: Full name (validated)
Bot:  ← Ask: Email address (validated)
Bot:  ← Ask: Describe your issue
Bot:  ← ✅ Support ticket saved (leadType=support)
Flow 5 — Business Hours
Mon–Fri: 09:00–18:00
Saturday: 10:00–14:00
Sunday: Closed
Reset / Help
Type "hi", "menu", "start", "reset", "back", or the help word in any language to return to the main menu at any point.

Admin Dashboard
Page	URL	Description
Overview	/	4 stat cards + sub-stats + 3 recent tables + webhook info
Leads	/leads	Full leads table with email, business, plan, description + export
Bookings	/bookings	Full bookings table with callbackPhone + export
Support	/support	All support tickets with email mailto links + export
API Endpoints
Method	Path	Description
POST	/api/webhook/whatsapp	Twilio WhatsApp webhook (TwiML response)
GET	/api/admin/stats	Stats: totalLeads, totalBookings, totalSupport, totalServiceLeads, totalPricingLeads, recentLeads, recentBookings, recentSupport
GET	/api/admin/leads	All leads (all types)
GET	/api/admin/bookings	All bookings
GET	/api/admin/support	Support requests only (leadType=support)
GET	/api/admin/leads/export	Download leads as JSON attachment
GET	/api/admin/bookings/export	Download bookings as JSON attachment
GET	/api/admin/support/export	Download support requests as JSON attachment
GET	/api/health	Health check
Maintenance Commands
# Rebuild TypeScript declarations after schema changes
pnpm --filter @workspace/db run build
pnpm --filter @workspace/api-client-react run build
# Regenerate API client after OpenAPI spec changes
cd lib/api-spec && pnpm run codegen
# Push database schema changes
pnpm --filter @workspace/db run push
Test the Bot Locally
# Start a conversation
curl -X POST http://localhost:8080/api/webhook/whatsapp \
  -d "From=whatsapp:+447700900000&Body=hello"
# Check stats
curl http://localhost:8080/api/admin/stats
Deploying to Production
Click Deploy in Replit to publish. After deploying:

Copy your production domain
Update TWILIO_WEBHOOK_BASE_URL to your production domain
Update the webhook URL in the Twilio console
Twilio signature validation activates automatically
Customising the Bot
Edit artifacts/api-server/src/config/businessConfig.ts to change:

Business name
Service names (with translations for all 13 languages)
Pricing plans and amounts
Booking types (with translations)
Business hours
No changes to bot logic needed.
