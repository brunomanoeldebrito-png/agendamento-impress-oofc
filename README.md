<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Impressão Rápida</title>
<style>
body {
    margin: 0;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: url('logo-fundo.png') no-repeat center center fixed;
    background-size: cover;
    color: #333;
}
.container {
    max-width: 700px;
    margin: 50px auto;
    background: rgba(255,255,255,0.95);
    padding: 35px;
    border-radius: 15px;
    box-shadow: 0 8px 30px rgba(0,0,0,0.2);
    transition: all 0.3s ease-in-out;
}
h1 { 
    text-align: center; 
    margin-bottom: 25px; 
    color:#007BFF; 
    text-shadow: 1px 1px 2px rgba(0,0,0,0.2);
}
label { display: block; margin: 12px 0 6px; font-weight: 600; }
input[type="number"], select, input[type="date"], input[type="time"] {
    width: 100%; 
    padding: 12px; 
    margin-bottom: 12px; 
    border-radius: 10px; 
    border: 1px solid #ccc;
    font-size: 16px;
    transition: border 0.3s;
}
input[type="number"]:focus, select:focus, input[type="date"]:focus, input[type="time"]:focus {
    border: 2px solid #007BFF;
}
button {
    background: linear-gradient(90deg,#28a745,#20c997);
    color: #fff; 
    padding: 14px 28px;
    border: none; 
    border-radius: 12px; 
    cursor: pointer; 
    font-size: 16px;
    margin-top: 10px;
    transition: all 0.3s;
    box-shadow: 0 4px 12px rgba(0,0,0,0.2);
}
button:hover { 
    transform: translateY(-3px);
    box-shadow: 0 6px 15px rgba(0,0,0,0.25);
}
button:disabled { background-color: #aaa; cursor: not-allowed; }
.hidden { display: none; }
.total { 
    font-weight: bold; 
    margin-top: 15px; 
    font-size: 22px; 
    color:#dc3545; 
    text-align: center;
}
.manutencao { text-decoration: line-through; color: #888; }
.checkbox-label { font-weight: normal; cursor: pointer; }
input[type="checkbox"] { margin-right: 10px; transform: scale(1.2); }
@media(max-width:600px){
    .container { margin: 20px; padding: 25px; }
    button { width: 100%; }
}
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

    <label class="checkbox-label"><input type="checkbox" id="pdf"> Enviar PDF (R$0,10/página)</label>

    <label class="checkbox-label"><input type="checkbox" id="fiado"> Fiado (R$5,00)</label>

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

    <label>Forma de pagamento:</label>
    <select id="pagamento">
        <option value="dinheiro">Dinheiro</option>
        <option value="pix">PIX (chave: 71982513027)</option>
    </select>

    <button id="proxima2">Próxima etapa</button>
    <button id="voltar">Voltar</button>
</div>

<!-- Etapa 3 -->
<div id="etapa3" class="hidden">
    <label>Assinar plano fidelidade por R$15/mês?</label>
    <label class="checkbox-label"><input type="checkbox" id="fidelidade"> Sim, quero</label>
    <small>Benefício: impressões grátis enquanto ativo. Cancelamento a qualquer momento. Mantém 4 meses grátis após cancelar.</small>

    <div class="total">Valor final: R$ <span id="totalFinal">0,00</span></div>

    <button id="confirmar">Confirmar Agendamento</button>
    <button id="voltar2">Voltar</button>
</div>

</div>

<script>
// Preços
const precoNormal = 2.5;
const precoCombo = 1.0;
const taxaTrabalho = 1.0;
const taxaPDF = 0.10;
const taxaFiado = 5.0;
const taxaSegunda = 2.0;

// Elementos
const servicoEl = document.getElementById('servico');
const quantidadeEl = document.getElementById('quantidade');
const freteEl = document.getElementById('frete');
const pdfEl = document.getElementById('pdf');
const fiadoEl = document.getElementById('fiado');
const totalFinalEl = document.getElementById('totalFinal');

const etapa1El = document.getElementById('etapa1');
const etapa2El = document.getElementById('etapa2');
const etapa3El = document.getElementById('etapa3');
const proximaBtn = document.getElementById('proxima');
const proxima2Btn = document.getElementById('proxima2');
const voltarBtn = document.getElementById('voltar');
const voltar2Btn = document.getElementById('voltar2');
const confirmarBtn = document.getElementById('confirmar');
const dataEl = document.getElementById('data');
const horarioEl = document.getElementById('horario');
const pagamentoEl = document.getElementById('pagamento');
const fidelidadeEl = document.getElementById('fidelidade');

// Avançar etapa 1 -> 2
proximaBtn.addEventListener('click', () => {
    etapa1El.classList.add('hidden');
    etapa2El.classList.remove('hidden');
    atualizarHorarios();
});

// Avançar etapa 2 -> 3
proxima2Btn.addEventListener('click', () => {
    if(!dataEl.value || !horarioEl.value){
        alert('Escolha data e horário.');
        return;
    }
    etapa2El.classList.add('hidden');
    etapa3El.classList.remove('hidden');
    calcularValorFinal();
});

// Voltar
voltarBtn.addEventListener('click', () => {
    etapa2El.classList.add('hidden');
    etapa1El.classList.remove('hidden');
});
voltar2Btn.addEventListener('click', () => {
    etapa3El.classList.add('hidden');
    etapa2El.classList.remove('hidden');
});

// Bloqueio horários
function atualizarHorarios(){
    const agendamentos = JSON.parse(localStorage.getItem('agendamentos') || '[]');
    const dataEscolhida = dataEl.value;
    if(!dataEscolhida) return;
    const diaSemana = new Date(dataEscolhida).getDay(); // 0=domingo, 1=segunda
    for(let option of horarioEl.options) option.disabled = false;

    // Bloqueio horários almoço
    for(let option of horarioEl.options){
        const hora = parseInt(option.value.split(':')[0]);
        if((diaSemana>=2 && diaSemana<=5 && hora>=12 && hora<18) || (diaSemana===0 || diaSemana===6) && hora>=12 && hora<14){
            option.disabled = true;
        }
    }

    // Bloqueio horários já agendados
    for(let ag of agendamentos){
        if(ag.data === dataEscolhida){
            for(let option of horarioEl.options){
                if(option.value === ag.horario) option.disabled = true;
            }
        }
    }
}

// Calcular valor final
function calcularValorFinal(){
    let qtd = parseInt(quantidadeEl.value) || 1;
    let frete = parseFloat(freteEl.value) || 0;
    let base = qtd === 5 ? precoCombo * qtd : precoNormal * qtd;
    let pdf = pdfEl.checked ? qtd * taxaPDF : 0;
    let fiado = fiadoEl.checked ? taxaFiado : 0;

    // Taxa segunda-feira
    let taxaSeg = 0;
    const diaSemana = new Date(dataEl.value).getDay();
    if(diaSemana === 1) taxaSeg = taxaSegunda;

    let total = base + pdf + frete + taxaTrabalho + fiado + taxaSeg;
    totalFinalEl.textContent = total.toFixed(2);
}

// Confirmar
confirmarBtn.addEventListener('click', () => {
    const data = dataEl.value;
    const horario = horarioEl.value;
    if(!data || !horario) return;
    let agendamentos = JSON.parse(localStorage.getItem('agendamentos') || '[]');
    agendamentos.push({data, horario});
    localStorage.setItem('agendamentos', JSON.stringify(agendamentos));
    alert(`Agendamento confirmado para ${data} às ${horario}`);
});
</script>

</body>
</html>
