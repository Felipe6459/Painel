<script>
  const supabaseUrl = "https://nghgqcgsuyyytrpfvfzh.supabase.co";
  const supabaseKey = "sb_publishable_fsnaUk2uQmlq0d5r7MwFnA_FoO-wYkf";

  const supabase = supabase.createClient(supabaseUrl, supabaseKey);

  async function carregar() {
    try {
      console.log("🚀 Iniciando requisição...");

      const { data, error } = await supabase
        .from("clientes")
        .select("*");

      console.log("📦 DATA:", data);
      console.log("❌ ERROR:", error);

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
        "<div class='card'>ERRO JS: " + e.message + "</div>";
    }
  }

  carregar();
</script>
