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

input, select, button {
  padding: 10px;
  margin: 5px;
  border-radius: 5px;
  border: none;
}

button {
  background: #2563eb;
  color: white;
  cursor: pointer;
}

.card {
  background: #1e293b;
  padding: 15px;
  margin-top: 10px;
  border-radius: 10px;
}

.ativo { border-left: 5px solid green; }
.vencido { border-left: 5px solid red; }
.aviso { border-left: 5px solid orange; }

.delete { background: red; }
</style>
</head>

<body>

<header>Painel IPTV PRO</header>

<div class="container">

<div class="cards">
  <div class="box">Total<br><span id="total">0</span></div>
  <div class="box">Ativos<br><span id="ativos">0</span></div>
  <div class="box">Vencendo<br><span id="aviso">0</span></div>
  <div class="box">Vencidos<br><span id="vencidos">0</span></div>
</div>

<canvas id="grafico"></canvas>

<h3>Buscar</h3>
<input id="busca" placeholder="Digite o nome..." oninput="carregar()">

<h3>Adicionar Cliente</h3>

<input id="nome" placeholder="Nome">
<input id="whatsapp" placeholder="WhatsApp">
<input id="plano" placeholder="Plano">
<input type="date" id="inicio">
<input type="date" id="vencimento">
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

function calcularStatus(dataVencimento) {
  const hoje = new Date();
  const venc = new Date(dataVencimento);
  const diff = (venc - hoje) / (1000 * 60 * 60 * 24);

  if (diff < 0) return "vencido";
  if (diff <= 3) return "aviso";
  return "ativo";
}

async function carregar() {
  const busca = document.getElementById("busca").value.toLowerCase();

  const { data } = await client.from("Painel ftv").select("*");

  let total = 0, ativos = 0, vencidos = 0, aviso = 0;
  let planos = {};

  let html = "";

  data.forEach(c => {
    if (busca && !c.nome.toLowerCase().includes(busca)) return;

    const status = calcularStatus(c.vencimento);

    total++;
    if (status === "ativo") ativos++;
    if (status === "vencido") vencidos++;
    if (status === "aviso") aviso++;

    planos[c.plano] = (planos[c.plano] || 0) + 1;

    html += `
      <div class="card ${status}">
        <p><strong>${c.nome}</strong></p>
        <p>${c.whatsapp}</p>
        <p>${c.plano}</p>
        <p>Vence: ${c.vencimento}</p>
        <button class="delete" onclick="deletar(${c.id})">Excluir</button>
      </div>
    `;
  });

  document.getElementById("total").innerText = total;
  document.getElementById("ativos").innerText = ativos;
  document.getElementById("vencidos").innerText = vencidos;
  document.getElementById("aviso").innerText = aviso;

  document.getElementById("lista").innerHTML = html;

  if (chart) chart.destroy();

  const ctx = document.getElementById("grafico").getContext("2d");
  chart = new Chart(ctx, {
    type: 'bar',
    data: {
      labels: Object.keys(planos),
      datasets: [{
        label: 'Planos',
        data: Object.values(planos)
      }]
    }
  });
}

async function adicionar() {
  await client.from("Painel ftv").insert([{
    nome: document.getElementById("nome").value,
    whatsapp: document.getElementById("whatsapp").value,
    plano: document.getElementById("plano").value,
    data_de_inicio: document.getElementById("inicio").value,
    vencimento: document.getElementById("vencimento").value,
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
