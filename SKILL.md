---
name: "CHT Specialist"
description: "Expert assistance with Community Health Toolkit (CHT) development, configuration, troubleshooting, and architecture. Use when working with CHT applications, forms (XLSForm/XForm), tasks.js, targets.js, contact-summary, app_settings.json, cht-conf CLI, Docker deployment, workflows, integrations, or any CHT-related questions. Provides guidance on building CHT apps, debugging issues, understanding architecture, and following best practices."
---

# CHT Specialist

## When to Consult Reference Files

**Read the detailed reference files** when the user asks about:

| Topic | Reference File |
|-------|----------------|
| cht-conf CLI commands, options, CSV imports, authentication | [references/cht-conf-reference.md](references/cht-conf-reference.md) |
| Tasks.js schema, events, actions, resolvedIf | [references/tasks-reference.md](references/tasks-reference.md) |
| Targets.js schema, counting modes, groupBy | [references/targets-reference.md](references/targets-reference.md) |
| Contact summary fields, cards, context | [references/contact-summary-reference.md](references/contact-summary-reference.md) |
| XLSForm syntax, CHT widgets, CHT-specific form patterns | [references/forms-reference.md](references/forms-reference.md) |
| app_settings.json configuration | [references/app-settings-reference.md](references/app-settings-reference.md) |

### ODK/XLSForm Documentation

**When working with XLSForm/ODK forms**, consult these files based on the topic:

| User asks about... | Read this file |
|-------------------|----------------|
| Form structure, survey/choices/settings sheets | [references/odk-forms/01-structure.md](references/odk-forms/01-structure.md) |
| Question types (text, select, date, geopoint, image, etc.) or `appearance` options | [references/odk-forms/02-question-types.md](references/odk-forms/02-question-types.md) |
| Skip logic (`relevant`), validation (`constraint`), `calculation`, `default`, `trigger` | [references/odk-forms/03-form-logic.md](references/odk-forms/03-form-logic.md) |
| XPath functions (string, math, date, select, repeat functions) | [references/odk-forms/04-functions.md](references/odk-forms/04-functions.md) |
| Groups, repeats, `field-list`, nested structures, repeat counts | [references/odk-forms/05-groups-repeats.md](references/odk-forms/05-groups-repeats.md) |
| External CSV data, cascading selects, `choice_filter`, `instance()` | [references/odk-forms/06-external-data.md](references/odk-forms/06-external-data.md) |
| Multiple languages, translations, `label::language` columns | [references/odk-forms/07-multilanguage.md](references/odk-forms/07-multilanguage.md) |
| Markdown formatting, grids, themes, `style` column | [references/odk-forms/08-styling.md](references/odk-forms/08-styling.md) |
| ODK Entities, longitudinal data, `entity_create`, `entity_update` | [references/odk-forms/09-entities.md](references/odk-forms/09-entities.md) |
| Audit logging, tracking enumerator behavior, `audit` type | [references/odk-forms/10-audit-logging.md](references/odk-forms/10-audit-logging.md) |
| Form encryption, RSA keys, `public_key` setting | [references/odk-forms/11-encryption.md](references/odk-forms/11-encryption.md) |
| CHT-specific: `db:person`, `contact-summary`, countdown-timer, `cht:` functions | [references/odk-forms/12-cht-extensions.md](references/odk-forms/12-cht-extensions.md) |
| Quick patterns, common calculations (age, BMI, EDD), cheat sheets | [references/odk-forms/13-quick-reference.md](references/odk-forms/13-quick-reference.md) |

**Guidance:**
- For **CHT-specific form features** (contact selector, countdown timer, `cht:` XPath functions): read `12-cht-extensions.md` first
- For **standard ODK/XLSForm syntax**: use the topic-specific files above
- For **quick lookup** of common patterns: start with `13-quick-reference.md`
- When **analyzing or creating a form**: read `01-structure.md` + relevant topic files
| Docker/Kubernetes deployment, monitoring | [references/hosting-reference.md](references/hosting-reference.md) |
| Version compatibility, migrations | [references/version-compatibility.md](references/version-compatibility.md) |
| Database document schemas | [references/database-schema.md](references/database-schema.md) |

---

## Overview

Expert guidance for the Community Health Toolkit (CHT), an open-source framework for building digital health applications in low-resource, offline-first settings. CHT powers applications for community health workers (CHWs), nurses, supervisors, and data managers.

**Key Characteristics:**
- Offline-first architecture using CouchDB/PouchDB
- XLSForm-based care guides and reports
- JavaScript-based task and target configuration
- Role-based permissions and data replication
- SMS workflow support

## Prerequisites

- Node.js 20+ and npm
- Docker and Docker Compose
- cht-conf CLI tool (`npm install -g cht-conf`)

## Quick Start

### Local Development Setup

```bash
# Install cht-conf globally
npm install -g cht-conf

# Initialize a new CHT project
mkdir my-cht-app && cd my-cht-app
cht initialise-project-layout

# Deploy to local instance
cht --url=https://medic:password@localhost --accept-self-signed-certs
```

### Docker CHT Instance

```bash
# Create directory structure
mkdir -p ~/cht/{compose,certs,upgrade-service,couchdb}

# Download compose files (check for latest version)
cd ~/cht/
curl -s -o ./compose/cht-core.yml https://staging.dev.medicmobile.org/_couch/builds_4/medic:medic:4.21.1/docker-compose/cht-core.yml
curl -s -o ./compose/cht-couchdb.yml https://staging.dev.medicmobile.org/_couch/builds_4/medic:medic:4.21.1/docker-compose/cht-couchdb.yml
curl -s -o ./upgrade-service/docker-compose.yml https://raw.githubusercontent.com/medic/cht-upgrade-service/main/docker-compose.yml

# Start CHT
cd ~/cht/upgrade-service
docker compose up --detach
```

---

## Project Structure

```
my-cht-app/
  app_settings.json          # Main app configuration (compiled)
  app_settings/
    base_settings.json       # Base settings
    forms.json               # Form settings
    schedules.json           # SMS schedules
  contact-summary.templated.js  # Contact profile configuration
  tasks.js                   # Task definitions
  targets.js                 # Target/goal definitions
  purge.js                   # Data purging rules
  resources.json             # Icon and resource mappings
  resources/                 # Icons and media
  forms/
    app/                     # App forms (care guides)
      form_name.xlsx
      form_name.properties.json
      form_name-media/
    contact/                 # Contact creation forms
  translations/
    messages-en.properties
    messages-fr.properties
```

---

## Tasks Configuration

Tasks are reminders or follow-ups that appear in the Tasks tab. See [references/tasks-reference.md](references/tasks-reference.md) for complete schema.

### Task Schema Overview

| Property | Type | Description | Required |
|----------|------|-------------|----------|
| `name` | string | Unique task identifier | Yes |
| `icon` | string | Icon from resources.json | No |
| `title` | translation key | Task title | Yes |
| `appliesTo` | 'contacts' or 'reports' | One task per contact or report | Yes |
| `appliesToType` | string[] | Filter contact types or form codes | No |
| `appliesIf` | function(contact, report) | Return true to show task | No |
| `events` | object[] | Timing configuration | Yes |
| `events[n].days` | integer | Days after reported_date | Yes* |
| `events[n].dueDate` | function | Custom due date calculation | Yes* |
| `events[n].start` | integer | Days before due to show | Yes |
| `events[n].end` | integer | Days after due to show | Yes |
| `actions` | object[] | Forms to open | Yes |
| `resolvedIf` | function | Return true to resolve | No |
| `priority` | object or function | High risk indicator (4.21.0+) | No |
| `priority.level` | integer | Positive number for urgency (higher = top) | No |
| `priority.label` | translation key | Label for priority indicator | No |

### Basic Task Example

```javascript
// tasks.js
module.exports = [
  {
    name: 'postnatal-visit',
    icon: 'mother-child',
    title: 'task.postnatal_followup',
    appliesTo: 'reports',
    appliesToType: ['delivery'],
    actions: [{ form: 'postnatal_visit' }],
    events: [
      { id: 'pnc-1', days: 7, start: 2, end: 2 },
      { id: 'pnc-2', days: 14, start: 2, end: 2 }
    ],
    resolvedIf: function(contact, report, event, dueDate) {
      return Utils.isFormSubmittedInWindow(
        contact.reports,
        'postnatal_visit',
        Utils.addDate(dueDate, -event.start).getTime(),
        Utils.addDate(dueDate, event.end + 1).getTime()
      );
    }
  }
];
```

### Understanding appliesTo

**`appliesTo: 'contacts'`**: One task per contact
- `appliesToType` filters by contact's `contact_type` value
- `appliesIf(c)` receives contact info and reports array
- Due date defaults to contact's creation date

**`appliesTo: 'reports'`**: One task per report
- `appliesToType` filters by report's `form` value
- `appliesIf(c, report)` receives contact info and specific report
- Due date defaults to report's `reported_date`
- Can result in multiple overlapping task schedules

### Task Actions

Actions can open forms or navigate to contacts:

```javascript
actions: [{
  type: 'report',  // or 'contact' (4.21.0+)
  form: 'postnatal_visit',
  label: 'task.action.pnc',
  modifyContent: function(content, contact, report, event) {
    content.delivery_date = report.fields.delivery_date;
    content.followup_number = event.id.split('-')[1];
  }
}]
```

### Contact Form Actions (4.21.0+)

```javascript
// Create new contact
actions: [{
  type: 'contact',
  modifyContent: (content, { contact }) => {
    content.type = 'person';
    content.parent_id = contact._id;
  }
}]

// Edit existing contact
actions: [{
  type: 'contact',
  modifyContent: (content, { contact }) => {
    content.edit_id = contact._id;
  }
}]
```

---

## Targets Configuration

Targets show key performance indicators on the Analytics tab. See [references/targets-reference.md](references/targets-reference.md) for complete schema.

### Target Schema Overview

| Property | Type | Description | Required |
|----------|------|-------------|----------|
| `id` | string | Unique identifier | Yes |
| `type` | 'count' or 'percent' | Widget type | Yes |
| `icon` | string | Icon from resources.json | No |
| `goal` | integer | Target goal (-1 for none) | Yes |
| `translation_key` | string | Title translation | Yes |
| `subtitle_translation_key` | string | Subtitle translation | No |
| `appliesTo` | 'contacts' or 'reports' | What to count | Yes |
| `appliesToType` | string[] | Filter types | No |
| `appliesIf` | function | Filter function | No |
| `passesIf` | function | For percent: numerator | Yes* |
| `date` | 'reported', 'now', or function | Time filtering | No |
| `idType` | 'report', 'contact', or function | Counting mode | No |
| `groupBy` | function | Aggregate in groups | No |
| `passesIfGroupCount` | object | Group passing criteria | Yes if groupBy |
| `visible` | boolean | Show on targets page (default: true) | No |
| `aggregate` | boolean | Show on TargetAggregates (3.9+) | No |

### Target Types

**Count Widget**: Displays numeric sum
- Without goal: Simple green number
- With goal: Shows progress toward goal

**Percent Widget**: Displays ratio
- Shows numerator/denominator
- Green if goal met, black otherwise

### Date Filter Options

| Value | Behavior |
|-------|----------|
| `'reported'` | Current month only (resets monthly) |
| `'now'` | All time (cumulative) |
| `function(contact, report)` | Custom time window |

### Target Examples

```javascript
// targets.js
module.exports = [
  // Count - This Month
  {
    id: 'births-this-month',
    type: 'count',
    icon: 'infant',
    goal: -1,
    translation_key: 'targets.births.title',
    subtitle_translation_key: 'targets.this_month.subtitle',
    appliesTo: 'reports',
    appliesToType: ['delivery'],
    date: 'reported'
  },

  // Percentage with Goal
  {
    id: 'deliveries-with-visit',
    type: 'percent',
    icon: 'nurse',
    goal: 100,
    translation_key: 'targets.delivery_visit.title',
    appliesTo: 'reports',
    appliesToType: ['delivery'],
    passesIf: function(contact, report) {
      return contact.reports.some(r =>
        r.form === 'anc_visit' &&
        r.reported_date < report.reported_date
      );
    }
  },

  // Count unique contacts
  {
    id: 'patients-with-cough',
    type: 'count',
    icon: 'cough',
    goal: -1,
    translation_key: 'targets.cough.title',
    appliesTo: 'reports',
    appliesToType: ['assessment'],
    appliesIf: function(contact, report) {
      return Utils.getField(report, 'symptoms.cough') === 'yes';
    },
    idType: 'contact',  // Count unique contacts, not reports
    date: 'reported'
  }
];
```

---

## Contact Summary Configuration

Customize contact profile pages with fields and condition cards. See [references/contact-summary-reference.md](references/contact-summary-reference.md).

### Available Variables

| Variable | Description |
|----------|-------------|
| `contact` | Currently selected contact (minimal parent stubs) |
| `reports` | Array of reports (max 500, ordered by reported_date) |
| `lineage` | Array of parents: `lineage[0]` = parent, `lineage[1]` = grandparent |
| `targetDoc` | Target document with config info (3.9.0+) |
| `uhcStats` | UHC statistics object (3.12.0+) |
| `cht` | CHT API object (3.12.0+) |

### Fields Schema

| Property | Type | Description | Required |
|----------|------|-------------|----------|
| `label` | string | Translation key | Yes |
| `value` | string | Value to display | Yes |
| `filter` | string | Display filter (age, phone, relativeDate, etc.) | No |
| `width` | integer | 12=full, 6=half, 3=quarter (default: 12) | No |
| `translate` | boolean | Translate the value (default: false) | No |
| `icon` | string | Icon name | No |
| `appliesToType` | string[] | Filter contact types | No |
| `appliesIf` | function() | Return true to show | No |

### Available Filters

| Filter | Description |
|--------|-------------|
| `age` | Formats date as age |
| `phone` | Formats phone number |
| `weeksPregnant` | Weeks of pregnancy |
| `relativeDate` | "3 days ago" |
| `relativeDay` | Relative day display |
| `simpleDate` | Simple date format |
| `lineage` | Displays hierarchy |

### Example Configuration

```javascript
// contact-summary.templated.js
module.exports = {
  context: {
    pregnant: isPregnant(contact, reports),
    high_risk: isHighRisk(contact, reports)
  },

  fields: [
    {
      appliesToType: 'person',
      label: 'patient_id',
      value: contact.patient_id,
      width: 4
    },
    {
      appliesToType: 'person',
      label: 'contact.age',
      value: contact.date_of_birth,
      filter: 'age',
      width: 4
    }
  ],

  cards: [
    {
      label: 'contact.profile.pregnancy',
      appliesToType: 'report',
      appliesIf: (report) => report.form === 'pregnancy' && !report.fields.delivered,
      fields: [
        {
          label: 'contact.profile.edd',
          value: (report) => report.fields.edd,
          filter: 'relativeDay'
        },
        {
          label: 'contact.profile.risk',
          value: (report) => report.fields.risk_level,
          icon: (report) => report.fields.risk_level === 'high' ? 'risk' : ''
        }
      ],
      modifyContext: function(ctx) {
        ctx.pregnant = true;
      }
    }
  ]
};
```

### Accessing Context in Forms

In XForm calculations:
```xpath
instance('contact-summary')/context/pregnant
```

User's contact summary (4.21.0+):
```xpath
instance('user-contact-summary')/context/variable_name
```

---

## Forms (XLSForm)

CHT uses XLSForm format converted to ODK XForm. See [references/forms-reference.md](references/forms-reference.md).

### Form File Structure

| File | Description |
|------|-------------|
| `forms/app/{name}.xlsx` | XLSForm definition |
| `forms/app/{name}.xml` | Generated XForm |
| `forms/app/{name}.properties.json` | Form properties |
| `forms/app/{name}-media/` | Media files |
| `forms/contact/{type}-create.xlsx` | Contact creation form |
| `forms/contact/{type}-edit.xlsx` | Contact edit form |

### Standard App Form Structure

```
| type        | name         | label         | relevant          | appearance       | calculate           |
|-------------|--------------|---------------|-------------------|------------------|---------------------|
| begin group | inputs       | Inputs        | ./source = 'user' | field-list       |                     |
| hidden      | source       |               |                   |                  |                     |
| begin group | contact      |               |                   |                  |                     |
| string      | _id          | Patient ID    |                   | select-contact type-person |             |
| string      | name         | Patient Name  |                   | hidden           |                     |
| end group   |              |               |                   |                  |                     |
| end group   |              |               |                   |                  |                     |
| calculate   | patient_id   |               |                   |                  | ../inputs/contact/_id |
| ...         |              |               |                   |                  |                     |
| begin group | group_summary| Summary       |                   | field-list summary |                   |
| note        | r_patient    | **${name}**   |                   |                  |                     |
| end group   |              |               |                   |                  |                     |
```

### CHT XPath Functions

| Function | Description | Version |
|----------|-------------|---------|
| `add-date(date, years, months, days, hours, minutes)` | Add time to date | 4.0.0+ |
| `cht:difference-in-days(date1, date2)` | Whole days between dates | 4.7.0+ |
| `cht:difference-in-weeks(date1, date2)` | Whole weeks between dates | 4.7.0+ |
| `cht:difference-in-months(date1, date2)` | Whole months between dates | All |
| `cht:difference-in-years(date1, date2)` | Whole years between dates | 4.7.0+ |
| `cht:extension-lib(name, ...params)` | Call extension library | 4.2.0+ |
| `cht:strip-whitespace(string)` | Remove whitespace | 4.10.0+ |
| `cht:validate-luhn(number, length)` | Luhn validation | 4.10.0+ |
| `z-score(table, sex, age, measurement)` | Z-score calculation | All |

### CHT Widgets

**Contact Selector:**
```
| type   | appearance                 |
|--------|----------------------------|
| string | select-contact type-person |
```

**Countdown Timer (4.7.0+):**
```
| type    | appearance      | instance::cht:duration |
|---------|-----------------|------------------------|
| trigger | countdown-timer | 30                     |
```

**Phone Validation (4.11.0+):**
```
| type   | appearance  | instance::cht:unique_tel |
|--------|-------------|--------------------------|
| string | numbers tel | true                     |
```

### Form Properties File

```json
{
  "title": [
    { "locale": "en", "content": "Pregnancy Registration" }
  ],
  "icon": "pregnancy-1",
  "hidden_fields": ["internal_calc"],
  "context": {
    "person": true,
    "place": false,
    "expression": "contact.type === 'person' && (!contact.sex || contact.sex === 'female')",
    "permission": "can_register_pregnancies"
  }
}
```

### Expression Functions (in properties.json)

| Function | Description |
|----------|-------------|
| `ageInDays(contact)` | Contact's age in days |
| `ageInMonths(contact)` | Contact's age in months |
| `ageInYears(contact)` | Contact's age in years |

---

## Utils Functions

Available in tasks.js and targets.js:

| Function | Description |
|----------|-------------|
| `Utils.addDate(date, days)` | Add days to date |
| `Utils.isFormSubmittedInWindow(reports, form, start, end)` | Check form in time window |
| `Utils.getMostRecentReport(reports, form)` | Get latest report |
| `Utils.getMostRecentTimestamp(reports, form)` | Get latest reported_date |
| `Utils.getField(report, fieldPath)` | Get nested field value |
| `Utils.isTimely(date, event)` | Check if date in event window |
| `Utils.getLmpDate(doc)` | Extract LMP date |
| `Utils.isDateValid(date)` | Validate date object |
| `Utils.now()` | Current date |
| `Utils.MS_IN_DAY` | Milliseconds in a day (86400000) |

---

## CHT API (3.12.0+)

Available in tasks.js, targets.js, and contact-summary.templated.js as the `cht` variable:

```javascript
// Check permissions
const canEdit = cht.v1.hasPermissions('can_edit');
const canManage = cht.v1.hasPermissions(['can_create_places', 'can_update_places']);

// Check any permission group
const hasAny = cht.v1.hasAnyPermission([
    ['can_view_messages', 'can_view_message_action'],
    ['can_view_reports', 'can_verify_reports']
]);

// Use extension library
const result = cht.v1.getExtensionLib('mylib.js')(param1, param2);

// Get target docs (4.11.0+)
const targetDocs = cht.v1.analytics.getTargetDocs();
```

---

## Database Schema

See [references/database-schema.md](references/database-schema.md) for complete schema.

### Document Types

| Type | Description |
|------|-------------|
| `data_record` | Reports/forms submitted |
| `person` | Person contacts |
| `clinic`, `health_center`, `district_hospital` | Place contacts |
| `contact` (3.7+) | Configurable contact type |
| `task` | Generated task documents |
| `target` | Target calculations |

### Report Structure

```json
{
  "_id": "report-uuid",
  "type": "data_record",
  "form": "pregnancy",
  "reported_date": 1234567890000,
  "contact": { "_id": "submitter-id", "parent": {...} },
  "fields": {
    "patient_id": "12345",
    "edd": "2024-06-15",
    "risk_factors": ["anemia"]
  }
}
```

### Contact Structure

```json
{
  "_id": "contact-uuid",
  "type": "person",
  "name": "Jane Doe",
  "date_of_birth": "1990-01-15",
  "sex": "female",
  "patient_id": "12345",
  "parent": {
    "_id": "household-id",
    "parent": {
      "_id": "chw-area-id"
    }
  }
}
```

### Task Document Structure

```json
{
  "_id": "task~org.couchdb.user:username~report-id~task-name~1~timestamp",
  "type": "task",
  "user": "org.couchdb.user:username",
  "requester": "contact-that-triggered-task",
  "owner": "contact-that-displays-task",
  "forId": "contact-passed-to-form",
  "state": "Ready",
  "emission": {
    "_id": "task-emission-id",
    "dueDate": "2024-01-15",
    "startDate": "2024-01-12",
    "endDate": "2024-01-22"
  },
  "stateHistory": [...]
}
```

### Task States

| State | Description |
|-------|-------------|
| Draft | Scheduled in future |
| Ready | Currently visible |
| Completed | resolvedIf returned true |
| Failed | Window passed without completion |
| Cancelled | appliesIf returned false |

---

## cht-conf CLI Commands

**For detailed cht-conf documentation**, see [references/cht-conf-reference.md](references/cht-conf-reference.md) which covers:
- All 42 CLI actions with descriptions
- Form conversion, validation, and upload options
- CSV data import with column type annotations
- Authentication methods (local, instance, URL, session token)
- Contact hierarchy management (move, merge, delete, edit)
- Configuration inheritance and watch mode
- Error troubleshooting

### Common Commands

```bash
# Initialize project
cht initialise-project-layout

# Compile and upload all settings
cht --local compile-app-settings backup-app-settings upload-app-settings

# Convert and upload all app forms
cht --local convert-app-forms upload-app-forms

# Convert and upload specific app forms
cht --local convert-app-forms upload-app-forms -- form1 form2

# Convert and upload all contact forms
cht --local convert-contact-forms upload-contact-forms

# Convert and upload specific contact forms
cht --local convert-contact-forms upload-contact-forms -- form1 form2

# Upload resources
cht --local upload-resources

# Upload translations
cht --local upload-custom-translations

# Convert CSV to docs and upload
cht --local csv-to-docs upload-docs

# Move contacts
cht --local move-contacts -- --contacts=<id> --parent=<id>

# Edit contacts from CSV
cht --local edit-contacts -- --columns=field_name --files=contacts.csv
```

### Connection Options

```bash
# Local with self-signed certs
cht --url=https://medic:password@localhost --accept-self-signed-certs

# Remote instance
cht --url=https://user:pass@your-instance.app.medicmobile.org

# Using instance shorthand
cht --instance=your-instance

# Skip server checks (offline compilation)
cht --no-check compile-app-settings
```

---

## Troubleshooting

### Common Issues

**Task not appearing:**
1. Check `appliesTo` and `appliesToType` match your data
2. Verify `appliesIf` returns true for your scenario
3. Check event timing (`days`, `start`, `end`)
4. Tasks only show for restricted users

**Target not counting correctly:**
1. Verify `appliesIf` logic
2. Check `date` filter ('reported' vs 'now')
3. For percent, check `passesIf` logic
4. Verify `appliesToType` matches form codes

**Form not showing:**
1. Check `context.expression` in properties.json
2. Verify user has required permissions
3. Check form was uploaded successfully

**Replication issues:**
1. Check user permissions and place assignment
2. Verify contact hierarchy
3. Check for document conflicts

### Debug Commands

```bash
# Check form conversion
cht --local convert-app-forms

# Validate app settings
cht --local compile-app-settings

# Check instance connection
cht --url=https://... fetch-forms-from-server
```

---

## Best Practices

### Forms
- Keep titles under 40 characters
- Use Title Case for titles
- Don't include patient name in form title
- Stack radio buttons vertically
- Use images to aid understanding
- Group related questions logically

### Tasks
- Limit task generation to near future (60 days past, 180 days future)
- Use unique task names
- Provide clear resolution conditions
- Consider performance with many contacts
- Use `appliesIf` to filter early for better performance

### Targets
- Use sentence case for titles
- Keep titles under 40-50 characters
- Choose appropriate date filter ('reported' vs 'now')
- Set meaningful goals
- Use `idType: 'contact'` to avoid double-counting

### Contact Summary
- Group related information in cards
- Only show actionable information
- Use icons for important statuses
- Keep field count reasonable for mobile

---

## Version Compatibility

| Feature | Minimum Version |
|---------|-----------------|
| Configurable contact types | 3.7.0 |
| CHT API (cht.v1) | 3.12.0 |
| Task priority scoring | 4.21.0 |
| Contact form task actions | 4.21.0 |
| `trigger` countdown timer | 4.7.0 |
| `string` tel input | 4.11.0 |
| `cht:difference-in-*` functions | 4.7.0 |
| `add-date` function | 4.0.0 |
| `userSummary` in expressions | 4.21.0 |
| `duplicate_check` in contact forms | 4.19.0 |

---

## Resources

### Reference Documentation

**Core Configuration:**
- [references/tasks-reference.md](references/tasks-reference.md) - Complete tasks.js schema
- [references/targets-reference.md](references/targets-reference.md) - Complete targets.js schema
- [references/contact-summary-reference.md](references/contact-summary-reference.md) - Contact summary schema
- [references/forms-reference.md](references/forms-reference.md) - XLSForm and forms guide
- [references/app-settings-reference.md](references/app-settings-reference.md) - App settings reference

**ODK/XLSForm Documentation:**
- [references/odk-forms/README.md](references/odk-forms/README.md) - Overview and navigation
- [references/odk-forms/01-structure.md](references/odk-forms/01-structure.md) - Form structure (survey, choices, settings sheets)
- [references/odk-forms/02-question-types.md](references/odk-forms/02-question-types.md) - All question types and appearances
- [references/odk-forms/03-form-logic.md](references/odk-forms/03-form-logic.md) - Relevance, constraints, calculations
- [references/odk-forms/04-functions.md](references/odk-forms/04-functions.md) - XPath functions reference
- [references/odk-forms/05-groups-repeats.md](references/odk-forms/05-groups-repeats.md) - Groups, repeats, nesting
- [references/odk-forms/06-external-data.md](references/odk-forms/06-external-data.md) - External datasets, cascading selects
- [references/odk-forms/07-multilanguage.md](references/odk-forms/07-multilanguage.md) - Translations and localization
- [references/odk-forms/08-styling.md](references/odk-forms/08-styling.md) - Markdown, grids, theming
- [references/odk-forms/09-entities.md](references/odk-forms/09-entities.md) - ODK Entities for longitudinal data
- [references/odk-forms/10-audit-logging.md](references/odk-forms/10-audit-logging.md) - Enumerator behavior tracking
- [references/odk-forms/11-encryption.md](references/odk-forms/11-encryption.md) - Form encryption setup
- [references/odk-forms/12-cht-extensions.md](references/odk-forms/12-cht-extensions.md) - CHT-specific XPath, widgets
- [references/odk-forms/13-quick-reference.md](references/odk-forms/13-quick-reference.md) - Cheat sheets and patterns

**Infrastructure & Operations:**
- [references/hosting-reference.md](references/hosting-reference.md) - Docker/Kubernetes deployment, CHT Watchdog monitoring, backup/recovery
- [references/cht-conf-reference.md](references/cht-conf-reference.md) - CLI commands, connection options, CSV data types
- [references/version-compatibility.md](references/version-compatibility.md) - Supported versions, dependency matrix, migration checklists, breaking changes

**Data & Architecture:**
- [references/database-schema.md](references/database-schema.md) - Database document schemas
- [references/doc_index.md](references/doc_index.md) - CHT documentation index and search patterns

### External Links
- [CHT Documentation](https://docs.communityhealthtoolkit.org)
- [CHT GitHub](https://github.com/medic/cht-core)
- [CHT Forum](https://forum.communityhealthtoolkit.org)
- [XLSForm Reference](https://xlsform.org)
