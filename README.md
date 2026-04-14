<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
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
      font-weight: bold;
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

    .status {
      padding: 5px 10px;
      border-radius: 5px;
      font-size: 12px;
      display: inline-block;
    }

    .ativo { background: green; }
    .vencido { background: red; }
  </style>
</head>

<body>

<header>Painel de Clientes</header>

<div class="container" id="lista">
  <div class="card">Carregando clientes...</div>
</div>

<script>
  const supabaseUrl = "https://nghgqcgsuyyytrpfvfzh.supabase.co";
  const supabaseKey = "sb_publishable_fsnaUk2uQmlq0d5r7MwFnA_FoO-wYkf";

  const client = supabase.createClient(supabaseUrl, supabaseKey);

  async function carregarClientes() {
    const { data, error } = await client
      .from("clientes")
      .select("*");

    if (error) {
      document.getElementById("lista").innerHTML =
        "<div class='card'>Erro: " + error.message + "</div>";
      return;
    }

    if (!data || data.length === 0) {
      document.getElementById("lista").innerHTML =
        "<div class='card'>Nenhum cliente encontrado</div>";
      return;
    }

    let html = "";

    data.forEach(c => {
      html += `
        <div class="card">
          <p><strong>Nome:</strong> ${c.nome}</p>
          <p><strong>WhatsApp:</strong> ${c.whatsapp}</p>
          <p><strong>Plano:</strong> ${c.plano}</p>
          <p><strong>Início:</strong> ${c.data_de_inicio}</p>
          <p><strong>Vencimento:</strong> ${c.vencimento}</p>
          <p><strong>Assinatura:</strong> ${c.assinatura}</p>
          <p>
            <strong>Status:</strong>
            <span class="status ${c.status}">
              ${c.status}
            </span>
          </p>
        </div>
      `;
    });

    document.getElementById("lista").innerHTML = html;
  }

  carregarClientes();
</script>

</body>
</html>
