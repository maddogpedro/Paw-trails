<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Paw Trails 🐾</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Fraunces:wght@300;600;800&family=DM+Sans:wght@300;400;500;600&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"/>
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<style>
:root{
  --earth:#2D4A22;--moss:#5C7A3E;--sage:#8FAF6E;
  --cream:#F5EFE0;--warm:#FDFAF4;--amber:#D4820A;--amber-l:#F0A830;
  --terra:#C0522A;--shadow:rgba(45,74,34,.15);
  --fd:'Fraunces',serif;--fb:'DM Sans',sans-serif;
  --th:66px;--hh:56px;
}
*{margin:0;padding:0;box-sizing:border-box;-webkit-tap-highlight-color:transparent;}
html,body{height:100%;overflow:hidden;font-family:var(--fb);background:#1a1a1a;color:var(--earth);}
#app{display:flex;flex-direction:column;height:100vh;height:100dvh;}

/* ── HEADER ── */
#hdr{background:var(--earth);height:var(--hh);display:flex;align-items:center;padding:0 16px;gap:10px;z-index:200;flex-shrink:0;box-shadow:0 2px 12px rgba(0,0,0,.35);}
.logo{font-family:var(--fd);font-weight:800;font-size:21px;color:var(--cream);display:flex;align-items:center;gap:7px;flex:1;}
.logo-paw{display:inline-block;animation:wag 2.5s ease-in-out infinite;}
@keyframes wag{0%,100%{transform:rotate(-10deg)}50%{transform:rotate(10deg) scale(1.15)}}
.hdr-r{display:flex;align-items:center;gap:8px;}
.db{width:7px;height:7px;border-radius:50%;background:#555;transition:.3s;}
.db.on{background:#4CAF50;box-shadow:0 0 6px rgba(76,175,80,.8);}
.db.err{background:var(--terra);}
.walk-pill{background:rgba(255,255,255,.12);border:1px solid rgba(255,255,255,.2);color:#fff;font-size:11px;font-weight:700;padding:4px 10px;border-radius:12px;display:flex;align-items:center;gap:4px;}
.av-btn{width:34px;height:34px;border-radius:50%;background:linear-gradient(135deg,var(--amber),var(--terra));border:2px solid var(--amber-l);display:flex;align-items:center;justify-content:center;font-size:16px;cursor:pointer;flex-shrink:0;}

/* ── MAP ── */
#map-wrap{flex:1;position:relative;overflow:hidden;}
#map{width:100%;height:100%;}
#backdrop{position:absolute;inset:0;background:rgba(0,0,0,.55);z-index:140;display:none;}
#backdrop.on{display:block;}

/* ── TABS ── */
#tabs{height:var(--th);background:var(--earth);display:flex;align-items:stretch;z-index:200;flex-shrink:0;padding-bottom:env(safe-area-inset-bottom);box-shadow:0 -2px 12px rgba(0,0,0,.3);}
.ti{flex:1;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:2px;cursor:pointer;color:rgba(255,255,255,.4);transition:all .2s;border-top:2.5px solid transparent;}
.ti.on{color:#fff;border-top-color:var(--amber-l);}
.ti:active{transform:scale(.92);}
.ti-ico{font-size:20px;}
.ti-lbl{font-size:9px;font-weight:700;letter-spacing:.3px;text-transform:uppercase;}

/* ── PANELS ── */
.panel{position:absolute;bottom:0;left:0;right:0;background:var(--warm);border-radius:22px 22px 0 0;z-index:150;transform:translateY(100%);transition:transform .32s cubic-bezier(.4,0,.2,1);max-height:90vh;display:flex;flex-direction:column;box-shadow:0 -6px 32px rgba(0,0,0,.28);}
.panel.open{transform:translateY(0);}
.drag{width:38px;height:4px;background:rgba(45,74,34,.18);border-radius:2px;margin:10px auto 0;flex-shrink:0;}
.pscroll{overflow-y:auto;flex:1;-webkit-overflow-scrolling:touch;padding-bottom:24px;}
::-webkit-scrollbar{width:3px;}::-webkit-scrollbar-thumb{background:rgba(45,74,34,.15);border-radius:2px;}
.ph{padding:12px 16px 6px;display:flex;align-items:center;justify-content:space-between;flex-shrink:0;}
.ph-t{font-family:var(--fd);font-size:21px;font-weight:800;color:var(--earth);}
.ph-x{width:44px;height:44px;border-radius:50%;background:rgba(45,74,34,.1);border:none;font-size:22px;cursor:pointer;display:flex;align-items:center;justify-content:center;color:var(--earth);}

/* ── WALK SEARCH (prominent) ── */
.walk-search-wrap{padding:8px 14px 10px;flex-shrink:0;}
.walk-search-box{width:100%;padding:13px 16px 13px 44px;border:2px solid rgba(45,74,34,.15);border-radius:16px;font-family:var(--fb);font-size:15px;background:#fff;color:var(--earth);outline:none;box-shadow:0 2px 8px var(--shadow);}
.walk-search-box:focus{border-color:var(--earth);box-shadow:0 2px 14px rgba(45,74,34,.2);}
.wsb-ico{position:absolute;left:28px;top:50%;transform:translateY(-50%);font-size:20px;pointer-events:none;}
.plan-sug{background:#fff;border-radius:14px;border:1.5px solid rgba(45,74,34,.12);margin:0 14px 8px;overflow:hidden;display:none;box-shadow:0 4px 18px var(--shadow);}
.sug-item{padding:13px 16px;border-bottom:1px solid rgba(45,74,34,.07);cursor:pointer;font-size:13px;display:flex;align-items:center;gap:10px;transition:background .15s;color:var(--earth);}
.sug-item:last-child{border-bottom:none;}
.sug-item:active{background:rgba(45,74,34,.05);}

/* ── CHIPS ── */
.chips{display:flex;gap:6px;padding:0 14px 10px;overflow-x:auto;flex-shrink:0;}
.chips::-webkit-scrollbar{display:none;}
.chip{padding:6px 14px;border-radius:20px;font-size:12px;font-weight:600;cursor:pointer;border:1.5px solid rgba(45,74,34,.18);background:#fff;color:var(--moss);white-space:nowrap;transition:all .2s;}
.chip.on{background:var(--earth);color:#fff;border-color:var(--earth);}

/* ── ROUTE CARDS ── */
.rlist{padding:0 12px;display:flex;flex-direction:column;gap:12px;}
.rc{background:#fff;border-radius:18px;overflow:hidden;border:1.5px solid rgba(45,74,34,.07);cursor:pointer;box-shadow:0 2px 10px var(--shadow);transition:transform .15s,box-shadow .15s;}
.rc:active{transform:scale(.985);box-shadow:0 1px 6px var(--shadow);}
.rc-photo{height:136px;position:relative;overflow:hidden;background:linear-gradient(135deg,#3D6B2F,#5C7A3E);}
.rc-photo img{width:100%;height:100%;object-fit:cover;}
.rc-photo-ov{position:absolute;inset:0;background:linear-gradient(to bottom,transparent 35%,rgba(0,0,0,.6));}
.rc-pb{position:absolute;bottom:8px;left:10px;right:10px;display:flex;align-items:flex-end;justify-content:space-between;}
.diff{font-size:10px;font-weight:700;letter-spacing:.7px;padding:4px 9px;border-radius:7px;text-transform:uppercase;}
.d-e{background:#4CAF50;color:#fff;}.d-m{background:var(--amber);color:#fff;}.d-h{background:var(--terra);color:#fff;}
.rc-walkers{background:rgba(0,0,0,.45);color:#fff;font-size:10px;padding:3px 8px;border-radius:6px;}
.rc-body{padding:10px 13px 5px;}
.rc-name{font-family:var(--fd);font-size:16px;font-weight:700;color:var(--earth);margin-bottom:4px;}
.rc-meta{display:flex;gap:8px;flex-wrap:wrap;font-size:11px;color:var(--moss);}
.rc-stars{display:flex;gap:2px;margin-top:5px;}
.star{font-size:13px;cursor:pointer;transition:transform .1s;}
.star:active{transform:scale(1.4);}
.rc-desc{font-size:11px;color:#999;font-style:italic;margin-top:4px;line-height:1.5;}
.rc-foot{padding:8px 13px 12px;display:flex;gap:6px;}
.btn-walk{flex:2;padding:11px;background:var(--earth);color:#fff;border:none;border-radius:12px;font-family:var(--fb);font-size:13px;font-weight:700;cursor:pointer;display:flex;align-items:center;justify-content:center;gap:5px;}
.btn-walk:active{opacity:.85;}
.btn-rx{flex:1;padding:10px;background:rgba(45,74,34,.07);border:1.5px solid rgba(45,74,34,.1);border-radius:12px;font-size:13px;cursor:pointer;display:flex;align-items:center;justify-content:center;gap:3px;font-family:var(--fb);color:var(--earth);font-weight:600;}
.btn-rx.on{background:rgba(240,168,48,.15);border-color:var(--amber);}
.btn-rx:active{opacity:.8;}
.btn-done{flex:1;padding:10px;background:rgba(76,175,80,.08);border:1.5px solid rgba(76,175,80,.2);border-radius:12px;font-size:13px;cursor:pointer;display:flex;align-items:center;justify-content:center;font-family:var(--fb);color:#2e7d32;font-weight:600;}
.btn-done.on{background:rgba(76,175,80,.22);border-color:#4CAF50;}

/* ── NAV CARD ── */
.nav-card{position:absolute;top:12px;left:12px;right:64px;background:var(--earth);color:#fff;border-radius:18px;padding:14px 16px;z-index:100;display:none;box-shadow:0 6px 24px rgba(0,0,0,.4);}
.nav-card.on{display:block;animation:drop .3s ease;}
@keyframes drop{from{opacity:0;transform:translateY(-8px)}to{opacity:1;transform:none}}
.nav-dir{font-family:var(--fd);font-size:18px;font-weight:700;margin-bottom:3px;}
.nav-sub{font-size:11px;opacity:.7;}
.nav-pb{height:4px;background:rgba(255,255,255,.18);border-radius:2px;margin-top:10px;overflow:hidden;}
.nav-pf{height:100%;background:var(--amber-l);border-radius:2px;transition:width .5s;}
.nav-btns{display:flex;gap:6px;margin-top:9px;}
.nb{flex:1;padding:8px;border:none;border-radius:9px;font-family:var(--fb);font-size:12px;font-weight:700;cursor:pointer;}
.nb-m{background:rgba(255,255,255,.18);color:#fff;}
.nb-s{background:var(--terra);color:#fff;}

/* ── MAP CONTROLS ── */
.map-ctrls{position:absolute;right:14px;top:12px;display:flex;flex-direction:column;gap:7px;z-index:100;}
.mc{width:42px;height:42px;background:#fff;border:none;border-radius:12px;cursor:pointer;font-size:18px;box-shadow:0 2px 10px rgba(0,0,0,.18);display:flex;align-items:center;justify-content:center;}
.mc:active{transform:scale(.92);}
.rec-fab{position:absolute;right:14px;bottom:14px;background:var(--earth);color:#fff;border:none;border-radius:24px;padding:13px 18px;font-family:var(--fb);font-size:13px;font-weight:700;cursor:pointer;display:flex;align-items:center;gap:7px;box-shadow:0 4px 18px rgba(45,74,34,.5);z-index:100;}
.rec-fab.rec{background:var(--terra);animation:pulse 1.5s infinite;}
@keyframes pulse{0%,100%{box-shadow:0 4px 18px rgba(192,82,42,.4)}50%{box-shadow:0 4px 28px rgba(192,82,42,.75)}}
.mode-bar{position:absolute;top:12px;left:50%;transform:translateX(-50%);background:var(--amber);color:#fff;padding:9px 16px;border-radius:20px;font-size:12px;font-weight:700;z-index:100;display:none;align-items:center;gap:7px;box-shadow:0 4px 14px rgba(212,130,10,.45);white-space:nowrap;}
.mode-bar.on{display:flex;}

/* ── REPORT PANEL ── */
.rank-hero{background:linear-gradient(135deg,var(--earth),var(--moss));margin:0 14px 14px;border-radius:18px;padding:18px;display:flex;align-items:center;gap:14px;color:#fff;}
.rank-badge{width:64px;height:64px;border-radius:50%;background:rgba(255,255,255,.15);border:3px solid var(--amber-l);display:flex;align-items:center;justify-content:center;font-size:28px;flex-shrink:0;}
.rank-info{}
.rank-title{font-family:var(--fd);font-size:18px;font-weight:800;}
.rank-sub{font-size:11px;opacity:.8;margin-top:2px;}
.rank-bar-bg{height:5px;background:rgba(255,255,255,.2);border-radius:3px;margin-top:8px;overflow:hidden;width:100%;}
.rank-bar-fill{height:100%;background:var(--amber-l);border-radius:3px;transition:width .6s;}
.rank-next{font-size:10px;opacity:.7;margin-top:4px;}
.report-btn-big{margin:0 14px 14px;padding:16px;background:var(--earth);color:#fff;border:none;border-radius:16px;font-family:var(--fb);font-size:16px;font-weight:700;cursor:pointer;width:calc(100% - 28px);display:flex;align-items:center;justify-content:center;gap:8px;box-shadow:0 4px 16px rgba(45,74,34,.35);}
.report-btn-big:active{opacity:.88;}
.recent-reports{padding:0 14px;}
.rr-title{font-family:var(--fd);font-size:15px;font-weight:700;color:var(--earth);margin-bottom:10px;}
.rr-item{background:#fff;border-radius:13px;padding:11px 13px;margin-bottom:8px;display:flex;align-items:center;gap:10px;border:1.5px solid rgba(45,74,34,.08);box-shadow:0 1px 6px var(--shadow);}
.rr-ico{font-size:22px;flex-shrink:0;}
.rr-info{flex:1;}
.rr-label{font-size:13px;font-weight:600;color:var(--earth);}
.rr-meta{font-size:10px;color:var(--moss);margin-top:1px;}
.rr-conf{background:rgba(45,74,34,.07);border:none;border-radius:8px;padding:6px 10px;font-size:11px;font-weight:700;cursor:pointer;color:var(--earth);}

/* ── HAZ CATS ── */
.haz-cats{padding:0 14px;}
.haz-cat{background:#fff;border-radius:14px;overflow:hidden;border:1.5px solid rgba(45,74,34,.09);margin-bottom:10px;}
.hc-hdr{padding:13px 14px;display:flex;align-items:center;justify-content:space-between;cursor:pointer;}
.hc-l{display:flex;align-items:center;gap:9px;}
.hc-ico{font-size:21px;}
.hc-name{font-family:var(--fd);font-size:14px;font-weight:700;color:var(--earth);}
.hc-cnt{font-size:11px;color:var(--moss);}
.hc-chev{font-size:11px;color:var(--moss);transition:transform .2s;}
.haz-cat.open .hc-chev{transform:rotate(180deg);}
.hc-items{display:none;padding:0 10px 10px;display:grid;grid-template-columns:1fr 1fr 1fr;gap:8px;}
.haz-cat:not(.open) .hc-items{display:none;}
.haz-cat.open .hc-items{display:grid;}
.hi{background:var(--warm);border:1.5px solid rgba(45,74,34,.1);border-radius:12px;padding:10px 6px;text-align:center;cursor:pointer;transition:all .2s;}
.hi:active,.hi.sel{border-color:var(--amber);background:rgba(240,168,48,.1);}
.hi-i{font-size:22px;margin-bottom:3px;}
.hi-l{font-size:10px;font-weight:600;color:var(--earth);line-height:1.2;}

/* ── MY TRAILS PANEL ── */
.mt-tabs{display:flex;border-bottom:1.5px solid rgba(45,74,34,.1);flex-shrink:0;margin:0 14px;}
.mt-tab{flex:1;padding:10px 4px;text-align:center;font-size:11px;font-weight:700;cursor:pointer;border-bottom:2.5px solid transparent;color:var(--moss);transition:all .2s;margin-bottom:-1.5px;}
.mt-tab.on{color:var(--earth);border-bottom-color:var(--earth);}

/* ── WALK STATS STRIP ── */
.stats-strip{display:flex;background:var(--warm);border-bottom:1px solid rgba(45,74,34,.1);flex-shrink:0;}
.ss-item{flex:1;padding:12px 8px;text-align:center;border-right:1px solid rgba(45,74,34,.08);}
.ss-item:last-child{border-right:none;}
.ss-n{font-family:var(--fd);font-size:20px;font-weight:800;color:var(--earth);}
.ss-l{font-size:9px;font-weight:700;color:var(--moss);text-transform:uppercase;letter-spacing:.4px;margin-top:1px;}

/* ── PACK (PROFILE) ── */
.pack-hero{background:linear-gradient(135deg,var(--earth) 0%,var(--moss) 100%);padding:22px 18px;color:#fff;}
.pack-top{display:flex;align-items:center;gap:14px;margin-bottom:18px;}
.pack-av{width:60px;height:60px;border-radius:50%;background:linear-gradient(135deg,var(--amber),var(--terra));border:3px solid var(--amber-l);display:flex;align-items:center;justify-content:center;font-size:28px;flex-shrink:0;}
.pack-name{font-family:var(--fd);font-size:20px;font-weight:800;}
.pack-rank-pill{display:inline-block;background:var(--amber);color:#fff;font-size:11px;font-weight:700;padding:3px 10px;border-radius:10px;margin-top:4px;}
.dogs-row{display:flex;gap:10px;overflow-x:auto;padding-bottom:4px;}
.dogs-row::-webkit-scrollbar{display:none;}
.dog-hero{background:rgba(255,255,255,.12);border-radius:14px;padding:12px 14px;flex-shrink:0;min-width:130px;text-align:center;}
.dog-hero-av{font-size:32px;margin-bottom:4px;}
.dog-hero-name{font-family:var(--fd);font-size:14px;font-weight:700;color:#fff;}
.dog-hero-dist{font-size:11px;opacity:.8;margin-top:2px;}
.dog-hero-lvl{background:var(--amber-l);color:var(--earth);font-size:10px;font-weight:800;padding:2px 8px;border-radius:8px;display:inline-block;margin-top:4px;}
.add-dog-card{background:rgba(255,255,255,.08);border:2px dashed rgba(255,255,255,.3);border-radius:14px;padding:12px 14px;flex-shrink:0;min-width:100px;display:flex;flex-direction:column;align-items:center;justify-content:center;cursor:pointer;gap:4px;}
.add-dog-card span{font-size:22px;}
.add-dog-card p{font-size:11px;color:rgba(255,255,255,.7);}
.sec{padding:16px 16px 0;}
.sec-t{font-family:var(--fd);font-size:16px;font-weight:700;color:var(--earth);margin-bottom:10px;}
.medals-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:10px;}
.medal{text-align:center;cursor:pointer;}
.med-i{width:52px;height:52px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:22px;margin:0 auto 4px;border:2px solid;transition:transform .2s;}
.medal:active .med-i{transform:scale(1.12) rotate(5deg);}
.med-i.earned{background:linear-gradient(135deg,var(--amber-l),var(--amber));border-color:var(--amber);box-shadow:0 3px 10px rgba(212,130,10,.4);}
.med-i.locked{background:rgba(45,74,34,.05);border-color:rgba(45,74,34,.12);filter:grayscale(1) opacity(.4);}
.med-n{font-size:10px;color:var(--earth);font-weight:600;line-height:1.2;}
.lb-item{display:flex;align-items:center;gap:9px;padding:10px 12px;border-radius:12px;margin-bottom:7px;background:#fff;border:1.5px solid rgba(45,74,34,.08);}
.lb-item.me{background:rgba(45,74,34,.06);border-color:var(--earth);}
.lb-rank{font-family:var(--fd);font-size:15px;font-weight:800;width:26px;color:var(--moss);}
.lb-name{flex:1;font-size:12px;font-weight:600;color:var(--earth);}
.lb-score{font-size:11px;color:var(--amber);font-weight:700;}

/* ── SAVE FORM ── */
.save-form{background:#fff;border-radius:16px;margin:0 12px 12px;padding:15px;border:1.5px solid rgba(45,74,34,.12);display:none;}
.save-form.open{display:block;}
.sf-t{font-family:var(--fd);font-size:16px;font-weight:700;color:var(--earth);margin-bottom:12px;}
.ff{margin-bottom:10px;}
.fl{font-size:10px;font-weight:700;color:var(--moss);display:block;margin-bottom:3px;text-transform:uppercase;letter-spacing:.5px;}
.fi,.fs,.fta{width:100%;padding:10px 12px;border:1.5px solid rgba(45,74,34,.18);border-radius:10px;font-family:var(--fb);font-size:13px;color:var(--earth);background:#fff;outline:none;}
.fi:focus,.fs:focus,.fta:focus{border-color:var(--earth);}
.fta{height:54px;resize:none;}
.frow{display:flex;gap:8px;}.frow .ff{flex:1;}
.fchk{display:flex;align-items:center;gap:7px;margin-bottom:7px;font-size:12px;cursor:pointer;}
.fchk input{accent-color:var(--earth);}
.btn-save{width:100%;padding:12px;background:var(--earth);color:#fff;border:none;border-radius:12px;font-family:var(--fb);font-size:14px;font-weight:700;cursor:pointer;margin-top:4px;}
.btn-save:disabled{opacity:.55;}
.btn-discard{width:100%;padding:9px;background:transparent;color:var(--moss);border:1.5px solid rgba(45,74,34,.18);border-radius:10px;font-family:var(--fb);font-size:12px;cursor:pointer;margin-top:6px;}

/* ── PLANNED ROUTE INFO ── */
.plan-result{background:#fff;border-radius:16px;margin:0 14px 14px;padding:14px;border:1.5px solid rgba(45,74,34,.12);display:none;}
.plan-result.on{display:block;}
.pr-t{font-family:var(--fd);font-size:15px;font-weight:700;color:var(--earth);margin-bottom:6px;}
.pr-meta{display:flex;gap:12px;font-size:12px;color:var(--moss);margin-bottom:12px;}

/* ── ROUTE DETAIL ── */
#detail{position:absolute;bottom:0;left:0;right:0;background:var(--warm);border-radius:22px 22px 0 0;z-index:160;transform:translateY(100%);transition:transform .32s cubic-bezier(.4,0,.2,1);max-height:92vh;display:flex;flex-direction:column;box-shadow:0 -6px 32px rgba(0,0,0,.3);}
#detail.open{transform:translateY(0);}
.det-photo{height:210px;position:relative;overflow:hidden;flex-shrink:0;}
.det-photo img{width:100%;height:100%;object-fit:cover;}
.det-ov{position:absolute;inset:0;background:linear-gradient(to bottom,rgba(0,0,0,.05) 0%,rgba(0,0,0,.65) 100%);}
.det-close{position:absolute;top:12px;right:12px;width:38px;height:38px;background:rgba(0,0,0,.35);border:none;border-radius:50%;color:#fff;font-size:20px;cursor:pointer;display:flex;align-items:center;justify-content:center;}
.det-title-ov{position:absolute;bottom:14px;left:14px;right:14px;}
.det-name{font-family:var(--fd);font-size:24px;font-weight:800;color:#fff;text-shadow:0 2px 8px rgba(0,0,0,.5);}
.det-body{padding:14px 16px;overflow-y:auto;flex:1;}
.det-tags{display:flex;gap:7px;flex-wrap:wrap;margin-bottom:12px;}
.det-tag{background:rgba(45,74,34,.08);border-radius:8px;padding:5px 10px;font-size:11px;font-weight:600;color:var(--moss);}
.det-stats{display:grid;grid-template-columns:repeat(3,1fr);gap:8px;margin-bottom:12px;}
.det-stat{background:#fff;border-radius:12px;padding:10px;text-align:center;border:1px solid rgba(45,74,34,.09);}
.det-stat-n{font-family:var(--fd);font-size:18px;font-weight:700;color:var(--earth);}
.det-stat-l{font-size:10px;color:var(--moss);margin-top:2px;}
.det-desc{font-size:13px;color:#555;line-height:1.65;margin-bottom:14px;}
.det-foot{display:flex;gap:8px;padding:12px 16px;border-top:1px solid rgba(45,74,34,.1);flex-shrink:0;}

/* ── POPUP ── */
.leaflet-popup-content-wrapper{border-radius:14px!important;box-shadow:0 8px 28px rgba(0,0,0,.22)!important;font-family:var(--fb)!important;padding:0!important;overflow:hidden!important;border:none!important;}
.leaflet-popup-content{margin:0!important;min-width:180px;}
.pop-hdr{padding:10px 14px;color:#fff;font-weight:700;font-size:13px;display:flex;align-items:center;gap:7px;}
.pop-body{padding:10px 14px 12px;}
.pop-meta{font-size:11px;color:var(--moss);margin-bottom:8px;}
.conf-btns{display:flex;gap:6px;}
.cb{flex:1;padding:7px;border:none;border-radius:8px;font-family:var(--fb);font-size:11px;font-weight:700;cursor:pointer;}
.cb-y{background:rgba(76,175,80,.15);color:#2e7d32;}
.cb-n{background:rgba(192,82,42,.1);color:var(--terra);}

/* ── ONBOARDING ── */
.ob-ov{position:fixed;inset:0;background:rgba(0,0,0,.8);z-index:9000;display:flex;align-items:flex-end;backdrop-filter:blur(4px);}
.ob-ov.gone{display:none;}
.ob-sheet{background:var(--warm);border-radius:26px 26px 0 0;width:100%;max-height:92vh;overflow-y:auto;}
.ob-hero{background:linear-gradient(135deg,var(--earth),var(--moss));padding:30px 24px 24px;text-align:center;color:#fff;}
.ob-paw{font-size:56px;display:block;animation:wag 2s ease-in-out infinite;margin-bottom:6px;}
.ob-ht{font-family:var(--fd);font-size:28px;font-weight:800;}
.ob-hs{font-size:13px;opacity:.8;margin-top:4px;}
.ob-body{padding:22px 20px 36px;}
.ob-step{display:none;}.ob-step.on{display:block;}
.ob-dots{display:flex;justify-content:center;gap:6px;margin-bottom:20px;}
.ob-dot{width:8px;height:8px;border-radius:50%;background:rgba(45,74,34,.18);transition:all .3s;}
.ob-dot.on{background:var(--earth);width:22px;border-radius:4px;}
.ob-st{font-family:var(--fd);font-size:19px;font-weight:700;color:var(--earth);margin-bottom:3px;}
.ob-ss{font-size:12px;color:var(--moss);margin-bottom:16px;}
.av-grid{display:grid;grid-template-columns:repeat(6,1fr);gap:8px;margin-bottom:16px;}
.av-o{font-size:22px;width:44px;height:44px;border-radius:12px;border:2px solid rgba(45,74,34,.12);background:#fff;cursor:pointer;display:flex;align-items:center;justify-content:center;transition:all .2s;}
.av-o.sel{border-color:var(--earth);background:rgba(45,74,34,.08);transform:scale(1.1);}
.ob-ff{margin-bottom:12px;}
.ob-fl{font-size:10px;font-weight:700;color:var(--moss);display:block;margin-bottom:4px;text-transform:uppercase;letter-spacing:.5px;}
.ob-fi{width:100%;padding:11px 13px;border:1.5px solid rgba(45,74,34,.18);border-radius:12px;font-family:var(--fb);font-size:14px;color:var(--earth);background:#fff;outline:none;}
.ob-fi:focus{border-color:var(--earth);}
.ob-btn{width:100%;padding:14px;background:var(--earth);color:#fff;border:none;border-radius:14px;font-family:var(--fb);font-size:15px;font-weight:700;cursor:pointer;margin-top:4px;}
.ob-skip{width:100%;padding:9px;background:transparent;color:var(--moss);border:none;font-family:var(--fb);font-size:12px;cursor:pointer;margin-top:8px;}
.de-ob{background:rgba(45,74,34,.05);border-radius:12px;padding:13px;margin-bottom:10px;position:relative;}
.de-rm{position:absolute;top:9px;right:9px;background:none;border:none;cursor:pointer;font-size:15px;color:var(--moss);}
.ready-wrap{text-align:center;padding:8px 0;}
.ready-av{font-size:60px;margin-bottom:8px;}
.ready-nm{font-family:var(--fd);font-size:24px;font-weight:800;color:var(--earth);}
.ready-dogs{display:flex;flex-wrap:wrap;gap:7px;justify-content:center;margin-top:12px;}
.dp{background:rgba(45,74,34,.08);border-radius:20px;padding:5px 13px;font-size:12px;color:var(--earth);}

/* ── TOAST ── */
.toast{position:fixed;bottom:calc(var(--th) + 14px);left:50%;transform:translateX(-50%) translateY(80px);background:var(--earth);color:#fff;padding:12px 20px;border-radius:16px;font-size:13px;font-weight:600;z-index:9999;transition:transform .4s cubic-bezier(.175,.885,.32,1.275);display:flex;align-items:center;gap:9px;box-shadow:0 6px 22px rgba(0,0,0,.32);max-width:88vw;pointer-events:none;}
.toast.show{transform:translateX(-50%) translateY(0);}

/* ── MISC ── */
.loading{text-align:center;padding:36px;color:var(--moss);font-size:13px;}
.spin{display:inline-block;width:24px;height:24px;border:3px solid rgba(45,74,34,.15);border-top-color:var(--earth);border-radius:50%;animation:sp .7s linear infinite;margin-bottom:8px;}
@keyframes sp{to{transform:rotate(360deg)}}
.empty{text-align:center;padding:40px 24px;color:var(--moss);}
.empty-i{font-size:48px;margin-bottom:12px;}
.empty p{font-size:13px;line-height:1.7;}
</style>
</head>
<body>

<!-- ══ ONBOARDING ══ -->
<div class="ob-ov" id="obOv">
  <div class="ob-sheet">
    <div class="ob-hero"><span class="ob-paw">🐾</span><div class="ob-ht">Paw Trails</div><div class="ob-hs">Built by dog walkers, for dog walkers</div></div>
    <div class="ob-body">
      <div class="ob-dots"><div class="ob-dot on" id="od0"></div><div class="ob-dot" id="od1"></div><div class="ob-dot" id="od2"></div></div>
      <div class="ob-step on" id="os0">
        <div class="ob-st">Who's walking? 👋</div><div class="ob-ss">Takes 30 seconds, keeps your walks personal</div>
        <div class="ob-ff"><label class="ob-fl">Your name</label><input class="ob-fi" id="onName" placeholder="e.g. Pete" maxlength="30"></div>
        <div class="ob-ff"><label class="ob-fl">Pick your avatar</label><div class="av-grid" id="avGrid"></div></div>
        <button class="ob-btn" onclick="obNext(1)">Next →</button>
      </div>
      <div class="ob-step" id="os1">
        <div class="ob-st">Meet your dog 🐕</div><div class="ob-ss">Your dog earns XP and levels up as you walk together</div>
        <div id="dogEntries"></div>
        <div style="border:2px dashed rgba(45,74,34,.2);border-radius:12px;padding:11px;text-align:center;cursor:pointer;color:var(--moss);font-size:12px;margin-bottom:12px" onclick="obAddDog()">+ Add another dog</div>
        <button class="ob-btn" onclick="obNext(2)">Next →</button>
        <button class="ob-skip" onclick="obNext(2)">Skip for now</button>
      </div>
      <div class="ob-step" id="os2">
        <div class="ready-wrap">
          <div class="ready-av" id="readyAv">🦊</div>
          <div class="ready-nm" id="readyNm">Ready!</div>
          <div style="font-size:13px;color:var(--moss);margin-top:8px;line-height:1.7">Discover routes · Report hazards · Earn your Pack Leader badge 🏅</div>
          <div class="ready-dogs" id="readyDogs"></div>
        </div>
        <button class="ob-btn" style="margin-top:22px" onclick="obFinish()">Start Walking 🐾</button>
      </div>
    </div>
  </div>
</div>

<!-- ══ APP ══ -->
<div id="app">
  <div id="hdr">
    <div class="logo"><span class="logo-paw">🐾</span>Paw Trails</div>
    <div class="hdr-r">
      <div class="db" id="dbDot"></div>
      <div class="walk-pill">🥾 <span id="walkCount">0</span> walks</div>
      <div class="av-btn" id="hdrAv" onclick="openPanel('pack')">🦊</div>
    </div>
  </div>

  <div id="map-wrap">
    <div id="map"></div>
    <div id="backdrop" onclick="closeAll()"></div>

    <!-- Nav card -->
    <div class="nav-card" id="navCard">
      <div class="nav-dir" id="navDir">🧭 Follow the route</div>
      <div class="nav-sub" id="navSub">Stay on the highlighted path</div>
      <div class="nav-pb"><div class="nav-pf" id="navProg" style="width:0%"></div></div>
      <div class="nav-btns">
        <button class="nb nb-m" id="muteBtn" onclick="toggleMute()">🔊 Voice</button>
        <button class="nb nb-s" onclick="stopNav()">⏹ Stop</button>
      </div>
    </div>

    <div class="mode-bar" id="modeBar">
      <span id="modeIco">📍</span><span id="modeTxt">Tap map</span>
      <button onclick="cancelMode()" style="background:rgba(255,255,255,.25);border:none;border-radius:7px;color:#fff;padding:3px 9px;cursor:pointer;font-size:11px;margin-left:4px">✕</button>
    </div>

    <div class="map-ctrls">
      <button class="mc" onclick="locateMe()">📍</button>
      <button class="mc" onclick="toggleSat()">🛰️</button>
      <button class="mc" onclick="doRefresh()">🔄</button>
    </div>

    <button class="rec-fab" id="recFab" onclick="toggleRec()">
      <span id="recIco">🐾</span><span id="recTxt">Record</span>
    </button>

    <!-- ══ WALK PANEL ══ -->
    <div class="panel" id="panel-walk">
      <div class="drag"></div>
      <div class="ph"><div class="ph-t">Walk</div><button class="ph-x" onclick="closeAll()">✕</button></div>
      <!-- Prominent search -->
      <div class="walk-search-wrap" style="position:relative">
        <span class="wsb-ico">🗺️</span>
        <input class="walk-search-box" id="walkSearch" placeholder="Search routes or plan a walk…" oninput="onWalkSearch(this.value)" autocomplete="off">
      </div>
      <div class="plan-sug" id="walkSug"></div>
      <!-- Plan result -->
      <div class="plan-result" id="planResult">
        <div class="pr-t" id="prTitle">Route planned!</div>
        <div class="pr-meta" id="prMeta"></div>
        <div class="ff"><input class="fi" id="planName" placeholder="Name this route…" maxlength="60"></div>
        <select class="fs" id="planDiff" style="width:100%;margin-bottom:8px;padding:9px 12px;border:1.5px solid rgba(45,74,34,.18);border-radius:10px;font-family:var(--fb);font-size:13px;color:var(--earth);background:#fff;outline:none;"><option value="easy">Easy</option><option value="moderate">Moderate</option><option value="hard">Hard</option></select>
        <button class="btn-save" onclick="savePlan()">💾 Save Route</button>
        <button class="btn-discard" onclick="clearPlan()">✕ Clear</button>
      </div>
      <div class="chips" id="walkChips">
        <div class="chip on" onclick="toggleChip(this,'all')">All</div>
        <div class="chip" onclick="toggleChip(this,'easy')">🟢 Easy</div>
        <div class="chip" onclick="toggleChip(this,'moderate')">🟡 Moderate</div>
        <div class="chip" onclick="toggleChip(this,'hard')">🔴 Hard</div>
        <div class="chip" onclick="toggleChip(this,'offLead')">🐕 Off-lead</div>
        <div class="chip" onclick="toggleChip(this,'water')">💧 Water</div>
      </div>
      <div class="pscroll">
        <div class="save-form" id="saveForm">
          <div class="sf-t">💾 Save Your Route</div>
          <div class="ff"><label class="fl">Name *</label><input class="fi" id="rName" placeholder="e.g. Healey Dell Loop" maxlength="60"></div>
          <div class="ff"><label class="fl">Description</label><textarea class="fta" id="rDesc" placeholder="Tips for other dog walkers…"></textarea></div>
          <div class="frow">
            <div class="ff"><label class="fl">Difficulty</label><select class="fs" id="rDiff"><option value="easy">Easy</option><option value="moderate">Moderate</option><option value="hard">Hard</option></select></div>
            <div class="ff"><label class="fl">Distance (km)</label><input class="fi" id="rDist" type="number" step="0.1" placeholder="3.2"></div>
          </div>
          <label class="fchk"><input type="checkbox" id="rOff"> 🟢 Safe off-lead</label>
          <label class="fchk"><input type="checkbox" id="rWater"> 💧 Water access</label>
          <button class="btn-save" id="btnSave" onclick="saveRoute()">Save to Paw Trails 🐾</button>
          <button class="btn-discard" onclick="cancelSave()">✕ Discard</button>
        </div>
        <div class="rlist" id="routeList"><div class="loading"><div class="spin"></div><br>Loading routes…</div></div>
      </div>
    </div>

    <!-- ══ REPORT PANEL ══ -->
    <div class="panel" id="panel-report">
      <div class="drag"></div>
      <div class="ph"><div class="ph-t">Report</div><button class="ph-x" onclick="closeAll()">✕</button></div>
      <div class="pscroll">
        <!-- Rank hero -->
        <div class="rank-hero" id="rankHero">
          <div class="rank-badge" id="rankBadge">🐾</div>
          <div class="rank-info" style="flex:1">
            <div class="rank-title" id="rankTitle">Pup</div>
            <div class="rank-sub" id="rankSub">0 reports · next rank at 5</div>
            <div class="rank-bar-bg"><div class="rank-bar-fill" id="rankBar" style="width:0%"></div></div>
            <div class="rank-next" id="rankNext">Report hazards to level up → Hound</div>
          </div>
        </div>
        <!-- Big report button -->
        <button class="report-btn-big" onclick="startReport()">⚠️ Drop a Hazard on Map</button>
        <!-- Hazard cats -->
        <div class="haz-cats" id="hazCats"></div>
        <!-- Recent reports -->
        <div class="recent-reports" style="margin-top:14px">
          <div class="rr-title">📍 Recent Reports Near You</div>
          <div id="recentReports"><div style="font-size:12px;color:var(--moss);padding:8px 0">Loading…</div></div>
        </div>
        <div style="height:20px"></div>
      </div>
    </div>

    <!-- ══ MY TRAILS PANEL ══ -->
    <div class="panel" id="panel-trails">
      <div class="drag"></div>
      <div class="ph"><div class="ph-t">My Trails</div><button class="ph-x" onclick="closeAll()">✕</button></div>
      <!-- Walk stats strip -->
      <div class="stats-strip">
        <div class="ss-item"><div class="ss-n" id="ssWalks">0</div><div class="ss-l">Walks</div></div>
        <div class="ss-item"><div class="ss-n" id="ssDist">0</div><div class="ss-l">km Total</div></div>
        <div class="ss-item"><div class="ss-n" id="ssReports">0</div><div class="ss-l">Reports</div></div>
        <div class="ss-item"><div class="ss-n" id="ssHeart">0</div><div class="ss-l">Hearted</div></div>
      </div>
      <div class="mt-tabs">
        <div class="mt-tab on" onclick="switchMtTab(this,'created')">📝 Created</div>
        <div class="mt-tab" onclick="switchMtTab(this,'hearted')">❤️ Hearted</div>
        <div class="mt-tab" onclick="switchMtTab(this,'completed')">✅ Done</div>
      </div>
      <div class="pscroll"><div class="rlist" id="mtList" style="padding-top:12px"></div></div>
    </div>

    <!-- ══ PACK PANEL ══ -->
    <div class="panel" id="panel-pack">
      <div class="drag"></div>
      <div class="pscroll">
        <div class="pack-hero">
          <div class="pack-top">
            <div class="pack-av" id="packAv">🦊</div>
            <div>
              <div class="pack-name" id="packName">Trail Walker</div>
              <div class="pack-rank-pill" id="packRankPill">🐾 Pup</div>
            </div>
          </div>
          <!-- Dogs are the heroes -->
          <div class="rr-title" style="color:rgba(255,255,255,.7);font-size:11px;text-transform:uppercase;letter-spacing:.5px;margin-bottom:10px">Your Pack</div>
          <div class="dogs-row" id="dogsRow">
            <div class="add-dog-card" onclick="addDogPrompt()"><span>➕</span><p>Add dog</p></div>
          </div>
        </div>
        <div class="sec"><div class="sec-t">🏅 Medals</div><div class="medals-grid" id="medGrid"></div></div>
        <div class="sec"><div class="sec-t">🏆 Local Pack Leaders</div><div id="lbList" style="margin-top:2px"></div></div>
        <div style="height:24px"></div>
      </div>
    </div>

    <!-- ══ ROUTE DETAIL ══ -->
    <div id="detail">
      <div class="det-photo">
        <img id="detImg" src="" alt="">
        <div class="det-ov"></div>
        <button class="det-close" onclick="closeDetail()">✕</button>
        <div class="det-title-ov">
          <div class="diff d-e" id="detDiff" style="display:inline-block;margin-bottom:6px">easy</div>
          <div class="det-name" id="detName">Route</div>
        </div>
      </div>
      <div class="det-body">
        <div class="det-tags" id="detTags"></div>
        <div class="det-stats" id="detStats"></div>
        <p class="det-desc" id="detDesc"></p>
        <div style="margin-bottom:6px"><div style="font-size:11px;font-weight:700;color:var(--moss);margin-bottom:6px;text-transform:uppercase;letter-spacing:.4px">Your rating</div><div class="rc-stars" id="detStars"></div></div>
      </div>
      <div class="det-foot">
        <button class="btn-walk" style="flex:2" onclick="followFromDetail()">🐾 Walk this Route</button>
        <button class="btn-rx" id="detHeart" onclick="reactDetail('heart')">❤️ <span id="detHN">0</span></button>
        <button class="btn-rx" id="detHF" onclick="reactDetail('highfive')">🙌 <span id="detHFN">0</span></button>
        <button class="btn-done" id="detDone" onclick="doneDetail()">✅</button>
      </div>
    </div>
  </div>

  <div id="tabs">
    <div class="ti on" id="tWalk" onclick="openPanel('walk')"><div class="ti-ico">🗺️</div><div class="ti-lbl">Walk</div></div>
    <div class="ti" id="tReport" onclick="openPanel('report')"><div class="ti-ico">⚠️</div><div class="ti-lbl">Report</div></div>
    <div class="ti" id="tTrails" onclick="openPanel('trails');renderMtTab('created')"><div class="ti-ico">🧡</div><div class="ti-lbl">My Trails</div></div>
    <div class="ti" id="tPack" onclick="openPanel('pack')"><div class="ti-ico">🐕</div><div class="ti-lbl">Pack</div></div>
  </div>
</div>

<div class="toast" id="toast"><span id="tIco">🎉</span><span id="tMsg"></span></div>

<script>
// ── SUPABASE ─────────────────────────────────────────────────────────────
const SB='https://mfulyfltqlxotpqrbzae.supabase.co';
const KEY='sb_publishable_WeQkdXSRDEOkQHvLVKfqhg_qNwhRpLB';
const H={'apikey':KEY,'Authorization':`Bearer ${KEY}`,'Content-Type':'application/json','Prefer':'return=representation'};
const sbG=(t,q='')=>fetch(`${SB}/rest/v1/${t}?${q}`,{headers:H}).then(r=>{if(!r.ok)throw r;return r.json();});
const sbI=(t,d)=>fetch(`${SB}/rest/v1/${t}`,{method:'POST',headers:H,body:JSON.stringify(d)}).then(r=>{if(!r.ok)throw r;return r.json();});
const sbP=(t,q,d)=>fetch(`${SB}/rest/v1/${t}?${q}`,{method:'PATCH',headers:H,body:JSON.stringify(d)}).then(r=>{if(!r.ok)throw r;return r.json();});

// ── PHOTOS ───────────────────────────────────────────────────────────────
const PHOTOS={
  lake:'https://images.unsplash.com/photo-1501854140801-50d01698950b?w=600&q=80',
  wood:'https://images.unsplash.com/photo-1448375240586-882707db888b?w=600&q=80',
  moor:'https://images.unsplash.com/photo-1506905925346-21bda4d32df4?w=600&q=80',
  valley:'https://images.unsplash.com/photo-1439853949212-36089f8a5831?w=600&q=80',
  hill:'https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=600&q=80',
  park:'https://images.unsplash.com/photo-1587502536900-31e4db8ee1f2?w=600&q=80',
};
function getPhoto(r){
  const n=(r.name||'').toLowerCase();
  if(n.includes('lake')||n.includes('reservoir'))return PHOTOS.lake;
  if(n.includes('dell')||n.includes('wood')||n.includes('forest'))return PHOTOS.wood;
  if(n.includes('moor')||n.includes('ashworth'))return PHOTOS.moor;
  if(n.includes('valley')||n.includes('roch'))return PHOTOS.valley;
  if(n.includes('hill')||n.includes('tandle'))return PHOTOS.hill;
  return PHOTOS.park;
}

// ── HAZARDS ───────────────────────────────────────────────────────────────
const HAZ=[
  {id:'animals',icon:'🐄',name:'Animals',items:[
    {id:'cow',icon:'🐄',label:'Cattle',color:'#8B4513',days:7},
    {id:'sheep',icon:'🐑',label:'Sheep',color:'#7f8c8d',days:7},
    {id:'horse',icon:'🐎',label:'Horses',color:'#6B4C2A',days:1},
    {id:'goat',icon:'🐐',label:'Goats',color:'#95a5a6',days:7},
    {id:'deer',icon:'🦌',label:'Deer',color:'#8B6914',days:3},
    {id:'geese',icon:'🦢',label:'Geese',color:'#7f8c8d',days:3},
    {id:'aggdog',icon:'🐕',label:'Ag. Dog',color:'#c0392b',days:1},
  ]},
  {id:'terrain',icon:'⚠️',name:'Terrain',items:[
    {id:'mud',icon:'🟤',label:'Muddy',color:'#8B6914',days:3},
    {id:'cliff',icon:'⛰️',label:'Cliff',color:'#7F8C8D',days:null},
    {id:'flood',icon:'🌊',label:'Flooding',color:'#2980b9',days:2},
    {id:'icy',icon:'🧊',label:'Icy',color:'#85c1e9',days:1},
    {id:'fallen',icon:'🌲',label:'Fallen tree',color:'#5C7A3E',days:7},
    {id:'stile',icon:'🚧',label:'Stile',color:'#E67E22',days:null},
  ]},
  {id:'traffic',icon:'🚗',name:'Traffic',items:[
    {id:'road',icon:'🚗',label:'Busy road',color:'#c0392b',days:14},
    {id:'cyclist',icon:'🚴',label:'Cyclists',color:'#2980b9',days:3},
    {id:'nodog',icon:'🚫',label:'No dogs',color:'#c0392b',days:null},
  ]},
  {id:'food',icon:'☕',name:'Food & Drink',items:[
    {id:'cafe',icon:'☕',label:'Café',color:'#8B6914',days:null},
    {id:'pub',icon:'🍺',label:'Pub',color:'#8B4513',days:null},
    {id:'water',icon:'💧',label:'Water tap',color:'#3498db',days:null},
    {id:'picnic',icon:'🧺',label:'Picnic',color:'#27ae60',days:null},
  ]},
  {id:'facilities',icon:'🗑️',name:'Facilities',items:[
    {id:'poobin',icon:'🗑️',label:'Poo bin',color:'#555',days:null},
    {id:'parking',icon:'🅿️',label:'Parking',color:'#2980b9',days:null},
    {id:'bench',icon:'🪑',label:'Bench',color:'#8B6914',days:null},
    {id:'toilet',icon:'🚻',label:'Toilets',color:'#555',days:null},
  ]},
];
const HFLAT={};HAZ.forEach(c=>c.items.forEach(h=>HFLAT[h.id]=h));

// ── RANKS ─────────────────────────────────────────────────────────────────
const RANKS=[
  {name:'Pup',icon:'🐾',min:0,next:5},
  {name:'Hound',icon:'🐕',min:5,next:15},
  {name:'Trail Dog',icon:'🦮',min:15,next:40},
  {name:'Pack Leader',icon:'👑',min:40,next:null},
];
function getRank(reports){return RANKS.slice().reverse().find(r=>reports>=r.min)||RANKS[0];}

// ── MEDALS ────────────────────────────────────────────────────────────────
const MEDALS=[
  {icon:'🥾',name:'First Steps',type:'walks',n:1,pts:50},
  {icon:'🗺️',name:'Explorer',type:'walks',n:10,pts:150},
  {icon:'🏔️',name:'Trailblazer',type:'walks',n:25,pts:300},
  {icon:'⚠️',name:'First Report',type:'reports',n:1,pts:50},
  {icon:'🌍',name:'Local Guide',type:'reports',n:10,pts:200},
  {icon:'👑',name:'Pack Leader',type:'reports',n:40,pts:500},
  {icon:'✅',name:'Completionist',type:'done',n:5,pts:150},
  {icon:'❤️',name:'Route Lover',type:'hearts',n:5,pts:100},
];

// ── STATE ─────────────────────────────────────────────────────────────────
let map,routeL=null,recL=null,followL=null,planL=null;
let isRec=false,recPath=[],mLayers=[];
let selHaz=null,markMode=false,sat=false;
let activeFilter='all',searchQ='';
let allRoutes=[],allMarkers=[];
let profile=null,dogs=[];
let reactions={},completions=new Set(),ratings={};
let totalWalks=0,totalReports=0,totalDist=0;
let navOn=false,navMuted=false,navWPs=[],navIdx=0;
let obStep=0,selAv='🦊',dogN=0;
let openPanelId=null,detailRoute=null,mtTab='created';
let planRoute=null,geocodeTO=null;
let xp=0;

// ── MAP ───────────────────────────────────────────────────────────────────
const OSM=L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',{attribution:'© OSM',maxZoom:19});
const SAT=L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}',{attribution:'© Esri',maxZoom:19});

window.onload=async()=>{
  map=L.map('map',{zoomControl:false,layers:[OSM]}).setView([53.620,-2.160],13);
  map.on('click',onMapClick);
  map.on('locationfound',e=>{if(navOn)checkNav(e.latlng);});
  initOb();renderHazCats();
  await loadAll();
  setTimeout(()=>openPanel('walk'),500);
};

// ── ONBOARDING ────────────────────────────────────────────────────────────
function initOb(){
  const s=localStorage.getItem('pt_profile');
  if(s){
    profile=JSON.parse(s);dogs=JSON.parse(localStorage.getItem('pt_dogs')||'[]');
    reactions=JSON.parse(localStorage.getItem('pt_reactions')||'{}');
    completions=new Set(JSON.parse(localStorage.getItem('pt_completions')||'[]'));
    ratings=JSON.parse(localStorage.getItem('pt_ratings')||'{}');
    totalWalks=parseInt(localStorage.getItem('pt_walks')||'0');
    totalReports=parseInt(localStorage.getItem('pt_reports')||'0');
    totalDist=parseFloat(localStorage.getItem('pt_dist')||'0');
    xp=parseInt(localStorage.getItem('pt_xp')||'0');
    document.getElementById('obOv').classList.add('gone');
    applyProfile();return;
  }
  const g=document.getElementById('avGrid');
  ['🦊','🐺','🦁','🐻','🦅','🦌','🦔','🦝','🐗','🦜','🌲','⛰️'].forEach((a,i)=>{
    const d=document.createElement('div');d.className='av-o'+(i===0?' sel':'');d.textContent=a;
    d.onclick=()=>{document.querySelectorAll('.av-o').forEach(x=>x.classList.remove('sel'));d.classList.add('sel');selAv=a;};
    g.appendChild(d);
  });
  obAddDog();
}

function obAddDog(){
  dogN++;const id=`de${dogN}`;
  const div=document.createElement('div');div.className='de-ob';div.id=id;
  div.innerHTML=`<button class="de-rm" onclick="document.getElementById('${id}').remove()">✕</button>
    <div class="ob-ff"><label class="ob-fl">Dog's name</label><input class="ob-fi" id="${id}n" placeholder="e.g. Buddy" maxlength="30"></div>
    <div class="ob-ff"><label class="ob-fl">Breed</label><input class="ob-fi" id="${id}b" placeholder="e.g. Labrador" maxlength="40"></div>`;
  document.getElementById('dogEntries').appendChild(div);
}

function obNext(to){
  if(to===1){const n=document.getElementById('onName').value.trim();if(!n){toast('Enter your name!','⚠️');return;}profile={username:n,avatar:selAv};}
  if(to===2){
    dogs=[];
    document.querySelectorAll('.de-ob').forEach(e=>{
      const nm=e.querySelector('[id$="n"]').value.trim(),br=e.querySelector('[id$="b"]').value.trim();
      if(nm)dogs.push({name:nm,breed:br,emoji:['🐕','🦮','🐩','🐾'][dogs.length%4],xp:0,dist:0});
    });
    document.getElementById('readyAv').textContent=profile.avatar;
    document.getElementById('readyNm').textContent=`Hey ${profile.username}! 👋`;
    document.getElementById('readyDogs').innerHTML=dogs.map(d=>`<div class="dp">🐕 ${d.name}${d.breed?' · '+d.breed:''}</div>`).join('');
  }
  document.getElementById(`os${obStep}`).classList.remove('on');document.getElementById(`od${obStep}`).classList.remove('on');
  obStep=to;
  document.getElementById(`os${obStep}`).classList.add('on');document.getElementById(`od${obStep}`).classList.add('on');
}

async function obFinish(){
  localStorage.setItem('pt_profile',JSON.stringify(profile));
  localStorage.setItem('pt_dogs',JSON.stringify(dogs));
  try{const s=await sbI('profiles',[{username:profile.username,avatar:profile.avatar}]);profile.id=s[0].id;if(dogs.length)await sbI('dogs',dogs.map(d=>({name:d.name,breed:d.breed,emoji:d.emoji,profile_id:profile.id})));localStorage.setItem('pt_profile',JSON.stringify(profile));}catch(e){console.warn(e);}
  document.getElementById('obOv').classList.add('gone');
  applyProfile();addXP(50);toast(`Welcome ${profile.username}! Your adventure starts now 🐾`,'🎉');
}

function applyProfile(){
  document.getElementById('hdrAv').textContent=profile.avatar;
  document.getElementById('packAv').textContent=profile.avatar;
  document.getElementById('packName').textContent=profile.username;
  renderDogs();renderMedals();renderLB();updateRank();updateWalkPill();
}

function renderDogs(){
  const row=document.getElementById('dogsRow');
  row.innerHTML='';
  dogs.forEach(d=>{
    const lvl=Math.floor((d.xp||0)/100)+1;
    const el=document.createElement('div');el.className='dog-hero';
    el.innerHTML=`<div class="dog-hero-av">${d.emoji}</div><div class="dog-hero-name">${d.name}</div><div class="dog-hero-dist">🐾 ${(d.dist||0).toFixed(1)} km walked</div><div class="dog-hero-lvl">Level ${lvl} ${lvl<3?'Pup':lvl<6?'Hound':'Trail Dog'}</div>`;
    row.appendChild(el);
  });
  const add=document.createElement('div');add.className='add-dog-card';add.onclick=addDogPrompt;
  add.innerHTML='<span>➕</span><p>Add dog</p>';row.appendChild(add);
}

function addDogPrompt(){
  const n=prompt('Dog\'s name?');if(!n)return;
  const b=prompt('Breed? (optional)')||'';
  const d={name:n,breed:b,emoji:['🐕','🦮','🐩','🐾'][dogs.length%4],xp:0,dist:0};
  dogs.push(d);localStorage.setItem('pt_dogs',JSON.stringify(dogs));
  if(profile?.id)sbI('dogs',[{name:d.name,breed:d.breed,emoji:d.emoji,profile_id:profile.id}]).catch(()=>{});
  renderDogs();toast(`${d.emoji} ${n} added to your pack!`,'🐕');
}

// ── PANELS ────────────────────────────────────────────────────────────────
function openPanel(id){
  if(openPanelId&&openPanelId!==id)document.getElementById(`panel-${openPanelId}`)?.classList.remove('open');
  document.querySelectorAll('.ti').forEach(t=>t.classList.remove('on'));
  const panel=document.getElementById(`panel-${id}`);if(!panel)return;
  if(openPanelId===id&&panel.classList.contains('open')){closeAll();return;}
  panel.classList.add('open');openPanelId=id;
  document.getElementById('backdrop').classList.add('on');
  const m={walk:'tWalk',report:'tReport',trails:'tTrails',pack:'tPack'};
  if(m[id])document.getElementById(m[id])?.classList.add('on');
}

function closeAll(){
  document.querySelectorAll('.panel').forEach(p=>p.classList.remove('open'));
  document.getElementById('backdrop').classList.remove('on');
  document.querySelectorAll('.ti').forEach(t=>t.classList.remove('on'));
  openPanelId=null;
}

// ── DATA ──────────────────────────────────────────────────────────────────
const fd=days=>{const d=new Date();d.setDate(d.getDate()+days);return d.toISOString();};

const SEED=[
  {name:"Hollingworth Lake Loop",description:"Gorgeous lakeside circuit. Dogs love the water access points. Mostly flat, great for all abilities.",difficulty:"easy",distance_km:4.2,duration_mins:55,off_lead:true,has_water:true,created_by:"PawTrails Team",path:[[53.644,-2.118],[53.641,-2.125],[53.635,-2.122],[53.630,-2.115],[53.632,-2.105],[53.638,-2.098],[53.645,-2.103],[53.644,-2.118]]},
  {name:"Healey Dell Nature Reserve",description:"Stunning wooded gorge with viaduct views. Keep dogs on lead near the drops.",difficulty:"moderate",distance_km:2.8,duration_mins:40,off_lead:false,has_water:true,created_by:"PawTrails Team",path:[[53.648,-2.186],[53.651,-2.190],[53.649,-2.196],[53.645,-2.193],[53.642,-2.188],[53.644,-2.182],[53.648,-2.186]]},
  {name:"Tandle Hill Country Park",description:"Panoramic views across Greater Manchester. Exposed at the top on windy days.",difficulty:"moderate",distance_km:5.1,duration_mins:70,off_lead:true,has_water:false,created_by:"PawTrails Team",path:[[53.570,-2.138],[53.574,-2.145],[53.571,-2.152],[53.565,-2.148],[53.560,-2.140],[53.562,-2.132],[53.568,-2.130],[53.570,-2.138]]},
  {name:"Ashworth Valley Trail",description:"Challenging moorland with reservoir views. Cattle in season — keep dogs close.",difficulty:"hard",distance_km:6.8,duration_mins:90,off_lead:true,has_water:true,created_by:"PawTrails Team",path:[[53.650,-2.226],[53.654,-2.234],[53.658,-2.240],[53.655,-2.248],[53.649,-2.245],[53.644,-2.238],[53.647,-2.228],[53.650,-2.226]]},
  {name:"Roch Valley Way",description:"Riverside path through Rochdale town. Paved sections — great for all weather.",difficulty:"easy",distance_km:3.4,duration_mins:45,off_lead:false,has_water:true,created_by:"PawTrails Team",path:[[53.614,-2.158],[53.611,-2.165],[53.607,-2.162],[53.604,-2.155],[53.607,-2.148],[53.612,-2.150],[53.614,-2.158]]},
];

async function loadAll(){
  setDb('connecting');
  try{
    const[routes,markers]=await Promise.all([sbG('routes','order=created_at.desc&limit=50'),sbG('markers','order=created_at.desc&limit=200')]);
    if(!routes.length){
      await sbI('routes',SEED);
      allRoutes=await sbG('routes','order=created_at.desc&limit=50');
      toast('Starter routes loaded! 🐾','🎉');
    }else allRoutes=routes;
    allMarkers=markers;
    setDb('on');renderList();drawRoutes();updateTrailsStats();renderRecentReports();
  }catch(e){
    setDb('err');allRoutes=SEED.map((r,i)=>({...r,id:`l${i}`}));
    renderList();drawRoutes();toast('Offline mode','⚠️');
  }
}

async function doRefresh(){toast('Refreshing…','🔄');clearLayers();await loadAll();}

// ── WALK SEARCH + PLAN ────────────────────────────────────────────────────
function onWalkSearch(v){
  searchQ=v;renderList();
  clearTimeout(geocodeTO);
  if(v.length<3){document.getElementById('walkSug').style.display='none';return;}
  geocodeTO=setTimeout(()=>geocode(v),500);
}

async function geocode(q){
  try{
    const r=await fetch(`https://nominatim.openstreetmap.org/search?q=${encodeURIComponent(q+', UK')}&format=json&limit=4`);
    const data=await r.json();
    const sug=document.getElementById('walkSug');
    if(!data.length){sug.style.display='none';return;}
    sug.style.display='block';
    sug.innerHTML=data.map(d=>`<div class="sug-item" onclick="planTo(${d.lat},${d.lon},'${d.display_name.replace(/'/g,"\\'")}')">🗺️ ${d.display_name.split(',').slice(0,2).join(',')}</div>`).join('');
  }catch(e){console.warn(e);}
}

async function planTo(lat,lon,name){
  document.getElementById('walkSug').style.display='none';
  document.getElementById('walkSearch').value=name.split(',')[0];
  document.getElementById('planName').value=`Walk to ${name.split(',')[0]}`;
  toast('Planning route…','🗺️');
  const c=map.getCenter();
  try{
    const url=`https://api.openrouteservice.org/v2/directions/foot-walking?api_key=5b3ce3597851110001cf6248a5b8b56d0f3a4f5b8a8e7b0d7c4d3a2b&start=${c.lng},${c.lat}&end=${lon},${lat}`;
    const res=await fetch(url);const data=await res.json();
    if(data.features?.[0]){
      const coords=data.features[0].geometry.coordinates;
      const wps=coords.map(c=>[c[1],c[0]]);
      const sum=data.features[0].properties.summary;
      const dist=(sum.distance/1000).toFixed(1);
      const mins=Math.round(sum.duration/60);
      drawPlan(wps,name,dist,mins,'moderate');
    }else throw new Error();
  }catch{
    const dist=(map.distance([c.lat,c.lng],[lat,lon])/1000).toFixed(1);
    drawPlan([[c.lat,c.lng],[lat,lon]],name,dist,Math.round(dist*15),'easy',true);
    toast('Showing straight-line estimate','⚠️');
  }
}

function drawPlan(wps,name,dist,mins,diff,approx=false){
  if(planL)map.removeLayer(planL);
  planL=L.polyline(wps,{color:approx?'#D4820A':'#2D4A22',weight:6,opacity:.85,dashArray:'10,5',lineCap:'round'}).addTo(map);
  map.fitBounds(L.latLngBounds(wps),{padding:[50,50]});
  planRoute={path:wps,dist:parseFloat(dist),mins,diff};
  document.getElementById('planResult').classList.add('on');
  document.getElementById('prTitle').textContent=`📍 Route to ${name.split(',')[0]}`;
  document.getElementById('prMeta').innerHTML=`<span>📏 ${dist} km</span><span>⏱ ~${mins} min</span>`;
  document.getElementById('planDiff').value=diff;
  toast(`Route found — ${dist} km`,'✅');
}

async function savePlan(){
  if(!planRoute)return;
  const name=document.getElementById('planName').value.trim()||'My planned route';
  const diff=document.getElementById('planDiff').value;
  try{
    const s=await sbI('routes',[{name,difficulty:diff,distance_km:planRoute.dist,duration_mins:planRoute.mins,path:planRoute.path,created_by:profile?.username||'Anonymous',description:'Planned walk'}]);
    allRoutes.unshift(s[0]);renderList();drawRoutes();clearPlan();
    addXP(100);toast(`"${name}" saved! 🐾`,'🗺️');
  }catch{toast('Save failed','❌');}
}

function clearPlan(){
  if(planL){map.removeLayer(planL);planL=null;}
  document.getElementById('planResult').classList.remove('on');
  planRoute=null;
}

// ── ROUTE LIST ────────────────────────────────────────────────────────────
function renderList(){
  const el=document.getElementById('routeList');
  const f=allRoutes.filter(r=>{
    const t=!searchQ||r.name.toLowerCase().includes(searchQ.toLowerCase());
    let c=true;
    if(activeFilter==='easy')c=r.difficulty==='easy';
    else if(activeFilter==='moderate')c=r.difficulty==='moderate';
    else if(activeFilter==='hard')c=r.difficulty==='hard';
    else if(activeFilter==='offLead')c=r.off_lead;
    else if(activeFilter==='water')c=r.has_water;
    return t&&c;
  });
  if(!f.length){el.innerHTML=`<div class="empty"><div class="empty-i">🐕</div><p>No routes yet.<br>Be the first to record one!</p></div>`;return;}
  el.innerHTML='';f.forEach(r=>el.appendChild(mkCard(r)));
}

function mkCard(r){
  const photo=getPhoto(r),rId=r.id;
  const rx=reactions[rId]||{},done=completions.has(String(rId)),myR=ratings[rId]||0;
  const hearts=(r.heart_count||0),hf=(r.highfive_count||0);
  const walkers=Math.floor(Math.random()*6)+1;
  const isNew=r.created_by!=='PawTrails Team';
  const dc={easy:'d-e',moderate:'d-m',hard:'d-h'};
  const card=document.createElement('div');card.className='rc';
  card.innerHTML=`
    <div class="rc-photo">
      <img src="${photo}" loading="lazy" alt="">
      <div class="rc-photo-ov"></div>
      <div class="rc-pb">
        <span class="diff ${dc[r.difficulty]||'d-e'}">${r.difficulty}</span>
        <span class="rc-walkers">👣 ${walkers} today</span>
      </div>
      ${isNew?'<div style="position:absolute;top:10px;left:10px;background:var(--earth);color:#fff;font-size:9px;padding:3px 7px;border-radius:5px;font-weight:700">NEW</div>':''}
    </div>
    <div class="rc-body">
      <div class="rc-name">${r.name}</div>
      <div class="rc-meta"><span>📏 ${r.distance_km||'?'} km</span><span>⏱ ${r.duration_mins||'?'} min</span><span>${r.off_lead?'🟢 Off-lead':'🔴 On-lead'}</span>${r.has_water?'<span>💧</span>':''}</div>
      <div class="rc-stars" id="stars-${rId}">${mkStars(rId,myR)}</div>
      ${r.description?`<div class="rc-desc">${r.description.slice(0,80)}…</div>`:''}
    </div>
    <div class="rc-foot">
      <button class="btn-walk" onclick="event.stopPropagation();startFollow('${rId}')">🐾 Walk</button>
      <button class="btn-rx${rx.heart?' on':''}" id="h-${rId}" onclick="event.stopPropagation();react('${rId}','heart')">❤️ ${hearts}</button>
      <button class="btn-rx${rx.highfive?' on':''}" id="hf-${rId}" onclick="event.stopPropagation();react('${rId}','highfive')">🙌 ${hf}</button>
      <button class="btn-done${done?' on':''}" id="d-${rId}" onclick="event.stopPropagation();markDone('${rId}')">✅</button>
    </div>`;
  card.onclick=()=>openDetail(r);
  return card;
}

function mkStars(rId,cur){
  return[1,2,3,4,5].map(i=>`<span class="star" onclick="event.stopPropagation();rateRoute('${rId}',${i})">${i<=cur?'⭐':'☆'}</span>`).join('');
}

// ── REACTIONS + RATINGS + DONE ────────────────────────────────────────────
async function react(rId,type){
  const rx=reactions[rId]||{};const was=rx[type];rx[type]=!was;reactions[rId]=rx;
  localStorage.setItem('pt_reactions',JSON.stringify(reactions));
  const r=allRoutes.find(x=>String(x.id)===String(rId));if(!r)return;
  if(type==='heart'){r.heart_count=Math.max(0,(r.heart_count||0)+(rx.heart?1:-1));const el=document.getElementById(`h-${rId}`);if(el){el.className='btn-rx'+(rx.heart?' on':'');el.innerHTML=`❤️ ${r.heart_count}`;}}
  else{r.highfive_count=Math.max(0,(r.highfive_count||0)+(rx.highfive?1:-1));const el=document.getElementById(`hf-${rId}`);if(el){el.className='btn-rx'+(rx.highfive?' on':'');el.innerHTML=`🙌 ${r.highfive_count}`;}}
  if(!was){addXP(5);toast(type==='heart'?'❤️ Hearted!':'🙌 High-fived!',type==='heart'?'❤️':'🙌');}
  updateTrailsStats();
  try{await sbI('reactions',[{route_id:rId,user_name:profile?.username||'Anon',reaction_type:type}]);}catch{}
}

async function rateRoute(rId,stars){
  ratings[rId]=stars;localStorage.setItem('pt_ratings',JSON.stringify(ratings));
  document.getElementById(`stars-${rId}`)?.innerHTML&&(document.getElementById(`stars-${rId}`).innerHTML=mkStars(rId,stars));
  document.getElementById('detStars')?.innerHTML&&(document.getElementById('detStars').innerHTML=mkStars(rId,stars));
  addXP(10);toast(`${stars}⭐ rated!`,'⭐');
  try{await sbI('ratings',[{route_id:rId,user_name:profile?.username||'Anon',stars}]);}catch{}
}

async function markDone(rId){
  const was=completions.has(String(rId));
  if(was)completions.delete(String(rId));else{
    completions.add(String(rId));
    addXP(50);toast('Walk completed! +50 XP 🏁','✅');
    // Give dog XP + dist
    const r=allRoutes.find(x=>String(x.id)===String(rId));
    if(r&&dogs[0]){dogs[0].xp=(dogs[0].xp||0)+50;dogs[0].dist=(dogs[0].dist||0)+(r.distance_km||0);localStorage.setItem('pt_dogs',JSON.stringify(dogs));renderDogs();}
    totalWalks++;totalDist+=(allRoutes.find(x=>String(x.id)===String(rId))?.distance_km||0);
    localStorage.setItem('pt_walks',totalWalks);localStorage.setItem('pt_dist',totalDist);
    updateWalkPill();updateTrailsStats();checkMedals();
  }
  localStorage.setItem('pt_completions',JSON.stringify([...completions]));
  document.getElementById(`d-${rId}`)?.classList.toggle('on',!was);
  document.getElementById('detDone')?.classList.toggle('on',!was);
}

// ── DETAIL ────────────────────────────────────────────────────────────────
function openDetail(r){
  detailRoute=r;
  document.getElementById('detImg').src=getPhoto(r);
  document.getElementById('detName').textContent=r.name;
  const dc={easy:'d-e',moderate:'d-m',hard:'d-h'};
  document.getElementById('detDiff').className=`diff ${dc[r.difficulty]||'d-e'}`;
  document.getElementById('detDiff').textContent=r.difficulty;
  document.getElementById('detDesc').textContent=r.description||'No description yet.';
  document.getElementById('detTags').innerHTML=[
    `<span class="det-tag">📏 ${r.distance_km||'?'} km</span>`,
    `<span class="det-tag">⏱ ${r.duration_mins||'?'} min</span>`,
    `<span class="det-tag">${r.off_lead?'🟢 Off-lead':'🔴 On-lead'}</span>`,
    r.has_water?'<span class="det-tag">💧 Water</span>':'',
    `<span class="det-tag">👤 ${r.created_by||'Community'}</span>`,
  ].join('');
  const rx=reactions[r.id]||{},done=completions.has(String(r.id)),myR=ratings[r.id]||0;
  const w=Math.floor(Math.random()*6)+1;
  document.getElementById('detStats').innerHTML=`<div class="det-stat"><div class="det-stat-n">${r.heart_count||0}</div><div class="det-stat-l">Hearts</div></div><div class="det-stat"><div class="det-stat-n">${w}</div><div class="det-stat-l">Today</div></div><div class="det-stat"><div class="det-stat-n">${completions.size}</div><div class="det-stat-l">Completions</div></div>`;
  document.getElementById('detHN').textContent=r.heart_count||0;
  document.getElementById('detHFN').textContent=r.highfive_count||0;
  document.getElementById('detHeart').className='btn-rx'+(rx.heart?' on':'');
  document.getElementById('detHF').className='btn-rx'+(rx.highfive?' on':'');
  document.getElementById('detDone').className='btn-done'+(done?' on':'');
  document.getElementById('detStars').innerHTML=mkStars(r.id,myR);
  // Show on map
  const path=typeof r.path==='string'?JSON.parse(r.path):r.path;
  if(path?.length>1){if(routeL)map.removeLayer(routeL);const col={easy:'#5C7A3E',moderate:'#D4820A',hard:'#C0522A'};routeL=L.polyline(path,{color:col[r.difficulty]||'#5C7A3E',weight:5,opacity:.9,lineCap:'round'}).addTo(map);map.fitBounds(L.latLngBounds(path),{padding:[50,50]});}
  closeAll();
  document.getElementById('detail').classList.add('open');
  document.getElementById('backdrop').classList.add('on');
}

function closeDetail(){document.getElementById('detail').classList.remove('open');document.getElementById('backdrop').classList.remove('on');detailRoute=null;}
function followFromDetail(){const r=detailRoute;closeDetail();if(r)startFollow(r.id);}
function reactDetail(t){if(detailRoute)react(detailRoute.id,t);}
function doneDetail(){if(detailRoute)markDone(detailRoute.id);}

// ── FOLLOW + NAV ──────────────────────────────────────────────────────────
function startFollow(rId){
  const r=allRoutes.find(x=>String(x.id)===String(rId));if(!r)return;
  const path=typeof r.path==='string'?JSON.parse(r.path):r.path;
  if(!path||path.length<2){toast('No path data','⚠️');return;}
  if(navOn)stopNav();
  map.flyTo([path[0][0],path[0][1]],17,{animate:true,duration:1.1});
  if(followL)map.removeLayer(followL);
  followL=L.polyline(path,{color:'#F0A830',weight:9,opacity:1,lineCap:'round'}).addTo(map);
  L.circleMarker(path[0],{radius:10,color:'#2D4A22',fillColor:'#F0A830',fillOpacity:1,weight:3}).addTo(map);
  L.circleMarker(path[path.length-1],{radius:10,color:'#C0522A',fillColor:'#fff',fillOpacity:1,weight:3}).addTo(map);
  navOn=true;navWPs=path;navIdx=0;
  document.getElementById('navCard').classList.add('on');
  document.getElementById('navProg').style.width='0%';
  showNavHazards(path);
  closeAll();closeDetail();
  give(`Starting: ${r.name}`,`${r.distance_km||'?'} km`);
  speak(`Starting route: ${r.name}. ${r.distance_km||''} kilometres. Follow the orange path.`);
  setTimeout(()=>{
    const nb=allMarkers.filter(m=>m.lat&&L.latLngBounds(path.map(p=>L.latLng(p[0],p[1]))).pad(.001).contains(L.latLng(m.lat,m.lng)));
    if(nb.length)speak(`Heads up — ${nb.length} hazard${nb.length>1?'s':''} on this route.`);
  },3500);
  map.locate({watch:true,setView:false});
  toast(`Walking ${r.name} 🧭`,'🐾');
}

function showNavHazards(path){
  mLayers.forEach(l=>map.removeLayer(l));mLayers=[];
  const b=L.latLngBounds(path.map(p=>L.latLng(p[0],p[1]))).pad(.005);
  allMarkers.filter(m=>m.lat&&b.contains(L.latLng(m.lat,m.lng))).forEach(m=>{
    const h=HFLAT[m.type]||{icon:m.icon||'📍',label:m.label||m.type,color:'#555'};
    mLayers.push(mkMarker(m,h));
  });
}

function checkNav(ll){
  if(!navOn||!navWPs.length)return;
  if(ll.distanceTo(L.latLng(navWPs[navIdx][0],navWPs[navIdx][1]))<40){
    navIdx++;
    document.getElementById('navProg').style.width=(navIdx/navWPs.length*100)+'%';
    map.setView(ll,17);
    if(navIdx>=navWPs.length-1){give('🏁 Arrived!','Great walk!');speak('You have arrived! Well done!');setTimeout(stopNav,4000);}
    else{const ins=getIns(navIdx);give(ins.t,ins.s);speak(ins.v);}
  }
}

function getIns(i){
  const wp=navWPs[i],nx=navWPs[Math.min(i+1,navWPs.length-1)];
  const b=bearing(wp,nx),d=bDir(b);
  const near=allMarkers.find(m=>m.lat&&L.latLng(m.lat,m.lng).distanceTo(L.latLng(wp[0],wp[1]))<80);
  return{t:`${d.a} ${d.l}${near?` — ⚠️ ${near.icon||''} ${near.label||''}`:''}`,s:`Waypoint ${i}/${navWPs.length-1}`,v:`${d.v}${near?`. Caution, ${near.label} ahead.`:''}`};
}

function bearing([a,b],[c,d]){const dl=(d-b)*Math.PI/180,ar=a*Math.PI/180,cr=c*Math.PI/180;return(Math.atan2(Math.sin(dl)*Math.cos(cr),Math.cos(ar)*Math.sin(cr)-Math.sin(ar)*Math.cos(cr)*Math.cos(dl))*180/Math.PI+360)%360;}
function bDir(b){if(b<22.5||b>=337.5)return{a:'⬆️',l:'Head north',v:'Continue north'};if(b<67.5)return{a:'↗️',l:'Bear right',v:'Bear right'};if(b<112.5)return{a:'➡️',l:'Turn right',v:'Turn right onto the path'};if(b<157.5)return{a:'↘️',l:'Bear right',v:'Bear right'};if(b<202.5)return{a:'⬇️',l:'Head south',v:'Continue south'};if(b<247.5)return{a:'↙️',l:'Bear left',v:'Bear left'};if(b<292.5)return{a:'⬅️',l:'Turn left',v:'Turn left onto the path'};return{a:'↖️',l:'Bear left',v:'Bear left'};}
function give(t,s){document.getElementById('navDir').textContent=t;document.getElementById('navSub').textContent=s;}
function speak(txt){if(navMuted||!window.speechSynthesis)return;speechSynthesis.cancel();const u=new SpeechSynthesisUtterance(txt);u.rate=.9;u.pitch=1;const vv=speechSynthesis.getVoices();const uk=vv.find(v=>v.lang==='en-GB');if(uk)u.voice=uk;speechSynthesis.speak(u);}
function toggleMute(){navMuted=!navMuted;document.getElementById('muteBtn').textContent=navMuted?'🔇 Muted':'🔊 Voice';}
function stopNav(){navOn=false;map.stopLocate();document.getElementById('navCard').classList.remove('on');if(followL){map.removeLayer(followL);followL=null;}mLayers.forEach(l=>map.removeLayer(l));mLayers=[];speechSynthesis?.cancel();map.setView([53.620,-2.160],13);toast('Navigation stopped','⏹');}

// ── REPORT ────────────────────────────────────────────────────────────────
function renderHazCats(){
  const c=document.getElementById('hazCats');
  HAZ.forEach(cat=>{
    const d=document.createElement('div');d.className='haz-cat';d.id=`hc-${cat.id}`;
    d.innerHTML=`<div class="hc-hdr" onclick="toggleCat('${cat.id}')"><div class="hc-l"><span class="hc-ico">${cat.icon}</span><div><div class="hc-name">${cat.name}</div><div class="hc-cnt">${cat.items.length} types</div></div></div><span class="hc-chev">▼</span></div><div class="hc-items">${cat.items.map(h=>`<div class="hi" id="hi-${h.id}" onclick="pickHaz('${h.id}')"><div class="hi-i">${h.icon}</div><div class="hi-l">${h.label}</div></div>`).join('')}</div>`;
    c.appendChild(d);
  });
}

function toggleCat(id){document.getElementById(`hc-${id}`).classList.toggle('open');}

function startReport(){
  closeAll();toast('Choose a hazard type below, then tap the map 👇','📍');}

function pickHaz(id){
  const h=HFLAT[id];if(!h)return;
  document.querySelectorAll('.hi').forEach(b=>b.classList.remove('sel'));
  document.getElementById(`hi-${id}`).classList.add('sel');
  selHaz=h;markMode=true;
  showMode(h.icon,`Tap map — ${h.label}`);
  map.getContainer().style.cursor='crosshair';
  closeAll();toast(`${h.icon} ${h.label} — tap the map!`,'📍');
}

function cancelMode(){markMode=false;selHaz=null;hideMode();document.querySelectorAll('.hi').forEach(b=>b.classList.remove('sel'));map.getContainer().style.cursor='';}

function renderRecentReports(){
  const el=document.getElementById('recentReports');
  const recent=allMarkers.slice(0,5);
  if(!recent.length){el.innerHTML='<div style="font-size:12px;color:var(--moss);padding:8px 0">No reports yet — be the first!</div>';return;}
  el.innerHTML=recent.map(m=>{
    const h=HFLAT[m.type]||{icon:m.icon||'📍',label:m.label||m.type,color:'#555'};
    return`<div class="rr-item"><div class="rr-ico">${h.icon}</div><div class="rr-info"><div class="rr-label">${h.label}</div><div class="rr-meta">By ${m.reported_by||'Community'}</div></div><button class="rr-conf" onclick="confHaz('${m.id}',true)">👍 Still there</button></div>`;
  }).join('');
}

function mkMarker(m,h){
  const ico=L.divIcon({className:'',html:`<div style="background:${h.color};width:34px;height:34px;border-radius:50% 50% 50% 0;transform:rotate(-45deg);display:flex;align-items:center;justify-content:center;border:2px solid #fff;box-shadow:0 3px 10px rgba(0,0,0,.3)"><span style="transform:rotate(45deg);font-size:15px">${h.icon}</span></div>`,iconSize:[34,34],iconAnchor:[17,34]});
  const conf=(!m.is_permanent&&m.id&&!String(m.id).startsWith('l'))?`<div class="conf-btns"><button class="cb cb-y" onclick="confHaz('${m.id}',true)">👍 Still there</button><button class="cb cb-n" onclick="confHaz('${m.id}',false)">👎 Gone</button></div>`:'';
  return L.marker({lat:m.lat,lng:m.lng},{icon:ico}).addTo(map).bindPopup(`<div class="pop-hdr" style="background:${h.color}">${h.icon} ${h.label}</div><div class="pop-body"><div class="pop-meta">By ${m.reported_by||'Community'}</div>${conf}</div>`);
}

async function confHaz(id,yes){
  try{
    await sbI('hazard_confirmations',[{marker_id:id,confirmed:yes,confirmed_by:profile?.username||'Anon'}]);
    toast(yes?'Hazard confirmed ✅':'Gone noted 👎',yes?'👍':'👎');addXP(yes?10:5);map.closePopup();
  }catch{toast('Could not save','⚠️');}
}

async function onMapClick(e){
  if(markMode&&selHaz){
    const who=profile?.username||'Anonymous';
    const exAt=selHaz.days?fd(selHaz.days):null;
    const md={type:selHaz.id,icon:selHaz.icon,label:selHaz.label,lat:e.latlng.lat,lng:e.latlng.lng,reported_by:who,is_permanent:!selHaz.days,expires_at:exAt};
    if(navOn){const h=selHaz;mLayers.push(mkMarker(md,h));}
    try{
      const s=await sbI('markers',[md]);allMarkers.unshift(s[0]);
      totalReports++;localStorage.setItem('pt_reports',totalReports);
      addXP(20);updateRank();updateTrailsStats();checkMedals();renderRecentReports();
      toast(`${selHaz.icon} ${selHaz.label} reported! +20 XP`,'✅');
    }catch{toast('Saved locally','⚠️');}
    cancelMode();
  }else if(isRec){
    recPath.push([e.latlng.lat,e.latlng.lng]);
    if(recL)map.removeLayer(recL);
    recL=L.polyline(recPath,{color:'#C0522A',weight:4,opacity:.9,dashArray:'8,4'}).addTo(map);
    L.circleMarker(e.latlng,{radius:5,color:'#C0522A',fillColor:'#fff',fillOpacity:1,weight:2}).addTo(map);
    toast(`Waypoint ${recPath.length}`,'📍');
  }
}

// ── RECORD ────────────────────────────────────────────────────────────────
function toggleRec(){
  isRec=!isRec;
  if(isRec){
    document.getElementById('recFab').classList.add('rec');
    document.getElementById('recIco').textContent='⏹';document.getElementById('recTxt').textContent='Stop';
    recPath=[];if(recL){map.removeLayer(recL);recL=null;}
    showMode('🔴','Tap map to add waypoints');closeAll();toast('Recording — tap the map!','🔴');
  }else{
    document.getElementById('recFab').classList.remove('rec');
    document.getElementById('recIco').textContent='🐾';document.getElementById('recTxt').textContent='Record';
    hideMode();
    if(recPath.length>1){openPanel('walk');document.getElementById('saveForm').classList.add('open');toast(`${recPath.length} waypoints — save your route!`,'📝');}
    else{if(recL){map.removeLayer(recL);recL=null;}toast('Need 2+ waypoints','⚠️');}
  }
}

async function saveRoute(){
  const n=document.getElementById('rName').value.trim();if(!n){toast('Enter a route name!','⚠️');return;}
  const btn=document.getElementById('btnSave');btn.disabled=true;btn.textContent='Saving…';
  try{
    const s=await sbI('routes',[{name:n,description:document.getElementById('rDesc').value.trim(),difficulty:document.getElementById('rDiff').value,distance_km:parseFloat(document.getElementById('rDist').value)||null,off_lead:document.getElementById('rOff').checked,has_water:document.getElementById('rWater').checked,path:recPath,created_by:profile?.username||'Anonymous'}]);
    allRoutes.unshift(s[0]);renderList();drawRoutes();cancelSave();
    if(recL){map.removeLayer(recL);recL=null;}
    addXP(100);toast(`"${n}" is live! +100 XP 🎉`,'🗺️');
  }catch{toast('Save failed','❌');}
  finally{btn.disabled=false;btn.textContent='Save to Paw Trails 🐾';}
}

function cancelSave(){document.getElementById('saveForm').classList.remove('open');['rName','rDesc','rDist'].forEach(id=>document.getElementById(id).value='');document.getElementById('rOff').checked=false;document.getElementById('rWater').checked=false;}

// ── MY TRAILS ─────────────────────────────────────────────────────────────
function switchMtTab(el,tab){document.querySelectorAll('.mt-tab').forEach(t=>t.classList.remove('on'));el.classList.add('on');mtTab=tab;renderMtTab(tab);}

function renderMtTab(tab){
  const el=document.getElementById('mtList');
  let routes=[];
  if(tab==='created')routes=allRoutes.filter(r=>r.created_by===profile?.username);
  else if(tab==='hearted')routes=allRoutes.filter(r=>reactions[r.id]?.heart);
  else if(tab==='completed')routes=allRoutes.filter(r=>completions.has(String(r.id)));
  const msgs={created:'Record your first route using the 🐾 button on the map!',hearted:'Heart routes you love — they\'ll appear here.',completed:'Complete walks and log them here.'};
  if(!routes.length){el.innerHTML=`<div class="empty"><div class="empty-i">${{created:'🗺️',hearted:'❤️',completed:'✅'}[tab]}</div><p>${msgs[tab]}</p></div>`;return;}
  el.innerHTML='';routes.forEach(r=>el.appendChild(mkCard(r)));
}

function updateTrailsStats(){
  document.getElementById('ssWalks').textContent=totalWalks;
  document.getElementById('ssDist').textContent=totalDist.toFixed(1);
  document.getElementById('ssReports').textContent=totalReports;
  document.getElementById('ssHeart').textContent=Object.values(reactions).filter(r=>r.heart).length;
}

// ── RANK ──────────────────────────────────────────────────────────────────
function updateRank(){
  const rank=getRank(totalReports);
  const next=RANKS.find(r=>r.min>totalReports);
  const pct=next?Math.min(((totalReports-rank.min)/(next.min-rank.min))*100,100):100;
  document.getElementById('rankBadge').textContent=rank.icon;
  document.getElementById('rankTitle').textContent=rank.name;
  document.getElementById('rankSub').textContent=`${totalReports} reports · ${rank.name}`;
  document.getElementById('rankBar').style.width=pct+'%';
  document.getElementById('rankNext').textContent=next?`${next.min-totalReports} more reports → ${next.name}`:'🎉 Maximum rank achieved!';
  document.getElementById('packRankPill').textContent=`${rank.icon} ${rank.name}`;
}

// ── MEDALS ────────────────────────────────────────────────────────────────
function renderMedals(){
  const g=document.getElementById('medGrid');g.innerHTML='';
  MEDALS.forEach(m=>{
    const hearts=Object.values(reactions).filter(r=>r.heart).length;
    const e=m.type==='walks'?totalWalks>=m.n:m.type==='reports'?totalReports>=m.n:m.type==='done'?completions.size>=m.n:m.type==='hearts'?hearts>=m.n:false;
    const d=document.createElement('div');d.className='medal';
    d.innerHTML=`<div class="med-i ${e?'earned':'locked'}">${e?m.icon:'🔒'}</div><div class="med-n">${m.name}</div>`;
    g.appendChild(d);
  });
}

function checkMedals(){
  MEDALS.forEach((m,i)=>{
    const hearts=Object.values(reactions).filter(r=>r.heart).length;
    const e=m.type==='walks'?totalWalks>=m.n:m.type==='reports'?totalReports>=m.n:m.type==='done'?completions.size>=m.n:m.type==='hearts'?hearts>=m.n:false;
    const el=document.getElementById('medGrid')?.children[i];if(!el)return;
    const ico=el.querySelector('.med-i');
    if(e&&ico?.classList.contains('locked')){ico.classList.replace('locked','earned');ico.textContent=m.icon;addXP(m.pts);toast(`🏅 ${m.name} unlocked! +${m.pts} XP`,'🎊');}
  });
}

function renderLB(){
  const nm=profile?.username||'You',av=profile?.avatar||'🦊';
  const rank=getRank(totalReports);
  const lb=[
    {av:'🦁',name:'Bruno\'s Pack',rank:'Pack Leader',s:156},{av:'🐺',name:'MoorsWalker',rank:'Trail Dog',s:94},
    {av,name:`${nm} (You)`,rank:rank.name,s:totalReports,me:true},
    {av:'🐻',name:'HealeyHiker',rank:'Hound',s:31},{av:'🦅',name:'DalesRunner',rank:'Pup',s:8}
  ].sort((a,b)=>b.s-a.s);
  document.getElementById('lbList').innerHTML=lb.map((p,i)=>`<div class="lb-item${p.me?' me':''}"><div class="lb-rank">${i===0?'🥇':i===1?'🥈':i===2?'🥉':i+1}</div><div style="font-size:18px">${p.av}</div><div class="lb-name"><div>${p.name}</div><div style="font-size:10px;color:var(--moss)">${p.rank}</div></div><div class="lb-score">⚠️ ${p.s}</div></div>`).join('');
}

// ── DRAW MAP ──────────────────────────────────────────────────────────────
function drawRoutes(){
  allRoutes.forEach(r=>{
    const path=typeof r.path==='string'?JSON.parse(r.path):r.path;if(!path||path.length<2)return;
    const col={easy:'#5C7A3E',moderate:'#D4820A',hard:'#C0522A'};
    L.polyline(path,{color:col[r.difficulty]||'#5C7A3E',weight:3,opacity:.28}).addTo(map).on('click',()=>openDetail(r));
  });
}

// ── UTILS ─────────────────────────────────────────────────────────────────
function addXP(n){xp+=n;localStorage.setItem('pt_xp',xp);renderMedals();checkMedals();}
function updateWalkPill(){document.getElementById('walkCount').textContent=totalWalks;}
function toggleChip(el,f){document.querySelectorAll('.chip').forEach(c=>c.classList.remove('on'));el.classList.add('on');activeFilter=f;renderList();}
function clearLayers(){mLayers.forEach(l=>map.removeLayer(l));mLayers=[];if(routeL){map.removeLayer(routeL);routeL=null;}if(recL){map.removeLayer(recL);recL=null;}if(followL){map.removeLayer(followL);followL=null;}if(planL){map.removeLayer(planL);planL=null;}}
function locateMe(){map.locate({setView:true,maxZoom:15});map.once('locationfound',()=>toast('Found you! 📍','✅'));map.once('locationerror',()=>toast('Location not available','⚠️'));}
function toggleSat(){sat=!sat;sat?(OSM.remove(),SAT.addTo(map)):(SAT.remove(),OSM.addTo(map));toast(sat?'Satellite':'Map view','🛰️');}
function showMode(ico,txt){document.getElementById('modeBar').classList.add('on');document.getElementById('modeIco').textContent=ico;document.getElementById('modeTxt').textContent=txt;}
function hideMode(){document.getElementById('modeBar').classList.remove('on');}
function setDb(s){const d=document.getElementById('dbDot');d.className='db'+(s==='on'?' on':s==='err'?' err':'');}
let _tt;
function toast(msg,ico='🎉'){const t=document.getElementById('toast');document.getElementById('tMsg').textContent=msg;document.getElementById('tIco').textContent=ico;t.classList.add('show');clearTimeout(_tt);_tt=setTimeout(()=>t.classList.remove('show'),3500);}
</script>
</body>
</html>
