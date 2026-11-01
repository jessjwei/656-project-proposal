# 656-project-proposal
## 1. StaySmart
 
### Tagline
 
A hotel finder that pulls listings from multiple booking sites and ranks them by how close they are to where you're going and how safe the area is.
 
### Major User Journeys
 
**As a traveler:**
 
1. I land on the home page and enter a destination (e.g., "Yale School of Management, New Haven"), check-in/check-out dates, and a budget.
2. I see a list of hotels and rentals pulled from several sources, each showing price, source site, walking/straight-line distance to my destination, and a neighborhood safety score.
3. I sort or filter by price, distance, or safety, and click a listing to see a detail page with the crime breakdown for that area and a link back to the original booking site.
4. I save a few listings to a shortlist and compare them side by side.
5. I ask, "Which of these is best if I'm walking back late at night?" and get an AI recommendation with reasoning.
**As an admin:**
 
1. I log in to an admin dashboard and see when each data source was last refreshed.
2. I trigger a re-scrape/import for a city and review any listings that failed to parse or geocode.
3. I upload or refresh crime datasets for supported cities.
### Core Features (MVP)
 
1. **Listing aggregation** – a background job imports hotel listings (name, price, address, source URL) from a small number of sources into one `listings` table, de-duplicating the same property across sites.
2. **Distance to destination** – geocode the destination and each listing once, store lat/long in Postgres, and compute straight-line (haversine) distance server-side.
3. **Neighborhood safety score** – import public crime data (e.g., city open-data portals) and compute a per-area score from recent incidents within a radius of each listing.
4. **Search, filter, and compare** – server-rendered results page with sort/filter, plus a shortlist and comparison view for logged-in users.
5. **User accounts** – sign up/log in to save searches and shortlists.
**What we'd skip:** booking/payments, interactive maps, real-time pricing, turn-by-turn directions, and nationwide coverage.
 
### AI Feature
 
The LLM writes a short **"neighborhood brief"** for each listing by summarizing the crime data (types of incidents, time-of-day patterns, trend) in plain language, e.g., *"Mostly property crime, concentrated near the train station after 10pm; violent incidents are rare."* It also powers a **shortlist advisor**: users describe their priorities ("traveling solo, no car, $150/night max") and the LLM ranks their saved options with a short explanation.
 
### Scope & Risks
 
- **Scraping limits:** Airbnb, Expedia, and similar sites restrict scraping in their terms and use heavy anti-bot measures. To stay tractable, the MVP will start with **1–2 cities** and use public/approved data sources (official APIs or sample datasets where available), with scraping as a stretch goal.
- **Crime data coverage:** varies by city, so the MVP will only support cities with open crime datasets (e.g., New Haven, NYC, Chicago).
- **Maps:** we avoid a heavy maps integration by using a free geocoder once per address and computing distance ourselves.
---
 
## 2. Better Yale Course Registration
 
### Tagline
 
A cleaner, friendlier way to find, compare, and plan Yale courses.
 
### Major User Journeys
 
**As a student:**
 
1. I log in and search for courses by keyword, subject, distributional requirement, meeting time, or professor.
2. I click a course to see the description, meeting times, prerequisites, and past evaluation summaries on one page.
3. I add courses to a worksheet and see them on a weekly calendar grid, with time conflicts highlighted.
4. I save multiple draft schedules ("Plan A," "Plan B") and compare them.
5. I ask, "Find me a Wednesday-afternoon QR course that isn't too heavy," and get suggestions.
**As an admin:**
 
1. I log in and import or refresh the semester's course catalog.
2. I review flagged data issues (missing times, broken listings) and fix them.
### Core Features (MVP)
 
1. **Course catalog import** – load course data (title, code, times, instructor, description, distributional tags) into Postgres from public course listings.
2. **Fast search and filters** – keyword search plus filters for subject, time, day, skills/distributionals, and level.
3. **Worksheet + calendar view** – add courses to a schedule rendered as a weekly grid with conflict detection.
4. **Multiple saved schedules** – create, rename, and compare draft schedules.
5. **Accounts** – each student keeps their own worksheets.
**What we'd skip:** actually enrolling students in courses (no integration with the official registrar system), waitlists, and real-time seat counts.
 
### AI Feature
 
A **natural-language course finder**: students describe what they want ("a writing-intensive humanities class, no Friday sections, good for a non-major") and the LLM turns that into structured filters plus a ranked shortlist with a one-line reason for each. The LLM can also **summarize course evaluations** into a quick "what students say" blurb (workload, teaching style, who it's good for).
 
### Scope & Risks
 
- The official registration system can't be modified, so this is a companion planning tool, not a replacement for enrollment.
- Overlaps with existing tools (e.g., CourseTable), so we'd need to differentiate on the AI finder and planning experience.
---
 
## 3. VibeDesk
 
### Tagline
 
An AI support agent that knows your vibe-coded app as well as the person who built it.
 
### Major User Journeys
 
**As an end user of an enterprise app:**
 
1. I click the "Help" widget in the app (or visit the support portal) and ask, "How do I export last month's invoices?"
2. The agent answers with step-by-step instructions grounded in the app's actual docs and code, including which page and button to use.
3. If the agent can't resolve it, I escalate to a human, and a ticket is created with the full conversation attached.
**As a company admin (the team that shipped the app):**
 
1. I register my app and upload its documentation, README, changelog, and selected source files.
2. I view a dashboard of incoming questions, unresolved tickets, and the most common topics users struggle with.
3. I review and correct AI answers; corrections are saved as approved answers the agent reuses.
### Core Features (MVP)
 
1. **Knowledge base ingestion** – admins upload docs/code; the app chunks and stores them in Postgres for retrieval.
2. **Support chat** – a server-rendered chat page where users ask questions and get grounded answers with cited sources.
3. **Escalation + ticketing** – unresolved conversations become tickets that admins can view, assign, and close.
4. **Admin analytics** – counts of questions, resolution rate, and top recurring topics.
5. **Approved answers** – admins edit AI responses and save them as canonical answers.
**What we'd skip:** live human chat, automatically editing or deploying the customer's code, multi-language support, and integrations with tools like Zendesk or Slack.
 
### AI Feature
 
The LLM is the support agent itself. It retrieves relevant docs and code snippets and gives **editor-level help**: explaining what a feature does, where to find it, why something might be failing, and how to work around it, citing the files it used. It also **clusters and summarizes recurring questions** for admins, e.g., *"30% of this week's tickets are about CSV export timing out."*
 
### Scope & Risks
 
- Retrieval quality is the hardest part; the MVP will use simple keyword/full-text search in Postgres (or pgvector as a stretch goal).
- Needs a realistic demo app with docs to ingest; we'd build or borrow a small sample app for testing.
