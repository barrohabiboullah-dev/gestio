<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>PharmaGest — Gestion de pharmacie</title>
<style>
  *{box-sizing:border-box;margin:0;padding:0}
  :root{
    --bg:#f4f6f8; --card:#fff; --text:#0f172a; --muted:#64748b;
    --primary:#0d9488; --primary-dark:#0f766e;
    --danger:#dc2626; --warn:#d97706; --ok:#16a34a;
    --border:#e2e8f0; --radius:14px;
    --shadow:0 1px 3px rgba(15,23,42,.08), 0 1px 2px rgba(15,23,42,.04);
  }
  body{
    font-family:system-ui,-apple-system,'Segoe UI',Roboto,Helvetica,Arial,sans-serif;
    background:var(--bg); color:var(--text); font-size:15px; line-height:1.5;
  }
  button{font-family:inherit}

  /* ---------- Barre supérieure ---------- */
  .topbar{background:var(--card);border-bottom:1px solid var(--border);position:sticky;top:0;z-index:50}
  .topbar-inner{max-width:1240px;margin:0 auto;padding:12px 20px;display:flex;align-items:center;gap:26px;flex-wrap:wrap}
  .brand{display:flex;align-items:center;gap:12px}
  .logo{width:42px;height:42px;border-radius:12px;background:linear-gradient(135deg,#0d9488,#2dd4bf);
        color:#fff;display:grid;place-items:center;font-size:22px;font-weight:700;flex:none}
  .brand h1{font-size:17px;font-weight:700;letter-spacing:-.2px}
  .brand p{font-size:12px;color:var(--muted)}
  .tabs{display:flex;gap:6px;flex-wrap:wrap}
  .tabs button{border:0;background:transparent;padding:9px 16px;border-radius:10px;
               font-size:14px;font-weight:600;color:var(--muted);cursor:pointer;transition:.15s}
  .tabs button:hover{background:#f1f5f9;color:var(--text)}
  .tabs button.active{background:var(--primary);color:#fff}

  /* ---------- Contenu ---------- */
  .wrap{max-width:1240px;margin:0 auto;padding:22px 20px 60px}
  .tab{display:none;animation:fade .2s ease}
  .tab.active{display:block}
  @keyframes fade{from{opacity:0;transform:translateY(4px)}to{opacity:1;transform:none}}

  .card{background:var(--card);border:1px solid var(--border);border-radius:var(--radius);
        box-shadow:var(--shadow);padding:18px}
  .card h2{font-size:15px;font-weight:700;margin-bottom:14px;display:flex;align-items:center;gap:8px}

  .grid-kpi{display:grid;grid-template-columns:repeat(auto-fit,minmax(210px,1fr));gap:14px;margin-bottom:18px}
  .kpi{background:var(--card);border:1px solid var(--border);border-radius:var(--radius);
       padding:16px 18px;box-shadow:var(--shadow)}
  .kpi .lbl{font-size:12px;font-weight:600;color:var(--muted);text-transform:uppercase;letter-spacing:.4px}
  .kpi .val{font-size:26px;font-weight:700;margin-top:6px;letter-spacing:-.5px}
  .kpi .sub{font-size:12px;color:var(--muted);margin-top:2px}
  .kpi.accent{background:linear-gradient(135deg,#0d9488,#14b8a6);border-color:transparent;color:#fff}
  .kpi.accent .lbl,.kpi.accent .sub{color:rgba(255,255,255,.85)}

  .grid-2{display:grid;grid-template-columns:1fr 1fr;gap:16px}
  @media(max-width:820px){.grid-2{grid-template-columns:1fr}}

  ul.alertes{list-style:none;display:flex;flex-direction:column;gap:8px;max-height:290px;overflow:auto}
  ul.alertes li{display:flex;justify-content:space-between;align-items:center;gap:10px;
                padding:9px 12px;border-radius:10px;background:#f8fafc;font-size:14px}
  ul.alertes li.vide{background:transparent;color:var(--muted);justify-content:center;font-style:italic}

  .badge{padding:3px 9px;border-radius:999px;font-size:12px;font-weight:700;white-space:nowrap}
  .badge-ok{background:#dcfce7;color:#166534}
  .badge-warn{background:#fef3c7;color:#92400e}
  .badge-danger{background:#fee2e2;color:#991b1b}

  /* ---------- Barre d'outils ---------- */
  .toolbar{display:flex;gap:10px;flex-wrap:wrap;align-items:center;margin-bottom:14px}
  .toolbar input,.toolbar select{padding:9px 12px;border:1px solid var(--border);border-radius:10px;
        font-size:14px;font-family:inherit;background:#fff;color:var(--text);outline:none}
  .toolbar input:focus,.toolbar select:focus{border-color:var(--primary);box-shadow:0 0 0 3px rgba(13,148,136,.12)}
  .toolbar input[type=search]{min-width:240px;flex:1}
  .spacer{flex:1}

  .btn{border:0;border-radius:10px;padding:10px 16px;font-size:14px;font-weight:600;cursor:pointer;transition:.15s}
  .btn-primary{background:var(--primary);color:#fff}
  .btn-primary:hover{background:var(--primary-dark)}
  .btn-ghost{background:#f1f5f9;color:var(--text)}
  .btn-ghost:hover{background:#e2e8f0}
  .btn-block{width:100%}
  .btn:disabled{opacity:.5;cursor:not-allowed}

  /* ---------- Tableaux ---------- */
  .table-scroll{overflow-x:auto;border-radius:var(--radius);border:1px solid var(--border);background:#fff}
  table{width:100%;border-collapse:collapse;font-size:14px;min-width:720px}
  thead th{background:#f8fafc;text-align:left;padding:11px 14px;font-size:12px;font-weight:700;
           color:var(--muted);text-transform:uppercase;letter-spacing:.4px;border-bottom:1px solid var(--border)}
  tbody td{padding:11px 14px;border-bottom:1px solid #f1f5f9;vertical-align:middle}
  tbody tr:last-child td{border-bottom:0}
  tbody tr:hover{background:#fafcfc}
  td.num,th.num{text-align:right;font-variant-numeric:tabular-nums}
  .sub{font-size:12px;color:var(--muted)}
  .vide{text-align:center;color:var(--muted);font-style:italic;padding:26px}
  .actions{display:flex;gap:6px}
  .btn-icon{border:1px solid var(--border);background:#fff;border-radius:8px;width:32px;height:32px;
            cursor:pointer;font-size:14px;display:grid;place-items:center;transition:.15s}
  .btn-icon:hover{background:#f1f5f9}

  /* ---------- Vente ---------- */
  .vente-layout{display:grid;grid-template-columns:1.7fr 1fr;gap:16px;align-items:start}
  @media(max-width:900px){.vente-layout{grid-template-columns:1fr}}
  .catalogue{display:grid;grid-template-columns:repeat(auto-fill,minmax(190px,1fr));gap:10px;
             max-height:560px;overflow:auto;padding:2px}
  .med-card{border:1px solid var(--border);background:#fff;border-radius:12px;padding:12px;
            text-align:left;cursor:pointer;transition:.15s;display:flex;flex-direction:column;gap:6px}
  .med-card:hover:not(:disabled){border-color:var(--primary);transform:translateY(-2px);
            box-shadow:0 4px 12px rgba(13,148,136,.13)}
  .med-card:disabled{opacity:.45;cursor:not-allowed}
  .med-nom{font-weight:600;font-size:14px;line-height:1.3}
  .med-cat{font-size:11px;color:var(--muted);text-transform:uppercase;letter-spacing:.4px}
  .med-bas{display:flex;justify-content:space-between;align-items:center;margin-top:auto;padding-top:6px}
  .prix{font-weight:700;color:var(--primary-dark)}
  .stock{font-size:12px;color:var(--muted)}
  .stock.bas{color:var(--danger);font-weight:600}

  .panier{display:flex;flex-direction:column;gap:10px;max-height:420px;overflow:auto}
  .ligne-panier{display:grid;grid-template-columns:1fr auto;gap:4px 10px;
                padding:10px;border-radius:10px;background:#f8fafc;font-size:14px}
  .lp-actions{display:flex;align-items:center;gap:6px}
  .lp-total{grid-column:2;text-align:right;font-weight:700;font-size:13px;color:var(--primary-dark)}
  .btn-mini{border:1px solid var(--border);background:#fff;border-radius:7px;width:26px;height:26px;
            cursor:pointer;font-size:13px;display:grid;place-items:center;line-height:1}
  .btn-mini:hover{background:#e2e8f0}
  .btn-mini.danger{color:var(--danger);border-color:#fecaca}
  .btn-mini.danger:hover{background:#fee2e2}
  .qte{min-width:20px;text-align:center;font-weight:700}
  .total-bar{display:flex;justify-content:space-between;align-items:center;
             padding-top:14px;margin-top:6px;border-top:2px dashed var(--border);font-size:15px}
  .total-bar strong{font-size:22px;color:var(--primary-dark);letter-spacing:-.5px}

  /* ---------- Modale ---------- */
  .modal{position:fixed;inset:0;background:rgba(15,23,42,.55);display:none;
         align-items:center;justify-content:center;padding:18px;z-index:100}
  .modal.ouvert{display:flex}
  .modal-box{background:#fff;border-radius:16px;max-width:520px;width:100%;max-height:90vh;
             overflow:auto;padding:22px;box-shadow:0 20px 50px rgba(15,23,42,.3);animation:pop .18s ease}
  @keyframes pop{from{transform:scale(.96);opacity:0}to{transform:scale(1);opacity:1}}
  .modal-box h2{font-size:17px;margin-bottom:16px}
  .form-grid{display:grid;grid-template-columns:1fr 1fr;gap:12px}
  .form-grid .full{grid-column:1/-1}
  @media(max-width:520px){.form-grid{grid-template-columns:1fr}}
  .field{display:flex;flex-direction:column;gap:5px}
  .field label{font-size:12px;font-weight:600;color:var(--muted)}
  .field input,.field select{padding:9px 11px;border:1px solid var(--border);border-radius:9px;
        font-size:14px;font-family:inherit;outline:none;background:#fff}
  .field input:focus,.field select:focus{border-color:var(--primary);box-shadow:0 0 0 3px rgba(13,148,136,.12)}
  .modal-actions{display:flex;gap:10px;justify-content:flex-end;margin-top:18px}

  /* ---------- Ticket ---------- */
  .ticket{font-family:'Courier New',monospace;font-size:13px;background:#fdfdfd;
          border:1px dashed #cbd5e1;border-radius:10px;padding:16px}
  .ticket .tk-head{text-align:center;margin-bottom:12px}
  .ticket .tk-head strong{font-size:16px;letter-spacing:1px}
  .ticket hr{border:0;border-top:1px dashed #cbd5e1;margin:10px 0}
  .ticket .tk-l{display:flex;justify-content:space-between;gap:10px}
  .ticket .tk-total{display:flex;justify-content:space-between;font-weight:700;font-size:16px;margin-top:8px}

  /* ---------- Toast ---------- */
  #toast{position:fixed;bottom:24px;left:50%;transform:translate(-50%,20px);background:#0f172a;color:#fff;
         padding:12px 22px;border-radius:12px;font-size:14px;font-weight:600;opacity:0;
         pointer-events:none;transition:.25s;z-index:200;box-shadow:0 8px 24px rgba(15,23,42,.3)}
  #toast.show{opacity:1;transform:translate(-50%,0)}
</style>
</head>
<body>

<header class="topbar">
  <div class="topbar-inner">
    <div class="brand">
      <div class="logo">℞</div>
      <div>
        <h1>PharmaGest</h1>
        <p>Gestion de pharmacie</p>
      </div>
    </div>
    <nav class="tabs">
      <button data-tab="dashboard" class="active">Tableau de bord</button>
      <button data-tab="stock">Stock</button>
      <button data-tab="vente">Nouvelle vente</button>
      <button data-tab="historique">Historique</button>
    </nav>
  </div>
</header>

<div class="wrap">

  <!-- ================= TABLEAU DE BORD ================= -->
  <section id="tab-dashboard" class="tab active">
    <div class="grid-kpi" id="kpis"></div>
    <div class="grid-2">
      <div class="card">
        <h2>📦 Stock à réapprovisionner</h2>
        <ul class="alertes" id="liste-stock"></ul>
      </div>
      <div class="card">
        <h2>⏳ Péremptions proches / dépassées</h2>
        <ul class="alertes" id="liste-exp"></ul>
      </div>
    </div>
  </section>

  <!-- ================= STOCK ================= -->
  <section id="tab-stock" class="tab">
    <div class="toolbar">
      <input type="search" id="f-texte" placeholder="Rechercher un médicament, DCI, fournisseur…">
      <select id="f-cat">
        <option value="">Toutes catégories</option>
      </select>
      <select id="f-statut">
        <option value="">Tous les statuts</option>
        <option value="ok">Disponible</option>
        <option value="bas">Stock bas</option>
        <option value="rupture">Rupture</option>
        <option value="expire">Expire bientôt</option>
        <option value="perime">Périmé</option>
      </select>
      <div class="spacer"></div>
      <button class="btn btn-primary" onclick="ouvrirForm()">+ Ajouter un médicament</button>
    </div>
    <div class="table-scroll">
      <table>
        <thead>
          <tr>
            <th>Médicament</th>
            <th>Catégorie</th>
            <th class="num">Prix</th>
            <th class="num">Qté</th>
            <th>Expiration</th>
            <th>Statut</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody id="stock-body"></tbody>
      </table>
    </div>
  </section>

  <!-- ================= NOUVELLE VENTE ================= -->
  <section id="tab-vente" class="tab">
    <div class="vente-layout">
      <div class="card">
        <h2>💊 Catalogue</h2>
        <div class="toolbar">
          <input type="search" id="recherche-vente" placeholder="Rechercher un médicament…">
        </div>
        <div class="catalogue" id="catalogue"></div>
      </div>
      <div class="card">
        <h2>🧾 Panier</h2>
        <div class="panier" id="panier"></div>
        <div class="total-bar">
          <span>Total</span>
          <strong id="total-panier">0,00</strong>
        </div>
        <button class="btn btn-primary btn-block" style="margin-top:12px" onclick="validerVente()">
          Valider la vente
        </button>
        <button class="btn btn-ghost btn-block" style="margin-top:8px" onclick="viderPanier()">
          Vider le panier
        </button>
      </div>
    </div>
  </section>

  <!-- ================= HISTORIQUE ================= -->
  <section id="tab-historique" class="tab">
    <div class="card">
      <h2>🕘 Historique des ventes</h2>
      <div class="table-scroll">
        <table>
          <thead>
            <tr>
              <th>N°</th>
              <th>Date</th>
              <th class="num">Articles</th>
              <th class="num">Total</th>
              <th>Reçu</th>
            </tr>
          </thead>
          <tbody id="histo-body"></tbody>
        </table>
      </div>
    </div>
  </section>

</div>

<!-- Modale générique -->
<div class="modal" id="modal" onclick="if(event.target===this)fermerModal()">
  <div class="modal-box" id="modal-contenu"></div>
</div>

<div id="toast"></div>

<script>
/* =========================================================
   CONFIGURATION
   ========================================================= */
const DEVISE = '€';            // Modifiable : 'FCFA', 'DH', '$'…
const SEUIL_EXPIRATION = 30;   // Jours avant expiration pour alerter
const LS_KEY = 'pharmagest_v1';

const CATEGORIES = ['Antalgique','Antibiotique','Anti-inflammatoire','Antihistaminique',
                    'Vitamine','Dermatologie','Gastro-entérologie','Autre'];

/* =========================================================
   ÉTAT
   ========================================================= */
let state = { medicaments: [], ventes: [], compteur: 0 };
let panier = [];                  // [{id, qte}]
let rechercheVente = '';
let filtreStock = { texte:'', categorie:'', statut:'' };

/* =========================================================
   UTILITAIRES
   ========================================================= */
const uid = () => Date.now().toString(36) + Math.random().toString(36).slice(2,7);
const esc = s => String(s ?? '').replace(/[&<>"']/g, c =>
  ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c]));
const fmt = n => new Intl.NumberFormat('fr-FR',{minimumFractionDigits:2,maximumFractionDigits:2})
                  .format(n || 0) + ' ' + DEVISE;
const aujourdhui = () => new Date().toISOString().slice(0,10);

function joursRestants(dateStr){
  if(!dateStr) return Infinity;
  const d = new Date(dateStr + 'T00:00:00');
  if(isNaN(d)) return Infinity;
  const now = new Date(); now.setHours(0,0,0,0);
  return Math.round((d - now) / 86400000);
}

function fmtDate(dateStr){
  if(!dateStr) return '—';
  const d = new Date(dateStr + 'T00:00:00');
  return isNaN(d) ? '—' : d.toLocaleDateString('fr-FR');
}

function statutMed(m){
  const j = joursRestants(m.expiration);
  if(m.quantite === 0)  return {code:'rupture', label:'Rupture',        cls:'badge-danger'};
  if(j < 0)             return {code:'perime',  label:'Périmé',         cls:'badge-danger'};
  if(m.quantite <= m.seuil) return {code:'bas', label:'Stock bas',      cls:'badge-warn'};
  if(j <= SEUIL_EXPIRATION) return {code:'expire', label:'Expire bientôt', cls:'badge-warn'};
  return {code:'ok', label:'Disponible', cls:'badge-ok'};
}

function toast(msg){
  const t = document.getElementById('toast');
  t.textContent = msg;
  t.classList.add('show');
  clearTimeout(t._tid);
  t._tid = setTimeout(() => t.classList.remove('show'), 2200);
}

/* =========================================================
   PERSISTANCE
   ========================================================= */
function save(){ localStorage.setItem(LS_KEY, JSON.stringify(state)); }

function load(){
  const raw = localStorage.getItem(LS_KEY);
  if(raw){
    try{
      const p = JSON.parse(raw);
      state = { medicaments: p.medicaments || [], ventes: p.ventes || [], compteur: p.compteur || 0 };
    }catch(e){ console.error('Données corrompues', e); }
  }else{
    seed(); save();
  }
}

function seed(){
  const dans = n => { const d = new Date(); d.setDate(d.getDate()+n); return d.toISOString().slice(0,10); };
  state.medicaments = [
    {id:uid(), nom:'Paracétamol 500mg',   dci:'Paracétamol',            categorie:'Antalgique',         prix:2.50, quantite:120, seuil:20, expiration:dans(400), fournisseur:'Laborex'},
    {id:uid(), nom:'Amoxicilline 1g',     dci:'Amoxicilline',           categorie:'Antibiotique',       prix:6.90, quantite:8,   seuil:15, expiration:dans(180), fournisseur:'Pharmadis'},
    {id:uid(), nom:'Ibuprofène 400mg',    dci:'Ibuprofène',             categorie:'Anti-inflammatoire', prix:3.80, quantite:60,  seuil:15, expiration:dans(300), fournisseur:'Laborex'},
    {id:uid(), nom:'Cétirizine 10mg',     dci:'Cétirizine',             categorie:'Antihistaminique',   prix:4.50, quantite:0,   seuil:10, expiration:dans(250), fournisseur:'MedSupply'},
    {id:uid(), nom:'Vitamine C 1000mg',   dci:'Acide ascorbique',       categorie:'Vitamine',           prix:5.20, quantite:45,  seuil:10, expiration:dans(25),  fournisseur:'NutriLab'},
    {id:uid(), nom:'Sérum physiologique', dci:'Chlorure de sodium',     categorie:'Autre',              prix:1.80, quantite:90,  seuil:20, expiration:dans(500), fournisseur:'MedSupply'},
    {id:uid(), nom:'Aspirine 100mg',      dci:'Acide acétylsalicylique',categorie:'Antalgique',         prix:2.90, quantite:30,  seuil:10, expiration:dans(-10), fournisseur:'Laborex'}
  ];
}

/* =========================================================
   RENDU — TABLEAU DE BORD
   ========================================================= */
function renderDashboard(){
  const meds = state.medicaments;
  const jour = aujourdhui();
  const ventesJour = state.ventes.filter(v => v.date.slice(0,10) === jour);
  const caJour     = ventesJour.reduce((s,v) => s + v.total, 0);
  const valeurStock= meds.reduce((s,m) => s + m.prix * m.quantite, 0);
  const totalUnites= meds.reduce((s,m) => s + m.quantite, 0);

  const alertesStock = meds.filter(m => m.quantite <= m.seuil);
  const alertesExp   = meds.filter(m => joursRestants(m.expiration) <= SEUIL_EXPIRATION);

  document.getElementById('kpis').innerHTML = `
    <div class="kpi accent">
      <div class="lbl">Chiffre d'affaires du jour</div>
      <div class="val">${fmt(caJour)}</div>
      <div class="sub">${ventesJour.length} vente(s) aujourd'hui</div>
    </div>
    <div class="kpi">
      <div class="lbl">Valeur du stock</div>
      <div class="val">${fmt(valeurStock)}</div>
      <div class="sub">${meds.length} référence(s)</div>
    </div>
    <div class="kpi">
      <div class="lbl">Unités en stock</div>
      <div class="val">${totalUnites}</div>
      <div class="sub">toutes références confondues</div>
    </div>
    <div class="kpi">
      <div class="lbl">Alertes actives</div>
      <div class="val">${alertesStock.length + alertesExp.length}</div>
      <div class="sub">${alertesStock.length} stock · ${alertesExp.length} péremption</div>
    </div>`;

  document.getElementById('liste-stock').innerHTML = alertesStock.length
    ? alertesStock
        .sort((a,b) => a.quantite - b.quantite)
        .map(m => `<li>
            <span>${esc(m.n
