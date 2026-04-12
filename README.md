<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Painel IPTV Completo</title>

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
    .demo { background: orange; }
  </style>
</head>

<body>

<header>
  Painel IPTV - Gestão de Clientes
</header>

<div class="container" id="lista">
  <div class="card">
    <p>Carregando clientes...</p>
  </div>
</div>

<script>
  // 🌐 SUPABASE CONFIG
  const supabaseUrl = "https://nghgqcgsuyyytrpfvfzh.supabase.co";
  const supabaseKey = "sb_publishable_fsnaUk2uQmlq0d5r7MwFnA_FoO-wYkf";

  const supabase = supabase.createClient(supabaseUrl, supabaseKey);

  async function carregarClientes() {

    const { data, error } = await supabase
      .from("clientes")
      .select("*");

    // ❌ erro de conexão
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

    // 🧪 CLIENTE FICTÍCIO (caso banco esteja vazio)
    const clienteFake = {
      nome: "Cliente Demo",
      plano: "1 Tela",
      servidor: "Servidor Teste",
      assinatura: "Mensal",
      data_vencimento: "2026-12-31",
      status: "ativo"
    };

    // 📭 se não tiver dados no banco
    if (!data || data.length === 0) {
      data = [clienteFake];
    }

    // 📊 montar lista
    data.forEach(cliente => {

      let statusClass =
        cliente.status === "ativo" ? "ativo" : "vencido";

      html += `
        <div class="card">
          <p><strong>Nome:</strong> ${cliente.nome}</p>
          <p><strong>Plano:</strong> ${cliente.plano || "---"}</p>
          <p><strong>Servidor:</strong> ${cliente.servidor || "Padrão"}</p>
          <p><strong>Assinatura:</strong> ${cliente.assinatura || "Mensal"}</p>
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
