---
layout: page
title: monitor
permalink: /monitor/
nav: true
nav_order: 10
---

<h2>📡 Incubation Room Monitor</h2>
<canvas id="historyChart" width="800" height="400"></canvas>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script>
fetch("/data/history.json")
  .then(res => res.json())
  .then(data => {
    const now = Date.now();
    const cutoff = now - 24 * 60 * 60 * 1000; // time 24 hours ago

    // Filter only the latest 24 hours
    const filtered = data.filter(d => new Date(d.timestamp).getTime() > cutoff);

    const labels = filtered.map(d => new Date(d.timestamp).toLocaleTimeString());
    const temps = filtered.map(d => d.temperature);
    const hums = filtered.map(d => d.humidity);
    const lights = filtered.map(d => d.lightLevel);

    new Chart(document.getElementById('historyChart').getContext('2d'), {
      type: 'line',
      data: {
        labels: labels,
        datasets: [
          { label: 'Temperature (°C)', data: temps, borderColor: 'red', tension: 0.3 },
          { label: 'Humidity (%)', data: hums, borderColor: 'blue', tension: 0.3 },
          { label: 'Light Level', data: lights, borderColor: 'orange', tension: 0.3 }
        ]
      },
      options: {
        responsive: true,
        plugins: {
          title: {
            display: true,
            text: 'Monitoring data for the past 24 hours'
          }
        },
        scales: {
          x: {
            title: { display: true, text: 'time' },
            ticks: { maxRotation: 45, minRotation: 45 }
          },
          y: {
            title: { display: true, text: 'value' },
            beginAtZero: true
          }
        }
      }
    });
  })
  .catch(err => {
    document.getElementById("historyChart").outerHTML = "⚠ Error：" + err;
  });
</script>