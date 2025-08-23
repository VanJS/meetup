# GitHub Actions Automations

This document summarizes the automated workflows configured for the VanJS meetup repository.

## Workflows

### 1. Close Past Meetups
**File**: `.github/workflows/close-past-meetups.yml`

**Purpose**: Automatically closes past meetup milestones and their associated issues.

**Triggers**:
- Daily at 10 AM PST (6 PM UTC)
- Manual trigger via workflow dispatch

**Key Features**:
- Uses PST/PDT timezone for accurate date comparisons
- Closes milestones that are 1+ days past their due date
- Automatically closes all open issues within past milestones
- Special thank-you messages for speaker issues (Talk/Demo labels)
- Generic closing messages for other meetup-related issues
- Safety features: dry-run mode and configurable enable/disable
- Rate limiting to prevent GitHub API abuse

**Configuration Variables**:
- `MEETUP_CLEANUP_ENABLED`: Enable/disable cleanup (default: true)
- `CLEANUP_DRY_RUN`: Run in test mode without making changes

### 2. Speaker Date Selection
**File**: `.github/workflows/speaker-date-selection.yml`

**Purpose**: Automatically posts available presentation dates when speakers submit talk proposals.

**Triggers**:
- Issues labeled with "Talk" or "Demo"

**Key Features**:
- Fetches open milestones ending with "Meetup"
- Shows only future dates within 6 months
- Limits to milestones with fewer than 3 speakers
- Creates interactive checkboxes for date selection
- Prevents duplicate comments with time-based checks
- Updates availability when Event label is applied

**Configuration Variables**:
- `SPEAKER_DATE_SELECTION_ENABLED`: Enable/disable automation (default: true)

### 3. Speaker Date Selected Notification
**File**: `.github/workflows/speaker-date-selected.yml`

**Purpose**: Notifies maintainers when speakers select their preferred presentation dates.

**Triggers**:
- Issue comments are edited (specifically checkbox interactions)

**Key Features**:
- Monitors for date selection checkbox changes
- Sends notifications to maintainers via GitHub mentions
- Sends email notifications (if configured with SendGrid)
- Posts confirmation messages to speakers
- Prevents duplicate notifications with timing checks
- Only processes official date selection comments

**Configuration Variables**:
- `SPEAKER_NOTIFICATIONS_ENABLED`: Enable/disable notifications (default: true)
- `MAINTAINERS`: Comma-separated list of GitHub usernames
- `MAINTAINER_EMAILS`: Comma-separated list of email addresses
- `FROM_EMAIL`: Sender email address for notifications

**Required Secrets**:
- `SENDGRID_API_KEY`: SendGrid API key for email notifications

## Workflow Integration

These workflows work together to automate the speaker management process:

1. **Speaker submits talk proposal** → Issue created with Talk/Demo label
2. **Date selection workflow** → Posts available meetup dates as checkboxes
3. **Speaker selects dates** → Edits comment to check preferred dates
4. **Notification workflow** → Alerts maintainers and confirms with speaker
5. **Maintainer assigns to milestone** → Speaker is scheduled for selected meetup
6. **After meetup occurs** → Cleanup workflow closes past milestones and thanks speakers

## Administrative Controls

All workflows can be disabled via repository variables:
- Set `MEETUP_CLEANUP_ENABLED=false` to disable meetup cleanup
- Set `SPEAKER_DATE_SELECTION_ENABLED=false` to disable date selection
- Set `SPEAKER_NOTIFICATIONS_ENABLED=false` to disable maintainer notifications
- Set `CLEANUP_DRY_RUN=true` to test cleanup without making changes