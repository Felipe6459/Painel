<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<title>Painel IPTV PRO</title>

<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js"></script>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<style>
body {
  margin: 0;
  font-family: Arial;
  background: #0f172a;
  color: white;
}

header {
  background: #1e3a8a;
  padding: 20px;
  text-align: center;
  font-size: 22px;
}

.container {
  padding: 20px;
}

.cards {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
}

.box {
  background: #1e293b;
  padding: 15px;
  border-radius: 10px;
  flex: 1;
  text-align: center;
}

.card {
  background: #1e293b;
  padding: 15px;
  margin-top: 10px;
  border-radius: 10px;
}

input, button {
  padding: 10px;
  margin: 5px;
  border-radius: 5px;
  border: none;
}

button {
  background: #2563eb;
  color: white;
}

.delete {
  background: red;
}
</style>
</head>

<body>

<header>Painel IPTV PRO</header>

<div class="container">

<div class="cards">
  <div class="box">Total<br><span id="total">0</span></div>
  <div class="box">Ativos<br><span id="ativos">0</span></div>
  <div class="box">Vencidos<br><span id="vencidos">0</span></div>
</div>

<canvas id="grafico"></canvas>

<h3>Adicionar Cliente</h3>

<input id="nome" placeholder="Nome">
<input id="whatsapp" placeholder="WhatsApp">
<input id="plano" placeholder="Plano">
<input id="inicio" placeholder="Data início">
<input id="vencimento" placeholder="Vencimento">
<input id="status" placeholder="Status">
<input id="assinatura" placeholder="Assinatura">

<button onclick="adicionar()">Adicionar</button>

<h3>Clientes</h3>
<div id="lista">Carregando...</div>

</div>

<script>
const supabaseUrl = "https://nghgqcgsuyyytrpfvfzh.supabase.co";
const supabaseKey = "sb_publishable_fsnaUk2uQmlq0d5r7MwFnA_FoO-wYkf";

const client = supabase.createClient(supabaseUrl, supabaseKey);

let chart;

async function carregar() {
  const { data } = await client.from("Painel ftv").select("*");

  let total = data.length;
  let ativos = data.filter(c => c.status === "ativo").length;
  let vencidos = data.filter(c => c.status === "vencido").length;

  document.getElementById("total").innerText = total;
  document.getElementById("ativos").innerText = ativos;
  document.getElementById("vencidos").innerText = vencidos;

  let planos = {};
  data.forEach(c => {
    planos[c.plano] = (planos[c.plano] || 0) + 1;
  });

  let labels = Object.keys(planos);
  let valores = Object.values(planos);

  if (chart) chart.destroy();

  const ctx = document.getElementById("grafico").getContext("2d");
  chart = new Chart(ctx, {
    type: 'bar',
    data: {
      labels: labels,
      datasets: [{
        label: 'Planos',
        data: valores
      }]
    }
  });

  let html = "";

  data.forEach(c => {
    html += `
      <div class="card">
        <p><strong>${c.nome}</strong></p>
        <p>${c["WhatsApp"]}</p>
        <p>${c.plano}</p>
        <p>${c.vencimento}</p>
        <p>${c.status}</p>
        <button class="delete" onclick="deletar(${c.id})">Excluir</button>
      </div>
    `;
  });

  document.getElementById("lista").innerHTML = html;
}

async function adicionar() {
  await client.from("Painel ftv").insert([{
    nome: document.getElementById("nome").value,
    "WhatsApp": document.getElementById("whatsapp").value,
    plano: document.getElementById("plano").value,
    data_de_inicio: document.getElementById("inicio").value,
    vencimento: document.getElementById("vencimento").value,
    status: document.getElementById("status").value,
    assinatura: document.getElementById("assinatura").value
  }]);

  carregar();
}

async function deletar(id) {
  await client.from("Painel ftv").delete().eq("id", id);
  carregar();
}

carregar();
</script>

</body>
</html>
