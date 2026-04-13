<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Painel IPTV</title>

  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js"></script>

  <style>
    body {
      font-family: Arial;
      margin: 0;
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
  </style>
</head>

<body>

<header>Painel IPTV</header>

<div class="container" id="lista">
  <div class="card">Carregando clientes...</div>
</div>

<script>
  const SUPABASE_URL = "https://nghgqcgsuyyytrpfvfzh.supabase.co";
  const SUPABASE_KEY = "sb_publishable_fsnaUk2uQmlq0d5r7MwFnA_FoO-wYkf";

  const client = supabase.createClient(SUPABASE_URL, SUPABASE_KEY);

  async function carregar() {
    try {
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
            <p>Nome: ${c.nome}</p>
            <p>Plano: ${c.plano}</p>
            <p>Vencimento: ${c.data_vencimento}</p>
            <p>Status: ${c.status}</p>
          </div>
        `;
      });

      document.getElementById("lista").innerHTML = html;

    } catch (e) {
      document.getElementById("lista").innerHTML =
        "<div class='card'>Erro JS: " + e.message + "</div>";
    }
  }

  carregar();
</script>

</body>
</html>
