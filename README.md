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
    .semdados { background: gray; }
  </style>
</head>

<body>

<header>
  Painel IPTV - Clientes
</header>

<div class="container" id="lista">

  <!-- estrutura inicial sempre visível -->
  <div class="card">
    <p>Carregando clientes...</p>
  </div>

</div>

<script>
  const supabaseUrl = "https://nghgqcgsuyyytrpfvfzh.supabase.co";
  const supabaseKey = "COLE_SUA_ANON_KEY_AQUI";

  const supabase = supabase.createClient(supabaseUrl, supabaseKey);

  async function carregarClientes() {

    const { data, error } = await supabase
      .from("clientes")
      .select("*");

    if (error) {
      document.getElementById("lista").innerHTML = `
        <div class="card">
          <p>❌ Erro ao conectar no Supabase</p>
          <p>${error.message}</p>
        </div>
      `;
      return;
    }

    let html = "";

    // 🔥 se não tiver clientes
    if (!data || data.length === 0) {
      html = `
        <div class="card">
          <p><strong>Nome:</strong> ---</p>
          <p><strong>Plano:</strong> ---</p>
          <p><strong>Vencimento:</strong> ---</p>
          <span class="status semdados">SEM CLIENTES</span>
        </div>
      `;

      document.getElementById("lista").innerHTML = html;
      return;
    }

    // 🔥 se tiver clientes
    data.forEach(cliente => {

      let statusClass =
        cliente.status === "ativo" ? "ativo" : "vencido";

      html += `
        <div class="card">
          <p><strong>Nome:</strong> ${cliente.nome}</p>
          <p><strong>Plano:</strong> ${cliente.plano || "---"}</p>
          <p><strong>Vencimento:</strong> ${cliente.data_vencimento || "---"}</p>

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
