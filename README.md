
<html lang="bn">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>নামায রিমাইন্ডার</title>
  <link rel="manifest" href="manifest.json" />
  <link rel="icon" href="icons/icon.png" />
  <style>
    body {
      font-family: sans-serif;
      background: #f0f9ff;
      text-align: center;
      padding: 2rem;
    }
    h1 {
      color: #007bff;
    }
    table {
      margin: 1rem auto;
      border-collapse: collapse;
    }
    th, td {
      border: 1px solid #ccc;
      padding: 0.5rem 1rem;
    }
    .time {
      font-size: 1.2rem;
      color: green;
    }
  </style>
</head>
<body>
  <h1>🕌 নামাযের সময়সূচী</h1>
  <p>আজকের সময় (লোকেশন অনুযায়ী)</p>
  <table id="prayer-table">
    <thead>
      <tr><th>নামায</th><th>সময়</th></tr>
    </thead>
    <tbody></tbody>
  </table>

  <audio id="azan-audio" src="https://cdn.islamic.network/audio/azan.mp3" preload="auto"></audio>

  <script>
    const prayers = ['Fajr', 'Dhuhr', 'Asr', 'Maghrib', 'Isha'];
    const tableBody = document.querySelector("#prayer-table tbody");
    const azanAudio = document.getElementById("azan-audio");

    navigator.geolocation.getCurrentPosition(async (pos) => {
      const { latitude, longitude } = pos.coords;
      const url = `https://api.aladhan.com/v1/timings?latitude=${latitude}&longitude=${longitude}&method=2`;

      const res = await fetch(url);
      const data = await res.json();
      const timings = data.data.timings;

      prayers.forEach(prayer => {
        const tr = document.createElement("tr");
        tr.innerHTML = `<td>${prayer}</td><td class="time">${timings[prayer]}</td>`;
        tableBody.appendChild(tr);

        const now = new Date();
        const [hr, min] = timings[prayer].split(":");
        const prayerTime = new Date();
        prayerTime.setHours(parseInt(hr), parseInt(min), 0, 0);

        if (prayerTime > now) {
          const delay = prayerTime - now;
          setTimeout(() => {
            alert(`${prayer} নামাযের সময় হয়েছে 🕌`);
            azanAudio.play();
          }, delay);
        }
      });
    }, () => {
      alert("লোকেশন অনুমতি দিন যেন নামাযের সময় দেখা যায়।");
    });

    // Register Service Worker
    if ('serviceWorker' in navigator) {
      navigator.serviceWorker.register('sw.js').then(() => {
        console.log('Service Worker Registered');
      });
    }
  </script>
</body>
</html>

