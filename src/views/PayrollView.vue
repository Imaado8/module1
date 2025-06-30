<template>
  <div class="payroll-wrapper">
    <div class="table-card">
      <h2 class="attendance-title mb-3 text-center">Payroll System</h2>

      <div class="text-center mb-4 position-relative">
        <input
          type="search"
          class="form-control mx-auto search-input"
          placeholder="Search employees name ..."
          v-model="searchQuery"
          @focus="showDropdown = true"
          @input="showDropdown = true"
          @keydown.down.prevent="highlightNext"
          @keydown.up.prevent="highlightPrev"
          @keydown.enter.prevent="selectHighlighted"
          ref="searchInput"
          autocomplete="off"
        />
        <ul
          v-if="showDropdown && filteredEmployees.length"
          class="list-group position-absolute w-100 search-dropdown"
          ref="dropdownList"
        >
          <li
            v-for="(emp, index) in filteredEmployees"
            :key="emp.employeeId"
            class="list-group-item list-group-item-action"
            :class="{ active: highlightedIndex === index }"
            @mousedown.prevent="selectEmployeeFromDropdown(emp)"
            @mouseover="highlightedIndex = index"
          >
            {{ emp.name }} (ID: {{ emp.employeeId }})
          </li>
        </ul>
      </div>

      <table class="styled-table">
        <thead>
          <tr>
            <th v-for="header in tableHeaders" :key="header">{{ header }}</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="emp in filteredEmployees" :key="emp.employeeId">
            <td v-for="field in fieldsToDisplay" :key="field">
              {{ emp[field] || "N/A" }}
            </td>
            <td>
              <button type="button" class="btn btn-info btn-sm" @click="viewPayslip(emp)">
                View
              </button>
              <button class="btn btn-success btn-sm ms-2" @click="downloadPayslip(emp)">
                Download
              </button>
            </td>
          </tr>
        </tbody>
      </table>

      <div class="modal fade" id="payslipModal" ref="payslipModal">
        <div class="modal-dialog modal-lg modal-dialog-centered">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">
                Payslip - {{ selectedEmployee?.name || "" }}
              </h5>
              <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body" v-if="selectedEmployee">
              <dl class="row">
                <template v-for="(value, key) in selectedEmployee" :key="key">
                  <dt class="col-sm-4">{{ formatKey(key) }}</dt>
                  <dd class="col-sm-8">{{ value || "N/A" }}</dd>
                </template>
              </dl>
            </div>
          </div>
        </div>
      </div>

      <div class="calculator-card mt-5 p-4">
        <h2 class="attendance-title text-center mb-3">Leave Deduction Calculator</h2>
        <label for="employeeSelect" class="form-label">Select Employee:</label>
        <select
          id="employeeSelect"
          v-model="selectedCalculatorEmployeeId"
          class="form-select w-100 mb-3"
        >
          <option disabled value="">-- Select --</option>
          <option v-for="emp in employees" :key="emp.employeeId" :value="emp.employeeId">
            {{ emp.name }} (ID: {{ emp.employeeId }})
          </option>
        </select>
        <div v-if="calculatedDeduction !== null" class="alert alert-info">
          <p><strong>Leave Days:</strong> {{ selectedCalculatorEmployee.leaveDeductions }}</p>
          <p><strong>Monthly Salary:</strong> R {{ selectedCalculatorEmployee.finalSalary.toLocaleString() }}</p>
          <p><strong>Deduction:</strong> R {{ calculatedDeduction.toFixed(0) }}</p>
          <p><strong>Salary After Deduction:</strong> R {{ salaryAfterDeduction.toFixed(0) }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import jsPDF from "jspdf";

export default {
  name: "PayrollSystem",
  data() {
    return {
      employees: [
        { employeeId: 2345, name: "Jane", hoursWorked: 160, leaveDeductions: 8, finalSalary: 69500 },
        { employeeId: 8522, name: "Max", hoursWorked: 150, leaveDeductions: 10, finalSalary: 79000 },
        { employeeId: 6123, name: "Sipho", hoursWorked: 168, leaveDeductions: 2, finalSalary: 64800 },
        { employeeId: 2536, name: "Joshua", hoursWorked: 162, leaveDeductions: 4, finalSalary: 57750 },
        { employeeId: 7896, name: "Laila", hoursWorked: 158, leaveDeductions: 5, finalSalary: 57850 },
        { employeeId: 1235, name: "Aisha", hoursWorked: 165, leaveDeductions: 6, finalSalary: 59700 },
        { employeeId: 6789, name: "Sarah", hoursWorked: 170, leaveDeductions: 8, finalSalary: 66780 },
        { employeeId: 8776, name: "Jade", hoursWorked: 177, leaveDeductions: 9, finalSalary: 78900 },
      ],

      fieldsToDisplay: ["employeeId", "name", "hoursWorked", "leaveDeductions"],
      selectedEmployee: null,
      selectedCalculatorEmployeeId: "",
      bootstrapModalInstance: null,
      searchQuery: "",
      showDropdown: false,
      highlightedIndex: -1,
    };
  },
  computed: {
    tableHeaders() {
      return this.fieldsToDisplay.map(f => this.formatKey(f));
    },
    filteredEmployees() {
      if (!this.searchQuery.trim()) return this.employees;
      return this.employees.filter(emp =>
        emp.name.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    },
    selectedCalculatorEmployee() {
      return this.employees.find(emp => emp.employeeId === this.selectedCalculatorEmployeeId) || {};
    },
    calculatedDeduction() {
      const emp = this.selectedCalculatorEmployee;
      if (!emp.leaveDeductions || !emp.finalSalary) return null;
      const workingDays = 22;
      return (emp.finalSalary / workingDays) * emp.leaveDeductions;
    },
    salaryAfterDeduction() {
      const emp = this.selectedCalculatorEmployee;
      return emp.finalSalary - this.calculatedDeduction;
    }
  },
  methods: {
    formatKey(key) {
      return key.replace(/([A-Z])/g, " $1").replace(/^./, s => s.toUpperCase());
    },
    viewPayslip(emp) {
      this.selectedEmployee = emp;
      if (!this.bootstrapModalInstance) {
        this.bootstrapModalInstance = new bootstrap.Modal(this.$refs.payslipModal);
      }
      this.bootstrapModalInstance.show();
    },
    downloadPayslip(emp) {
      const pdf = new jsPDF();
      const today = new Date();
      const date = `${today.getDate()}/${today.getMonth() + 1}/${today.getFullYear()}`;
      const dailyRate = emp.finalSalary / 22;
      const deduction = emp.leaveDeductions * dailyRate;
      const salaryAfter = emp.finalSalary - deduction;

      pdf.setFontSize(22);
      pdf.text("ModernTech Solutions", 105, 20, { align: "center" });
      pdf.setFontSize(12);
      pdf.text("Employee Payslip", 105, 28, { align: "center" });
      pdf.text(`Date: ${date}`, 160, 38);
      pdf.line(20, 32, 190, 32);

      let y = 48;
      ["employeeId", "name", "hoursWorked", "leaveDeductions"].forEach(field => {
        pdf.text(`${this.formatKey(field)}:`, 25, y);
        pdf.text(String(emp[field] || "N/A"), 80, y);
        y += 8;
      });

      y += 4;
      pdf.setFontSize(14);
      pdf.text("Salary Breakdown", 25, y);
      y += 8;
      pdf.setFontSize(12);
      pdf.text(`Monthly Salary: R ${emp.finalSalary.toLocaleString()}`, 25, y);
      y += 8;
      pdf.text(`Daily Rate: R ${dailyRate.toFixed(2)}`, 25, y);
      y += 8;
      pdf.text(`Deduction: R ${deduction.toFixed(2)}`, 25, y);
      y += 8;
      pdf.setFont("helvetica", "bold");
      pdf.text(`Net Salary: R ${salaryAfter.toFixed(2)}`, 25, y);
      pdf.save(`Payslip_${emp.name}.pdf`);
    },
    selectEmployeeFromDropdown(emp) {
      this.searchQuery = emp.name;
      this.viewPayslip(emp);
      this.showDropdown = false;
    },
    highlightNext() {
      if (this.highlightedIndex < this.filteredEmployees.length - 1) {
        this.highlightedIndex++;
      }
    },
    highlightPrev() {
      if (this.highlightedIndex > 0) {
        this.highlightedIndex--;
      }
    },
    selectHighlighted() {
      if (this.highlightedIndex >= 0) {
        this.selectEmployeeFromDropdown(this.filteredEmployees[this.highlightedIndex]);
      }
    }
  }
};
</script>

<style scoped>
.payroll-wrapper {
  padding: 2rem 1rem;
  padding-left: 170px;
  background: #225c70;
  min-height: 100vh;
  color: #333;
}

@media (max-width: 768px) {
  .payroll-wrapper {
    padding-left: 4.5rem;
    padding-right: 1rem;
  }
}

.table-card {
  background: #fbf9f5;
  padding: 1.5rem;
  border-radius: 12px;
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.1);
}

.attendance-title {
  font-size: 1.8rem;
  color: #ffffff;
  background-color: #295c6d;
  padding: 16px;
  border-radius: 12px;
  max-width: 800px;
  margin: 0 auto;
}

.styled-table {
  width: 100%;
  border-collapse: separate;
  border-spacing: 0 0.5rem;
  font-size: 1rem;
  background-color: #fbf9f5;
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.1);
  border-radius: 12px;
  overflow: hidden;
}

.styled-table th,
.styled-table td {
  padding: 1rem;
  background-color: #fff;
  border-bottom: 1px solid #eee;
  white-space: nowrap;
  text-align: left;
}

.styled-table th {
  background-color: #f3f4f6;
  font-weight: 600;
}

.calculator-card {
  background: #f8f9fa;
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.search-input {
  max-width: 600px;
  margin: 0 auto;
  display: block;
}
</style>

<style>
.search-dropdown {
  max-width: 600px;
  left: 50%;
  transform: translateX(-50%);
  background-color: white;
  z-index: 1050;
}
</style>
