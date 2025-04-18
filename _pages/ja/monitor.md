---
layout: page
title: 培養室監視
permalink: /monitor/
nav: true
nav_order: 10
---

<h2>📡 培養室モニター</h2>
<canvas id="historyChart" width="800" height="400"></canvas>

<!-- ✅ 最新データ表示エリアを追加 -->
<div id="latestValues" style="margin-top: 1em; font-weight: bold;"></div>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script>
fetch("/data/history.json")
  .then(res => res.json())
  .then(data => {
    const now = Date.now();
    const cutoff = now - 24 * 60 * 60 * 1000; // 24時間前

    const filtered = data
      .filter(d => new Date(d.timestamp).getTime() > cutoff)
      .sort((a, b) => new Date(a.timestamp) - new Date(b.timestamp)); // 昇順に整列

    const labels = filtered.map(d => new Date(d.timestamp).toLocaleTimeString());
    const temps = filtered.map(d => d.temperature);
    const hums = filtered.map(d => d.humidity);
    const lights = filtered.map(d => d.lightLevel);

    // ✅ 最新データの表示処理
    const latest = filtered[filtered.length - 1];
    const latestTime = new Date(latest.timestamp).toLocaleString("ja-JP", { hour12: false });
    document.getElementById("latestValues").innerHTML =
      `🕒 最終更新: ${latestTime}<br>` +
      `🌡 温度: ${latest.temperature} ℃　💧 湿度: ${latest.humidity} %　💡 照度: ${latest.lightLevel}`;

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
