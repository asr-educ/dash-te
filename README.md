<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Controle de Dispositivos · Fev–Mai 2026</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@300;400;500;600&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0F1117;
    --surface: #181C27;
    --surface2: #1E2335;
    --border: rgba(255,255,255,0.07);
    --border2: rgba(255,255,255,0.12);
    --text: #E8EAF0;
    --text2: #8B90A4;
    --text3: #555C72;
    --blue: #4A8FE8;
    --blue-dim: rgba(74,143,232,0.15);
    --orange: #E8904A;
    --orange-dim: rgba(232,144,74,0.15);
    --red: #E84A6F;
    --red-dim: rgba(232,74,111,0.12);
    --green: #4AE8A0;
    --green-dim: rgba(74,232,160,0.12);
    --amber: #E8C94A;
    --amber-dim: rgba(232,201,74,0.12);
    --font: 'DM Sans', sans-serif;
    --mono: 'DM Mono', monospace;
    --radius: 10px;
    --radius-sm: 6px;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  html, body { height: 100%; }
  body { background: var(--bg); color: var(--text); font-family: var(--font); font-size: 13px; }

  .shell { display: flex; height: 100vh; overflow: hidden; }

  /* SIDEBAR */
  .sidebar { width: 210px; flex-shrink: 0; background: var(--surface); border-right: 1px solid var(--border); display: flex; flex-direction: column; gap: 2px; padding: 1.25rem 0; overflow-y: auto; }
  .sidebar-logo { padding: 0 1rem 1.25rem; border-bottom: 1px solid var(--border); margin-bottom: 0.75rem; }
  .logo-text { font-size: 13px; font-weight: 600; color: var(--text); letter-spacing: -0.01em; line-height: 1.3; }
  .logo-sub { font-size: 11px; color: var(--text3); margin-top: 3px; }
  .logo-badge { display: inline-block; margin-top: 8px; font-size: 10px; font-family: var(--mono); padding: 2px 8px; border-radius: 4px; background: var(--amber-dim); color: var(--amber); border: 1px solid rgba(232,201,74,0.2); }
  .nav-section { font-size: 10px; font-weight: 600; color: var(--text3); text-transform: uppercase; letter-spacing: 0.08em; padding: 8px 1rem 4px; margin-top: 6px; }
  .nav-item { display: flex; align-items: center; gap: 8px; padding: 7px 1rem; cursor: pointer; color: var(--text2); font-size: 12.5px; border-left: 2px solid transparent; transition: all 0.15s; user-select: none; }
  .nav-item:hover { color: var(--text); background: rgba(255,255,255,0.03); }
  .nav-item.active { color: var(--blue); background: var(--blue-dim); border-left-color: var(--blue); }
  .nav-icon { font-size: 13px; width: 16px; text-align: center; flex-shrink: 0; }
  .nav-pending { font-size: 10px; color: var(--text3); background: var(--surface2); border-radius: 10px; padding: 1px 6px; margin-left: auto; font-family: var(--mono); }
  .nav-disabled { opacity: 0.35; cursor: default; pointer-events: none; }

  /* MAIN */
  .main { flex: 1; display: flex; flex-direction: column; overflow: hidden; }
  .topbar { padding: 0.9rem 1.5rem; border-bottom: 1px solid var(--border); background: var(--surface); display: flex; align-items: center; justify-content: space-between; flex-shrink: 0; }
  .topbar-left { display: flex; flex-direction: column; gap: 2px; }
  .topbar-title { font-size: 14px; font-weight: 500; color: var(--text); }
  .topbar-sub { font-size: 11px; color: var(--text3); }
  .topbar-right { display: flex; align-items: center; gap: 10px; }
  .status-pill { font-size: 11px; padding: 3px 10px; border-radius: 20px; background: var(--amber-dim); color: var(--amber); font-family: var(--mono); border: 1px solid rgba(232,201,74,0.2); }
  .update-info { font-size: 11px; color: var(--text3); }

  .content { flex: 1; overflow-y: auto; padding: 1.25rem 1.5rem; }

  /* SECTIONS */
  .section { display: none; }
  .section.active { display: block; }

  /* KPI GRID */
  .kpi-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; margin-bottom: 1.25rem; }
  .kpi { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius); padding: 1rem 1.1rem; }
  .kpi-label { font-size: 10.5px; color: var(--text2); margin-bottom: 8px; display: flex; align-items: center; gap: 5px; text-transform: uppercase; letter-spacing: 0.05em; }
  .kpi-value { font-size: 28px; font-weight: 300; color: var(--text); line-height: 1; font-family: var(--mono); letter-spacing: -0.02em; }
  .kpi-sub { font-size: 11px; color: var(--text3); margin-top: 5px; }
  .kpi-sub.up     { color: var(--green); }
  .kpi-sub.warn   { color: var(--amber); }
  .kpi-sub.danger { color: var(--red); }

  /* CARDS */
  .chart-card { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius); padding: 1.1rem; margin-bottom: 1rem; }
  .card-header { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 1rem; flex-wrap: wrap; gap: 8px; }
  .card-title { font-size: 13px; font-weight: 500; color: var(--text); }
  .card-sub { font-size: 11px; color: var(--text3); margin-top: 2px; }
  .chart-wrap { position: relative; width: 100%; }
  .h160 { height: 160px; }
  .h200 { height: 200px; }
  .h220 { height: 220px; }
  .h240 { height: 240px; }

  .grid2 { display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; margin-bottom: 1rem; }

  /* LEGEND */
  .legend { display: flex; flex-wrap: wrap; gap: 12px; font-size: 11px; color: var(--text2); }
  .legend span { display: flex; align-items: center; gap: 5px; }
  .ldot { width: 8px; height: 8px; border-radius: 2px; flex-shrink: 0; }

  /* TABLE */
  .dtable { width: 100%; border-collapse: collapse; font-size: 12px; }
  .dtable th { font-size: 10.5px; color: var(--text3); text-align: left; padding: 5px 8px; border-bottom: 1px solid var(--border); text-transform: uppercase; letter-spacing: 0.05em; font-weight: 500; }
  .dtable td { padding: 7px 8px; border-bottom: 1px solid var(--border); color: var(--text); vertical-align: middle; }
  .dtable tr:last-child td { border-bottom: none; }
  .dtable tr:hover td { background: rgba(255,255,255,0.015); }

  /* BADGES */
  .bdg { font-size: 10px; padding: 2px 7px; border-radius: 4px; font-weight: 500; display: inline-block; font-family: var(--mono); }
  .bdg-blue   { background: var(--blue-dim);   color: var(--blue); }
  .bdg-green  { background: var(--green-dim);  color: var(--green); }
  .bdg-amber  { background: var(--amber-dim);  color: var(--amber); }
  .bdg-red    { background: var(--red-dim);    color: var(--red); }
  .bdg-orange { background: var(--orange-dim); color: var(--orange); }

  /* PILLS */
  .month-pills { display: flex; gap: 6px; flex-wrap: wrap; }
  .mpill { font-size: 11px; padding: 4px 12px; border-radius: 20px; border: 1px solid var(--border2); color: var(--text2); cursor: pointer; background: transparent; transition: all 0.15s; font-family: var(--font); }
  .mpill:hover { border-color: var(--blue); color: var(--text); }
  .mpill.active { background: var(--blue-dim); color: var(--blue); border-color: rgba(74,143,232,0.3); }

  /* STACKED BAR */
  .sbar-row { display: flex; align-items: center; gap: 10px; margin-bottom: 8px; }
  .sbar-label { font-size: 11px; color: var(--text2); width: 28px; flex-shrink: 0; font-family: var(--mono); }
  .sbar-bg { flex: 1; height: 24px; border-radius: 4px; overflow: hidden; display: flex; background: var(--surface2); }
  .sbar-seg { height: 100%; display: flex; align-items: center; justify-content: center; font-size: 10px; font-weight: 500; overflow: hidden; white-space: nowrap; padding: 0 4px; }
  .sbar-right { font-size: 11px; color: var(--text3); width: 44px; text-align: right; font-family: var(--mono); flex-shrink: 0; }

  /* CALLOUT */
  .callout { border-left: 2px solid var(--orange); padding: 0.75rem 1rem; background: rgba(232,144,74,0.06); border-radius: 0 var(--radius-sm) var(--radius-sm) 0; font-size: 12px; color: var(--text2); line-height: 1.6; margin-top: 1rem; }
  .callout strong { color: var(--text); font-weight: 500; }

  /* ALERT */
  .alert { background: rgba(232,74,111,0.07); border: 1px solid rgba(232,74,111,0.18); border-radius: var(--radius-sm); padding: 0.75rem 1rem; font-size: 12px; color: var(--text2); line-height: 1.6; margin-bottom: 1.25rem; display: flex; gap: 10px; align-items: flex-start; }
  .alert-icon { font-size: 16px; flex-shrink: 0; margin-top: 1px; }
  .alert strong { color: var(--red); }

  /* MINI STATS */
  .mini-stats { display: grid; grid-template-columns: repeat(3,1fr); gap: 8px; margin-bottom: 1rem; }
  .mini-stat { background: var(--surface2); border-radius: var(--radius-sm); padding: 0.7rem 0.85rem; }
  .ms-label { font-size: 10px; color: var(--text3); text-transform: uppercase; letter-spacing: 0.05em; margin-bottom: 4px; }
  .ms-value { font-size: 20px; font-weight: 300; color: var(--text); font-family: var(--mono); }
  .ms-sub { font-size: 10px; color: var(--text3); margin-top: 2px; }

  /* PENDING */
  .pending-grid { display: grid; grid-template-columns: repeat(3,1fr); gap: 1rem; margin-top: 0.5rem; }
  .pending-block { background: var(--surface2); border: 1px dashed var(--border2); border-radius: var(--radius); padding: 2rem 1.5rem; text-align: center; }
  .pending-block .pb-icon { font-size: 24px; margin-bottom: 10px; opacity: 0.3; }
  .pending-block .pb-title { font-size: 13px; font-weight: 500; color: var(--text2); margin-bottom: 4px; }
  .pending-block .pb-sub { font-size: 11px; color: var(--text3); }

  /* SCROLLBAR */
  ::-webkit-scrollbar { width: 6px; height: 6px; }
  ::-webkit-scrollbar-track { background: transparent; }
  ::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.1); border-radius: 3px; }
</style>
</head>
<body>
<div class="shell">

  <!-- ══ SIDEBAR ══ -->
  <div class="sidebar">
    <div class="sidebar-logo">
      <div class="logo-text">Controle de<br>Dispositivos</div>
      <div class="logo-sub">Empréstimos de computadores</div>
      <div class="logo-badge">Fev–Mai 2026</div>
    </div>

    <div class="nav-section">Visões</div>
    <div class="nav-item active" data-section="visao-geral">
      <span class="nav-icon">◈</span> Visão Geral
    </div>
    <div class="nav-item" data-section="emprestimos">
      <span class="nav-icon">⇄</span> Empréstimos
    </div>
    <div class="nav-item" data-section="carga">
      <span class="nav-icon">◷</span> Carga Operacional
    </div>

    <div class="nav-section">Em breve</div>
    <div class="nav-item nav-disabled">
      <span class="nav-icon">⚙</span> Reparos <span class="nav-pending">—</span>
    </div>
    <div class="nav-item nav-disabled">
      <span class="nav-icon">◉</span> Licenças <span class="nav-pending">—</span>
    </div>
    <div class="nav-item nav-disabled">
      <span class="nav-icon">▣</span> Parque <span class="nav-pending">—</span>
    </div>
  </div>

  <!-- ══ MAIN ══ -->
  <div class="main">
    <div class="topbar">
      <div class="topbar-left">
        <div class="topbar-title" id="topbar-title">Visão Geral</div>
        <div class="topbar-sub">2 colaboradores · ciclo completo (retirada + devolução) · 15 min/empréstimo</div>
      </div>
      <div class="topbar-right">
        <div class="update-info">Atualizado em 25/05/2026</div>
        <div class="status-pill">Maio em andamento</div>
      </div>
    </div>

    <div class="content">

      <!-- ══════════════════════════════════════
           VISÃO GERAL
      ══════════════════════════════════════ -->
      <div class="section active" id="sec-visao-geral">
        <div class="kpi-grid">
          <div class="kpi">
            <div class="kpi-label">⇄ Total de empréstimos</div>
            <div class="kpi-value">526</div>
            <div class="kpi-sub">Fevereiro a Maio 2026</div>
          </div>
          <div class="kpi">
            <div class="kpi-label">≈ Média mensal</div>
            <div class="kpi-value">131</div>
            <div class="kpi-sub">empréstimos por mês</div>
          </div>
          <div class="kpi">
            <div class="kpi-label">◷ Carga real / colab. / mês</div>
            <div class="kpi-value">24,7h</div>
            <div class="kpi-sub warn">atend. + recomp. + interrupções</div>
          </div>
          <div class="kpi">
            <div class="kpi-label">⚠ Livre efetivo · abril (pico)</div>
            <div class="kpi-value">~27min</div>
            <div class="kpi-sub danger">por dia no período 13h–17h</div>
          </div>
        </div>

        <div class="chart-card">
          <div class="card-header">
            <div>
              <div class="card-title">Evolução mensal de empréstimos</div>
              <div class="card-sub">Total por mês · Maio ainda em andamento</div>
            </div>
            <div class="legend">
              <span><span class="ldot" style="background:var(--blue);"></span>concluído</span>
              <span><span class="ldot" style="background:var(--amber);"></span>em andamento</span>
            </div>
          </div>
          <div class="chart-wrap h200"><canvas id="cMensal"></canvas></div>
        </div>

        <div class="grid2">
          <div class="chart-card" style="margin-bottom:0;">
            <div class="card-header">
              <div>
                <div class="card-title">Carga operacional real por mês</div>
                <div class="card-sub">Horas por colaborador · três camadas</div>
              </div>
            </div>
            <div class="legend" style="margin-bottom:0.75rem;">
              <span><span class="ldot" style="background:var(--blue);"></span>atendimento</span>
              <span><span class="ldot" style="background:var(--orange);"></span>recomposição</span>
              <span><span class="ldot" style="background:var(--amber);"></span>interrupções</span>
            </div>
            <div class="chart-wrap h200"><canvas id="cCargaGeral"></canvas></div>
          </div>
          <div class="chart-card" style="margin-bottom:0;">
            <div class="card-header">
              <div>
                <div class="card-title">% da jornada consumida</div>
                <div class="card-sub">Evolução e comparativo das três leituras</div>
              </div>
            </div>
            <div class="chart-wrap h200"><canvas id="cPctGeral"></canvas></div>
            <div class="callout">
              Em abril, pico de <strong>22,6% da jornada</strong> consumida — com apenas <strong>~27 min livres/dia</strong> no período 13h–17h por colaborador.
            </div>
          </div>
        </div>
      </div>

      <!-- ══════════════════════════════════════
           EMPRÉSTIMOS
      ══════════════════════════════════════ -->
      <div class="section" id="sec-emprestimos">
        <div class="kpi-grid">
          <div class="kpi">
            <div class="kpi-label">⇄ Total geral</div>
            <div class="kpi-value">526</div>
            <div class="kpi-sub">Fev–Mai 2026</div>
          </div>
          <div class="kpi">
            <div class="kpi-label">↑ Maior mês</div>
            <div class="kpi-value">178</div>
            <div class="kpi-sub up">Abril 2026</div>
          </div>
          <div class="kpi">
            <div class="kpi-label">≈ Média mensal</div>
            <div class="kpi-value">131,5</div>
            <div class="kpi-sub">fev–abr (meses completos)</div>
          </div>
          <div class="kpi">
            <div class="kpi-label">◷ Maio · em andamento</div>
            <div class="kpi-value">171</div>
            <div class="kpi-sub warn">sem. 4 com apenas 1 até agora</div>
          </div>
        </div>

        <div class="chart-card">
          <div class="card-header">
            <div>
              <div class="card-title">Empréstimos por semana</div>
              <div class="card-sub">Selecione o mês para detalhar</div>
            </div>
            <div class="month-pills" id="weekPills">
              <div class="mpill active" data-idx="0">Fevereiro</div>
              <div class="mpill" data-idx="1">Março</div>
              <div class="mpill" data-idx="2">Abril</div>
              <div class="mpill" data-idx="3">Maio</div>
            </div>
          </div>
          <div class="grid2" style="margin-bottom:0;">
            <div class="chart-wrap h220"><canvas id="cSemanas"></canvas></div>
            <div>
              <div class="mini-stats" id="weekStats"></div>
              <table class="dtable" id="weekTable"></table>
            </div>
          </div>
        </div>

        <div class="chart-card">
          <div class="card-header">
            <div>
              <div class="card-title">Comparativo mensal completo</div>
              <div class="card-sub">Total e variação mês a mês</div>
            </div>
            <div class="legend">
              <span><span class="ldot" style="background:var(--blue);"></span>concluído</span>
              <span><span class="ldot" style="background:var(--amber);"></span>em andamento</span>
            </div>
          </div>
          <div class="chart-wrap h200"><canvas id="cMensalEmp"></canvas></div>
        </div>
      </div>

      <!-- ══════════════════════════════════════
           CARGA OPERACIONAL
      ══════════════════════════════════════ -->
      <div class="section" id="sec-carga">
        <div class="alert">
          <div class="alert-icon">⚠</div>
          <div>O período 13h–17h está operacionalmente <strong>saturado</strong>. Em abril, cada colaborador tem apenas <strong>~27 min livres/dia</strong> — empréstimos distribuídos ao longo do período mais <strong>~13,5 interrupções/dia</strong> de alunos do 5º e 6º anos consomem quase toda a janela disponível.</div>
        </div>

        <div class="kpi-grid">
          <div class="kpi">
            <div class="kpi-label">◷ Custo real / empréstimo</div>
            <div class="kpi-value">22,5</div>
            <div class="kpi-sub">min · atend. (15) + recomp. (7,5)</div>
          </div>
          <div class="kpi">
            <div class="kpi-label">⚡ Interrupções/dia</div>
            <div class="kpi-value">~13,5</div>
            <div class="kpi-sub warn">alunos 5º e 6º anos · não planejadas</div>
          </div>
          <div class="kpi">
            <div class="kpi-label">◈ Recomp. interrupções/dia</div>
            <div class="kpi-value">~101</div>
            <div class="kpi-sub danger">min · 13,5 × 7,5 min por colaborador</div>
          </div>
          <div class="kpi">
            <div class="kpi-label">◷ % jornada · pico (abril)</div>
            <div class="kpi-value">22,6%</div>
            <div class="kpi-sub danger">incluindo todas as interrupções</div>
          </div>
        </div>

        <div class="chart-card">
          <div class="card-header">
            <div>
              <div class="card-title">Como o período 13h–17h é consumido · média diária por colaborador</div>
              <div class="card-sub">240 min disponíveis por dia · distribuição estimada por mês</div>
            </div>
            <div class="legend">
              <span><span class="ldot" style="background:var(--blue);"></span>atendimento</span>
              <span><span class="ldot" style="background:var(--orange);"></span>recomp. empréstimos</span>
              <span><span class="ldot" style="background:var(--amber);"></span>recomp. interrupções</span>
              <span><span class="ldot" style="background:#2A2F3E;border:1px solid rgba(255,255,255,0.1);"></span>livre efetivo</span>
            </div>
          </div>
          <div id="stackedPeriodo" style="margin-top:0.75rem;"></div>
        </div>

        <div class="grid2">
          <div class="chart-card" style="margin-bottom:0;">
            <div class="card-header">
              <div>
                <div class="card-title">Carga mensal empilhada / colab.</div>
                <div class="card-sub">Três camadas acumuladas em horas</div>
              </div>
            </div>
            <div class="legend" style="margin-bottom:0.75rem;">
              <span><span class="ldot" style="background:var(--blue);"></span>atendimento</span>
              <span><span class="ldot" style="background:var(--orange);"></span>recomposição</span>
              <span><span class="ldot" style="background:var(--amber);"></span>interrupções</span>
            </div>
            <div class="chart-wrap h200"><canvas id="cCargaStack"></canvas></div>
          </div>
          <div class="chart-card" style="margin-bottom:0;">
            <div class="card-header">
              <div>
                <div class="card-title">Impacto real na jornada individual</div>
                <div class="card-sub">Comparativo das três leituras de carga</div>
              </div>
            </div>
            <table class="dtable">
              <thead>
                <tr>
                  <th>Mês</th>
                  <th>Só atend.</th>
                  <th>+ Recomp.</th>
                  <th>+ Interrup.</th>
                  <th>Nível</th>
                </tr>
              </thead>
              <tbody id="impactoTbody"></tbody>
            </table>
            <div class="callout">
              Nos meses completos (fev–abr), a carga real média é <strong>19,4% da jornada</strong>. Em abril, o pico de <strong>22,6%</strong> deixa apenas <strong>~27 min/dia</strong> de janela efetiva para outras atividades no período 13h–17h.
            </div>
          </div>
        </div>

        <div class="chart-card">
          <div class="card-header">
            <div>
              <div class="card-title">Tempo livre efetivo no período 13h–17h · por colaborador</div>
              <div class="card-sub">Quanto sobra após empréstimos e interrupções · total mensal</div>
            </div>
          </div>
          <table class="dtable">
            <thead>
              <tr>
                <th>Mês</th>
                <th>Dias úteis</th>
                <th>Disponível</th>
                <th>Atendimento</th>
                <th>Recomp. empréstimos</th>
                <th>Recomp. interrupções</th>
                <th>Livre efetivo</th>
              </tr>
            </thead>
            <tbody id="livreTbody"></tbody>
          </table>
        </div>

        <div class="chart-card">
          <div class="card-header">
            <div>
              <div class="card-title">Seções em desenvolvimento</div>
              <div class="card-sub">Aguardando dados para expansão do painel</div>
            </div>
          </div>
          <div class="pending-grid">
            <div class="pending-block">
              <div class="pb-icon">⚙</div>
              <div class="pb-title">Reparos</div>
              <div class="pb-sub">Registro de manutenções e tempo fora de uso</div>
            </div>
            <div class="pending-block">
              <div class="pb-icon">◉</div>
              <div class="pb-title">Licenciamentos</div>
              <div class="pb-sub">Controle de software licenciado vs. em uso</div>
            </div>
            <div class="pending-block">
              <div class="pb-icon">▣</div>
              <div class="pb-title">Parque de dispositivos</div>
              <div class="pb-sub">Inventário aprovisionado vs. real</div>
            </div>
          </div>
        </div>
      </div>

    </div><!-- /content -->
  </div><!-- /main -->
</div><!-- /shell -->

<script>
// ════════════════════════════════════════
// DADOS
// ════════════════════════════════════════
const ATEND=15, RECOMP=7.5, INT_MIN=7.5, INT_DIA=13.5, COLABS=2, PERIODO=4, JORNADA=10;

const meses = [
  { mes:'Fevereiro', abrev:'Fev', emp:86,  dias:19, partial:false,
    semanas:[{l:'Sem 1',p:'02–06/fev',v:20},{l:'Sem 2',p:'09–13/fev',v:15},{l:'Sem 3',p:'16–20/fev',v:7},{l:'Sem 4',p:'23–27/fev',v:44}] },
  { mes:'Março',     abrev:'Mar', emp:107, dias:21, partial:false,
    semanas:[{l:'Sem 1',p:'02–06/mar',v:20},{l:'Sem 2',p:'09–13/mar',v:38},{l:'Sem 3',p:'16–20/mar',v:20},{l:'Sem 4',p:'23–27/mar',v:22},{l:'Sem 5',p:'30–31/mar',v:7}] },
  { mes:'Abril',     abrev:'Abr', emp:178, dias:22, partial:false,
    semanas:[{l:'Sem 1',p:'01–03/abr',v:35},{l:'Sem 2',p:'06–10/abr',v:31},{l:'Sem 3',p:'13–17/abr',v:38},{l:'Sem 4',p:'20–24/abr',v:25},{l:'Sem 5',p:'27–30/abr',v:49}] },
  { mes:'Maio',      abrev:'Mai', emp:171, dias:17, partial:true,
    semanas:[{l:'Sem 1',p:'04–08/mai',v:65},{l:'Sem 2',p:'11–15/mai',v:46},{l:'Sem 3',p:'18–22/mai',v:59},{l:'Sem 4',p:'25–29/mai',v:1,partial:true}] },
];

meses.forEach(d => {
  d.jornada      = d.dias * JORNADA;
  d.dispPer      = d.dias * PERIODO;
  d.hAtend       = (d.emp * ATEND)  / 60 / COLABS;
  d.hRecomp      = (d.emp * RECOMP) / 60 / COLABS;
  d.hInt         = (d.dias * INT_DIA * INT_MIN) / 60;
  d.hTotal       = d.hAtend + d.hRecomp + d.hInt;
  d.hLivre       = Math.max(0, d.dispPer - d.hTotal);
  d.pctSo        = (d.hAtend / d.jornada) * 100;
  d.pctRecomp    = ((d.hAtend + d.hRecomp) / d.jornada) * 100;
  d.pctTotal     = (d.hTotal / d.jornada) * 100;
  d.minAtendDia  = (d.hAtend  / d.dias) * 60;
  d.minRecompDia = (d.hRecomp / d.dias) * 60;
  d.minIntDia    = INT_DIA * INT_MIN;
  d.minLivreDia  = Math.max(0, PERIODO * 60 - d.minAtendDia - d.minRecompDia - d.minIntDia);
});

// ════════════════════════════════════════
// HELPERS
// ════════════════════════════════════════
const f1 = v => v.toFixed(1);
const fh = v => v.toFixed(1) + 'h';
const fp = v => v.toFixed(1) + '%';
const fm = v => Math.round(v) + 'min';
const tC = 'rgba(255,255,255,0.38)';
const gC = 'rgba(255,255,255,0.05)';

function badgeNivel(p) {
  if (p > 20) return `<span class="bdg bdg-red">crítico</span>`;
  if (p > 15) return `<span class="bdg bdg-orange">alto</span>`;
  if (p > 10) return `<span class="bdg bdg-amber">moderado</span>`;
  return `<span class="bdg bdg-green">baixo</span>`;
}
function badgeLivre(h) {
  if (h < 5)  return `<span class="bdg bdg-red">${fh(h)}</span>`;
  if (h < 20) return `<span class="bdg bdg-amber">${fh(h)}</span>`;
  return `<span class="bdg bdg-green">${fh(h)}</span>`;
}

const baseOpts = () => ({
  responsive: true, maintainAspectRatio: false,
  plugins: { legend: { display: false } },
  scales: {
    x: { ticks: { color: tC, font: { size: 11 } }, grid: { display: false }, border: { display: false } },
    y: { beginAtZero: true, ticks: { color: tC, font: { size: 11 } }, grid: { color: gC }, border: { display: false } }
  }
});

// ════════════════════════════════════════
// VISÃO GERAL
// ════════════════════════════════════════
new Chart(document.getElementById('cMensal'), {
  type: 'bar',
  data: {
    labels: meses.map(d => d.abrev),
    datasets: [{ data: meses.map(d => d.emp), backgroundColor: ['#4A8FE8','#4A8FE8','#4A8FE8','#E8C94A'], borderRadius: 5, borderSkipped: false }]
  },
  options: {
    ...baseOpts(),
    plugins: { legend: { display: false }, tooltip: { callbacks: { label: c => ' ' + c.parsed.y + ' empréstimos' + (c.dataIndex === 3 ? ' (parcial)' : '') } } }
  }
});

new Chart(document.getElementById('cCargaGeral'), {
  type: 'bar',
  data: {
    labels: meses.map(d => d.abrev),
    datasets: [
      { label: 'Atendimento', data: meses.map(d => +f1(d.hAtend)), backgroundColor: '#4A8FE8', stack: 's', borderRadius: 0, borderSkipped: false },
      { label: 'Recomposição', data: meses.map(d => +f1(d.hRecomp)), backgroundColor: '#E8904A', stack: 's', borderRadius: 0, borderSkipped: false },
      { label: 'Interrupções', data: meses.map(d => +f1(d.hInt)), backgroundColor: '#E8C94A', stack: 's', borderRadius: 5, borderSkipped: 'bottom' },
    ]
  },
  options: {
    responsive: true, maintainAspectRatio: false,
    plugins: { legend: { display: false }, tooltip: { callbacks: { label: c => ' ' + c.dataset.label + ': ' + c.parsed.y + 'h' } } },
    scales: {
      x: { stacked: true, ticks: { color: tC, font: { size: 11 } }, grid: { display: false }, border: { display: false } },
      y: { stacked: true, beginAtZero: true, ticks: { color: tC, font: { size: 11 }, callback: v => v + 'h' }, grid: { color: gC }, border: { display: false } }
    }
  }
});

new Chart(document.getElementById('cPctGeral'), {
  type: 'line',
  data: {
    labels: meses.map(d => d.abrev),
    datasets: [
      { label: 'Só atend.', data: meses.map(d => +f1(d.pctSo)), borderColor: 'rgba(74,143,232,0.5)', backgroundColor: 'transparent', tension: 0.4, pointRadius: 3, borderWidth: 1.5, borderDash: [4,3] },
      { label: '+Recomp.', data: meses.map(d => +f1(d.pctRecomp)), borderColor: 'rgba(232,144,74,0.6)', backgroundColor: 'transparent', tension: 0.4, pointRadius: 3, borderWidth: 1.5, borderDash: [4,3] },
      { label: '+Interrup.', data: meses.map(d => +f1(d.pctTotal)), borderColor: '#E8C94A', backgroundColor: 'rgba(232,201,74,0.06)', tension: 0.4, pointRadius: 4, borderWidth: 2, fill: true },
    ]
  },
  options: {
    responsive: true, maintainAspectRatio: false,
    plugins: { legend: { display: false }, tooltip: { callbacks: { label: c => ' ' + c.dataset.label + ': ' + c.parsed.y + '%' } } },
    scales: {
      x: { ticks: { color: tC, font: { size: 11 } }, grid: { display: false }, border: { display: false } },
      y: { beginAtZero: true, ticks: { color: tC, font: { size: 11 }, callback: v => v + '%' }, grid: { color: gC }, border: { display: false } }
    }
  }
});

// ════════════════════════════════════════
// EMPRÉSTIMOS
// ════════════════════════════════════════
let semChart = null;

function buildWeekUI(idx) {
  const m = meses[idx];
  const maxV = Math.max(...m.semanas.map(s => s.v));
  if (semChart) semChart.destroy();
  semChart = new Chart(document.getElementById('cSemanas'), {
    type: 'bar',
    data: {
      labels: m.semanas.map(s => s.l),
      datasets: [{ data: m.semanas.map(s => s.v), backgroundColor: m.semanas.map(s => s.partial ? '#E8C94A' : '#4A8FE8'), borderRadius: 5, borderSkipped: false }]
    },
    options: {
      ...baseOpts(),
      plugins: { legend: { display: false }, tooltip: { callbacks: { label: c => ' ' + c.parsed.y + ' empréstimos' + (m.semanas[c.dataIndex].partial ? ' (parcial)' : '') } } }
    }
  });

  const total = m.semanas.reduce((s, x) => s + x.v, 0);
  const avg   = total / m.semanas.length;
  document.getElementById('weekStats').innerHTML = `
    <div class="mini-stat"><div class="ms-label">Total</div><div class="ms-value">${total}</div><div class="ms-sub">${m.mes}</div></div>
    <div class="mini-stat"><div class="ms-label">Média/sem</div><div class="ms-value">${f1(avg)}</div><div class="ms-sub">empréstimos</div></div>
    <div class="mini-stat"><div class="ms-label">Pico</div><div class="ms-value">${maxV}</div><div class="ms-sub">${m.semanas.find(s => s.v === maxV).l}</div></div>`;

  let html = '<thead><tr><th>Semana</th><th>Período</th><th>Qtd</th><th>Var.</th></tr></thead><tbody>';
  m.semanas.forEach((s, i) => {
    const diff    = i > 0 ? s.v - m.semanas[i-1].v : null;
    const diffStr = diff === null ? '—' : diff >= 0
      ? `<span style="color:var(--green);font-family:var(--mono);">+${diff}</span>`
      : `<span style="color:var(--red);font-family:var(--mono);">${diff}</span>`;
    html += `<tr>
      <td>${s.l}${s.partial ? ' <span class="bdg bdg-amber">parcial</span>' : ''}</td>
      <td style="color:var(--text3);">${s.p}</td>
      <td style="font-family:var(--mono);font-weight:500;">${s.v}</td>
      <td>${diffStr}</td>
    </tr>`;
  });
  html += '</tbody>';
  document.getElementById('weekTable').innerHTML = html;
}

buildWeekUI(0);
document.getElementById('weekPills').addEventListener('click', e => {
  const pill = e.target.closest('.mpill');
  if (!pill) return;
  document.querySelectorAll('.mpill').forEach(p => p.classList.remove('active'));
  pill.classList.add('active');
  buildWeekUI(parseInt(pill.dataset.idx));
});

new Chart(document.getElementById('cMensalEmp'), {
  type: 'bar',
  data: {
    labels: meses.map(d => d.mes),
    datasets: [{ data: meses.map(d => d.emp), backgroundColor: ['#4A8FE8','#4A8FE8','#4A8FE8','#E8C94A'], borderRadius: 5, borderSkipped: false }]
  },
  options: {
    ...baseOpts(),
    plugins: { legend: { display: false }, tooltip: { callbacks: { label: c => ' ' + c.parsed.y + ' empréstimos' + (c.dataIndex === 3 ? ' (parcial)' : '') } } }
  }
});

// ════════════════════════════════════════
// CARGA OPERACIONAL
// ════════════════════════════════════════
const spDiv = document.getElementById('stackedPeriodo');
meses.forEach(d => {
  const total = PERIODO * 60;
  const pA = (d.minAtendDia  / total * 100).toFixed(1);
  const pR = (d.minRecompDia / total * 100).toFixed(1);
  const pI = (d.minIntDia    / total * 100).toFixed(1);
  const pL = Math.max(0, (d.minLivreDia / total * 100)).toFixed(1);
  const lA = parseFloat(pA) > 8 ? fm(d.minAtendDia)  : '';
  const lR = parseFloat(pR) > 5 ? fm(d.minRecompDia) : '';
  const lI = parseFloat(pI) > 5 ? fm(d.minIntDia)    : '';
  const lL = parseFloat(pL) > 5 ? fm(d.minLivreDia)  : '';
  spDiv.innerHTML += `<div class="sbar-row">
    <div class="sbar-label">${d.abrev}</div>
    <div class="sbar-bg">
      <div class="sbar-seg" style="width:${pA}%;background:#4A8FE8;color:#fff;">${lA}</div>
      <div class="sbar-seg" style="width:${pR}%;background:#E8904A;color:#fff;">${lR}</div>
      <div class="sbar-seg" style="width:${pI}%;background:#E8C94A;color:#1A1200;">${lI}</div>
      <div class="sbar-seg" style="width:${pL}%;background:#2A2F3E;color:rgba(255,255,255,0.2);">${lL}</div>
    </div>
    <div class="sbar-right">${(100 - parseFloat(pL)).toFixed(0)}% uso</div>
  </div>`;
});
spDiv.innerHTML += `<div style="font-size:11px;color:var(--text3);margin-top:6px;">* Maio parcial até 25/05/2026</div>`;

new Chart(document.getElementById('cCargaStack'), {
  type: 'bar',
  data: {
    labels: meses.map(d => d.abrev),
    datasets: [
      { label: 'Atendimento',   data: meses.map(d => +f1(d.hAtend)),  backgroundColor: '#4A8FE8', stack: 's', borderRadius: 0, borderSkipped: false },
      { label: 'Recomposição',  data: meses.map(d => +f1(d.hRecomp)), backgroundColor: '#E8904A', stack: 's', borderRadius: 0, borderSkipped: false },
      { label: 'Interrupções',  data: meses.map(d => +f1(d.hInt)),    backgroundColor: '#E8C94A', stack: 's', borderRadius: 5, borderSkipped: 'bottom' },
    ]
  },
  options: {
    responsive: true, maintainAspectRatio: false,
    plugins: { legend: { display: false }, tooltip: { callbacks: { label: c => ' ' + c.dataset.label + ': ' + c.parsed.y + 'h' } } },
    scales: {
      x: { stacked: true, ticks: { color: tC, font: { size: 11 } }, grid: { display: false }, border: { display: false } },
      y: { stacked: true, beginAtZero: true, ticks: { color: tC, font: { size: 11 }, callback: v => v + 'h' }, grid: { color: gC }, border: { display: false } }
    }
  }
});

const impTb = document.getElementById('impactoTbody');
meses.forEach(d => {
  impTb.innerHTML += `<tr>
    <td>${d.abrev}${d.partial ? ` <span class="bdg bdg-amber">parcial</span>` : ''}</td>
    <td style="color:var(--text3);font-family:var(--mono);">${fp(d.pctSo)}</td>
    <td style="color:var(--text3);font-family:var(--mono);">${fp(d.pctRecomp)}</td>
    <td style="font-family:var(--mono);font-weight:500;">${fp(d.pctTotal)}</td>
    <td>${badgeNivel(d.pctTotal)}</td>
  </tr>`;
});

const livTb = document.getElementById('livreTbody');
meses.forEach(d => {
  livTb.innerHTML += `<tr>
    <td>${d.mes}${d.partial ? ` <span class="bdg bdg-amber">parcial</span>` : ''}</td>
    <td style="font-family:var(--mono);">${d.dias}</td>
    <td style="font-family:var(--mono);">${fh(d.dispPer)}</td>
    <td style="font-family:var(--mono);color:var(--blue);">${fh(d.hAtend)}</td>
    <td style="font-family:var(--mono);color:var(--orange);">${fh(d.hRecomp)}</td>
    <td style="font-family:var(--mono);color:var(--amber);">${fh(d.hInt)}</td>
    <td>${badgeLivre(d.hLivre)}</td>
  </tr>`;
});

// ════════════════════════════════════════
// NAVEGAÇÃO
// ════════════════════════════════════════
const sectionTitles = {
  'visao-geral': 'Visão Geral',
  'emprestimos': 'Empréstimos',
  'carga':       'Carga Operacional'
};

document.querySelectorAll('.nav-item[data-section]').forEach(item => {
  item.addEventListener('click', () => {
    document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
    document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
    item.classList.add('active');
    const key = item.dataset.section;
    document.getElementById('sec-' + key).classList.add('active');
    document.getElementById('topbar-title').textContent = sectionTitles[key];
  });
});
</script>
</body>
</html>
