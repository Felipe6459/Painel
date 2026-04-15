<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<title>Painel IPTV PRO</title>

<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js"></script>

<style>
body { margin:0; font-family:Arial; background:#0f172a; color:white; }

.login {
  display:flex;
  justify-content:center;
  align-items:center;
  height:100vh;
  flex-direction:column;
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
.cobrar { background:green; }
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
Painel IPTV PRO
<button onclick="sair()">Sair</button>
</header>

<div class="container">

<button onclick="cobrarTodos()">📲 Cobrar Todos Vencidos</button>

<input id="nome" placeholder="Nome">
<input id="whatsapp" placeholder="WhatsApp">
<input id="plano" placeholder="Plano">
<input id="valor" type="number" placeholder="Valor">
<input type="date" id="vencimento">

<button onclick="salvar()">Salvar</button>

<div id="lista"></div>

</div>
</div>

<script>
const client = supabase.createClient(
  "https://nghgqcgsuyyytrpfvfzh.supabase.co",
  "sb_publishable_fsnaUk2uQmlq0d5r7MwFnA_FoO-wYkf"
);

const USUARIO = "admin";
const SENHA = "1234";

let dadosClientes = [];

// LOGIN
function entrar() {
  if (user.value === USUARIO && pass.value === SENHA) {
    localStorage.setItem("logado", "sim");
    mostrar();
  } else {
    alert("Login inválido");
  }
}

function sair() {
  localStorage.removeItem("logado");
  location.reload();
}

function mostrar() {
  login.style.display = "none";
  painel.style.display = "block";
  carregar();
}

if (localStorage.getItem("logado") === "sim") mostrar();

// STATUS
function statusCalc(v){
  let hoje = new Date();
  let d = new Date(v);
  let diff = Math.ceil((d-hoje)/(1000*60*60*24));

  if(diff<0) return "vencido";
  if(diff<=2) return "aviso";
  return "ativo";
}

// CARREGAR
async function carregar(){
  const { data } = await client.from("Painel ftv").select("*");
  dadosClientes = data;

  let html="";
  let receita=0;

  data.forEach(c=>{
    let status = statusCalc(c.vencimento);
    receita += Number(c.valor || 0);

    html+=`
    <div class="card ${status}">
      <b>${c.nome}</b>
      <p>${c.plano} - R$ ${c.valor}</p>
      <p>Vence: ${c.vencimento}</p>

      <button class="edit" onclick="editar(${c.id})">Editar</button>
      <button class="delete" onclick="del(${c.id})">Excluir</button>
      <button class="cobrar" onclick="cobrar('${c.whatsapp}','${c.nome}','${c.vencimento}')">Cobrar</button>
    </div>`;
  });

  lista.innerHTML = html;
}

// SALVAR
async function salvar(){
  await client.from("Painel ftv").insert([{
    id: Date.now(),
    nome: nome.value,
    whatsapp: whatsapp.value,
    plano: plano.value,
    valor: parseFloat(valor.value) || 0,
    vencimento: vencimento.value
  }]);

  carregar();
}

// EXCLUIR
async function del(id){
  await client.from("Painel ftv").delete().eq("id",id);
  carregar();
}

// EDITAR (simples)
function editar(id){
  alert("Edite manualmente e salve novamente");
}

// COBRAR INDIVIDUAL
function cobrar(whatsapp, nome, vencimento){
  let hoje = new Date();
  let v = new Date(vencimento);
  let diff = Math.ceil((v-hoje)/(1000*60*60*24));

  let msg="";

  if(diff<0){
    msg=`🚨 Olá ${nome}, seu plano venceu (${vencimento}). Regularize para evitar bloqueio.`;
  } else if(diff===0){
    msg=`⚠️ Olá ${nome}, seu plano vence hoje (${vencimento}).`;
  } else if(diff<=2){
    msg=`🔔 Olá ${nome}, seu plano está próximo do vencimento (${vencimento}).`;
  } else {
    msg=`🙂 Olá ${nome}, seu plano vence em breve (${vencimento}).`;
  }

  let num = whatsapp.replace(/\D/g,'');
  window.open(`https://wa.me/55${num}?text=${encodeURIComponent(msg)}`);
}

// COBRAR TODOS VENCIDOS
function cobrarTodos(){
  dadosClientes.forEach(c=>{
    let status = statusCalc(c.vencimento);

    if(status === "vencido"){
      cobrar(c.whatsapp, c.nome, c.vencimento);
    }
  });
}
</script>

</body>
</html>
