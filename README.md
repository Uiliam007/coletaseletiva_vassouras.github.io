# coletaseletiva_vassouras.github.io
Coleta Seletiva Municipal 
# Criar versão corrigida do site com funcionamento gráfico garantido (Leaflet + CSS completos)

import zipfile, os

base = "/mnt/data/coleta_seletiva_site_corrigido"
os.makedirs(base, exist_ok=True)

index_html = """<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <title>Gerenciamento de Coleta Seletiva</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />

  <!-- Leaflet CSS -->
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"/>

  <!-- Estilo local -->
  <link rel="stylesheet" href="css/style.css" />
</head>
<body>

<header>
  Sistema de Gerenciamento da Coleta Seletiva
</header>

<div id="map"></div>

<div class="panel">
  <h3>Registrar Situação da Coleta</h3>

  <label for="status">Situação do Caminhão</label>
  <select id="status">
    <option value="coletado">✅ Resíduo entregue ao caminhão</option>
    <option value="nao_passou">❌ Caminhão não passou</option>
  </select>

  <label for="obs">Observações</label>
  <textarea id="obs" placeholder="Ex: caminhão atrasou, rua bloqueada..."></textarea>

  <button id="btn">Registrar ponto</button>
</div>

<!-- Leaflet JS -->
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

<!-- Script local -->
<script src="js/script.js"></script>

</body>
</html>
"""

style_css = """* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: Arial, Helvetica, sans-serif;
  background: #f4f6f8;
}

header {
  background: #2e7d32;
  color: white;
  text-align: center;
  padding: 15px;
  font-size: 18px;
  font-weight: bold;
}

#map {
  height: 60vh;
  width: 100%;
}

.panel {
  padding: 15px;
  background: #ffffff;
  box-shadow: 0 -2px 6px rgba(0,0,0,0.15);
}

.panel h3 {
  margin-top: 0;
}

label {
  font-weight: bold;
  display: block;
  margin-top: 10px;
}

select, textarea, button {
  width: 100%;
  padding: 10px;
  margin-top: 5px;
  font-size: 14px;
}

textarea {
  resize: vertical;
  min-height: 60px;
}

button {
  background: #2e7d32;
  color: white;
  border: none;
  cursor: pointer;
}

button:hover {
  background: #256428;
}
"""

script_js = """// Inicialização do mapa
const map = L.map('map').setView([-23.5505, -46.6333], 13);

// Camada do OpenStreetMap
L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
  maxZoom: 19,
  attribution: '© OpenStreetMap'
}).addTo(map);

// Botão
document.getElementById('btn').addEventListener('click', registrarPonto);

function registrarPonto() {
  if (!navigator.geolocation) {
    alert('Geolocalização não suportada.');
    return;
  }

  navigator.geolocation.getCurrentPosition(
    pos => {
      const lat = pos.coords.latitude;
      const lng = pos.coords.longitude;
      const status = document.getElementById('status').value;
      const obs = document.getElementById('obs').value || 'Sem observações';

      const cor = status === 'coletado' ? 'green' : 'red';
      const texto = status === 'coletado'
        ? 'Resíduo entregue ao caminhão'
        : 'Caminhão não passou';

      const marker = L.circleMarker([lat, lng], {
        radius: 8,
        color: cor,
        fillColor: cor,
        fillOpacity: 0.8
      }).addTo(map);

      marker.bindPopup(
        `<strong>Status:</strong> ${texto}<br>
         <strong>Observação:</strong> ${obs}`
      ).openPopup();

      map.setView([lat, lng], 16);
      document.getElementById('obs').value = '';
    },
    () => {
      alert('Erro ao obter localização. Permita o acesso ao GPS.');
    }
  );
}
"""

os.makedirs(f"{base}/css", exist_ok=True)
os.makedirs(f"{base}/js", exist_ok=True)

open(f"{base}/index.html", "w", encoding="utf-8").write(index_html)
open(f"{base}/css/style.css", "w", encoding="utf-8").write(style_css)
open(f"{base}/js/script.js", "w", encoding="utf-8").write(script_js)

zip_path = "/mnt/data/site_coleta_seletiva_FUNCIONAL.zip"
with zipfile.ZipFile(zip_path, "w") as zipf:
    for folder, _, files in os.walk(base):
        for file in files:
            zipf.write(
                os.path.join(folder, file),
                os.path.relpath(os.path.join(folder, file), base)
            )

zip_path

