
<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <title>Painel IPTV</title>

  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js"></script>

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

    .card {
      background: #1e293b;
      padding: 15px;
      margin-bottom: 10px;
      border-radius: 10px;
    }
  </style>
</head>

<body>

<header>Painel</header>

<div class="container" id="lista">
Carregando...
</div>

<script>
const supabaseUrl = "https://nghgqcgsuyyytrpfvfzh.supabase.co";
const supabaseKey = "sb_publishable_fsnaUk2uQmlq0d5r7MwFnA_FoO-wYkf";

const client = supabase.createClient(supabaseUrl, supabaseKey);

async function carregar() {
  const { data, error } = await client
    .from("Painel ftv")
    .select("*");

  if (error) {
    document.getElementById("lista").innerHTML = "Erro: " + error.message;
    console.log(error);
    return;
  }

  if (!data || data.length === 0) {
    document.getElementById("lista").innerHTML = "Nenhum cliente encontrado";
    return;
  }

  let html = "";

  data.forEach(c => {
    html += `
      <div class="card">
        <p><strong>Nome:</strong> ${c.nome}</p>
        <p><strong>WhatsApp:</strong> ${c["WhatsApp"]}</p>
        <p><strong>Plano:</strong> ${c.plano}</p>
        <p><strong>Início:</strong> ${c.data_de_inicio}</p>
        <p><strong>Vencimento:</strong> ${c.vencimento}</p>
        <p><strong>Status:</strong> ${c.status}</p>
      </div>
    `;
  });

  document.getElementById("lista").innerHTML = html;
}

carregar();
</script>

</body>
</html>
