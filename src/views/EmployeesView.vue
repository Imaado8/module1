<template>
  <div class="employee-wrapper">
    <div class="employee-card p-4">

      <div class="d-flex flex-column align-items-center mb-4">
  <h2 class="dashboard-title text-center">Employee Directory</h2>
  <button class="btn btn-request-leave mt-2" @click="showForm = true">
    Add Employee
  </button>
</div>

      <div class="flex-card-container">
        <div
          v-for="employee in employeeInformation"
          :key="employee.employeeId"
          class="employee-card card-container"
        >
          <div class="flip-card">
            <div class="flip-card-inner">
              <div class="flip-card-front card bg-light p-3 text-center">
                <h5 class="card-title fs-6 mb-2">{{ employee.name }}</h5>
                <p class="card-subtitle text-muted small">{{ employee.position }}</p>
              </div>
              <div class="flip-card-back card bg-primary text-white p-4 small">
                <p><strong>Dept:</strong> {{ employee.department }}</p>
                <p><strong>Email:</strong> {{ employee.contact }}</p>
                <p><strong>History:</strong> {{ employee.employmentHistory }}</p>
                <p><strong>Salary:</strong> R{{ Number(employee.salary || 0).toLocaleString() }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>


      <div v-if="showForm" class="modal fade show d-block" tabindex="-1" style="background-color: #295c6d;">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">Add New Employee</h5>
              <button type="button" class="btn-close" @click="showForm = false"></button>
            </div>
            <div class="modal-body">
              <form @submit.prevent="handleFormSubmit">
                <div class="mb-3" v-for="field in ['name', 'position', 'department', 'email', 'history', 'salary']" :key="field">
                  <label class="form-label">{{ field.charAt(0).toUpperCase() + field.slice(1) }}:</label>
                  <input :type="field === 'email' ? 'email' : field === 'salary' ? 'number' : 'text'" v-model="form[field]" class="form-control" :required="field !== 'history'" />
                </div>
                <div class="modal-footer">
                  <button type="button" class="btn btn-secondary" @click="showForm = false">Cancel</button>
                  <button type="submit" class="btn btn-primary">Submit</button>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>


<script>
import { mapState, mapMutations } from 'vuex'

export default {
  data() {
    return {
      showForm: false,
      form: {
        name: '',
        position: '',
        department: '',
        email: '',
        history: '',
        salary: ''
      }
    }
  },
  computed: {
    ...mapState(['employeeInformation'])
  },
methods: {
  ...mapMutations(['ADD_EMPLOYEE']),
  handleFormSubmit() {
    const newEmployee = {
      employeeId: Date.now(),
      name: this.form.name,
      position: this.form.position,
      department: this.form.department,
      contact: this.form.email,
      employmentHistory: this.form.history,
      salary: parseFloat(this.form.salary)
    };
    this.ADD_EMPLOYEE(newEmployee);
    this.showForm = false;
    this.form = {
      name: '',
      position: '',
      department: '',
      email: '',
      history: '',
      salary: ''
    };
  }
}
}
</script>

<style scoped>
.employee-wrapper {
  background-color: #295c6d;
  min-height: 100vh;
  padding: 50px 20px;
  display: flex;
  justify-content: center;
  align-items: flex-start;
}

.employee-card {
  background-color: #fbf9f5;
  border-radius: 16px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.15);
  width: 100%;
  max-width: 1200px;
}


.flex-card-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 1rem;
  padding: 1rem;
}

.card-container {
  flex: 0 0 calc(20% - 1rem);
  height: 220px;
}

@media (max-width: 1200px) {
  .card-container {
    flex: 0 0 calc(25% - 1rem);
  }
}
@media (max-width: 992px) {
  .card-container {
    flex: 0 0 calc(33.33% - 1rem);
  }
}
@media (max-width: 768px) {
  .card-container {
    flex: 0 0 calc(50% - 1rem);
  }
}
@media (max-width: 576px) {
  .card-container {
    flex: 0 0 100%;
  }
}

.flip-card {
  perspective: 1000px;
  height: 100%;
}
.flip-card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  transition: transform 0.6s;
  transform-style: preserve-3d;
}
.flip-card:hover .flip-card-inner {
  transform: rotateY(180deg);
}
.flip-card-front,
.flip-card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  border-radius: 10px;
}
.flip-card-front {
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 1rem;
}
.flip-card-back {
  transform: rotateY(180deg);
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 1rem;
}
.btn-request-leave {
  background-color: #708090;
  color: white;
  border: none;
  padding: 0.6rem 1.2rem;
  font-weight: bold;
  border-radius: 8px;
  transition: background-color 0.3s ease;
}

.btn-request-leave:hover {
  background-color: #5a6770;
}
.dashboard-title {
  color: #ffffff;
  background-color: #295c6d;
  padding: 12px 20px;
  border-radius: 10px;
  margin-bottom: 20px;
}
.flip-card-front .card-title {
  font-size: 0.95rem;
}

.flip-card-front .card-subtitle {
  font-size: 0.75rem;
}

.flip-card-back p {
  font-size: 0.75rem;
  line-height: 1.2rem;
}
</style>
