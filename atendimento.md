<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>MONITOR MULTI-OLT PRO</title>
    <style>
        :root {
            --bg-body: #f0f2f5; --bg-container: #ffffff; --text-main: #2d3436;
            --border-color: #e1e8ed; --input-bg: #ffffff; --header-color: #0984e3;
            --card-before: #f8f9fa; --accent-green: #00b894; --accent-red: #d63031;
            --text-highlight: #0984e3;
        }
        [data-theme="dark"] {
            --bg-body: #0d1117; --bg-container: #161b22; --text-main: #c9d1d9;
            --border-color: #30363d; --input-bg: #0d1117; --header-color: #58a6ff;
            --card-before: #21262d; --text-highlight: #58a6ff;
        }
        body { font-family: 'Inter', 'Segoe UI', sans-serif; background-color: var(--bg-body); padding: 20px; color: var(--text-main); transition: 0.3s; margin: 0; text-transform: uppercase; }
        .container { max-width: 1400px; margin: auto; background: var(--bg-container); padding: 30px; border-radius: 16px; box-shadow: 0 10px 40px rgba(0,0,0,0.2); border: 1px solid var(--border-color); }
        
        .tabs-nav { display: flex; gap: 8px; margin-bottom: -1px; position: relative; z-index: 2; overflow-x: auto; }
        .tab-btn { padding: 14px 24px; cursor: pointer; border: 1px solid transparent; background: transparent; color: var(--text-main); border-radius: 10px 10px 0 0; font-weight: 600; transition: 0.2s; opacity: 0.6; white-space: nowrap; text-transform: uppercase; }
        .tab-btn.active { background: var(--bg-container); border: 1px solid var(--border-color); border-bottom: 2px solid var(--header-color); opacity: 1; color: var(--header-color); }
        .tab-content { display: none; border: 1px solid var(--border-color); padding: 30px; border-radius: 0 12px 12px 12px; background: var(--bg-container); animation: fadeIn 0.3s ease; }
        .tab-content.active { display: block; }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }

        .input-group label { font-size: 11px; font-weight: 700; text-transform: uppercase; margin-bottom: 6px; display: block; color: var(--header-color); }
        
        textarea, input, select { 
            width: 100%; padding: 10px; border: 1px solid var(--border-color); border-radius: 8px; 
            background: var(--input-bg); color: var(--text-main); font-size: 13px; box-sizing: border-box; 
            outline: none; text-transform: uppercase; 
        }

        .portas-container { display: grid; grid-template-columns: repeat(2, 1fr); gap: 8px; background: var(--card-before); padding: 15px; border-radius: 12px; }
        .porta-item { display: flex; align-items: center; gap: 6px; font-size: 11px; font-weight: bold; }
        .porta-item input { padding: 4px; height: 25px; font-size: 11px; }

        .btn-group { display: flex; gap: 10px; margin-top: 20px; }
        .btn-primary { background: var(--header-color); color: white; padding: 14px 25px; width: 100%; border: none; border-radius: 8px; font-weight: 700; cursor: pointer; transition: 0.2s; text-transform: uppercase; }
        .btn-clear { background: #636e72; color: white; width: 30%; border: none; border-radius: 8px; font-weight: 700; cursor: pointer; text-transform: uppercase; }
        
        .script-output { background: #011627; color: #addb67; padding: 20px; border-radius: 10px; font-family: monospace; display: none; white-space: pre-wrap; border-left: 4px solid var(--accent-green); font-size: 13px; margin-top: 20px; text-transform: uppercase; }
        
        /* Tabela de Histórico */
        .history-table { width: 100%; border-collapse: collapse; margin-top: 20px; font-size: 11px; }
        .history-table th, .history-table td { padding: 10px; border: 1px solid var(--border-color); text-align: left; }
        .history-table th { background: var(--card-before); color: var(--header-color); }
        .history-controls { display: flex; gap: 10px; margin-bottom: 20px; }
        
        .btn-delete-row { background: var(--accent-red); color: white; border: none; padding: 5px 8px; border-radius: 4px; cursor: pointer; font-size: 10px; font-weight: bold; }
        .btn-delete-row:hover { opacity: 0.8; }

        .saturada-layout { display: grid; grid-template-columns: 1fr 1fr; gap: 40px; align-items: start; }
        .output-wrapper { display: flex; flex-direction: column; height: 100%; }
        .placeholder-icon { display: flex; align-items: center; justify-content: center; opacity: 0.1; height: 300px; font-size: 150px; }
        .btn-copy { background: var(--accent-green); margin-top: 10px; display: none; }
        .section-title { font-size: 12px; color: var(--accent-red); margin-top: 20px; margin-bottom: 10px; border-bottom: 1px solid var(--border-color); padding-bottom: 5px; font-weight: bold; }
        .obs-manual { margin-top: 10px; display: none; }
    </style>
</head>
<body data-theme="dark">

<div class="container">
    <div class="tabs-nav">
        <button class="tab-btn active" onclick="showTab(event, 'tab-at-1')">📝 ATEND. 1</button>
        <button class="tab-btn" onclick="showTab(event, 'tab-at-2')">📝 ATEND. 2</button>
        <button class="tab-btn" onclick="showTab(event, 'tab-at-3')">📝 ATEND. 3</button>
        <button class="tab-btn" onclick="showTab(event, 'tab-saturada')" style="color: #e67e22;">⚠️ CTO SATURADA</button>
        <button class="tab-btn" onclick="showTab(event, 'tab-historico')" style="color: #00b894;">🗄️ HISTÓRICO</button>
    </div>

    <div id="at-area"></div>

    <div id="tab-saturada" class="tab-content">
        <div class="saturada-layout">
            <div>
                <h3 style="color:var(--accent-red)">🚫 RELATÓRIO DE CTO SATURADA</h3>
                <div class="section-title">// CTO SATURADA //</div>
                <div style="display:grid; grid-template-columns: 1fr 1fr; gap:10px;">
                    <div class="input-group"><label>BKO</label><input type="text" class="sat_bko" value="CAIO BRANDÃO"></div>
                    <div class="input-group"><label>DATA</label><input type="date" class="sat_data" id="data_atual_sat"></div>
                    <div class="input-group"><label>ID CHAT</label><input type="text" class="sat_chat"></div>
                    <div class="input-group"><label>CIDADE</label><input type="text" class="sat_cidade"></div>
                    <div class="input-group"><label>ATENDIMENTO</label><input type="text" class="sat_atend"></div>
                    <div class="input-group"><label>CLIENTE</label><input type="text" class="sat_cliente"></div>
                    <div class="input-group"><label>CTO VALIDADA</label><input type="text" class="sat_ctovald" oninput="syncSaturada(this, 'sat_ctos_verif')"></div>
                    <div class="input-group"><label>LOC. CLIENTE</label><input type="text" class="sat_loc_cli" oninput="syncSaturada(this, 'sat_loc_ass')"></div>
                    <div class="input-group" style="grid-column: span 2"><label>LOC. CTO</label><input type="text" class="sat_loc_cto" oninput="syncSaturada(this, 'sat_loc_cto_ordem')"></div>
                    <div class="input-group" style="grid-column: span 2"><label>OBS</label><textarea class="sat_obs" style="height:40px;" oninput="syncSaturada(this, 'sat_obs_ordem')"></textarea></div>
                </div>

                <div class="section-title">// SCRIPT ABERTURA ORDEM //</div>
                <div style="display:grid; grid-template-columns: 1fr 1fr; gap:10px;">
                    <div class="input-group"><label>CTO'S VERIFICADAS</label><input type="text" class="sat_ctos_verif"></div>
                    <div class="input-group"><label>CTO INDICADA AMPLIAÇÃO</label><input type="text" class="sat_cto_amp"></div>
                    <div class="input-group"><label>LOC. CTO</label><input type="text" class="sat_loc_cto_ordem"></div>
                    <div class="input-group"><label>LOC. ASSINANTE</label><input type="text" class="sat_loc_ass"></div>
                    <div class="input-group" style="grid-column: span 2"><label>OBS</label><textarea class="sat_obs_ordem" style="height:40px;"></textarea></div>
                </div>

                <div class="btn-group">
                    <button class="btn-primary" style="background: #e67e22;" onclick="gerarScriptSaturada(this)">🚀 GERAR SCRIPT</button>
                    <button class="btn-clear" onclick="limparForm(this)">🗑️ LIMPAR</button>
                </div>
            </div>
            <div class="output-wrapper">
                <div class="placeholder-icon">⚠️</div>
                <div class="script-output"></div>
                <button class="btn-primary btn-copy" onclick="copiarScript(this)">📋 COPIAR SCRIPT</button>
            </div>
        </div>
    </div>

    <div id="tab-historico" class="tab-content">
        <h3 style="color:var(--accent-green)">🗄️ HISTÓRICO DE REGISTROS</h3>
        <div class="history-controls">
            <button class="btn-primary" style="background:#2980b9; width:auto;" onclick="exportarCSV()">📥 BAIXAR EXCEL (CSV)</button>
            <button class="btn-clear" style="background:#c0392b; width:auto; padding: 0 20px;" onclick="limparHistorico()">🗑️ APAGAR TUDO</button>
        </div>
        <div style="overflow-x: auto;">
            <table class="history-table" id="tabelaHist">
                <thead>
                    <tr>
                        <th>DATA/HORA</th>
                        <th>ID CHAT</th>
                        <th>OS</th>
                        <th>CTO</th>
                        <th>MODELO</th>
                        <th>PORTA LIB.</th>
                        <th>CORREÇÃO</th>
                        <th>OBS</th>
                        <th style="text-align:center">AÇÕES</th>
                    </tr>
                </thead>
                <tbody></tbody>
            </table>
        </div>
    </div>
</div>

<script>
window.onload = function() {
    const hoje = new Date().toISOString().split('T')[0];
    document.querySelectorAll('input[type="date"]').forEach(el => el.value = hoje);
    carregarHistorico();
};

const tabs = ['tab-at-1', 'tab-at-2', 'tab-at-3'];
const atArea = document.getElementById('at-area');

tabs.forEach((id, i) => {
    atArea.innerHTML += `
    <div id="${id}" class="tab-content ${i === 0 ? 'active' : ''}">
        <div style="display:grid; grid-template-columns: 1fr 1fr; gap:40px;">
            <div>
                <h3 style="color:var(--header-color)">📋 ATENDIMENTO ${i+1}</h3>
                <div style="display:grid; grid-template-columns: 1fr 1fr; gap:10px;">
                    <div class="input-group"><label>ID CHAT</label><input type="text" class="at_chat"></div>
                    <div class="input-group"><label>ORDEM DE SERVIÇO</label><input type="text" class="at_os"></div>
                    <div class="input-group" style="grid-column:span 2"><label>ID CTO</label><input type="text" class="at_cto"></div>
                    <div class="input-group" style="grid-column:span 2"><label>CAMINHO DE REDE CTO</label><input type="text" class="at_caminho_rede"></div>
                    <div class="input-group" style="grid-column:span 2">
                        <label>MODELO CTO</label>
                        <select class="at_modelo_cto" onchange="atualizarInterfaceAT('${id}', this.value)">
                            <option value="16 PORTAS">16 PORTAS</option>
                            <option value="8 PORTAS">8 PORTAS</option>
                        </select>
                    </div>
                    <div class="input-group"><label>PORTA LIBERADA</label><select class="at_plib_id"></select></div>
                    <div class="input-group"><label>ID PORTA</label><input type="text" class="at_plib_num"></div>
                    <div class="input-group" style="grid-column:span 2">
                        <label>CORREÇÃO DE CAMINHO DE REDE DA PORTA LIBERA</label>
                        <select class="at_correcao_caminho"><option value="NAO">NÃO</option><option value="SIM">SIM</option></select>
                    </div>
                    <div class="input-group" style="grid-column:span 2">
                        <label>OBS</label>
                        <select class="at_obs_select" onchange="toggleObsManual('${id}', this.value)">
                            <option value="SEM OBSERVAÇÕES ADICIONAIS.">SELECIONE UMA OPÇÃO...</option>
                            <option value="CTO MAIS PROXIMA DA CASA DO CLIENTE.">CTO MAIS PROXIMA DA CASA DO CLIENTE.</option>
                            <option value="USOU A PORTA RESERVADA PARA O CLIENTE">USOU A PORTA RESERVADA PARA O CLIENTE</option>
                            <option value="CLIENTE DESCONECTADO INDEVIDO DA CTO">CLIENTE DESCONECTADO INDEVIDO DA CTO</option>
                            <option value="MANUAL">OUTRO (ESCREVER MANUALMENTE)</option>
                        </select>
                        <textarea class="at_obs_manual obs-manual" placeholder="DESCREVA O OCORRIDO..."></textarea>
                    </div>
                </div>
                <div class="btn-group">
                    <button class="btn-primary" onclick="gerarScript(this)">🚀 GERAR SCRIPT</button>
                    <button class="btn-clear" onclick="limparForm(this)">🗑️ LIMPAR</button>
                </div>
                <div class="script-output"></div>
                <button class="btn-primary btn-copy" onclick="copiarScript(this)">📋 COPIAR SCRIPT</button>
            </div>
            <div>
                <h3 style="opacity:0.7">🔌 PORTAS</h3>
                <div class="portas-container" id="grid-${id}"></div>
            </div>
        </div>
    </div>`;
});

function toggleObsManual(tabId, value) {
    const area = document.querySelector(`#${tabId} .at_obs_manual`);
    area.style.display = (value === 'MANUAL') ? 'block' : 'none';
}

function atualizarInterfaceAT(tabId, modeloTexto) {
    const quantidade = parseInt(modeloTexto);
    renderizarPortas(tabId, quantidade);
    renderizarListaPortas(tabId, quantidade);
}

function renderizarListaPortas(tabId, quantidade) {
    const select = document.querySelector(`#${tabId} .at_plib_id`);
    if(!select) return;
    let options = `<option value="NENHUMA">NENHUMA</option>`;
    for(let i=1; i<=quantidade; i++) { options += `<option value="PORTA ${i}">PORTA ${i}</option>`; }
    select.innerHTML = options;
}

function renderizarPortas(tabId, quantidade) {
    const grid = document.getElementById(`grid-${tabId}`);
    if(!grid) return;
    grid.innerHTML = '';
    for(let i=1; i<=quantidade; i++) {
        grid.innerHTML += `<div class="porta-item"><span>P${i}</span><input type="text" class="pt${i}" placeholder="..."></div>`;
    }
}

tabs.forEach(id => { renderizarPortas(id, 16); renderizarListaPortas(id, 16); });

function showTab(e, id) {
    document.querySelectorAll('.tab-content, .tab-btn').forEach(x => x.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    e.currentTarget.classList.add('active');
}

// LOGICA DO HISTÓRICO ATUALIZADA
function salvarNoHistorico(dados) {
    let historico = JSON.parse(localStorage.getItem('atendimentos_logs') || '[]');
    dados.id_reg = Date.now(); // ID Único baseado no tempo
    dados.timestamp = new Date().toLocaleString('pt-BR');
    historico.unshift(dados);
    localStorage.setItem('atendimentos_logs', JSON.stringify(historico));
    carregarHistorico();
}

function carregarHistorico() {
    const historico = JSON.parse(localStorage.getItem('atendimentos_logs') || '[]');
    const corpoTabela = document.querySelector('#tabelaHist tbody');
    corpoTabela.innerHTML = historico.map(reg => `
        <tr>
            <td>${reg.timestamp}</td>
            <td>${reg.chat}</td>
            <td>${reg.os}</td>
            <td>${reg.cto}</td>
            <td>${reg.modelo}</td>
            <td>${reg.plib} (${reg.plib_num})</td>
            <td>${reg.correcao}</td>
            <td>${reg.obs}</td>
            <td align="center">
                <button class="btn-delete-row" onclick="excluirLinha(${reg.id_reg})">🗑️</button>
            </td>
        </tr>
    `).join('');
}

function excluirLinha(id) {
    if(confirm("EXCLUIR ESTE REGISTRO?")) {
        let historico = JSON.parse(localStorage.getItem('atendimentos_logs') || '[]');
        historico = historico.filter(reg => reg.id_reg !== id);
        localStorage.setItem('atendimentos_logs', JSON.stringify(historico));
        carregarHistorico();
    }
}

function limparHistorico() {
    if(confirm("DESEJA APAGAR TODO O HISTÓRICO?")) {
        localStorage.removeItem('atendimentos_logs');
        carregarHistorico();
    }
}

function exportarCSV() {
    const historico = JSON.parse(localStorage.getItem('atendimentos_logs') || '[]');
    if(historico.length === 0) return alert("HISTÓRICO VAZIO!");
    let csv = "\uFEFFDATA;CHAT;OS;CTO;MODELO;PORTA;CORRECAO;OBS\n";
    historico.forEach(r => {
        csv += `${r.timestamp};${r.chat};${r.os};${r.cto};${r.modelo};${r.plib};${r.correcao};${r.obs}\n`;
    });
    const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
    const link = document.createElement("a");
    link.href = URL.createObjectURL(blob);
    link.download = `HISTORICO_ATENDIMENTOS.csv`;
    link.click();
}

function gerarScript(btn) {
    const aba = btn.closest('.tab-content');
    const get = (c) => (aba.querySelector('.'+c).value || '---').toUpperCase();
    const modeloCTO = get('at_modelo_cto');
    const obsSelect = aba.querySelector('.at_obs_select').value;
    const obsFinal = (obsSelect === 'MANUAL') ? aba.querySelector('.at_obs_manual').value.toUpperCase() : obsSelect.toUpperCase();

    const dadosParaSalvar = {
        chat: get('at_chat'), os: get('at_os'), cto: get('at_cto'),
        modelo: modeloCTO, plib: get('at_plib_id'), plib_num: get('at_plib_num'),
        correcao: get('at_correcao_caminho'), obs: obsFinal
    };

    let s = `ID CHAT: ${dadosParaSalvar.chat}\nORDEM DE SERVIÇO: ${dadosParaSalvar.os}\nID CTO: ${dadosParaSalvar.cto}\n`;
    s += `MODELO CTO: ${dadosParaSalvar.modelo}\nCAMINHO DE REDE: ${get('at_caminho_rede')}\n`;
    s += `PORTA LIBERADA: ${dadosParaSalvar.plib}\nID PORTA LIBERADA: ${dadosParaSalvar.plib_num}\n`;
    s += `CORREÇÃO CAMINHO DE REDE: ${dadosParaSalvar.correcao}\nOBSERVAÇÕES: ${dadosParaSalvar.obs}\n\n`;
    s += `IDENTIFICAÇÃO DAS PORTAS:\n`;
    
    for(let i=1; i<=parseInt(modeloCTO); i++) {
        const valPorta = (aba.querySelector('.pt'+i).value || 'SEM ID').toUpperCase();
        s += `PORTA ${i.toString().padStart(2, '0')}: ${valPorta}\n`;
    }
    salvarNoHistorico(dadosParaSalvar);
    exibirResultado(aba, s);
}

function gerarScriptSaturada(btn) {
    const aba = btn.closest('.tab-content');
    const get = (c) => (aba.querySelector('.'+c).value || '---').toUpperCase();
    const dataInput = aba.querySelector('.sat_data').value;
    const dataFormatada = dataInput ? dataInput.split('-').reverse().join('/') : '---';
    let s = `// CTO SATURADA //\nBKO: ${get('sat_bko')}\nDATA: ${dataFormatada}\nID CHAT: ${get('sat_chat')}\nCIDADE: ${get('sat_cidade')}\nATENDIMENTO: ${get('sat_atend')}\nCLIENTE: ${get('sat_cliente')}\nCTO VALIDADA: ${get('sat_ctovald')}\nLOC. CLIENTE: ${get('sat_loc_cli')}\nLOC. CTO: ${get('sat_loc_cto')}\nOBS: ${get('sat_obs')}\n\n`;
    s += `// SCRIPT ABERTURA ORDEM //\nCTO'S VERIFICADAS: ${get('sat_ctos_verif')}\nCTO INDICADA PARA AMPLIAÇÃO: ${get('sat_cto_amp')}\nLOC. CTO: ${get('sat_loc_cto_ordem')}\nLOCALIZAÇÃO ASSINANTE: ${get('sat_loc_ass')}\nOBS: ${get('sat_obs_ordem')}`;
    exibirResultado(aba, s.toUpperCase());
}

function exibirResultado(aba, texto) {
    const out = aba.querySelector('.script-output');
    const copyBtn = aba.querySelector('.btn-copy');
    const placeholder = aba.querySelector('.placeholder-icon');
    if(placeholder) placeholder.style.display = 'none';
    out.innerText = texto; out.style.display = 'block'; copyBtn.style.display = 'block';
}

function copiarScript(btn) {
    const aba = btn.closest('.tab-content');
    const texto = aba.querySelector('.script-output').innerText;
    navigator.clipboard.writeText(texto).then(() => {
        const originalText = btn.innerText;
        btn.innerText = "✅ COPIADO!"; btn.style.background = "#2ecc71";
        setTimeout(() => { btn.innerText = originalText; btn.style.background = ""; }, 2000);
    });
}

function limparForm(btn) {
    const aba = btn.closest('.tab-content');
    aba.querySelectorAll('input, textarea').forEach(el => { if(!el.classList.contains('sat_bko')) el.value = ''; });
    aba.querySelectorAll('select').forEach(el => el.selectedIndex = 0);
    const out = aba.querySelector('.script-output');
    const copyBtn = aba.querySelector('.btn-copy');
    if(out) out.style.display = 'none';
    if(copyBtn) copyBtn.style.display = 'none';
}
</script>
</body>
</html>
