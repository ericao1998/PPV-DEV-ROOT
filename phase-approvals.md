# Phase Approvals

This file is updated by Codex only. CI uses it to validate approval tokens.

Latest Approval Token: APPROVED-POWER-DIALER-PHASE-4-PD4-20-ISO-R1
Date: 2026-03-08
Phase: Power Dialer Phase 4 - isolated PD4-20 follow-up (active-call highlight reconciliation)
Approved By: Codex
Summary: Approved to begin a single-bug, single-file implementation pass per the then-active Phone Stack handoff and edge-case docs (legacy `Architect-Docs` reference): `PD4-20` active on-call phone highlight drift, lookup `PD4-20-ISOLATED-HIGHLIGHT-RECONCILE-R1`. Scope is limited to `embeddedDialer.js` highlight reconciliation only. Token is single-use and must be reissued if scope changes.
Additional Minted Token (Queued): `APPROVED-POWER-DIALER-PHASE-4-R2-CB3-R1` (`R2-CB3-PD4-4-INBOUND-CALLER-ANSWER-R1`) for inbound caller/answer follow-up after `PD4-20`.
Additional Minted Token (Queued): `APPROVED-POWER-DIALER-PHASE-4-R2-CH-R1` (`R2-CH-PD4-25-SESSION-CONTROL-STATE-SYNC-R1`) for session-control state-sync follow-up after `PD4-20` and `R2-CB3`.

## Phase 7 Post-Validation Fixes (2026-01-18)

### Codex Validation Findings Addressed
1. **HIGH - DNC Gate Fix**: Replaced lead-level `DoNotCall` with phone-level gates:
   - `Phone_DNC__c` / `Mobile_DNC__c` fields on Lead
   - `SMS_Conversation__c.Opt_Out_Status__c = 'Opted_Out'` check
   - Per SMS_Consent_Runbook: "DoNotCall is not used; DNC is phone-level only"

2. **MEDIUM - Meta fbc Format Fix**: Added `formatFbcForMeta()` to convert raw FBCLID to:
   - Format: `fb.1.<creation_timestamp>.<fbclid>`
   - Handles pre-formatted values (already starting with `fb.`)

3. **LOW - Unused Field Documented**: `Event_Router_Endpoint__c` removed from SOQL query;
   - Endpoint always uses Named Credential (`callout:Event_Router_API`)

### Files Modified
- `OfflineConversionService.cls`: Phone-level DNC gates, `normalizePhoneForSMS()`, `getOptedOutPhones()`
- `OfflineConversionQueueable.cls`: `formatFbcForMeta()` method
- `OfflineConversionServiceTest.cls`: Added tests for DNC, SMS opt-out, phone normalization, fbc format

### Named/External Credentials
- `Event_Router_API.namedCredential-meta.xml`: URL = `https://event-router.azurecontainerapps.io`
- `Event_Router_API.externalCredential-meta.xml`: Custom auth with `X-API-Key` header
- **Note**: API key must be configured in Salesforce Setup after deployment (not in source control)

### Deployment Steps
1. Deploy: `sf project deploy start --source-dir force-app --test-level RunLocalTests`
2. Configure External Credential Principal in Setup > Named Credentials > Event_Router_API
3. Set API key value (from lead-intake-api `EVENT_ROUTER_API_KEY`)
4. Enable feature: Set `Offline_Conversion_Upload_Config__mdt.Default.Is_Enabled__c = true` when PPC goes live

## Phase 6 Evidence (Implementation Complete 2026-01-12)

### Salesforce Metadata Created
- **Daily_Event_Health__c**: Custom object with 30+ fields for KPI storage
  - External ID: `External_ID__c` (Date__Environment__Service composite key)
  - Traffic fields: Sessions, Leads, Lead Conversion Rate
  - Ad fields: Ad Spend, Clicks, CPL, Paid/Organic split
  - Quality fields: Qualified Lead Rate, Appointment Set Rate, Coverage Rate
  - Health fields: Event Delivery Rate, Event Lag P95, 5xx Error Rate
  - Status: Health_Status__c (Green/Yellow/Red)
- **eventHealthDashboard LWC**: Owner-only dashboard with KPI cards, filters, external links
- **EventHealthDashboardController.cls**: Apex controller for data retrieval
- **Ad_Loop_Health FlexiPage + Tab**: Lightning App Page for dashboard

### Azure Container App Job Created
- **kpi-rollup-job**: Python job aggregating metrics from GA4, Google Ads, Meta, Log Analytics, Salesforce
- Schedule: 06:00 US/Eastern daily (cron: `0 11 * * *` UTC)
- Backfill: Last 7 days, idempotent upsert by External_ID__c
- Kill switch: SYNC_ENABLED env var

### Monitoring & Alerts Created
- **AdLoopHealth.workbook.json**: Azure Workbook with API health, event router delivery, error tracking
- **ad-loop-health-alerts.bicep**: Bicep template for alerts (delivery < 95%, 5xx spike, lag > 60min, job failure)

### Deployment Workflows
- `.github/workflows/deploy-kpi-rollup-job.yml`: CI/CD for job deployment

### Files Created
- `salesforce-metadata/objects/Daily_Event_Health__c/` (object + 30 fields)
- `salesforce-metadata/lwc/eventHealthDashboard/` (HTML, JS, CSS, meta)
- `salesforce-metadata/classes/EventHealthDashboardController.cls`
- `salesforce-metadata/flexipages/Ad_Loop_Health.flexipage-meta.xml`
- `salesforce-metadata/tabs/Ad_Loop_Health.tab-meta.xml`
- `salesforce-metadata/layouts/Daily_Event_Health__c-Daily Event Health Layout.layout-meta.xml`
- `kpi-rollup-job/` (Dockerfile, requirements.txt, app/*.py)
- `workbooks/AdLoopHealth.workbook.json`
- `azure-alerts/ad-loop-health-alerts.bicep`

### Pending Deployment Steps
1. Deploy Salesforce metadata: `sf project deploy start --target-org prod --source-dir salesforce-metadata --test-level RunLocalTests`
2. Configure Key Vault secrets for GA4, Google Ads, Meta credentials
3. Deploy kpi-rollup-job via GitHub Actions (merge to main triggers deploy)
4. Deploy Azure Workbook via Azure Portal (import JSON)
5. Deploy alerts: `az deployment group create -g ppv-web-rg -f azure-alerts/ad-loop-health-alerts.bicep`
6. Verify: Run manual job execution, check Daily_Event_Health__c records, validate dashboard

### URLs (to be populated after deployment)
- Azure Workbook: [TBD after deployment]
- Salesforce Dashboard: [TBD - App Launcher > Ad Loop Health]

### Open Questions Resolved
1. Job Type: Azure Container App Job (not Function)
2. Schedule: 06:00 US/Eastern (11:00 UTC)
3. Environment: Prod only
4. Status Mapping: Lead.Status (Warm/Proposal = Qualified), Opportunity.StageName (Closed Won = Purchase), Last_Disposition__c (Appointment Set = Schedule)

## Approval Log
- APPROVED-PHASE-0 | 2026-01-07 | Phase 0 - Governance Setup | Initial approval gate and runbook governance added.
- APPROVED-PHASE-1 | 2026-01-07 | Phase 1 - Foundation | Phase 1 foundation implementation approved.
- APPROVED-PHASE-2 | 2026-01-08 | Phase 2 - SEO & Content | Phase 2 SEO and content implementation approved.
- APPROVED-PHASE-3 | 2026-01-08 | Phase 3 - Trust & Social Proof | Approved with video testimonials deferred to Phase 4.
- APPROVED-PHASE-1 | 2026-01-09 | Phase 1 - Foundation | Backend lead intake fixes validated; quick + full forms passing.
- APPROVED-PHASE-4 | 2026-01-11 | Phase 4 - Launch Prep | Site live, Core Web Vitals passing, DNS cutover confirmed, testimonial compliance implemented.
- APPROVED-PHASE-5 | 2026-01-11 | Phase 5 - Tracking & Iteration | Event-router live in code: API key enforced, 24h dedup, PII hashing, lead-intake integration, deploy workflow corrected.
- APPROVED-PHASE-6-PLAN | 2026-01-11 | Phase 6 - Tracking & Optimization (Planning Only) | Approval granted for Daily_Event_Health__c schema and GA4/Google Ads/Meta APIs for daily rollups.
- APPROVED-PROPSTREAM-ARCHITECTURE | 2026-01-15 | PropStream Bulk Import - Architecture | Approval granted to proceed with Phase 1.
- APPROVED-PROPSTREAM-PHASE-1.1 | 2026-01-15 | PropStream Bulk Import - Phase 1.1 | Generic CSV support, 5 phone/5 email slotting, strict headers default approved.
- APPROVED-PROPSTREAM-PHASE-2 | 2026-01-15 | PropStream Bulk Import - Phase 2 | BatchData client integration approved.
- APPROVED-PROPSTREAM-PHASE-3 | 2026-01-15 | PropStream Bulk Import - Phase 3 | Salesforce bulk upsert, dedupe, and field mapping approved.
- APPROVED-PROPSTREAM-PHASE-4 | 2026-01-15 | PropStream Bulk Import - Phase 4 | Bulk import endpoint with DNC verification gate approved.
- APPROVED-PROPSTREAM-PHASE-5 | 2026-01-16 | PropStream Bulk Import - Phase 5 | Deployment validated; bulk import test, DNC flags, and Salesforce verification complete.
- APPROVED-PHASE-1 | 2026-01-16 | Phase 1 - Foundation | Privacy policy updated to cover offline conversion attribution and analytics.
- APPROVED-SMS-CONSENT-PHASE-0 | 2026-01-16 | SMS Consent Phase 0 - Prep / Safety | Kill switch + monitoring scaffolding approved.
- APPROVED-SMS-CONSENT-PHASE-1 | 2026-01-16 | SMS Consent Phase 1 - Inbound Keywords + Logging | Inbound keyword handling + consent logging approved.
- APPROVED-SMS-CONSENT-PHASE-2 | 2026-01-16 | SMS Consent Phase 2 - Outbound Gating + Queue | Outbound consent gating + pending queue approved.
- APPROVED-SMS-CONSENT-PHASE-3 | 2026-01-16 | SMS Consent Phase 3 - Ops Tooling | Ops tooling and admin overrides approved.
- APPROVED-SMS-CONSENT-PHASE-4 | 2026-01-16 | SMS Consent Phase 4 - Hardening | Legacy handling and hardening approved.
- APPROVED-SMS-CONSENT-PHASE-4 | 2026-01-20 | SMS Consent Phase 4 - Hardening | Slack daily consent report metrics fix approved.
- APPROVED-SMS-CONSENT-PHASE-2 | 2026-01-20 | SMS Consent Phase 2 - Outbound Gating + Queue | Consent prompt template configuration approved.
- APPROVED-PHASE-7 | 2026-01-17 | Phase 7 - Offline Conversion Sync | Final validation approved.
- APPROVED-PHASE-7-FIXES | 2026-01-18 | Phase 7 - Offline Conversion Sync (Fixes) | Phone-level DNC gate, Meta fbc format, unused field cleanup.
- APPROVED-POWER-DIALER-PHASE-0 | 2026-02-05 | Power Dialer Phase 0 - Safety Infrastructure | Phase 0 prerequisites verified in repo; approved to begin Phase 0 implementation work under plan gates.
- APPROVED-POWER-DIALER-PHASE-0.5 | 2026-02-05 | Power Dialer Phase 0.5 - Observability Wiring | Phase 0 repo verification complete; approved to begin Phase 0.5 observability wiring under plan gates.
- APPROVED-POWER-DIALER-PHASE-1 | 2026-02-05 | Power Dialer Phase 1 - Server-Side Guard Clauses | Approved to implement Phase 1 items 1A-1G under plan gates.
- APPROVED-POWER-DIALER-PHASE-2 | 2026-02-05 | Power Dialer Phase 2 - Server + Non-Frozen LWC | Approved to implement Phase 2 items 2A-2D under plan gates.
- APPROVED-POWER-DIALER-PHASE-3 | 2026-02-06 | Power Dialer Phase 3 - Minimal Frozen Guard Clauses (+ U3) | Approved to begin Phase 3 implementation. Includes mic-permission signaling required to fully address U3 (Twilio 31208/31401), plus minimal frozen guard clauses per plan.
- APPROVED-POWER-DIALER-PHASE-4 | 2026-02-07 | Power Dialer Phase 4 - Inbound Call Routing & PD Interruption (U2/U4/U5) | Approved to begin Phase 4 implementation (4A-4K) per edge-case plan. Must add new feature flags: Inbound_Server_Routing, Voicemail_Transcription_Wiring, Pool_Registration_Gating, PD_Interruption_Resume, Inbound_UX; keep flags OFF by default and enable incrementally after deploy/validation.
- APPROVED-POWER-DIALER-PHASE-4-CLUSTER-C3-R1 | 2026-02-18 | Power Dialer Phase 4 - Cluster C3 (`PD4-8B`) | Scoped approval for Cluster C3 implementation pass: master-tab discoverability/no-hunt UX only in `twilioPhoneStatusBar`; no Apex changes; reissue required on scope change.
- APPROVED-POWER-DIALER-PHASE-4-R2-CA-R1 | 2026-02-18 | Power Dialer Phase 4 - Round 2 Cluster A (`PD4-17`,`PD4-1`,`PD4-2`,`PD4-3`) | Scoped approval for R2 Cluster A implementation pass: lead/phone context integrity + disposition/advance hardening; reissue required on scope change.
- APPROVED-POWER-DIALER-PHASE-4-R2-CB-R1 | 2026-02-18 | Power Dialer Phase 4 - Round 2 Cluster B (`PD4-4`,`PD4-9`,`PD4-11`,`PD4-14`) | Scoped approval for R2 Cluster B implementation pass: inbound answer/state correctness and remote hangup cleanup controls; reissue required on scope change.
- APPROVED-POWER-DIALER-PHASE-4-R2-CC-R1 | 2026-02-18 | Power Dialer Phase 4 - Round 2 Cluster C (`PD4-5`,`PD4-5A`,`PD4-5B`) | Scoped approval for R2 Cluster C implementation pass: inbound notification reliability and click/open behavior hardening; reissue required on scope change.
- APPROVED-POWER-DIALER-PHASE-4-R2-CD-R1 | 2026-02-18 | Power Dialer Phase 4 - Round 2 Cluster D (`PD4-8`,`PD4-8A`) | Scoped approval for R2 Cluster D implementation pass: stabilize single-master inbound ownership and restore pool fallback ringing; reissue required on scope change.
- APPROVED-POWER-DIALER-PHASE-4-R2-CA3-R1 | 2026-02-24 | Power Dialer Phase 4 - Round 2 Cluster A follow-up (`PD4-1`) | Scoped approval for R2-CA3 implementation pass: transition-level disposition suppression hardening for VM-drop/DNC convergence; reissue required on scope change.
- APPROVED-POWER-DIALER-PHASE-4-R2-CA3-R2 | 2026-02-24 | Power Dialer Phase 4 - Round 2 Cluster A follow-up return pass (`PD4-1`) | Scoped approval for R2-CA3 return pass: all-path closure across VM quick, VM preview/fallback, and DNC transitions; reissue required on scope change.
- APPROVED-POWER-DIALER-PHASE-4-R2-CA4-R1 | 2026-02-24 | Power Dialer Phase 4 - Round 2 Cluster A follow-up regression pass (`PD4-2`) | Scoped approval for R2-CA4 implementation pass: fix VM-drop pause/redial regression by preserving attempted-phone identity and enforcing session-safe post-VM progression; reissue required on scope change.
- APPROVED-POWER-DIALER-PHASE-4-R2-CB2-R1 | 2026-02-24 | Power Dialer Phase 4 - Round 2 Cluster B follow-up (`PD4-4`) | Scoped approval for R2-CB2 implementation pass: restore inbound caller visibility and Answer action reliability in PSB under active PD flow; reissue required on scope change.
- APPROVED-POWER-DIALER-PHASE-4-R2-CF2-R1 | 2026-02-26 | Power Dialer Phase 4 - Round 2 Cluster F2 follow-up (`PD4-21`,`PD4-24`) | Scoped approval for R2-CF2 implementation pass: fix unexpected watchdog pause recurrence and prevent pause-induced rewind to previously dialed numbers; reissue required on scope change.
- APPROVED-POWER-DIALER-PHASE-4-R2-CB3-R1 | 2026-02-26 | Power Dialer Phase 4 - Round 2 Cluster B follow-up (`PD4-4`) | Scoped approval for R2-CB3 implementation pass: close remaining inbound caller visibility/answer reliability gaps after R2-CF2 stabilization; reissue required on scope change.
- APPROVED-POWER-DIALER-PHASE-4-PD4-20-ISO-R1 | 2026-03-08 | Power Dialer Phase 4 - isolated follow-up (`PD4-20`) | Scoped approval for isolated PD4-20 implementation pass: enforce single-row active-call highlight integrity in `embeddedDialer.js`; reissue required on scope change.
 

- APPROVED-POWER-DIALER-PHASE-4-R2-CH-R1 | 2026-03-03 | Power Dialer Phase 4 - Round 2 Cluster H follow-up (PD4-25) | Scoped approval for R2-CH implementation pass: fix Start/Pause/End session-control state desync by enforcing canonical session-status signaling across embedded/container paths; reissue required on scope change.


