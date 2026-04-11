# Painel
Painel iptv

<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Painel IPTV</title>

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

    .ativo {
      background: green;
    }

    .vencido {
      background: red;
    }

    button {
      padding: 10px;
      border: none;
      background: #2563eb;
      color: white;
      border-radius: 8px;
      cursor: pointer;
    }

    button:hover {
      background: #1d4ed8;
    }
  </style>
</head>

<body>

<header>
  Painel IPTV - Controle de Clientes
</header>

<div class="container">

  <h2>Dashboard</h2>

  <div class="card">
    <p>Total de Clientes: 0</p>
    <p>Ativos: 0</p>
    <p>Vencidos: 0</p>
  </div>

  <h2>Clientes</h2>

  <div class="card">
    <p><strong>Nome:</strong> Exemplo Cliente</p>
    <p><strong>Plano:</strong> 1 Tela</p>
    <p><strong>Vencimento:</strong> 20/04/2026</p>

    <span class="status ativo">ATIVO</span>
  </div>

  <div class="card">
    <p><strong>Nome:</strong> Cliente Teste</p>
    <p><strong>Plano:</strong> 2 Telas</p>
    <p><strong>Vencimento:</strong> 10/04/2026</p>

    <span class="status vencido">VENCIDO</span>
  </div>

  <br>

  <button onclick="alert('Depois vamos conectar no Supabase 🚀')">
    + Adicionar Cliente
  </button>

</div>

</body>
</html>
