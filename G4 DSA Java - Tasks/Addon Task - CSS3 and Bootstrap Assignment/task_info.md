# CSS3 and Bootstrap Assignment for Full Stack Developers

## Assignment Title

**20 Mini UI Use Cases Using CSS3 and Bootstrap**

---

## Objective

The objective of this assignment is to help full stack developers practice building small, reusable frontend UI components using **HTML5, CSS3, and Bootstrap 5**.

Each use case is simple and practical. These components are commonly used in real full stack applications such as admin dashboards, portals, landing pages, login systems, reports, and CRUD screens.

---

## Technologies Required

* HTML5
* CSS3
* Bootstrap 5
* Basic JavaScript
* Browser Developer Tools

---

## Project Folder Structure

```text
css-bootstrap-use-cases/
│
├── index.html
├── css/
│   └── style.css
├── js/
│   └── script.js
├── images/
│   └── placeholder images
└── README.md
```

---

# Assignment Instructions

Create a single webpage or multiple HTML pages that demonstrate the following **20 UI use cases**.

You may create:

```text
Option 1: One single index.html with all use cases
Option 2: Separate HTML files for each use case
```

Bootstrap can be added using CDN.

```html
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
```

---

# 20 Simple Use Cases

---

## Use Case 1: Responsive Navigation Bar

### Requirement

Create a responsive navbar for a business application.

### Must Include

* Brand name
* Home, About, Services, Contact links
* Login button
* Mobile hamburger menu

### Concepts Covered

* Bootstrap navbar
* Responsive collapse
* CSS hover effects

---

## Use Case 2: Hero Section

### Requirement

Create a landing page hero section.

### Must Include

* Main heading
* Short description
* Primary button
* Secondary button
* Image placeholder

### Concepts Covered

* Bootstrap grid
* CSS background gradient
* Button styling
* Responsive alignment

---

## Use Case 3: Login Form

### Requirement

Create a simple login form.

### Must Include

* Email field
* Password field
* Remember me checkbox
* Login button
* Forgot password link

### Concepts Covered

* Bootstrap forms
* Form spacing
* Input focus styling

---

## Use Case 4: Registration Form

### Requirement

Create a user registration form.

### Must Include

* Full name
* Email
* Password
* Confirm password
* Terms and conditions checkbox
* Register button

### Concepts Covered

* Bootstrap form controls
* Form layout
* Validation message UI

---

## Use Case 5: Profile Card

### Requirement

Create a user profile card.

### Must Include

* Profile image
* User name
* Role
* Short bio
* Follow button

### Concepts Covered

* Bootstrap cards
* CSS border radius
* Box shadow
* Hover effect

---

## Use Case 6: Product Card

### Requirement

Create a product card for an e-commerce page.

### Must Include

* Product image
* Product title
* Price
* Rating
* Add to Cart button

### Concepts Covered

* Bootstrap card
* Badge
* Button styling
* CSS transitions

---

## Use Case 7: Pricing Cards

### Requirement

Create pricing cards for a SaaS application.

### Must Include

* Basic plan
* Standard plan
* Premium plan
* Price
* Feature list
* Choose Plan button

### Concepts Covered

* Bootstrap grid
* Cards
* List styling
* Highlighting recommended plan

---

## Use Case 8: Services Section

### Requirement

Create a services section for a company website.

### Must Include

At least 4 service cards:

* Web Development
* API Integration
* UI Design
* Database Support

### Concepts Covered

* Bootstrap cards
* Equal height layout
* Hover effect
* Responsive columns

---

## Use Case 9: Contact Form

### Requirement

Create a contact form.

### Must Include

* Name
* Email
* Subject
* Message
* Submit button

### Concepts Covered

* Bootstrap forms
* Textarea
* Required field UI
* Form button alignment

---

## Use Case 10: Responsive Table

### Requirement

Create a responsive employee table.

### Must Include

Columns:

* Employee ID
* Name
* Department
* Email
* Status
* Action

### Concepts Covered

* Bootstrap table
* Responsive table wrapper
* Badges
* Action buttons

---

## Use Case 11: Dashboard Stat Cards

### Requirement

Create dashboard summary cards.

### Must Include

At least 4 cards:

* Total Users
* Total Orders
* Revenue
* Pending Tasks

### Concepts Covered

* Bootstrap grid
* Cards
* Icons or initials
* CSS custom styling

---

## Use Case 12: Alert Messages

### Requirement

Create different alert messages.

### Must Include

* Success alert
* Error alert
* Warning alert
* Info alert

### Concepts Covered

* Bootstrap alerts
* Alert icons
* Dismissible alerts

---

## Use Case 13: Modal Popup

### Requirement

Create a Bootstrap modal.

### Must Include

* Open modal button
* Modal title
* Modal body
* Close button
* Save button

### Concepts Covered

* Bootstrap modal
* Buttons
* JavaScript Bootstrap behavior

---

## Use Case 14: Accordion FAQ Section

### Requirement

Create an FAQ section using accordion.

### Must Include

At least 5 questions and answers.

Example questions:

* What is this application?
* How do I register?
* Can I reset my password?
* Is my data secure?
* How do I contact support?

### Concepts Covered

* Bootstrap accordion
* Collapsible content
* FAQ layout

---

## Use Case 15: Image Gallery

### Requirement

Create a responsive image gallery.

### Must Include

* Minimum 6 images
* Responsive grid
* Hover zoom effect
* Caption below image

### Concepts Covered

* Bootstrap grid
* CSS object-fit
* CSS transform
* Hover transition

---

## Use Case 16: Progress Bars

### Requirement

Create progress bars for skill tracking.

### Must Include

At least 5 skills:

* HTML
* CSS
* Bootstrap
* JavaScript
* C#

### Concepts Covered

* Bootstrap progress bar
* Width utilities
* Labels
* Custom colors through CSS

---

## Use Case 17: Tabs Section

### Requirement

Create a tab-based content section.

### Must Include

Tabs:

* Overview
* Features
* Reviews
* Support

### Concepts Covered

* Bootstrap tabs
* Active tab state
* Content switching

---

## Use Case 18: Breadcrumb and Pagination

### Requirement

Create navigation helpers for a listing page.

### Must Include

* Breadcrumb navigation
* Pagination with Previous and Next buttons
* Active page highlight

### Concepts Covered

* Bootstrap breadcrumb
* Bootstrap pagination
* Navigation UI

---

## Use Case 19: Order Summary Card

### Requirement

Create an order summary UI.

### Must Include

* Product name
* Quantity
* Price
* Discount
* Total amount
* Checkout button

### Concepts Covered

* Bootstrap card
* Flex utilities
* Text alignment
* Button styling

---

## Use Case 20: Admin Sidebar Layout

### Requirement

Create a simple admin dashboard layout.

### Must Include

* Sidebar menu
* Header section
* Main content area
* Dashboard cards
* Logout button

### Concepts Covered

* Bootstrap grid
* Flexbox
* Sidebar layout
* Responsive behavior

---

# CSS3 Requirements

Your `style.css` file must include custom styling. Do not depend only on Bootstrap.

Use at least the following CSS3 concepts:

* CSS variables
* Custom colors
* Font styling
* Flexbox
* Box shadow
* Border radius
* Hover effects
* Transitions
* Media queries
* Pseudo-classes such as `:hover`, `:focus`, and `:active`

---

## Example CSS Variables

```css
:root {
  --primary-color: #0d6efd;
  --secondary-color: #6c757d;
  --light-bg: #f8f9fa;
  --dark-text: #212529;
}
```

---

## Example Hover Effect

```css
.custom-card {
  transition: all 0.3s ease;
}

.custom-card:hover {
  transform: translateY(-6px);
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
}
```

---

# Bootstrap Components to Use

Use at least the following Bootstrap components:

* Navbar
* Buttons
* Cards
* Forms
* Modal
* Table
* Alerts
* Accordion
* Progress bar
* Tabs
* Breadcrumb
* Pagination
* Badges
* Grid system

---

# JavaScript Requirements

Use basic JavaScript only where required.

Examples:

* Open Bootstrap modal
* Form validation
* Toggle dark mode
* Update active sidebar item
* Show alert after form submit

---

# Bonus Tasks

## Bonus 1: Dark Mode Toggle

Add a dark mode button that changes the page theme.

## Bonus 2: Form Validation

Add simple JavaScript validation for login, registration, and contact forms.

## Bonus 3: Search Filter

Add a search box to filter rows in the employee table.

## Bonus 4: Responsive Sidebar

Make the admin sidebar collapse on smaller screens.

---

# Submission Format

Submit the project as a ZIP file.

```text
css-bootstrap-use-cases.zip
│
├── index.html
├── css/
│   └── style.css
├── js/
│   └── script.js
├── images/
│   └── images used in project
└── README.md
```

---

# README.md Format

```md
# CSS3 and Bootstrap 20 Use Cases Assignment

## Project Overview

This project contains 20 simple UI use cases built using HTML5, CSS3, and Bootstrap 5.

## Technologies Used

- HTML5
- CSS3
- Bootstrap 5
- JavaScript

## Use Cases Implemented

1. Responsive Navigation Bar
2. Hero Section
3. Login Form
4. Registration Form
5. Profile Card
6. Product Card
7. Pricing Cards
8. Services Section
9. Contact Form
10. Responsive Table
11. Dashboard Stat Cards
12. Alert Messages
13. Modal Popup
14. Accordion FAQ Section
15. Image Gallery
16. Progress Bars
17. Tabs Section
18. Breadcrumb and Pagination
19. Order Summary Card
20. Admin Sidebar Layout

## How to Run

Open `index.html` in any modern browser.

## Author

Your Name
```

---

# Evaluation Criteria

| Criteria                         |   Marks |
| -------------------------------- | ------: |
| HTML structure and semantic tags |      10 |
| CSS3 usage                       |      20 |
| Bootstrap component usage        |      20 |
| Responsive design                |      15 |
| UI cleanliness and spacing       |      10 |
| JavaScript functionality         |      10 |
| Code readability                 |      10 |
| README and folder structure      |       5 |
| **Total**                        | **100** |

---

# Expected Outcome

After completing this assignment, developers should be able to:

* Build reusable UI components
* Use Bootstrap grid and components effectively
* Apply custom CSS3 styling
* Create responsive layouts
* Design forms, cards, tables, modals, and dashboard sections
* Prepare frontend screens that can later be connected to backend APIs

---

# Important Notes

* Do not copy full templates from the internet.
* Custom CSS3 styling is mandatory.
* Bootstrap should be used properly, not randomly.
* Keep the design clean and professional.
* Use meaningful class names.
* Maintain proper indentation.
* Test the page on desktop and mobile screen sizes.

---

# Recommended Completion Time

**2 to 3 days**

---

# Difficulty Level

**Beginner to Intermediate**
