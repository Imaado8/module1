<template>
  <div class="leave-requests-wrapper">
    <div class="table-card">
      <h2 class="attendance-title mb-3 text-center">Leave Requests</h2>
      <div class="text-center mb-3">
        <button class="btn btn-request-leave" @click="showModal = true">
          Request Leave
        </button>
      </div>
      <table class="styled-table d-none d-md-table">
        <thead>
          <tr>
            <th>Employee Name</th>
            <th>Leave Date</th>
            <th>Reason</th>
            <th>Status</th>
          </tr>
        </thead>
        <tbody>
          <template v-for="employee in attendanceAndLeave" :key="employee.employeeId">
            <tr
              v-for="(leave, index) in employee.leaveRequests"
              :key="leave.date + leave.reason + leave.status"
            >
              <td v-if="index === 0" :rowspan="employee.leaveRequests.length">
                {{ employee.name }}
              </td>
              <td>{{ leave.date }}</td>
              <td>{{ leave.reason }}</td>
              <td>
                <div v-if="leave.status === 'Pending'" class="d-flex gap-2">
                  <button
                    class="btn btn-sm btn-success"
                    @click="updateStatus(employee.employeeId, index, 'Approved')"
                  >
                    Accept
                  </button>
                  <button
                    class="btn btn-sm btn-danger"
                    @click="updateStatus(employee.employeeId, index, 'Denied')"
                  >
                    Deny
                  </button>
                </div>
                <span v-else :class="getStatusClass(leave.status)">
                  {{ leave.status }}
                </span>
              </td>
            </tr>
          </template>
        </tbody>
      </table>
      <div class="mobile-cards d-block d-md-none">
        <template v-for="employee in attendanceAndLeave" :key="employee.employeeId">
          <div
            v-for="(leave, index) in employee.leaveRequests"
            :key="leave.date + leave.reason + leave.status"
            class="leave-card"
          >
            <p><strong>Employee:</strong> {{ employee.name }}</p>
            <p><strong>Date:</strong> {{ leave.date }}</p>
            <p><strong>Reason:</strong> {{ leave.reason }}</p>
            <div v-if="leave.status === 'Pending'" class="d-flex gap-2">
              <button
                class="btn btn-sm btn-success"
                @click="updateStatus(employee.employeeId, index, 'Approved')"
              >
                Accept
              </button>
              <button
                class="btn btn-sm btn-danger"
                @click="updateStatus(employee.employeeId, index, 'Denied')"
              >
                Deny
              </button>
            </div>
            <span v-else :class="getStatusClass(leave.status)">
              {{ leave.status }}
            </span>
          </div>
        </template>
      </div>
    </div>
    <div
      class="modal fade show d-block"
      v-if="showModal"
      tabindex="-1"
      style="background-color: rgba(0, 0, 0, 0.5)"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Request Leave</h5>
            <button type="button" class="btn-close" @click="showModal = false"></button>
          </div>
          <div class="modal-body">
            <form @submit.prevent="submitLeave">
              <div class="mb-3">
                <label class="form-label">Employee Name</label>
                <input v-model="form.name" class="form-control" required />
              </div>
              <div class="mb-3">
                <label class="form-label">Leave Date</label>
                <input type="date" v-model="form.date" class="form-control" required />
              </div>
              <div class="mb-3">
                <label class="form-label">Reason</label>
                <input v-model="form.reason" class="form-control" required />
              </div>
              <div class="modal-footer">
                <button type="button" class="btn btn-secondary" @click="showModal = false">
                  Cancel
                </button>
                <button type="submit" class="btn btn-primary">Submit</button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState } from "vuex";

export default {
  data() {
    return {
      showModal: false,
      form: { name: "", date: "", reason: "" },
    };
  },
  computed: {
    ...mapState(["attendanceAndLeave"]),
  },
  methods: {
    getStatusClass(status) {
      const normalized = status.toLowerCase();
      if (normalized === "approved") return "badge-approved";
      if (normalized === "pending") return "badge-pending";
      if (normalized === "rejected" || normalized === "denied") return "badge-rejected";
      return "";
    },
    submitLeave() {
      const employee = this.attendanceAndLeave.find(
        (emp) => emp.name.toLowerCase() === this.form.name.toLowerCase()
      );
      if (!employee) return alert("Employee not found.");

      this.$store.dispatch("requestLeave", {
        employeeId: employee.employeeId,
        newRequest: {
          date: this.form.date,
          reason: this.form.reason,
          status: "Pending",
        },
      });
      this.showModal = false;
      this.form = { name: "", date: "", reason: "" };
    },
    updateStatus(employeeId, index, newStatus) {
      const employee = this.attendanceAndLeave.find((e) => e.employeeId === employeeId);
      if (employee?.leaveRequests?.[index]) {
        employee.leaveRequests[index].status = newStatus;
      }
    },
  },
};
</script>

<style scoped>
.leave-requests-wrapper {
  padding: 2rem 1rem;
  padding-left: 170px;
  background: #225c70;
  min-height: 100vh;
  color: #333;
}

@media (max-width: 768px) {
  .leave-requests-wrapper {
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
  width: 100%;
  max-width: 900px;
  margin: 0 auto;
}

.styled-table {
  width: 100%;
  border-collapse: separate;
  border-spacing: 0 0.5rem;
  font-size: 1rem;
  color: #333;
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

.badge-approved,
.badge-pending,
.badge-rejected {
  padding: 6px 12px;
  border-radius: 999px;
  font-weight: bold;
  display: inline-block;
  color: white;
}

.badge-approved {
  background-color: #10b981;
}
.badge-pending {
  background-color: #f59e0b;
}
.badge-rejected {
  background-color: #ef4444;
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

.mobile-cards .leave-card {
  background: #fbf9f5;
  border-radius: 10px;
  padding: 1rem;
  margin-bottom: 1rem;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}
</style>
