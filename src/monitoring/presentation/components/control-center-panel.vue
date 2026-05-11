<script setup>
import { computed } from 'vue';
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  LineElement,
  PointElement,
  LinearScale,
  CategoryScale,
  Filler,
  RadialLinearScale,
  BarElement,
  ArcElement
} from 'chart.js';
import { Line, Radar, Bar, Doughnut } from 'vue-chartjs';

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  LineElement,
  PointElement,
  LinearScale,
  CategoryScale,
  Filler,
  RadialLinearScale,
  BarElement,
  ArcElement
);

const props = defineProps({
  db: {
    type: Object,
    required: true
  }
});

// Chart 1: Biometric Monitoring (Line)
const biometricData = computed(() => ({
  labels: ['00:00', '04:00', '08:00', '12:00', '16:00', '20:00'],
  datasets: [
    {
      label: 'Temperatura (°C)',
      data: [2.5, 3.2, 5.1, 4.3, 3.5, 2.8],
      borderColor: '#3b82f6',
      backgroundColor: 'rgba(59, 130, 246, 0.1)',
      fill: true,
      tension: 0.4
    },
    {
      label: 'Humedad (%)',
      data: [45, 48, 52, 50, 47, 45],
      borderColor: '#0d9488',
      backgroundColor: 'rgba(13, 148, 136, 0.1)',
      fill: true,
      tension: 0.4
    }
  ]
}));

const biometricOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: { position: 'top', align: 'end', labels: { usePointStyle: true, boxWidth: 6 } }
  },
  scales: {
    y: { grid: { borderDash: [5, 5], color: '#e2e8f0' }, ticks: { color: '#64748b' } },
    x: { grid: { display: false }, ticks: { color: '#64748b' } }
  }
};

// Chart 2: Asset Stability (Radar)
const stabilityData = computed(() => ({
  labels: ['Vibración', 'Presión', 'Partículas', 'Calidad Aire'],
  datasets: [{
    label: 'Asset Stability Index',
    data: [85, 92, 78, 88],
    borderColor: '#3b82f6',
    backgroundColor: 'rgba(59, 130, 246, 0.2)',
    pointBackgroundColor: '#3b82f6'
  }]
}));

const radarOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: { legend: { display: false } },
  scales: {
    r: {
      grid: { color: '#e2e8f0' },
      angleLines: { color: '#e2e8f0' },
      pointLabels: { color: '#64748b', font: { weight: '600' } },
      ticks: { display: false }
    }
  }
};

// Chart 3: Response Efficiency (Bar)
const efficiencyData = computed(() => {
  const sortedOperators = [...props.db.operators]
    .sort((a, b) => b.alerts_answered - a.alerts_answered)
    .slice(0, 5);
  
  return {
    labels: sortedOperators.map(o => props.db.users.find(u => u.id === o.users_id)?.name),
    datasets: [{
      label: 'Alertas Respondidas',
      data: sortedOperators.map(o => o.alerts_answered),
      backgroundColor: '#0d9488',
      borderRadius: 8,
      barThickness: 12
    }]
  };
});

const barOptions = {
  indexAxis: 'y',
  responsive: true,
  maintainAspectRatio: false,
  plugins: { legend: { display: false } },
  scales: {
    x: { grid: { display: false }, ticks: { display: false } },
    y: { grid: { display: false }, ticks: { color: '#1e293b', font: { weight: '600' } } }
  }
};

// Chart 4: Business Health (Doughnut)
const businessData = computed(() => {
  const active = props.db.subscriptions.filter(s => s.status === 'ACTIVE').length;
  const pending = props.db.subscriptions.filter(s => s.status === 'PENDING').length;
  const expired = props.db.subscriptions.filter(s => s.status === 'EXPIRED').length;
  
  return {
    labels: ['Active', 'Pending', 'Expired'],
    datasets: [{
      data: [active, pending, expired],
      backgroundColor: ['#3b82f6', '#f59e0b', '#ef4444'],
      borderWidth: 0,
      cutout: '80%'
    }]
  };
});

const doughnutOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: { legend: { display: false } }
};
</script>

<template>
  <div class="control-panel" v-if="db">
    <div class="panel-header">
      <div class="header-info">
        <h2 class="panel-title">Centro de Control Operativo</h2>
        <p class="panel-subtitle">Análisis avanzado de cadena de frío y logística crítica</p>
      </div>
      <div class="header-status">
        <div class="status-badge">
          <div class="pulse-indicator"></div>
          <span>Sistemas en Línea</span>
        </div>
      </div>
    </div>

    <div class="bento-grid">
      <!-- Main Biometric Chart -->
      <div class="bento-card span-2 main-chart">
        <div class="card-header">
          <h3>Monitoreo Biométrico</h3>
          <p>Temperatura vs Humedad (24h)</p>
        </div>
        <div class="chart-wrapper line-chart">
          <Line :data="biometricData" :options="biometricOptions" />
        </div>
      </div>

      <!-- Radar Stability -->
      <div class="bento-card stability-chart">
        <div class="card-header">
          <h3>Estabilidad de Activos</h3>
          <p>Síntesis Ambiental</p>
        </div>
        <div class="chart-wrapper">
          <Radar :data="stabilityData" :options="radarOptions" />
        </div>
      </div>

      <!-- Efficiency -->
      <div class="bento-card efficiency-chart">
        <div class="card-header">
          <h3>Eficiencia de Respuesta</h3>
          <p>Alertas por Operador</p>
        </div>
        <div class="chart-wrapper">
          <Bar :data="efficiencyData" :options="barOptions" />
        </div>
      </div>

      <!-- Business Health -->
      <div class="bento-card business-chart">
        <div class="card-header">
          <h3>Estado de Suscripciones</h3>
          <p>Salud del Negocio</p>
        </div>
        <div class="doughnut-container">
          <div class="chart-wrapper">
            <Doughnut :data="businessData" :options="doughnutOptions" />
          </div>
          <div class="doughnut-center">
            <span class="center-label">Total</span>
            <span class="center-value">{{ db.subscriptions?.length || 0 }}</span>
          </div>
        </div>
      </div>
    </div>
  </div>
  <div v-else class="panel-loading">
    <div class="loader-content">
      <i class="pi pi-spin pi-spinner" style="font-size: 2rem"></i>
      <p>Sincronizando con estaciones de monitoreo...</p>
    </div>
  </div>
</template>

<style scoped>
.control-panel {
  margin-top: 1rem;
}

.panel-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
  margin-bottom: 1.5rem;
  padding: 0 0.5rem;
}

.panel-title {
  margin: 0;
  font-size: 1.5rem;
  font-weight: 800;
  color: var(--mt-heading);
  letter-spacing: -0.02em;
}

.panel-subtitle {
  margin: 0.25rem 0 0;
  color: var(--mt-text-muted);
  font-size: 0.95rem;
}

.status-badge {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.5rem 1rem;
  background: white;
  border: 1px solid var(--mt-border);
  border-radius: 100px;
  font-size: 0.85rem;
  font-weight: 600;
  color: var(--mt-text-muted);
  box-shadow: var(--mt-shadow-sm);
}

.pulse-indicator {
  width: 8px;
  height: 8px;
  background: #10b981;
  border-radius: 50%;
  position: relative;
}

.pulse-indicator::after {
  content: '';
  position: absolute;
  inset: -4px;
  border-radius: 50%;
  border: 2px solid #10b981;
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0% { transform: scale(0.5); opacity: 1; }
  100% { transform: scale(1.5); opacity: 0; }
}

.bento-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-auto-rows: minmax(180px, auto);
  gap: 1.25rem;
}

.bento-card {
  background: white;
  border: 1px solid var(--mt-border);
  border-radius: var(--mt-radius-lg);
  padding: 1.5rem;
  box-shadow: var(--mt-shadow-sm);
  transition: all 0.3s ease;
}

.bento-card:hover {
  box-shadow: var(--mt-shadow-md);
  border-color: var(--mt-primary-soft);
}

.span-2 {
  grid-column: span 2;
  grid-row: span 2;
}

.card-header {
  margin-bottom: 1rem;
}

.card-header h3 {
  margin: 0;
  font-size: 1.1rem;
  font-weight: 700;
  color: var(--mt-heading);
}

.card-header p {
  margin: 0.25rem 0 0;
  font-size: 0.85rem;
  color: var(--mt-text-muted);
}

.chart-wrapper {
  height: 200px;
  position: relative;
}

.line-chart {
  height: 320px;
}

.doughnut-container {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}

.doughnut-center {
  position: absolute;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.center-label {
  font-size: 0.75rem;
  color: var(--mt-text-muted);
  text-transform: uppercase;
  font-weight: 600;
}

.center-value {
  font-size: 1.5rem;
  font-weight: 800;
  color: var(--mt-heading);
}

.panel-loading {
  background: white;
  border: 1px solid var(--mt-border);
  border-radius: var(--mt-radius-lg);
  padding: 4rem;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  margin-top: 1rem;
}

.loader-content {
  color: var(--mt-text-muted);
}

.loader-content p {
  margin-top: 1rem;
  font-weight: 500;
}

@media (max-width: 1200px) {
  .bento-grid { grid-template-columns: repeat(2, 1fr); }
  .span-2 { grid-row: span 1; }
}

@media (max-width: 768px) {
  .bento-grid { grid-template-columns: 1fr; }
  .span-2 { grid-column: span 1; }
  .panel-header { flex-direction: column; align-items: flex-start; gap: 1rem; }
}
</style>
