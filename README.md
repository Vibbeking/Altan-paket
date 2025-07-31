# Altan-paket
<!DOCTYPE html>
<html lang="sv">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Altanpaket – Bloms Trä</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 20px; }
    h1, h2 { color: #2c3e50; }
    select, input[type=checkbox] { margin: 10px 0; }
    table { border-collapse: collapse; width: 100%; margin-top: 15px; }
    th, td { border: 1px solid #ccc; padding: 8px; }
    .step { cursor: pointer; background: #eef; padding: 10px; margin: 5px 0; }
    .step-content { display: none; margin-left: 10px; }
    .visuellbild { max-width: 100%; margin-top: 20px; border: 1px solid #ddd; }
    .offert-box { border: 1px dashed #aaa; padding: 10px; margin-top: 20px; background: #f9f9f9; }
    button { margin-top: 10px; padding: 10px 15px; cursor: pointer; }
  </style>
</head>
<body>

<h1>Altanpaket – Bloms Trä</h1>

<h2>Välj altanstorlek</h2>
<select id="sizeSelector">
  <option value="10">10 kvm</option>
  <option value="15" selected>15 kvm</option>
  <option value="20">20 kvm</option>
</select>

<h2>Tillval</h2>
<label><input type="checkbox" id="railing" /> Räcke</label><br>
<label><input type="checkbox" id="stairs" /> Trappa</label><br>
<label><input type="checkbox" id="roof" /> Skärmtak</label>

<h2>Visuell skiss (förenklad)</h2>
<img id="skissBild" class="visuellbild" src="" alt="Visuell skiss av altan" />

<h2>Materiallista</h2>
<table id="materialTable">
  <thead>
    <tr><th>Material</th><th>Antal</th></tr>
  </thead>
  <tbody></tbody>
</table>

<h2>Pris</h2>
<p id="priceDisplay"><strong>Pris:</strong> 0 kr</p>

<button onclick="downloadPDF()">📄 Ladda ner PDF</button>
<button onclick="generateQuote()">📋 Generera offert</button>

<div class="offert-box" id="offertBox" style="display:none;">
  <h3>Offert</h3>
  <p id="offertText"></p>
</div>

<h2>Monteringssteg</h2>
<div class="step" onclick="toggleStep(1)">1. Förbered marken</div>
<div class="step-content" id="step1">Gräv ur och jämna till ytan där altanen ska stå.</div>

<div class="step" onclick="toggleStep(2)">2. Lägg grunden</div>
<div class="step-content" id="step2">Placera plintar enligt ritning. Kontrollera nivåer.</div>

<div class="step" onclick="toggleStep(3)">3. Montera regelverket</div>
<div class="step-content" id="step3">Fäst bärlinor och reglar med vinkeljärn och skruv.</div>

<div class="step" onclick="toggleStep(4)">4. Lägg trall</div>
<div class="step-content" id="step4">Skruva fast trallbrädor med 5 mm mellanrum.</div>

<script>
const priser = {10: 7900, 15: 10900, 20: 13900};
const materialdata = {
  10: [{namn: "Trall 28x120", antal: 25}, {namn: "Regel 45x145", antal: 12}],
  15: [{namn: "Trall 28x120", antal: 35}, {namn: "Regel 45x145", antal: 16}],
  20: [{namn: "Trall 28x120", antal: 45}, {namn: "Regel 45x145", antal: 20}]
};
const tillval = {railing: 1200, stairs: 1500, roof: 3000};

function updatePage() {
  const size = document.getElementById("sizeSelector").value;
  const railing = document.getElementById("railing").checked;
  const stairs = document.getElementById("stairs").checked;
  const roof = document.getElementById("roof").checked;

  // Pris
  let pris = priser[size];
  if (railing) pris += tillval.railing;
  if (stairs) pris += tillval.stairs;
  if (roof) pris += tillval.roof;
  document.getElementById("priceDisplay").innerHTML = `<strong>Pris:</strong> ${pris.toLocaleString()} kr`;

  // Material
  const tbody = document.querySelector("#materialTable tbody");
  tbody.innerHTML = "";
  materialdata[size].forEach(row => {
    const tr = document.createElement("tr");
    tr.innerHTML = `<td>${row.namn}</td><td>${row.antal}</td>`;
    tbody.appendChild(tr);
  });

  // Visuell bild
  const bild = document.getElementById("skissBild");
  bild.src = `https://via.placeholder.com/600x200?text=Altan+${size}+kvm${railing ? "+räcke" : ""}${stairs ? "+trappa" : ""}${roof ? "+tak" : ""}`;
}

function toggleStep(nr) {
  const content = document.getElementById("step" + nr);
  content.style.display = content.style.display === "block" ? "none" : "block";
}

function downloadPDF() {
  window.open("Altanpaket_15kvm_BlomsTra.pdf", "_blank");
}

function generateQuote() {
  const size = document.getElementById("sizeSelector").value;
  const railing = document.getElementById("railing").checked;
  const stairs = document.getElementById("stairs").checked;
  const roof = document.getElementById("roof").checked;
  const pris = priser[size] +
    (railing ? tillval.railing : 0) +
    (stairs ? tillval.stairs : 0) +
    (roof ? tillval.roof : 0);

  const tillvalText = [
    railing ? "✔ Räcke" : "",
    stairs ? "✔ Trappa" : "",
    roof ? "✔ Skärmtak" : ""
  ].filter(Boolean).join(", ");

  document.getElementById("offertBox").style.display = "block";
  document.getElementById("offertText").innerHTML = `
    <strong>Valt paket:</strong> ${size} kvm<br>
    <strong>Tillval:</strong> ${tillvalText || "Inga"}<br>
    <strong>Totalpris:</strong> ${pris.toLocaleString()} kr inkl. moms
  `;
}

// Event listeners
document.getElementById("sizeSelector").addEventListener("change", update