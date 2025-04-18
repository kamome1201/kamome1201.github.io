---
layout: page
title: 培養室監視
permalink: /monitor/
nav: true
nav_order: 10
---

<h2>📡 培養室モニター</h2>
<canvas id="historyChart" width="800" height="400"></canvas>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script>
fetch("/data/history.json")
  .then(res => res.json())
  .then(data => {
    const now = Date.now();
    const cutoff = now - 24 * 60 * 60 * 1000; // 24時間前の時刻

    // 最新24時間分のみフィルター
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
            text: '過去24時間のモニタリングデータ'
          }
        },
        scales: {
          x: {
            title: { display: true, text: '時間' },
            ticks: { maxRotation: 45, minRotation: 45 }
          },
          y: {
            title: { display: true, text: '値' },
            beginAtZero: true
          }
        }
      }
    });
  })
  .catch(err => {
    document.getElementById("historyChart").outerHTML = "⚠ グラフ表示エラー：" + err;
  });

</script>