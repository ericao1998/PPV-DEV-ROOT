# Codebase Map - Quick Navigation Guide

> AI assistants: Use this map to quickly locate relevant code based on the task domain.

---

## Domain Routing

**When user mentions... → Look here first:**

| Keywords | Domain Folder | Key Files |
|----------|---------------|-----------|
| phone, dial, call, twilio, transfer, voicemail, VM drop | `Phone-Stack/` | PowerDialerController, TwilioPhoneController, embeddedDialer, twilioPhoneStatusBar |
| lead, hot lead, lead queue, disposition | `Leads/` | hotLeads, LeadController, DispositionHandler |
| email, template, send email | `Email-Stack/` | EmailSendQueueable, EmailManualSendFlowAction |
| sms, text, message | `SMS/` | smsMessenger, SMSManualSendFlowAction, SMSScheduledExecutor |
| kpi, dashboard, performance, metrics | `Ops-Performance-Hub/` | KPI components, performance tracking |
| timeline, activity, interaction, history | `Front-End-UI/` | InteractionTimelineController, timeline components |
| merge code, placeholder, template variable | `Merge-Codes/` | Merge code utilities |
| webhook, inbound, external event | Root classes | SmrtPhoneWebhookHandler, CallRecordingHandler |

---

## Prime Salesforce Dev Structure

```
force-app/main/default/
├── classes/
│   ├── Phone-Stack/        ← Twilio, dialer, call handling
│   ├── Leads/              ← Lead management, hot leads
│   ├── Email-Stack/        ← Email sending, templates
│   ├── SMS/                ← SMS automation
│   ├── Ops-Performance-Hub/← KPI, dashboards
│   ├── Front-End-UI/       ← Timeline, UI controllers
│   ├── Merge-Codes/        ← Template placeholders
│   └── [root]              ← Shared utilities, handlers
│
├── lwc/
│   ├── embeddedDialer/     ← PowerDialer UI (can modify)
│   ├── twilioPhoneStatusBar/ ← Phone controls (can modify)
│   ├── hotLeads/           ← FROZEN
│   ├── smsMessenger/       ← FROZEN
│   ├── twilioVoiceContainer/ ← FROZEN
│   └── [other components]
│
├── pages/
│   └── TwilioVoiceBridge.page ← VF bridge for Twilio SDK
│
├── objects/                ← Custom object definitions
├── flows/                  ← Flow definitions
└── triggers/               ← Apex triggers
```

---

## Reusable Patterns (Search These First)

| Pattern | Location | Use For |
|---------|----------|---------|
| **TestUtility** | `classes/TestUtility.cls` | Creating test data |
| **GlobalConstants** | `classes/GlobalConstants.cls` | System-wide constants |
| **RecordInfoFetcher** | `classes/RecordInfoFetcher.cls` | Fetching record data (FROZEN) |
| **DebugConfig** | `lwc/debugConfig/` | Debug logging toggle |
| **BaseController** | Look for `*Controller.cls` | Controller patterns |

**Before creating new code, search:**
```
1. GlobalConstants - for existing constants
2. TestUtility - for test data patterns
3. *Helper.cls - for existing helpers
4. *Service.cls - for service patterns
5. *Handler.cls - for trigger handlers
```

---

## Component Dependencies

### Phone Stack Dependencies
```
embeddedDialer (LWC)
    ↓ uses
TwilioVoiceBridge.page (VF)
    ↓ controlled by
TwilioVFPageController.cls (Apex)
    ↓ calls
TwilioPhoneController.cls (Apex)
    ↓ triggers
PowerDialerController.cls (Apex) [FROZEN]
    ↓ fires
PubSub_Phone_Event__e (Platform Event)
    ↓ listened by
twilioPhoneStatusBar (LWC)
```

### Lead Stack Dependencies
```
hotLeads (LWC) [FROZEN]
    ↓ uses
LeadController.cls / HotLeadsController.cls
    ↓ triggers
DispositionHandler.cls [FROZEN]
    ↓ updates
Lead (standard object)
```

---

## LWC ↔ VF Message Contract (Phone Stack)

**Parent → VF Bridge:**
| Message | Purpose |
|---------|---------|
| `dial` | Initiate outbound call |
| `hangup` / `disconnect` | End call |
| `answer` | Answer incoming |
| `getStatus` | Request current status |

**VF Bridge → Parent:**
| Message | Purpose |
|---------|---------|
| `vf:loaded` | Bridge ready |
| `twilio:ready` | SDK initialized |
| `twilio:ringing` | Call ringing |
| `twilio:connected` | Call connected |
| `twilio:disconnected` | Call ended |
| `twilio:error` | Error occurred |
| `status` | Status update |

---

## Test File Locations

| Domain | Test Location |
|--------|---------------|
| Phone-Stack | `classes/Phone-Stack/*Test.cls` or `classes/*Test.cls` |
| Leads | `classes/Leads/*Test.cls` |
| Email | `classes/Email-Stack/*Test.cls` |
| SMS | `classes/SMS/*Test.cls` |
| General | `classes/TestUtility.cls` + domain tests |

**Naming Convention:** `<ClassName>Test.cls`

---

## Common Gotchas

| Issue | Solution |
|-------|----------|
| PowerDialer session stuck | Check sessionStorage keys, look at `PowerDialerController` (read-only) |
| Phone not dialing | Check VF bridge communication, `TwilioVoiceBridge.page` |
| LWC not updating | Check platform event subscription, `PubSub_Phone_Event__e` |
| Test coverage low | Use `TestUtility` patterns, mock callouts |
| Deployment fails | Check if touching FROZEN component |
| Email not sending | Check `EmailSendQueueable` queue processing |

---

## Azure Portal Dev Structure

```
azure-portal-dev/
├── lead-crawler/           ← Python lead processing
│   ├── normalize/          ← Data normalization (AOAI)
│   ├── sf-sync/            ← Salesforce sync
│   ├── skip-trace/         ← Skip trace processing
│   └── admin-api/          ← Admin API
│
├── PPV_Website/            ← Company website
│   ├── prime-property-web/ ← Next.js frontend
│   ├── ppv-backend/        ← Backend API
│   ├── event-router/       ← Event routing
│   └── kpi-rollup-job/     ← KPI aggregation
│
└── .github/workflows/      ← CI/CD pipelines
```

---

## Quick Search Commands

```bash
# Find all controllers
grep -r "class.*Controller" --include="*.cls"

# Find all LWC components
ls force-app/main/default/lwc/

# Find usages of a class
grep -r "ClassName" --include="*.cls" --include="*.js"

# Find all platform events
grep -r "Platform_Event__e\|__e" --include="*.cls"

# Find all test classes
ls force-app/main/default/classes/*Test.cls
```

---

## Session Context Shortcuts

When starting work, AI should ask:
1. **"Is this Phone, Lead, Email, SMS, or KPI related?"** → Routes to correct domain
2. **"Is this a bug fix or new feature?"** → Determines if frozen check needed
3. **"Does this touch Apex or just LWC?"** → Determines test requirements

---

*Use this map to reduce search time and improve first-attempt accuracy.*
