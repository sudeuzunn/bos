<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <title>Çöp Kutusu Kaydı - Demo</title>
  <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
  <style>
    #map { height: 500px; margin-top: 10px; }
    #preview { display:none; margin-top:10px; border:1px solid #ccc; }
  </style>
</head>
<body>
  <h2>📍 Çöp Kutusu Kaydı (Demo)</h2>

  <form id="form">
    <label>Fotoğraf:</label><br>
    <input type="file" accept="image/*" capture="environment" id="photo" required><br><br>
    <button type="submit">Kaydet</button>
  </form>

  <h3>Son Yüklenen:</h3>
  <p id="coords">Henüz konum alınmadı...</p>
  <img id="preview" width="300">

  <h3>Harita:</h3>
  <div id="map"></div>

  <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
  <script>
    let map, markers = [];

    map = L.map('map').setView([39.0, 35.0], 6);
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      maxZoom: 19,
      attribution: '© OpenStreetMap'
    }).addTo(map);

    document.getElementById("form").addEventListener("submit", function(e) {
      e.preventDefault();
      let file = document.getElementById("photo").files[0];
      if (!file) return alert("Lütfen bir fotoğraf seçin!");

      let reader = new FileReader();
      reader.onload = function(ev) {
        navigator.geolocation.getCurrentPosition(function(pos) {
          let lat = pos.coords.latitude;
          let lng = pos.coords.longitude;

          document.getElementById("coords").innerText =
            "Enlem: " + lat + " | Boylam: " + lng;

          let img = document.getElementById("preview");
          img.src = ev.target.result;
          img.style.display = "block";

          let marker = L.marker([lat, lng]).addTo(map)
            .bindPopup("<b>Çöp Kutusu 📍</b><br>" +
                       "Enlem: " + lat + "<br>Boylam: " + lng + "<br>" +
                       "<img src='" + ev.target.result + "' width='150'>");
          markers.push(marker);

          map.setView([lat, lng], 16);
        }, function(error) {
          alert("Konum alınamadı: " + error.message);
        });
      };
      reader.readAsDataURL(file);

      document.getElementById("form").reset();
    });
  </script>
</body>
</html>

