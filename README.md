<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<title>Painel IPTV PRO++</title>

<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js"></script>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<style>
body { margin:0; font-family:Arial; background:#0f172a; color:white; }
header { background:#1e3a8a; padding:20px; text-align:center; }

.container { padding:20px; }

.cards { display:flex; flex-wrap:wrap; gap:10px; }

.box {
  background:#1e293b;
  padding:15px;
  border-radius:10px;
  flex:1;
  text-align:center;
}

input, button {
  padding:10px;
  margin:5px;
  border:none;
  border-radius:5px;
}

button { background:#2563eb; color:white; cursor:pointer; }

.card {
  background:#1e293b;
  padding:15px;
  margin-top:10px;
  border-radius:10px;
}

.ativo { border-left:5px solid green; }
.vencido { border-left:5px solid red; }
.aviso { border-left:5px solid orange; }

.delete { background:red; }
.edit { background:orange; }
</style>
</head>

<body>

<header>Painel IPTV PRO++</header>

<div class="container">

<div class="cards">
  <div class="box">Total<br><span id="total">0</span></div>
  <div class="box">Ativos<br><span id="ativos">0</span></div>
  <div class="box">Vencendo<br><span id="aviso">0</span></div>
  <div class="box">Vencidos<br><span id="vencidos">0</span></div>
  <div class="box">💰 Receita Total<br>R$ <span id="receita">0</span></div>
  <div class="box">📅 A Receber<br>R$ <span id="receber">0</span></div>
</div>

<canvas id="grafico"></canvas>

<h3>Buscar</h3>
<input id="busca" oninput="carregar()" placeholder="Nome...">

<h3>Adicionar / Editar Cliente</h3>

<input id="nome" placeholder="Nome">
<input id="whatsapp" placeholder="WhatsApp">
<input id="plano" placeholder="Plano">
<input id="valor" type="number" placeholder="Valor (R$)">
<input type="date" id="inicio">
<input type="date" id="vencimento">

<button onclick="adicionar()">Salvar</button>

<h3>Clientes</h3>
<div id="lista"></div>

</div>

<script>
const supabaseUrl = "https://nghgqcgsuyyytrpfvfzh.supabase.co";
const supabaseKey = "sb_publishable_fsnaUk2uQmlq0d5r7MwFnA_FoO-wYkf";

const client = supabase.createClient(supabaseUrl, supabaseKey);

let chart;
let editandoId = null;

function statusCalc(v) {
  let hoje = new Date();
  let d = new Date(v);
  let diff = (d - hoje) / (1000*60*60*24);

  if (diff < 0) return "vencido";
  if (diff <= 3) return "aviso";
  return "ativo";
}

async function carregar() {
  const busca = document.getElementById("busca").value.toLowerCase();
  const { data, error } = await client.from("Painel ftv").select("*");

  if (error) {
    alert(error.message);
    return;
  }

  let total=0, ativos=0, vencidos=0, aviso=0;
  let receita=0, receber=0;
  let planos={};
  let html="";

  data.forEach(c=>{
    if (busca && !c.nome.toLowerCase().includes(busca)) return;

    let status = statusCalc(c.vencimento);

    total++;
    if(status==="ativo") ativos++;
    if(status==="vencido") vencidos++;
    if(status==="aviso") aviso++;

    receita += Number(c.valor || 0);

    if(status!=="vencido") {
      receber += Number(c.valor || 0);
    }

    planos[c.plano]=(planos[c.plano]||0)+1;

    html+=`
    <div class="card ${status}">
      <p><b>${c.nome}</b></p>
      <p>${c.whatsapp}</p>
      <p>${c.plano} - R$ ${c.valor}</p>
      <p>Vence: ${c.vencimento}</p>
      <button class="edit" onclick="editar(${c.id})">Editar</button>
      <button class="delete" onclick="deletar(${c.id})">Excluir</button>
    </div>`;
  });

  document.getElementById("total").innerText=total;
  document.getElementById("ativos").innerText=ativos;
  document.getElementById("vencidos").innerText=vencidos;
  document.getElementById("aviso").innerText=aviso;
  document.getElementById("receita").innerText=receita.toFixed(2);
  document.getElementById("receber").innerText=receber.toFixed(2);

  document.getElementById("lista").innerHTML=html;

  if(chart) chart.destroy();

  chart=new Chart(document.getElementById("grafico"),{
    type:"bar",
    data:{
      labels:Object.keys(planos),
      datasets:[{ data:Object.values(planos) }]
    }
  });
}

async function adicionar() {
  const dados={
    id: editandoId || Date.now(),
    nome: nome.value,
    whatsapp: whatsapp.value,
    plano: plano.value,
    valor: valor.value ? Number(valor.value) : 0,
    data_de_inicio: inicio.value,
    vencimento: vencimento.value
  };

  if (!dados.nome || !dados.whatsapp || !dados.plano || !dados.vencimento) {
    alert("Preencha os campos obrigatórios!");
    return;
  }

  const { error } = editandoId
    ? await client.from("Painel ftv").update(dados).eq("id", editandoId)
    : await client.from("Painel ftv").insert([dados]);

  if (error) {
    alert("Erro: " + error.message);
    console.log(error);
    return;
  }

  alert("Salvo com sucesso!");

  editandoId = null;
  limpar();
  carregar();
}

function editar(id){
  editandoId=id;
  alert("Edite os campos acima e clique em SALVAR");
}

async function deletar(id){
  await client.from("Painel ftv").delete().eq("id", id);
  carregar();
}

function limpar(){
  nome.value="";
  whatsapp.value="";
  plano.value="";
  valor.value="";
  inicio.value="";
  vencimento.value="";
}

carregar();
</script>

</body>
</html>
