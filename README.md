# PiggyTrack - Code Explanation

**PiggyTrack** is a simple expenditure tracking application built with HTML, CSS, and JavaScript. This README provides a detailed explanation of the code structure, functionality, and purpose of each file to help you understand how the application works.

---

## **Project Overview**
PiggyTrack allows users to:
- Add daily expenses with category, description, amount, and date.
- View all expenses in a table format.
- Visualize spending habits using a dynamic pie chart.

The project uses **localStorage** for data persistence and **Chart.js** for graphical reports.

---

## **Folder Structure**
```
PiggyTrack/
├── index.html           # Main HTML file
├── styles/
│   └── style.css        # Main CSS file
├── scripts/
│   ├── app.js           # Handles core app functionality
│   ├── charts.js        # Manages chart creation using Chart.js
│   └── storage.js       # Handles localStorage operations
├── assets/
│   ├── images/          # Contains any icons or images
│   └── chart.js         # Chart.js library (optional; CDN also used)
└── README.md            # Project documentation
```

---

## **Code Explanation**

### 1. **index.html**
The `index.html` file provides the structure of the application. It consists of:
- **Header**: Includes navigation buttons to switch between sections.
- **Sections**:
  - **Add Expense Section**: A form to input expense details.
  - **Expense History Section**: Displays all saved expenses in a table.
  - **Reports Section**: Contains a `<canvas>` element for generating the pie chart.

#### Key Code Snippets:
```html
<header>
  <h1>PiggyTrack</h1>
  <nav>
    <button id="addExpenseBtn">Add Expense</button>
    <button id="viewExpensesBtn">Expense History</button>
    <button id="reportsBtn">Reports</button>
  </nav>
</header>
```
- **Purpose**: Allows navigation between the three main sections.

```html
<section id="addExpenseSection" class="hidden">
  <form id="expenseForm">
    <label for="amount">Amount (₹):</label>
    <input type="number" id="amount" required />

    <label for="category">Category:</label>
    <select id="category">
      <option value="Food">Food</option>
      <option value="Rent">Rent</option>
      <option value="Transport">Transport</option>
      <option value="Utilities">Utilities</option>
      <option value="Miscellaneous">Miscellaneous</option>
    </select>

    <label for="date">Date:</label>
    <input type="date" id="date" required />

    <label for="description">Description:</label>
    <textarea id="description" rows="3"></textarea>

    <button type="submit">Save Expense</button>
  </form>
</section>
```
- **Purpose**: Captures user input for expense details and submits the data.

---

### 2. **style.css**
The `style.css` file handles the appearance of the application, including:
- **Styling Buttons**: Ensures buttons are visually distinct and consistent.
- **Hiding/Showing Sections**: Uses the `.hidden` class to toggle visibility between sections.
- **Table Design**: Adds borders, spacing, and alignment for the expense table.

#### Key CSS Snippet:
```css
.hidden {
  display: none;
}

table {
  width: 100%;
  border-collapse: collapse;
}

table th, table td {
  border: 1px solid #ddd;
  padding: 8px;
}
```

---

### 3. **app.js**
The `app.js` file is the main script that manages user interactions and updates the UI dynamically. 

#### Key Functions:

1. **Switch Between Sections**:
```javascript
function showSection(section) {
  [addExpenseSection, expenseHistorySection, reportsSection].forEach((sec) =>
    sec.classList.add("hidden")
  );
  section.classList.remove("hidden");
}
```
- **Purpose**: Hides all sections and shows the selected one.

2. **Handle Form Submission**:
```javascript
expenseForm.addEventListener("submit", (e) => {
  e.preventDefault();
  const expense = {
    amount: parseFloat(document.getElementById("amount").value),
    category: document.getElementById("category").value,
    date: document.getElementById("date").value,
    description: document.getElementById("description").value,
  };
  saveExpense(expense);
  alert("Expense Added!");
  expenseForm.reset();
});
```
- **Purpose**: Saves a new expense and clears the form fields.

3. **Render Expense Table**:
```javascript
function renderExpenseTable() {
  const expenses = getExpenses();
  expenseTableBody.innerHTML = expenses
    .map(
      (expense, index) => `
    <tr>
      <td>${expense.date}</td>
      <td>${expense.category}</td>
      <td>₹${expense.amount}</td>
      <td>${expense.description || "-"}</td>
      <td><button onclick="deleteExpense(${index})">Delete</button></td>
    </tr>
  `
    )
    .join("");
}
```
- **Purpose**: Dynamically updates the table with saved expenses.

---

### 4. **storage.js**
Handles interactions with `localStorage` to store, retrieve, and delete expense data.

#### Key Functions:

1. **Get Expenses**:
```javascript
const getExpenses = () => JSON.parse(localStorage.getItem("expenses")) || [];
```
- **Purpose**: Retrieves the list of expenses from `localStorage`.

2. **Save Expense**:
```javascript
const saveExpense = (expense) => {
  const expenses = getExpenses();
  expenses.push(expense);
  localStorage.setItem("expenses", JSON.stringify(expenses));
};
```
- **Purpose**: Adds a new expense to `localStorage`.

3. **Delete Expense**:
```javascript
const deleteExpense = (index) => {
  const expenses = getExpenses();
  expenses.splice(index, 1);
  localStorage.setItem("expenses", JSON.stringify(expenses));
  renderExpenseTable();
  generateCategoryChart();
};
```
- **Purpose**: Removes an expense by index and updates the UI.

---

### 5. **charts.js**
Manages the creation of a pie chart using **Chart.js**.

#### Key Function:

1. **Generate Category Chart**:
```javascript
function generateCategoryChart() {
  const canvas = document.getElementById("categoryChart");
  if (chartInstance) {
    chartInstance.destroy();
  }
  const ctx = canvas.getContext("2d");
  const expenses = getExpenses();
  const categoryData = expenses.reduce((acc, expense) => {
    acc[expense.category] = (acc[expense.category] || 0) + expense.amount;
    return acc;
  }, {});

  chartInstance = new Chart(ctx, {
    type: "pie",
    data: {
      labels: Object.keys(categoryData),
      datasets: [
        {
          data: Object.values(categoryData),
          backgroundColor: ["#FF6384", "#36A2EB", "#FFCE56", "#4BC0C0", "#9966FF"],
        },
      ],
    },
  });
}
```
- **Purpose**: Aggregates expense data by category and visualizes it as a pie chart.

---

## **Conclusion**
This project demonstrates how to build a simple yet functional web app using HTML, CSS, JavaScript, and Chart.js. It introduces key concepts like localStorage, DOM manipulation, and dynamic chart generation. The modular structure ensures maintainability and scalability for future enhancements.
