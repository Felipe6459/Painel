<script>
  const supabaseUrl = "https://nghgqcgsuyyytrpfvfzh.supabase.co";
  const supabaseKey = "sb_publishable_fsnaUk2uQmlq0d5r7MwFnA_FoO-wYkf";

  const client = supabase.createClient(supabaseUrl, supabaseKey);

  async function carregarClientes() {
    const { data, error } = await client
      .from("Painel ftv") // 👈 AGORA CORRETO
      .select("*");

    if (error) {
      document.getElementById("lista").innerHTML =
        "<div class='card'>Erro: " + error.message + "</div>";
      return;
    }

    let html = "";

    data.forEach(c => {
      html += `
        <div class="card">
          <p><strong>Nome:</strong> ${c.nome}</p>
          <p><strong>WhatsApp:</strong> ${c.whatsapp}</p>
        </div>
      `;
    });

    document.getElementById("lista").innerHTML = html;
  }

  carregarClientes();
</script>
