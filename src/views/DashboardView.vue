<template>
  <div class="dashboard-wrapper">
    <div class="dashboard-card">
      <div class="d-flex flex-column align-items-center mb-4">
        <h2 class="dashboard-title text-center">HR Dashboard</h2>
        <!-- <p class="text-center text-white">Your HR system at a glance</p> -->
        <button @click="refreshData" class="btn btn-sm refresh-button mt-2">
          <i class="bi bi-arrow-clockwise"></i> Refresh
        </button>
      </div>

      <div class="row mb-4">
        <div class="col-md-3" v-for="item in stats" :key="item.label">
          <div class="card summary-card text-white">
            <div class="card-body text-center">
              <h5 class="card-title">{{ item.label }}</h5>
              <p class="card-text fs-4">{{ item.value }}</p>
            </div>
          </div>
        </div>
      </div>

      <div class="welcome-message text-center mb-4">
        <h4>Welcome Back!</h4>
        <p>Have a productive day managing your HR system</p>
      </div>

      <div class="chart-container mb-3">
        <AttendanceChart :stats="attendanceStats" />
      </div>
    </div>
  </div>
</template>


<script>
import { mapState } from 'vuex'
import AttendanceChart from "@/components/AttendanceChart.vue"

export default {
  components: {
    AttendanceChart,
  },
  computed: {
    ...mapState(['employeeInformation', 'attendanceAndLeave']),

    attendanceStats() {
      let present = 0
      let absent = 0
      let leave = 0

      this.attendanceAndLeave.forEach(employee => {
        if (Array.isArray(employee.leaveRequests)) {
          employee.leaveRequests.forEach(req => {
            const status = req.status?.toLowerCase()
            if (status === 'approved') leave++
            else if (status === 'rejected') absent++
            else if (status === 'present') present++
          })
        }
      })

      return { present, absent, leave }
    },

    stats() {
      const totalEmployees = this.employeeInformation.length
      let employeesOnLeave = 0
      let pendingLeaveDays = 0
      let pendingPayments = 3
    

      this.attendanceAndLeave.forEach(employee => {
        if (Array.isArray(employee.leaveRequests)) {
          employee.leaveRequests.forEach(req => {
            const status = req.status?.toLowerCase()
            if (status === 'approved') employeesOnLeave++
            if (status === 'pending') pendingLeaveDays++
          })
        }
      })

      return [
        { label: "Total Employees", value: totalEmployees },
        { label: "Employees on Leave", value: employeesOnLeave },
        { label: "Pending Leave Days", value: pendingLeaveDays },
        { label: "Pending Payments", value: pendingPayments }
      ]
    }
  },
  methods: {
    refreshData() {
      console.log("Refreshing (reactive data, no action needed)")
    }
  }
}
</script>


<style scoped>
.dashboard-wrapper {
  background-color: #295c6d;
  min-height: 100vh;
  padding: 50px 20px;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  /* color: #333; */
}

.dashboard-card {
  background-color: #fbf9f5;
  border-radius: 16px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.15);
  padding: 40px 30px;
  width: 100%;
  max-width: 1100px;
  box-shadow: #4a4a4a;
}

.dashboard-title {
  color: #ffffff;
  background-color: #295c6d;
  padding: 12px 20px;
  border-radius: 10px;
  margin-bottom: 20px;
}

.summary-card {
  background-color: #295c6d !important;
  border-radius: 12px;
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.1);
}

.welcome-message h4 {
  font-weight: bold;
  color: #295c6d;
}

.welcome-message p {
  color: #4a4a4a;
}
.summary-card {
  background-color: #fbf9f5 !important;
  border-radius: 12px;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.2);
  transition: transform 0.2s ease, box-shadow 0.2s ease;
  color: #333;
}

.summary-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 30px rgba(0, 0, 0, 0.25);
  /* background-color: #f0f0f0; */
  /* color: #222; */
}

.summary-card .card-title {
  color: #222;
  font-weight: 600;
}

.summary-card .card-text {
  color: #555;
  font-size: 1.5rem;
  font-weight: bold;
}
.refresh-button {
  background-color: #5a6770;
  color: white;
  border: none;
  transition: background-color 0.3s;
}

.refresh-button:hover {
  background-color: #48525a;
  color: white;
  /* box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); */
  /* transform: translateY(-2px); */
}
.chart-container {
  max-width: 300px;
  margin: 0 auto;
}
</style>
