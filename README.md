<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Impressão Rápida</title>
<style>
body {
    margin: 0;
    font-family: Arial, sans-serif;
    background: url('logo-fundo.png') no-repeat center center fixed;
    background-size: cover;
    color: #333;
}
.container {
    max-width: 600px;
    margin: 50px auto;
    background: rgba(255,255,255,0.95);
    padding: 30px;
    border-radius: 12px;
    box-shadow: 0 4px 20px rgba(0,0,0,0.1);
}
h1 { text-align: center; margin-bottom: 20px; }
label { display: block; margin: 10px 0 5px; }
input[type="number"], select, input[type="date"], input[type="time"] {
    width: 100%; padding: 8px; margin-bottom: 10px; border-radius: 5px; border: 1px solid #ccc;
}
button {
    background-color: #007BFF; color: #fff; padding: 10px 20px;
    border: none; border-radius: 5px; cursor: pointer; font-size: 16px;
}
button:disabled { background-color: #aaa; cursor: not-allowed; }
.hidden { display: none; }
.total { font-weight: bold; margin-top: 10px; font-size: 18px; }
.manutencao { text-decoration: line-through; color: #888; }
</style>
</head>
<body>

<div class="container">
<h1>Impressão Rápida</h1>

<!-- Etapa 1 -->
<div id="etapa1">
    <label>Serviço:</label>
    <select id="servico">
        <option value="impressao">Impressão</option>
        <option value="xerox" disabled class="manutencao">Xerox (em manutenção)</option>
    </select>

    <label>Quantidade de páginas:</label>
    <input type="number" id="quantidade" min="1" value="1">

    <label>Frete:</label>
    <select id="frete">
        <option value="0">Sem frete</option>
        <option value="2">Com frete R$ 2,00</option>
    </select>

    <label><input type="checkbox" id="pdf"> Enviar PDF (opcional)</label>
    <small>Se envia o PDF podemos imprimir logo e você só busca ou entregamos.</small>

    <label><input type="checkbox" id="fiado"> Fiado (R$5,00)</label>

    <div class="total">Total: R$ <span id="total">7,50</span></div>

    <button id="proxima">Próxima etapa</button>
</div>

<!-- Etapa 2 -->
<div id="etapa2" class="hidden">
    <label>Escolha a data:</label>
    <input type="date" id="data">

    <label>Escolha o horário:</label>
    <select id="horario">
        <option value="08:00">08:00</option>
        <option value="10:00">10:00</option>
        <option value="12:00">12:00</option>
        <option value="14:00">14:00</option>
        <option value="16:00">16:00</option>
        <option value="18:00">18:00</option>
        <option value="20:00">20:00</option>
    </select>

    <button id="agendar">Agendar</button>
    <button id="voltar">Voltar</button>
</div>

</div>

<script>
// Elementos
const servicoEl = document.getElementById('servico');
const etapa1El = document.getElementById('etapa1');
const etapa2El = document.getElementById('etapa2');
const proximaBtn = document.getElementById('proxima');
const voltarBtn = document.getElementById('voltar');
const agendarBtn = document.getElementById('agendar');
const dataEl = document.getElementById('data');
const horarioEl = document.getElementById('horario');
const totalEl = document.getElementById('total');

// Avançar etapa
proximaBtn.addEventListener('click', () => {
    etapa1El.classList.add('hidden');
    etapa2El.classList.remove('hidden');
    atualizarHorarios();
});

// Voltar
voltarBtn.addEventListener('click', () => {
    etapa2El.classList.add('hidden');
    etapa1El.classList.remove('hidden');
});

// Bloqueio horários já agendados
function atualizarHorarios() {
    const agendamentos = JSON.parse(localStorage.getItem('agendamentos') || '[]');
    const dataEscolhida = dataEl.value;
    for(let option of horarioEl.options){
        option.disabled = false;
    }
    for(let ag of agendamentos){
        if(ag.data === dataEscolhida){
            for(let option of horarioEl.options){
                if(option.value === ag.horario) option.disabled = true;
            }
        }
    }
}

// Atualiza horários ao mudar data
dataEl.addEventListener('change', atualizarHorarios);

// Agendar
agendarBtn.addEventListener('click', () => {
    const data = dataEl.value;
    const horario = horarioEl.value;
    if(!data || !horario){
        alert('Escolha data e horário.');
        return;
    }
    let agendamentos = JSON.parse(localStorage.getItem('agendamentos') || '[]');
    agendamentos.push({data, horario});
    localStorage.setItem('agendamentos', JSON.stringify(agendamentos));
    alert(`Agendado para ${data} às ${horario}`);
    atualizarHorarios();
});
</script>

</body>
</html>
