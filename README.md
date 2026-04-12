<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Painel IPTV</title>

  <!-- SUPABASE -->
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
    .erro { background: orange; }
  </style>
</head>

<body>

<header>
  Painel IPTV - Clientes
</header>

<div class="container" id="lista">
  <div class="card">
    <p>Carregando clientes...</p>
  </div>
</div>

<script>
  // 🌐 SUA URL (JÁ DEFINIDA)
  const supabaseUrl = "https://nghgqcgsuyyytrpfvfzh.supabase.co";

  // 🔑 eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Im5naGdxY2dzdXl5eXRycGZ2ZnpoIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzU5MjU1MTAsImV4cCI6MjA5MTUwMTUxMH0.KCGkmxL36Qr4y2wnoIvQIOr2naN-PAjDy3ejDUq7MQk
  const supabaseKey = "COLE_SUA_ANON_KEY_AQUI";

  const supabase = supabase.createClient(supabaseUrl, supabaseKey);

  async function carregarClientes() {

    const { data, error } = await supabase
      .from("clientes")
      .select("*");

    // ❌ ERRO DE CONEXÃO OU PERMISSÃO
    if (error) {
      document.getElementById("lista").innerHTML = `
        <div class="card">
          <p>❌ Erro ao conectar no Supabase</p>
          <p>${error.message}</p>
        </div>
      `;
      return;
    }

    // 🔥 SEM CLIENTES
    if (!data || data.length === 0) {
      document.getElementById("lista").innerHTML = `
        <div class="card">
          <p><strong>Nome:</strong> ---</p>
          <p><strong>Plano:</strong> ---</p>
          <p><strong>Vencimento:</strong> ---</p>
          <span class="status semdados">SEM CLIENTES</span>
        </div>
      `;
      return;
    }

    // 🔥 MONTAR LISTA DE CLIENTES
    let html = "";

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
