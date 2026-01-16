# DRCA Lending Library - Technical Architecture Documentation

## System Overview
The DRCA Lending Library is a web-based tool and item sharing platform built using Google Forms, Google Sheets, Google Apps Script, and a static HTML website hosted on GitHub.

## Components

### 1. Google Forms (Data Input)

**Form 1: "Add Items to Lending Library"**
- Purpose: Allows DRCA members to add items they want to lend
- URL: https://forms.gle/gW78HfbavvZQK5Yv7
- Collects: Item name, category, description, photo URL, product link, notes, location landmark, owner name, email, phone, contact preferences
- Output: Populates "Item Catalog" tab in Google Sheet

**Form 2: "Borrowing Request"**
- Purpose: Allows verified lenders to request to borrow items
- Collects: Item name (pre-filled from website), borrower name, email, phone, when needed, when returning, message to owner
- Output: Populates "Loan Requests" tab in Google Sheet
- Trigger: Runs email notification script on submission

### 2. Google Sheet (Data Storage)

**Sheet ID:** `1psqVu7RcHRBlLoqgICNRzZeNm7lMA3WoMtpfJDKBQns`

**Tab 1: "Item Catalog"**
- Populated by: Form 1
- Contains: All available items with lender details
- Column structure:
  - A: Timestamp
  - B: Item Name
  - C: Category
  - D: Description
  - E: Photo URL
  - F: Product Link
  - G: Notes & Instructions
  - H: Landmark/Location
  - I: Owner Name
  - J: Owner Email (lenderEmail)
  - K: Owner Phone
  - L: Contact Preference

**Tab 2: "Form Responses 2" (to be renamed "Loan Requests")**
- Populated by: Form 2
- Contains: All borrowing requests with associated details

### 3. Google Apps Scripts

**Script 1: Attached to Google Sheet**
- Location: Sheet → Extensions → Apps Script
- Functions:
  - `doGet(e)`: Exposes Item Catalog data as JSON API for website to fetch
  - `doPost(e)`: Legacy function, not currently used
- Deployment: Deployed as web app with access "Anyone"
- API URL: `https://script.google.com/macros/s/AKfycbxNu-Rj8bJARt0C6AhLydoFyy_VV_Zx9wxrvfbvjCp0b9Pny5ZUlVuDxFgZy63EEA-2Ww/exec`

**Script 2: Attached to Form 2 (Borrowing Request)**
- Location: Form 2 → ⋮ → Script editor
- Function: `onFormSubmit(e)`
  - Triggered automatically when Form 2 is submitted
  - Extracts form response data
  - Looks up item owner details from Item Catalog tab
  - Sends formatted email to 4 recipients:
    1. Borrower (from form)
    2. Owner (from catalog lookup)
    3. vp@delraycitizen.net
    4. drcalending@gmail.com
- Trigger: "From form - On form submit" (automatically runs on each submission)

### 4. Website (User Interface)

**Hosting:** GitHub Pages
**File:** `index.html` (or similar)

**Functionality:**
1. Fetches item data from Sheet Script API on page load
2. Displays items in searchable/filterable grid
3. Search by keyword
4. Filter by category
5. Click item to see details modal
6. "Request This Item" button opens Form 2 with item name pre-filled

**Planned Enhancement:**
- Email verification before allowing requests (check if borrower email exists as lender in Item Catalog)

## Data Flow

### Adding an Item
1. Lender fills out Form 1
2. Form 1 appends row to "Item Catalog" tab
3. Item becomes available on website immediately (next page load)

### Requesting an Item
1. User browses website, clicks item
2. User clicks "Request This Item"
3. Form 2 opens in new tab with item name pre-filled
4. User completes form and submits
5. Form 2 appends row to "Loan Requests" tab
6. Form 2 script triggers:
   - Looks up owner details from Item Catalog
   - Sends email to borrower, owner, and DRCA addresses
7. Parties coordinate pickup/return via email/phone

### Planned Verification Flow
1. User clicks "Request This Item"
2. Modal prompts for email address
3. JavaScript checks if email exists in Item Catalog (lenderEmail field)
4. If YES: Opens Form 2 with item name AND email pre-filled
5. If NO: Shows message with link to add first item via Form 1

## Key Technical Details

**Email Recipients (Borrowing Requests):**
- Borrower email (from form)
- Owner email (from Item Catalog lookup)
- vp@delraycitizen.net (DRCA VP)
- drcalending@gmail.com (Lending Library dedicated address)

**Authentication/Access Control:**
- Currently: None on website (public access)
- Form 1: Manual DRCA membership verification when items added
- Form 2: Planned email verification against Item Catalog
- Sheet/Scripts: Owner has full access, scripts run with owner permissions

**Data Validation:**
- Category dropdown ensures consistent categorization
- Photo URLs expected to be public image links
- No authentication at website level (community trust-based)

## Maintenance Tasks

**Regular:**
- Review new items in Item Catalog for DRCA membership
- Monitor email notifications for issues

**As Needed:**
- Update category list in both Form 1 and website HTML
- Rename "Form Responses 2" to "Loan Requests" for clarity
- Deploy updated HTML to GitHub when changes made

## Future Enhancements Discussed
- Email verification before borrowing (in progress)
- Success story collection mechanism
- Delivery/pickup coordination features
- Platform-agnostic version for other community associations

## Contact
Questions: lendinglibrary@delraycitizen.org
