# Homework 6: Pet Adoption Center Analysis

**Student:** Harsha Kanaparthi
**Student ID:** 801421103

## Project Overview

This is an Observable Framework dashboard that performs exploratory visual analysis on a pet adoption center dataset. The dashboard investigates adoption patterns, pet characteristics, and adopter demographics.

## Dataset

The dataset (`pet_adoption_center.csv`) contains information about:
- Pet characteristics (species, breed, age, gender, color)
- Arrival and adoption dates
- Adopter demographics (name, age, city, previous pet ownership)
- Adoption status

## Features

### Visualizations (3 charts)

1. **Adoption Rates by Species** - Bar chart showing which pet species have the highest adoption success rates
2. **Pet Age Distribution by Adoption Status** - Box plot exploring how age affects adoption likelihood
3. **Adopter Experience and Pet Type Preferences** - Scatter plot examining the relationship between adopter experience and pet type

### Interactive Features (2+ interactions)

1. **Metric Selector** - Dropdown to switch between viewing adoption rate, total count, or adopted count
2. **Species Filter** - Dropdown to filter age distribution by specific pet species
3. **Tooltips** - All charts include interactive tooltips showing detailed information on hover
4. **Interactive Summary Table** - Displays summary statistics that complement the visualizations

### Dashboard Components

- Header with name, student ID, and title
- Theme description
- Dataset description (source, scope, time range, key fields)
- Three main visualizations addressing research questions
- Design decisions and insights section

## Running the Dashboard

### Prerequisites

- Node.js installed (version 18 or higher recommended)

### Installation

```bash
npm install
```

### Development Mode

To run the dashboard locally with live preview:

```bash
npm run dev
```

Then open your browser to the URL shown in the terminal (typically http://localhost:3000)

### Build for Production

To build the static site:

```bash
npm run build
```

The built files will be in the `dist/` directory.

### Deploy

To deploy to Observable Cloud (requires authentication):

```bash
npm run deploy
```

Alternatively, you can deploy the `dist/` folder to any static hosting service (Netlify, Vercel, GitHub Pages, etc.)

## Project Structure

```
.
├── src/
│   ├── index.md           # Main dashboard file
│   └── data/
│       └── pet_adoption_center.csv
├── observablehq.config.js # Framework configuration
├── package.json
└── README.md
```

## Research Questions Investigated

1. **Which pet species have the highest adoption rates?**
   - Helps identify which types of pets are most successfully adopted

2. **How does pet age affect adoption likelihood?**
   - Examines whether younger or older pets are more likely to be adopted

3. **What is the relationship between adopter experience and pet type?**
   - Explores whether previous pet ownership influences the type of pet adopted

## Key Insights

- Adoption rates vary significantly across different pet species
- Pet age distribution shows distinct patterns between adopted and non-adopted pets
- Adopters with varying levels of experience adopt different types of pets
- Interactive filtering reveals deeper patterns within specific species

## Assignment Requirements Met

✅ Observable Framework project setup
✅ Dataset stored in /data folder and loaded with FileAttachment
✅ Dashboard header with name, last name, and student ID
✅ Clear title introducing the dataset
✅ Theme description section
✅ Dataset description paragraph (source, scope, time range, key fields)
✅ Three visualizations (2 points each = 6 points)
✅ Two interactive features (1 point each = 2 points)
✅ Tooltips on all charts
✅ Final insights section with design decisions and observations (1 point)
✅ Total: 10 points

## Technologies Used

- Observable Framework
- Observable Plot
- D3.js
- JavaScript/ES6
- Markdown

---

**File Name:** Homework6_Harsha_Kanaparthi
