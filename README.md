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

    .status {
      padding: 5px 10px;
      border-radius: 5px;
      display: inline-block;
      font-size: 12px;
    }

    .ativo { background: green; }
    .vencido { background: red; }
  </style>
</head>

<body>

<header>
  Painel IPTV - Clientes
</header>

<div class="container" id="lista"></div>

<script>
  const supabaseUrl = "https://nghgqcgsuyyytrpfvfzh.supabase.co";

  // 🔑 COLE SUA ANON KEY AQUI (obrigatório)
  const supabaseKey = "COLE_SUA_ANON_KEY_AQUI";

  const supabase = supabase.createClient(supabaseUrl, supabaseKey);

  async function carregarClientes() {
    const { data, error } = await supabase
      .from("clientes")
      .select("*");

    if (error) {
      console.log("Erro ao buscar clientes:", error);
      document.getElementById("lista").innerHTML =
        "<p>Erro ao carregar dados do Supabase</p>";
      return;
    }

    if (!data || data.length === 0) {
      document.getElementById("lista").innerHTML =
        "<p>Nenhum cliente encontrado</p>";
      return;
    }

    let html = "";

    data.forEach(cliente => {

      let statusClass = cliente.status === "ativo" ? "ativo" : "vencido";

      html += `
        <div class="card">
          <p><strong>Nome:</strong> ${cliente.nome}</p>
          <p><strong>Plano:</strong> ${cliente.plano || "N/A"}</p>
          <p><strong>Vencimento:</strong> ${cliente.data_vencimento || "N/A"}</p>

          <span class="status ${statusClass}">
            ${cliente.status}
          </span>
        </div>
      `;
    });

    document.getElementById("lista").innerHTML = html;
  }

  carregarClientes();
</script>

</body>
</html>
