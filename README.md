<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<title>Painel IPTV PRO++</title>

<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js"></script>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<style>
body { margin:0; font-family:Arial; background:#0f172a; color:white; }

.login {
  display:flex;
  flex-direction:column;
  justify-content:center;
  align-items:center;
  height:100vh;
}

input, button {
  padding:10px;
  margin:5px;
  border:none;
  border-radius:5px;
}

button { background:#2563eb; color:white; cursor:pointer; }

header {
  background:#1e3a8a;
  padding:20px;
  text-align:center;
}

.container { padding:20px; }

.cards { display:flex; flex-wrap:wrap; gap:10px; }

.box {
  background:#1e293b;
  padding:15px;
  border-radius:10px;
  flex:1;
  text-align:center;
}

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
.logout { background:black; }
</style>
</head>

<body>

<!-- LOGIN -->
<div id="login" class="login">
  <h2>Login</h2>
  <input id="user" placeholder="Usuário">
  <input id="pass" type="password" placeholder="Senha">
  <button onclick="entrar()">Entrar</button>
</div>

<!-- PAINEL -->
<div id="painel" style="display:none">

<header>
Painel IPTV PRO++
<button class="logout" onclick="sair()">Sair</button>
</header>

<div class="container">

<div class="cards">
  <div class="box">Total<br><span id="total">0</span></div>
  <div class="box">Ativos<br><span id="ativos">0</span></div>
  <div class="box">Vencendo<br><span id="aviso">0</span></div>
  <div class="box">Vencidos<br><span id="vencidos">0</span></div>
  <div class="box">💰 Receita<br>R$ <span id="receita">0</span></div>
  <div class="box">📅 A Receber<br>R$ <span id="receber">0</span></div>
</div>

<canvas id="grafico"></canvas>

<input id="busca" oninput="carregar()" placeholder="Buscar cliente...">

<h3>Adicionar / Editar</h3>

<input id="nome" placeholder="Nome">
<input id="whatsapp" placeholder="WhatsApp">
<input id="plano" placeholder="Plano">
<input id="valor" type="number" placeholder="Valor">
<input type="date" id="inicio">
<input type="date" id="vencimento">

<button onclick="salvar()">Salvar</button>

<div id="lista"></div>

</div>
</div>

<script>
// 🔥 CORRIGIDO
const client = supabase.createClient(
  "https://nghgqcgsuyyytrpfvfzh.supabase.co",
  "sb_publishable_fsnaUk2uQmlq0d5r7MwFnA_FoO-wYkf"
);

let editandoId = null;

// LOGIN
function entrar() {
  if (user.value === "admin" && pass.value === "1234") {
    localStorage.setItem("logado", "sim");
    mostrarPainel();
  } else {
    alert("Login inválido");
  }
}

function sair() {
  localStorage.removeItem("logado");
  location.reload();
}

function mostrarPainel() {
  document.getElementById("login").style.display = "none";
  document.getElementById("painel").style.display = "block";
  carregar();
}

// manter login
if (localStorage.getItem("logado") === "sim") {
  mostrarPainel();
}

// STATUS
function statusCalc(v) {
  let hoje = new Date();
  let d = new Date(v);
  let diff = (d - hoje) / (1000*60*60*24);

  if (diff < 0) return "vencido";
  if (diff <= 3) return "aviso";
  return "ativo";
}

// CARREGAR
async function carregar() {
  const { data, error } = await client.from("Painel ftv").select("*");

  if (error) {
    alert(error.message);
    return;
  }

  let html="", t=0, a=0, v=0, av=0, r=0, rec=0;

  data.forEach(c=>{
    let status = statusCalc(c.vencimento);

    t++;
    if(status==="ativo") a++;
    if(status==="vencido") v++;
    if(status==="aviso") av++;

    r += Number(c.valor || 0);
    if(status!=="vencido") rec += Number(c.valor || 0);

    html+=`
    <div class="card ${status}">
      <b>${c.nome}</b>
      <p>${c.plano} - R$ ${c.valor}</p>
      <p>Vence: ${c.vencimento}</p>
      <button class="edit" onclick="editar(${c.id})">Editar</button>
      <button class="delete" onclick="del(${c.id})">Excluir</button>
    </div>`;
  });

  document.getElementById("total").innerText=t;
  document.getElementById("ativos").innerText=a;
  document.getElementById("vencidos").innerText=v;
  document.getElementById("aviso").innerText=av;
  document.getElementById("receita").innerText=r.toFixed(2);
  document.getElementById("receber").innerText=rec.toFixed(2);

  document.getElementById("lista").innerHTML=html;
}

// SALVAR
async function salvar() {
  const dados={
    id: editandoId || Date.now(),
    nome: nome.value,
    whatsapp: whatsapp.value,
    plano: plano.value,
    valor: parseFloat(valor.value) || 0,
    data_de_inicio: inicio.value,
    vencimento: vencimento.value
  };

  if (!dados.nome || !dados.whatsapp || !dados.plano || !dados.vencimento) {
    alert("Preencha os campos obrigatórios!");
    return;
  }

  const { error } = editandoId
    ? await client.from("Painel ftv").update(dados).eq("id",editandoId)
    : await client.from("Painel ftv").insert([dados]);

  if(error){
    alert("Erro: "+error.message);
    return;
  }

  alert("Salvo com sucesso!");
  editandoId=null;
  limpar();
  carregar();
}

// EDITAR
function editar(id){
  editandoId=id;
  alert("Edite os dados e clique em salvar");
}

// EXCLUIR
async function del(id){
  await client.from("Painel ftv").delete().eq("id",id);
  carregar();
}

// LIMPAR
function limpar(){
  nome.value="";
  whatsapp.value="";
  plano.value="";
  valor.value="";
  inicio.value="";
  vencimento.value="";
}
</script>

</body>
</html>
