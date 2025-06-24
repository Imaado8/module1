<template>
  <div class="container mt-4">
    <h2>HR Dashboard</h2>

    <div class="row mt-4">
      <div class="col-md-3" v-for="item in stats" :key="item.label">
        <div class="card text-white bg-primary mb-3">
          <div class="card-body">
            <h5 class="card-title">{{ item.label }}</h5>
            <p class="card-text fs-4">{{ item.value }}</p>
          </div>
        </div>
      </div>
    </div>

    <div class="mt-5">
      <h4>Welcome Back!</h4>
      <p>Have a productive day managing your HR system</p>
    </div>
  </div>
    <div class="d-flex justify-content-between align-items-center mb-4">

    <button @click="refreshData" class="btn btn-sm btn-outline-secondary">
      <i class="bi bi-arrow-clockwise"></i> Refresh
    </button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      stats: [
        { label: "Total Employees", value: 10 },
        { label: "Employees on Leave", value: 8 },
        { label: "Pending Leave Days", value: 2 },
        { label: "Pending Payments", value: 3 }
      ]
    }
  }
}
</script>

<style scoped>
.card {
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}
</style>
