# LaunchPath

A career guidance web application built for Computer Science and IT students in the Philippines. LaunchPath provides structured career roadmaps, AI-powered job application tools, job match listings, and mentor booking — all in a single, self-contained HTML file with no backend or build tooling required.

---

## Table of Contents!

- [Overview](#overview)
- [Project Structure](#project-structure)
- [Features](#features)
- [Career Paths](#career-paths)
- [Roadmap System](#roadmap-system)
- [Tools and Modals](#tools-and-modals)
- [Job Listings](#job-listings)
- [Mentor Booking](#mentor-booking)
- [Career Quiz](#career-quiz)
- [UI Architecture](#ui-architecture)
- [JavaScript Architecture](#javascript-architecture)
- [Data Structures](#data-structures)
- [Styling System](#styling-system)
- [Responsive Design](#responsive-design)
- [Dependencies](#dependencies)
- [Running the Project](#running-the-project)
- [Customization Guide](#customization-guide)
- [Known Limitations](#known-limitations)

---

## Overview

LaunchPath is delivered as a single HTML file (`index.html`) with all CSS and JavaScript inlined. It is designed to be opened directly in a browser without a server, build step, or package manager. The application targets fresh graduates and final-year students in the Philippines who are navigating their first job search in fields such as software engineering, data analytics, UX design, digital marketing, finance, and healthcare.

The application walks users through five sequential steps: choosing a career path, following a personalized roadmap, using career tools, browsing job matches, and booking a mentor session. A separate career quiz helps undecided users identify their best-fit path.

---

## Project Structure

The entire application lives in a single HTML file. Internally it is organized as follows:

```
index.html
  <style>          CSS custom properties, layout, component styles
  Toast element    Global notification component
  Modal overlays   Six modal dialogs (resume, interview, LinkedIn, cover letter, apply, book mentor)
  <nav>            Fixed navigation bar with mobile hamburger menu
  Mobile menu      Off-canvas navigation for small screens
  Hero section     Landing area with headline, CTAs, and key statistics
  #paths           Career path selection grid
  #roadmap         Dynamic roadmap rendered by JavaScript
  #tools           Six career tool cards
  #jobs            Job match listings with filter buttons
  #mentors         Mentor cards with booking buttons
  #quiz            Four-question career matching quiz
  CTA section      Final conversion call to action
  <footer>         Site footer
  <script>         All application logic
```

---

## Features

### Career Path Selection

Six career paths are available. Clicking a path card marks it as selected and immediately renders a tailored roadmap in the section below. Selection state is tracked in a variable and used to personalize several downstream interactions.

### Personalized Roadmap

Each path has five milestones. Users can mark individual milestones as complete, which updates a progress percentage displayed in the roadmap header and a progress fill bar. Completed steps are stored in memory for the duration of the session. Marking a step done also increments the progress indicator on the corresponding career tool card.

### Resume Builder

A form collects the user's name, chosen career path, experience level, skills, summary, and work or project experience. Submitting the form generates a set of ATS optimization tips rendered inline beneath the form. No file is actually generated; the feedback is text-based coaching tailored to the selected path and experience level.

### Mock Interview Practice

The user selects a target role and interview type (behavioral, technical, or case study). The tool returns three interview questions appropriate to that combination, each accompanied by a structured answer tip and a note about the STAR method. A "New Session" button resets the interface.

### LinkedIn Optimizer

The user inputs their current LinkedIn headline and target career field. The tool returns five suggestions: a critique of the existing headline, a rewritten headline, keywords to add to the About section, profile strength advice, and a connection growth strategy.

### Cover Letter Generator

The user provides their name, the job title, the company name, a paste of the job description, and a summary of their relevant skills. The tool assembles a three-paragraph cover letter and renders it in a scrollable area beneath the form.

### Job Applications

Each job listing has an Apply button. Clicking it opens a modal pre-filled with the job title and company. The user enters their name, email, a portfolio or LinkedIn URL, and a brief fit statement. Submitting shows a success confirmation screen within the same modal.

### Mentor Session Booking

Each mentor card has a Book Session button. Clicking it opens a booking modal pre-filled with the mentor's name and role. The user enters their name, email, preferred date, preferred time slot, and a description of what they need help with. Submitting shows a booking confirmation screen.

### Career Quiz

Four multiple-choice questions assess task preferences, current strengths, job priorities, and comfort with learning software. Each answer is recorded and used to compute a score across four career archetypes. The highest-scoring archetype is displayed as the recommended career match with a supporting description.

### Progress Tracking

Tool cards each show a progress bar that starts at 0% and increments when the user completes roadmap milestones. The mapping is approximate — roadmap step completion nudges the corresponding tool card rather than tracking precise tool usage.

### Toast Notifications

A fixed-position toast component appears at the bottom-right of the screen after user actions such as marking a step done, submitting a form, or triggering a tool. It disappears automatically after 3.2 seconds.

---

## Career Paths

| Path Key | Display Name | Salary Range (PH) | Tag |
|---|---|---|---|
| tech | Tech & Software | 40,000 – 80,000/mo | High demand |
| data | Data & Analytics | 35,000 – 70,000/mo | Fast growing |
| design | Design & UX | 30,000 – 65,000/mo | Creative |
| marketing | Marketing & Media | 25,000 – 55,000/mo | Always needed |
| finance | Finance & Banking | 35,000 – 75,000/mo | Prestigious |
| health | Healthcare | 30,000 – 90,000/mo | Very stable |

---

## Roadmap System

The roadmap data is stored in the `RM` constant object. Each key corresponds to a career path and contains:

- `icon` — An inline SVG string rendered in the roadmap header
- `title` — The displayed roadmap title
- `sub` — A subtitle line showing step count
- `steps` — An array of step objects

Each step object has the following fields:

- `l` — The milestone label (short title)
- `d` — A paragraph describing the milestone in detail
- `sk` — An array of skill tags displayed as pills
- `t` — An estimated time string (e.g., "2–3 months")

### Completion Logic

Step completion is stored in `completedSteps`, an object keyed by path name where each value is a `Set` of completed step indices. This means completion state persists across re-renders of the same path but resets on page reload since there is no localStorage integration.

When a step is marked done, `renderRoadmap` is called to re-render all steps with updated styling, and `updateProgress` recalculates the displayed percentage.

---

## Tools and Modals

Six tool cards are displayed in a grid in the `#tools` section. Each opens a modal, except for Skill Courses and Portfolio Builder which show a toast indicating the feature is coming soon.

| Card | Modal ID | Function |
|---|---|---|
| Resume Builder | resumeModal | `generateResume()` |
| Mock Interviews | interviewModal | `startInterview()` / `resetInterview()` |
| LinkedIn Optimizer | linkedinModal | `optimizeLinkedIn()` |
| Skill Courses | — | Toast only |
| Cover Letter Generator | coverModal | `generateCoverLetter()` |
| Portfolio Builder | — | Toast only |

Modals open via `openModal(id)` and close via `closeModal(id)` or by clicking the overlay background. Body scroll is locked while a modal is open.

---

## Job Listings

Five job listings are hardcoded in the `#jobs` section. Each card has a `data-type` attribute containing one or more space-separated filter values: `entry`, `intern`, or `remote`.

The filter buttons call `filterJobs(btn, type)`, which toggles the active class on the button and sets `display: flex` or `display: none` on each card based on whether its `data-type` includes the selected filter. Selecting "All Roles" shows every card.

Clicking a card or its Apply button opens the apply modal via `openApply(title, co)`, which populates the modal header with the job title and company string.

---

## Mentor Booking

Four mentor profiles are hardcoded in the `#mentors` section. Each has an avatar (rendered as colored initials), name, role, company, skill tags, star rating, session count, and rate.

Clicking "Book Session" calls `openBooking(name, role)`, which pre-fills the booking modal header. After the user fills in the form and clicks Confirm Booking, `submitBooking()` validates that name and email are present, then replaces the form with a success confirmation screen inside the same modal.

---

## Career Quiz

The quiz consists of four steps rendered as `div.qstep` elements, only one of which has the `on` class (and therefore `display: block`) at a time.

Each answer button calls `qa(step, answerIndex)`. This function:

1. Stores the answer index in the `qA` array at the current step index.
2. Visually selects the clicked option.
3. Marks the corresponding progress dot as done.
4. After a 320ms delay, hides the current step and shows the next one, or computes the result if the final step was answered.

Result computation counts how many times each answer index (0–3) appears across all four answers. The index with the highest count maps to one of four career archetypes in the `qMap` array: Tech & Software Engineering, UX/UI Design, Data Analytics, or Digital Marketing. The result is rendered in `div.q-result` which replaces the last question step.

---

## UI Architecture

### Section Flow

The page uses a linear section structure corresponding to the five-step user journey. Each section is labeled with an eyebrow tag ("Step 1" through "Step 5") to guide users through the flow in order.

### Navigation

The fixed navigation highlights the active section by comparing `window.scrollY + 120` against each section's `offsetTop` and `offsetHeight`. On mobile, the navigation collapses to a hamburger button that toggles an off-canvas menu below the nav bar.

### Roadmap Reveal

The roadmap section (`#rmWrap`) has `display: none` by default and gains the `on` class when a path is selected. It then scrolls into view after a 120ms delay to allow the display change to settle before the scroll animation runs.

### Modal Overlay Pattern

Each modal is a fixed overlay with `opacity: 0` and `pointer-events: none` by default. Adding the `open` class transitions opacity to 1 and enables pointer events. Clicking outside the modal box (on the overlay itself) closes it via an event listener that checks `e.target === m`.

---

## JavaScript Architecture

All JavaScript is written as vanilla ES5-compatible code with no modules, classes, or imports. The code is organized into logical sections within a single `<script>` block:

- Roadmap data constant (`RM`)
- State variables (`completedSteps`, `currentPath`)
- Path selection and roadmap rendering
- Progress update utilities
- Job filtering
- Modal open/close utilities
- Apply modal logic
- Booking modal logic
- Resume builder logic
- Mock interview logic (with hardcoded question data in `interviewQs`)
- LinkedIn optimizer logic
- Cover letter generator logic
- Quiz state and logic (with result map in `qMap`)
- Toast notification utility
- Scroll helper
- Navigation scroll and active link detection
- Mobile menu toggle

---

## Data Structures

### RM (Roadmap Data)

```javascript
{
  [pathKey: string]: {
    icon: string,        // Inline SVG HTML string
    title: string,       // Roadmap heading
    sub: string,         // Subtitle
    steps: Array<{
      l: string,         // Milestone label
      d: string,         // Description paragraph
      sk: string[],      // Skill pill labels
      t: string          // Time estimate
    }>
  }
}
```

### completedSteps

```javascript
{
  [pathKey: string]: Set   // Set of completed step indices
}
```

### qA (Quiz Answers)

```javascript
number[]   // Array of 4 answer indices (0–3), one per question
```

### qMap (Quiz Result Map)

```javascript
Array<{
  career: string,   // Career name displayed in result
  icon: string,     // Inline SVG HTML string
  desc: string      // Explanatory paragraph
}>
```

### interviewQs

```javascript
{
  [roleName: string]: {
    behavioral: string[],   // 3 behavioral questions
    technical: string[]     // 3 technical questions
  }
}
```

---

## Styling System

All styles are written as a single embedded `<style>` block using CSS custom properties defined on `:root`.

### Color Tokens

| Variable | Value | Usage |
|---|---|---|
| --bg | #020408 | Page background |
| --bg2 | #060a12 | Alternate section background |
| --bg3 | #0a1020 | Input and code backgrounds |
| --card | #0d1525 | Card backgrounds |
| --card2 | #111c30 | Hovered card backgrounds |
| --border | rgba(59,130,246,0.1) | Default borders |
| --border2 | rgba(59,130,246,0.22) | Emphasized borders |
| --border3 | rgba(59,130,246,0.4) | Focused/active borders |
| --text | #e8edf5 | Primary text |
| --muted | #5a6a82 | Tertiary text |
| --muted2 | #8899b0 | Secondary text |
| --blue | #3b82f6 | Primary brand color |
| --blue2 | #60a5fa | Lighter blue |
| --blue3 | #93c5fd | Lightest blue, used for tinted text |
| --cyan | #06b6d4 | Accent color |

### Typography

Two fonts are loaded from Google Fonts:

- **Cabinet Grotesk** (weights 400, 500, 700, 800, 900) — Used for all headings, numbers, and the logo. Applied via `font-family: 'Cabinet Grotesk', sans-serif`.
- **Instrument Sans** (weights 400, 500, 600 including italic) — Used for all body text, buttons, labels, and navigation. Applied as the default `font-family` on `body`.

### Animations

Two keyframe animations are defined:

- `fadeUp` — Fades an element in from 22px below its final position. Used on hero content and quiz steps.
- `pulse` — Scales and fades the status dot in the hero eyebrow badge.

A `slideUp` animation is applied to modal dialogs when they open.

### Icon Convention

SVG icons are inline within HTML. Icon styling is applied via utility classes:

- `.icon-blue` — Blue stroke, no fill
- `.icon-cyan` — Cyan stroke, no fill
- `.icon-white` — Muted gray stroke, no fill
- `.icon-fill-blue` — Blue fill
- `.icon-fill-cyan` — Cyan fill

All icon strokes use `stroke-width: 1.75`, `stroke-linecap: round`, and `stroke-linejoin: round`.

---

## Responsive Design

A single breakpoint at `max-width: 768px` handles mobile layouts. At this breakpoint:

- The navigation links and ghost button are hidden; the hamburger button is shown.
- The hero and section padding is reduced.
- The paths, tools, and mentors grids collapse to a single column.
- The hero stats wrap and center.
- The quiz box padding is reduced.
- Two-column form rows (`form-row`) collapse to a single column.
- The footer stacks vertically.

---

## Dependencies

All dependencies are loaded from external CDNs. No local installation is required.

| Resource | Source | Purpose |
|---|---|---|
| Cabinet Grotesk | fonts.googleapis.com | Heading typeface |
| Instrument Sans | fonts.googleapis.com | Body typeface |

No JavaScript libraries or frameworks are used. The application is pure HTML, CSS, and vanilla JavaScript.

---

## Running the Project

Because the application has no backend, server, or build requirements, it can be run in any of the following ways:

**Option 1: Open directly in a browser**
Download or save the HTML file and open it with any modern browser (Chrome, Firefox, Safari, Edge). All features function without a server.

**Option 2: Serve with a local HTTP server**
If you prefer to serve it over HTTP (e.g., to test on a mobile device on the same network):

```bash
# Python 3
python -m http.server 8080

# Node.js (with npx)
npx serve .
```

Then navigate to `http://localhost:8080/index.html`.

**Browser compatibility:** The application uses CSS custom properties, `backdrop-filter`, `SVG`, `Set`, and template literals. All of these are supported in any browser released after 2018. Internet Explorer is not supported.

---

## Customization Guide

### Adding a Career Path

1. Add a new card in the `#paths` section with a unique `onclick="selectPath(this,'yourkey')"` attribute.
2. Add a corresponding entry in the `RM` object with `icon`, `title`, `sub`, and `steps` fields.
3. Optionally add interview questions to `interviewQs` and a quiz result to `qMap`.

### Modifying Roadmap Steps

Edit the `steps` array for the relevant path key inside the `RM` constant. Each step requires `l` (label), `d` (description), `sk` (skills array), and `t` (time estimate).

### Adding Job Listings

Add a new `div.job-card` inside `#jobsList`. Set the `data-type` attribute to one or more of `entry`, `intern`, or `remote` (space-separated) so the filter buttons work correctly.

### Adding Mentors

Add a new `div.mentor-card` inside `.mentors-grid`. Call `openBooking('Name', 'Role @ Company')` from the card's Book Session button.

### Persisting Progress Across Sessions

Currently, `completedSteps` and quiz answers are stored only in memory and are lost on page reload. To persist them, replace the `completedSteps` object with `localStorage` reads and writes. The relevant functions are `markDone`, `renderRoadmap`, and `selectPath`.

### Changing Brand Colors

All colors are defined as CSS custom properties on `:root` at the top of the `<style>` block. Changing `--blue` and `--cyan` will propagate through the entire UI including gradients, borders, glows, and icon strokes.

---

## Known Limitations

- **No data persistence.** All state (roadmap progress, quiz answers, form inputs) is lost on page reload. There is no localStorage, IndexedDB, or backend integration.
- **No real AI integration.** The resume feedback, interview questions, LinkedIn suggestions
