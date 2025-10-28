```html
<link rel="stylesheet" href="./custom-style.css">
```

# Pet Adoption Center Exploratory Data Analysis

<div style="background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); padding: 1.5rem; border-radius: 12px; margin-bottom: 2rem; box-shadow: 0 4px 12px rgba(102, 126, 234, 0.2);">
  <p style="color: white; margin: 0; font-size: 1.1rem;"><strong style="color: white;">Name:</strong> Harsha Kanaparthi</p>
  <p style="color: white; margin: 0.5rem 0 0 0; font-size: 1.1rem;"><strong style="color: white;">Student ID:</strong> 801421103</p>
</div>

---

## Theme

This dashboard explores pet adoption patterns and trends at a pet adoption center, analyzing factors that influence adoption success rates and understanding the characteristics of both pets and adopters.

---

## About the Dataset

This dataset contains information about pets available for adoption at a pet adoption center. The data includes:

- **Source:** Pet Adoption Center records
- **Scope:** Comprehensive records of pets (dogs, cats, birds, rabbits, hamsters) and their adoption status
- **Time Range:** 2023-2025
- **Key Fields:** Pet characteristics (species, breed, age, gender, color), arrival and adoption dates, adopter demographics (name, age, city, previous pet ownership)
- **Data Cleaning:** The dataset includes both adopted and non-adopted pets. Adopted pets have complete adopter information, while non-adopted pets have N/A values for adopter fields.

---

```js
// Load the dataset
const pets = FileAttachment("data/pet_adoption_center.csv").csv({typed: true});
```

```js
// Data processing
const adoptedPets = pets.filter(d => d.adopted === "True");
const notAdoptedPets = pets.filter(d => d.adopted === "False");

// Calculate adoption rates by species
const speciesStats = d3.rollup(
  pets,
  v => ({
    total: v.length,
    adopted: v.filter(d => d.adopted === "True").length,
    adoptionRate: (v.filter(d => d.adopted === "True").length / v.length) * 100
  }),
  d => d.species
);

const speciesData = Array.from(speciesStats, ([species, stats]) => ({
  species,
  ...stats
})).sort((a, b) => b.adoptionRate - a.adoptionRate);

// Age distribution analysis
const ageDistribution = pets.map(d => ({
  age: d.age_years,
  species: d.species,
  adopted: d.adopted
}));

// Adopter previous pets analysis
const adopterStats = adoptedPets
  .filter(d => d.adopter_previous_pets >= 0)
  .map(d => ({
    previousPets: d.adopter_previous_pets,
    species: d.species,
    adopterAge: d.adopter_age
  }));
```

---

<div style="background: #f0f4ff; padding: 2rem; border-radius: 12px; margin: 2rem 0; border-left: 5px solid #667eea; box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06); border: 1px solid #e0e7ff;">

## 📊 Question 1: Which pet species have the highest adoption rates?

<p style="color: #1a202c; font-size: 1.05rem; line-height: 1.7; font-weight: 500;">This visualization shows the adoption success rate across different species to identify which types of pets are most likely to find homes.</p>

</div>

```js
// Create dropdown for interactive filtering
const selectedMetric = view(Inputs.select(
  ["Adoption Rate (%)", "Total Count", "Adopted Count"],
  {value: "Adoption Rate (%)", label: "Display Metric"}
));
```

```js
// Bar chart showing adoption rates by species
Plot.plot({
  style: {
    background: "white",
    fontSize: "14px",
    fontFamily: "-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif"
  },
  marginLeft: 100,
  marginRight: 40,
  marginTop: 40,
  marginBottom: 50,
  height: 400,
  x: {
    label: selectedMetric + " →",
    grid: true,
    labelAnchor: "center"
  },
  y: {
    label: "↑ Species",
    domain: speciesData.map(d => d.species),
    labelAnchor: "center"
  },
  color: {
    type: "linear",
    scheme: "purples",
    legend: true,
    label: "Adoption Rate (%)"
  },
  marks: [
    Plot.barX(speciesData, {
      y: "species",
      x: selectedMetric === "Adoption Rate (%)" ? "adoptionRate" :
         selectedMetric === "Total Count" ? "total" : "adopted",
      fill: "adoptionRate",
      sort: {y: "-x"},
      tip: {
        fontSize: 14,
        fontFamily: "system-ui"
      },
      title: d => `${d.species}\n━━━━━━━━━━━━━━━\nTotal: ${d.total}\nAdopted: ${d.adopted}\nAdoption Rate: ${d.adoptionRate.toFixed(1)}%`,
      rx: 4
    }),
    Plot.ruleX([0], {stroke: "#e2e8f0", strokeWidth: 2})
  ]
})
```

---

<div style="background: #f0f4ff; padding: 2rem; border-radius: 12px; margin: 2rem 0; border-left: 5px solid #667eea; box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06); border: 1px solid #e0e7ff;">

## 📈 Question 2: How does pet age affect adoption likelihood?

<p style="color: #1a202c; font-size: 1.05rem; line-height: 1.7; font-weight: 500;">This visualization explores the relationship between pet age and adoption success across different species.</p>

</div>

```js
// Create species filter
const selectedSpecies = view(Inputs.select(
  ["All", ...new Set(pets.map(d => d.species))],
  {value: "All", label: "Filter by Species"}
));
```

```js
// Filter data based on selection
const filteredAgeData = selectedSpecies === "All"
  ? ageDistribution
  : ageDistribution.filter(d => d.species === selectedSpecies);
```

```js
// Box plot showing age distribution by adoption status
Plot.plot({
  style: {
    background: "white",
    fontSize: "14px",
    fontFamily: "-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif"
  },
  marginLeft: 120,
  marginRight: 40,
  marginTop: 40,
  marginBottom: 50,
  height: 350,
  x: {
    label: "Age (years) →",
    grid: true,
    labelAnchor: "center"
  },
  y: {
    label: "↑ Adoption Status",
    labelAnchor: "center",
    domain: ["False", "True"],
    tickFormat: d => d === "True" ? "✓ Adopted" : "✗ Not Adopted"
  },
  color: {
    legend: true,
    domain: ["True", "False"],
    range: ["#667eea", "#f59e0b"],
    label: "Status"
  },
  marks: [
    Plot.boxX(filteredAgeData, {
      x: "age",
      y: "adopted",
      fill: "adopted",
      stroke: "adopted",
      strokeWidth: 2,
      tip: {
        fontSize: 14
      }
    }),
    Plot.dot(filteredAgeData, {
      x: "age",
      y: "adopted",
      fill: "adopted",
      r: 2.5,
      opacity: 0.4,
      tip: true,
      title: d => `Age: ${d.age} years\nSpecies: ${d.species}\nAdopted: ${d.adopted === "True" ? "Yes" : "No"}`
    })
  ]
})
```

---

<div style="background: #f0f4ff; padding: 2rem; border-radius: 12px; margin: 2rem 0; border-left: 5px solid #667eea; box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06); border: 1px solid #e0e7ff;">

## 🎯 Question 3: What is the relationship between adopter experience and pet type?

<p style="color: #1a202c; font-size: 1.05rem; line-height: 1.7; font-weight: 500;">This scatter plot examines whether adopters with previous pet ownership experience tend to adopt certain types of pets.</p>

</div>

```js
// Scatter plot with adopter age vs previous pets
Plot.plot({
  style: {
    background: "white",
    fontSize: "14px",
    fontFamily: "-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif"
  },
  marginLeft: 60,
  marginRight: 40,
  marginTop: 40,
  marginBottom: 60,
  height: 450,
  width: 800,
  x: {
    label: "Number of Previous Pets →",
    grid: true,
    labelAnchor: "center",
    nice: true
  },
  y: {
    label: "↑ Adopter Age (years)",
    grid: true,
    labelAnchor: "center",
    nice: true
  },
  color: {
    legend: true,
    label: "Pet Species",
    scheme: "tableau10"
  },
  marks: [
    Plot.dot(adopterStats, {
      x: "previousPets",
      y: "adopterAge",
      fill: "species",
      stroke: "white",
      strokeWidth: 1.5,
      r: 6,
      opacity: 0.8,
      tip: {
        fontSize: 14
      },
      title: d => `🐾 Species: ${d.species}\n━━━━━━━━━━━━━━━\nPrevious Pets: ${d.previousPets}\nAdopter Age: ${d.adopterAge} years`
    }),
    Plot.frame({stroke: "#e2e8f0", strokeWidth: 2})
  ]
})
```

### 📋 Summary Statistics by Species

```js
// Summary statistics table
const summaryStats = d3.rollup(
  adopterStats,
  v => ({
    count: v.length,
    avgPreviousPets: d3.mean(v, d => d.previousPets).toFixed(1),
    avgAdopterAge: d3.mean(v, d => d.adopterAge).toFixed(1)
  }),
  d => d.species
);

const summaryData = Array.from(summaryStats, ([species, stats]) => ({
  Species: species,
  "Adoptions": stats.count,
  "Avg Previous Pets": stats.avgPreviousPets,
  "Avg Adopter Age": stats.avgAdopterAge
})).sort((a, b) => b.Adoptions - a.Adoptions);
```

```js
html`<div style="overflow-x: auto; margin: 1.5rem 0;">
  ${Inputs.table(summaryData, {
    header: {
      Species: "Pet Species",
      Adoptions: "Number of Adoptions",
      "Avg Previous Pets": "Avg Previous Pets",
      "Avg Adopter Age": "Avg Adopter Age (years)"
    },
    width: {
      Species: 150,
      Adoptions: 150,
      "Avg Previous Pets": 150,
      "Avg Adopter Age": 180
    }
  })}
</div>`
```

---

## Design Decisions and Insights

### Design Rationale

- **Interactive Filtering:** Added dropdown selectors to allow users to explore different metrics (adoption rate, total count) and filter by species, making the analysis more flexible and comprehensive.

- **Color Encoding:** Used a blue gradient for adoption rates to show performance intensity, and contrasting colors (green for adopted, red for not adopted) to clearly distinguish adoption status in the age distribution chart.

- **Multiple Chart Types:** Combined bar charts, box plots, and scatter plots to address different analytical questions - categorical comparisons, distribution analysis, and relationship exploration.

- **Tooltips:** Implemented detailed tooltips on all charts to provide specific data points on hover, enhancing the interactive exploration experience.

### Key Observations

- **Species Adoption Patterns:** The adoption rates vary significantly across species, with some species showing notably higher success rates than others. This could inform the center about which species need more marketing or special programs.

- **Age Factor:** The box plot reveals that age distribution differs between adopted and non-adopted pets, suggesting that certain age ranges are more desirable to adopters. Younger pets generally show higher adoption rates.

- **Adopter Experience:** There's a diverse range of adopter experience levels across all species. Interestingly, both first-time and experienced pet owners adopt various types of pets, though certain species may attract more experienced adopters.

- **Time Insights:** The data spans multiple years (2023-2025), allowing for temporal analysis of adoption trends and seasonal patterns if needed.

- **Demographics:** Adopter age ranges widely, indicating that pet adoption appeals to diverse age groups. The relationship between adopter demographics and pet preferences could guide targeted outreach efforts.

---

*Dashboard created with Observable Framework and Observable Plot*
