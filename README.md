<!doctype html>
<html lang="de">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Foto Upload</title>

  <style>
    :root{
      --bg: #F7E6EA;
      --panel: rgba(255,247,248,.78);
      --panel2: rgba(255,247,248,.65);
      --border: rgba(231,201,207,.75);
      --text: #3A2C28;
      --muted:#7A5A53;
      --rose:#F1D7DC;
      --rose2:#E7C9CF;
      --shadow: 0 10px 30px rgba(58,44,40,.10);
      --radius: 18px;
    }
    *{ box-sizing:border-box; }
    body{
      margin:0;
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
      color:var(--text);
      min-height:100vh;
      background:
        radial-gradient(900px 500px at 15% 10%, rgba(185,138,143,.22), transparent 55%),
        radial-gradient(700px 450px at 85% 20%, rgba(139,107,98,.18), transparent 60%),
        linear-gradient(180deg, var(--bg), #F4DDE1);
    }

    .app{
      width:min(1100px, 100%);
      margin: 0 auto;
      padding: 18px;
      display:grid;
      grid-template-columns: 260px 1fr;
      gap: 14px;
      min-height:100vh;
      align-items: start;
    }

    .sidebar{
      background: rgba(241,215,220,.80);
      border: 1px solid rgba(231,201,207,.85);
      border-radius: var(--radius);
      box-shadow: var(--shadow);
      padding: 14px;
      position: sticky;
      top: 14px;
      max-height: calc(100vh - 28px);
      overflow:auto;
    }
    .sideTitle{
      margin: 2px 0 10px;
      font-size: 14px;
      color: var(--muted);
      display:flex;
      align-items:center;
      justify-content: space-between;
      gap: 10px;
    }
    .countBadge{
      font-size: 12px;
      border: 1px solid rgba(58,44,40,.12);
      background: rgba(255,255,255,.45);
      padding: 3px 8px;
      border-radius: 999px;
      color: var(--muted);
      white-space: nowrap;
    }

    .dateList{
      display:flex;
      flex-direction: column;
      gap: 6px;
      padding: 0;
      margin: 0;
      list-style:none;
    }
    .dateItem{
      border: 1px solid rgba(58,44,40,.10);
      background: rgba(255,255,255,.35);
      border-radius: 12px;
      padding: 10px 10px;
      cursor:pointer;
      color: #6E4F48; /* braun-rosé Text */
      display:flex;
      align-items:center;
      justify-content: space-between;
      gap: 10px;
      user-select:none;
    }
    .dateItem:hover{
      background: rgba(255,255,255,.48);
      border-color: rgba(185,138,143,.55);
    }
    .dateItem.active{
      font-weight: 700;
      text-decoration: underline;
      background: rgba(255,255,255,.58);
      border-color: rgba(185,138,143,.75);
    }
    .slots{
      font-size: 12px;
      color: rgba(110,79,72,.85);
      padding: 3px 8px;
      border-radius: 999px;
      border: 1px solid rgba(110,79,72,.20);
      background: rgba(255,255,255,.45);
      white-space: nowrap;
    }

    .main{
      background: var(--panel);
      border: 1px solid var(--border);
      border-radius: var(--radius);
      box-shadow: var(--shadow);
      padding: 16px;
      backdrop-filter: blur(8px);
    }

    header{
      display:flex;
      align-items:flex-start;
      justify-content: space-between;
      gap: 12px;
      margin-bottom: 12px;
      flex-wrap: wrap;
    }
    h1{
      margin:0;
      font-size: 18px;
      letter-spacing: .2px;
    }
    .sub{
      margin:4px 0 0;
      font-size: 13px;
      color: var(--muted);
    }

    .controls{
      display:grid;
      gap: 10px;
      margin-top: 6px
