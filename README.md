# agendamento-impress-oofc<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Agendamento Impressão Rápida</title>
  <style>
    body { font-family: Arial, sans-serif; background: #f7f7f7; padding: 20px; }
    .card { background: white; padding: 20px; border-radius: 10px; max-width: 500px; margin: auto; box-shadow: 0 2px 8px rgba(0,0,0,0.1); }
    .input { width: 100%; padding: 8px; margin: 10px 0; border: 1px solid #ccc; border-radius: 6px; }
    .btn { background: #4CAF50; color: white; padding: 12px; border: none; border-radius: 6px; cursor: pointer; width: 100%; font-weight: bold; }
    .btn:hover { background: #45a049; }
    label { font-weight: bold; display: block; margin-top: 15px; }
  </style>
</head>
<body>
  <div class="card">
    <h2>Agende seu Serviço</h2>

    <!-- Escolha do serviço -->
    <label>Serviço:</label>
    <select id="servico" class="input">
      <option value="Impressão Comum">Impressão Comum - R$ 2,50</option>
      <option value="Xerox">Xerox - R$ 2,00</option>
    </select>

    <label>Quantidade de páginas:</label>
    <input type="number" id="qtd" class="input" min="1" value="1">

    <!-- Data e hora -->
    <label>Escolha a data:</label>
    <input type="date" id="data" class="input">

    <label>Escolha o horário:</label>
    <select id="hora" class="input"></select>

    <!-- Opções extras -->
    <label><input type="checkbox" id="enviaPdf"> Enviar PDF (opcional)</label>
    <p style="font-size: 12px; color: #555;">💡 Se enviar o PDF, podemos imprimir agora e você só retira.</p>

    <label><input type="checkbox" id="frete"> Frete R$ 2,00 (opcional)</label>

    <label>Observações:</label>
    <textarea id="obs" class="input"></textarea>

    <button class="btn" onclick="enviar()">Agendar via WhatsApp</button>
  </div>

  <script>
    // Horários disponíveis
    const horasDisponiveis = [
      "08:00","09:00","10:00","11:00","12:00",
      "13:00","14:00","15:00","16:00","17:00"
    ];

    // Simulação: horários já agendados
    let horariosOcupados = {
      "2025-09-20": ["09:00","14:00"],
      "2025-09-21": ["10:00"]
    };

    const horaSelect = document.getElementById("hora");
    const dataInput = document.getElementById("data");

    // Atualiza horários quando muda a data
    dataInput.addEventListener("change", () => {
      horaSelect.innerHTML = "";
      let dataEscolhida = dataInput.value;
      horasDisponiveis.forEach(hora => {
        let option = document.createElement("option");
        option.value = hora;
        option.text = hora;

        // Se o horário estiver ocupado, desabilita
        if (horariosOcupados[dataEscolhida] && horariosOcupados[dataEscolhida].includes(hora)) {
          option.disabled = true;
          option.text += " (Indisponível)";
        }
        horaSelect.appendChild(option);
      });
    });

    function enviar() {
      let servico = document.getElementById("servico").value;
      let qtd = document.getElementById("qtd").value;
      let data = document.getElementById("data").value;
      let hora = document.getElementById("hora").value;
      let enviaPdf = document.getElementById("enviaPdf").checked ? "✅ Sim" : "❌ Não";
      let frete = document.getElementById("frete").checked ? "✅ Sim (R$2,00)" : "❌ Não";
      let obs = document.getElementById("obs").value;

      if (!data || !hora) {
        alert("Escolha uma data e horário!");
        return;
      }

      let resumo = `📄 *Novo Agendamento* \n
📌 Serviço: ${servico}
📑 Páginas: ${qtd}
📅 Data: ${data}
⏰ Hora: ${hora}
📂 Enviar PDF: ${enviaPdf}
🚚 Frete: ${frete}
📝 Observações: ${obs}`;

      // Número do zap
      let numeroZap = "5571982513027";
      let url = `https://wa.me/${numeroZap}?text=${encodeURIComponent(resumo)}`;
      window.open(url, "_blank");
    }
  </script>
</body>
</html>
