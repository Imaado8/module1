<template>
  <div class="performance-wrapper">
    <div class="card shadow-sm p-4 bg-white rounded">
      <h2 class="text-center mb-4 performance-title">Performance Reviews</h2>

      <div class="table-responsive">
        <table class="table table-bordered table-striped styled-table">
          <thead class="table-header">
            <tr>
              <th>EMPLOYEE NAME</th>
              <th>POSITION</th>
              <th>DEPARTMENT</th>
              <th>EXCELLENT</th>
              <th>SATISFACTORY</th>
              <th>UNSATISFACTORY</th>
              <th>HR COMMENT</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="employee in employeeInformation" :key="employee.employeeId">
              <td>{{ employee.name }}</td>
              <td>{{ employee.position }}</td>
              <td>{{ employee.department }}</td>
              <td>{{ employee.excellent }}</td>
              <td>{{ employee.satisfactory }}</td>
              <td>{{ employee.unsatisfactory }}</td>
              <td>{{ employee.hrComment }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex';
export default {
  computed: {
    ...mapState(['employeeInformation'])
  }
}
</script>

<style scoped>
.performance-wrapper {
  padding: 2rem 1rem;
  padding-left: 170px;
  background: #225c70;
  min-height: 100vh;
}

@media (max-width: 768px) {
  .performance-wrapper {
    padding-left: 1rem;
    padding-right: 1rem;
  }
}

.performance-title {
  color: #ffffff;
  background-color: #295c6d;
  padding: 1rem;
  border-radius: 12px;
}

.styled-table {
  background-color: #fbf9f5;
  border-collapse: separate;
  border-spacing: 0 0.3rem;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  overflow: hidden;
  font-size: 0.95rem;
}

.table-header th {
  background-color: #fbf9f5;
  font-weight: bold;
  text-align: center;
  white-space: nowrap;
}

.styled-table td, .styled-table th {
  padding: 12px;
  text-align: center;
  vertical-align: middle;
  background-color: white;
}
</style>
