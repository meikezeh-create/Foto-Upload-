<!doctype html>
<html lang="de">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Foto-Tagebuch</title>

  <!-- Supabase JS (CDN) -->
  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>

  <style>
    :root{
      --bg:#f6f7fb;
      --card:#ffffff;
      --text:#111827;
      --muted:#6b7280;
      --line:#e5e7eb;
      --accent:#2563eb;
      --accent2:#16a34a;
      --danger:#dc2626;
      --shadow:0 8px 24px rgba(0,0,0,.08);
      --radius:16px;
    }
    *{box-sizing:border-box}
    body{
      margin:0;
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial;
      color:var(--text);
      background:linear-gradient(180deg, #eef2ff 0%, var(--bg) 25%, var(--bg) 100%);
    }

    header{
      position:sticky; top:0; z-index:5;
      backdrop-filter: blur(10px);
      background:rgba(246,247,251,.75);
      border-bottom:1px solid var(--line);
    }
    .header-inner{
      max-width:1100px;
      margin:0 auto;
      padding:14px 16px;
      display:flex;
      align-items:center;
      justify-content:space-between;
      gap:12px;
    }
    .brand{
      display:flex; align-items:center; gap:10px;
      font-weight:800;
    }
    .dot{
      width:12px;height:12px;border-radius:50%;
      background:var(--accent);
      box-shadow:0 0 0 4px rgba(37,99,235,.12);
    }
    .sub{
      font-size:12px;color:var(--muted);font-weight:600;
    }

    main{
      max-width:1100px;
      margin:0 auto;
      padding:18px 16px 28px;
      display:grid;
      grid-template-columns: 320px 1fr;
      gap:16px;
    }

    .card{
      background:var(--card);
      border:1px solid var(--line);
      border-radius:var(--radius);
      box-shadow:var(--shadow);
    }

    .upload{
      padding:14px;
    }
    .upload h2{
      margin:0 0 10px;
      font-size:16px;
    }
    label{
      display:block;
      font-size:12px;
      color:var(--muted);
      font-weight:700;
      margin:10px 0 6px;
    }
    input[type="date"], input[type="text"], input[type="file"]{
      width:100%;
      border:1px solid var(--line);
      border-radius:12px;
      padding:10px 12px;
      font-size:14px;
      background:#fff;
      outline:none;
    }
    input[type="file"]{
      padding:10px;
    }
    .row{
      display:flex;
      gap:10px;
      align-items:center;
      margin-top:12px;
    }
    button{
      border:0;
      border-radius:12px;
      padding:10px 12px;
      font-weight:800;
      cursor:pointer;
      font-size:14px;
    }
    .btn-primary{
      background:var(--accent);
      color:white;
      flex:1;
    }
    .btn-secondary{
      background:#eef2ff;
      color:#1e40af;
    }
    .btn-primary:disabled{
      opacity:.55; cursor:not-allowed;
    }

    .status{
      margin-top:10px;
      padding:10px 12px;
      border-radius:12px;
      font-size:13px;
      background:#f9fafb;
      border:1px dashed var(--line);
      color:var(--muted);
      white-space:pre-wrap;
    }
    .status.ok{border-style:solid;background:#ecfdf5;color:#065f46;border-color:#bbf7d0}
    .status.err{border-style:solid;background:#fef2f2;color:#7f1d1d;border-color:#fecaca}

    .sidebar{
      display:flex;
      flex-direction:column;
      gap:16px;
    }

    .list{
      padding:12px;
    }
    .list h3{
      margin:0 0 10px;
      font-size:14px;
      color:var(--muted);
      letter-spacing:.2px;
    }
    .items{
      display:flex;
      flex-direction:column;
      gap:8px;
      max-height:55vh;
      overflow:auto;
      padding-right:4px;
    }
    .item{
      border:1px solid var(--line);
      background:#fff;
      border-radius:12px;
      padding:10px 12px;
      display:flex;
      align-items:center;
      justify-content:space-between;
      gap:10px;
      cursor:pointer;
      transition:.12s transform, .12s box-shadow, .12s border-color;
    }
    .item:hover{
      transform:translateY(-1px);
      box-shadow:0 10px 18px rgba(0,0,0,.06);
      border-color:#c7d2fe;
    }
    .item.active{
      border-color:rgba(37,99,235,.55);
      box-shadow:0 10px 18px rgba(37,99,235,.12);
    }
    .date{
      font-weight:900;
      font-size:14px;
    }
    .meta{
      font-size:12px;
      color:var(--muted);
      font-weight:700;
    }

    .viewer{
      padding:14px;
      min-height: 540px;
      display:flex;
      flex-direction:column;
      gap:12px;
    }
    .viewer h2{
      margin:0;
      font-size:16px;
    }
    .frame{
      flex:1;
      border:1px dashed var(--line);
      border-radius:var(--radius);
      background:linear-gradient(180deg,#ffffff 0%, #fafafa 100%);
      display:flex;
      align-items:center;
      justify-content:center;
      overflow:hidden;
      position:relative;
    }
    .frame img{
      max-width:100%;
      max-height:100%;
      object-fit:contain;
      display:none;
    }
    .placeholder{
      text-align:center;
      padding:20px;
      color:var(--muted);
      font-weight:700;
      line-height:1.35;
    }
    .small{
      font-size:12px;
      color:var(--muted);
      font-weight:700;
    }

    @media (max-width: 900px){
      main{grid-template-columns:1fr}
      .items{max-height:260px}
      .viewer{min-height:420px}
    }
  </style>
</head>

<body>
<header>
  <div class="header-inner">
    <div>
      <div class="brand"><span class="dot"></span> Foto-Tagebuch</div>
      <div class="sub">Datum speichern → links auswählen → Foto rechts ansehen</div>
    </div>
    <div class="sub" id="connState">Verbinden…</div>
  </div>
</header>

<main>
  <div class="sidebar">
    <section class="card upload">
      <h2>Foto hochladen</h2>

      <label for="entryDate">Datum</label>
      <input id="entryDate" type="date" />

      <label for="photoFile">Foto auswählen</label>
      <input id="photoFile" type="file" accept="image/*" />

      <div class="row">
        <button id="saveBtn" class="btn-primary">Speichern</button>
        <button id="refreshBtn" class="btn-secondary" title="Liste neu laden">↻</button>
      </div>

      <div id="status" class="status">Hinweis: Damit Fotos nach Reload bleiben, müssen sie in Supabase Storage gespeichert werden (nicht nur lokal im Browser).</div>
    </section>

    <section class="card list">
      <h3>Gespeicherte Daten</h3>
      <div class="items" id="items"></div>
      <div class="small" id="countInfo" style="margin-top:10px;"></div>
    </section>
  </div>

  <section class="card viewer">
    <h2 id="viewerTitle">Foto anzeigen</h2>
    <div class="frame">
      <div class="placeholder" id="placeholder">
        Wähle links ein Datum aus.<br/>
        Danach wird hier das gespeicherte Foto angezeigt.
      </div>
      <img id="img" alt="Gespeichertes Foto" />
    </div>
    <div class="small" id="viewerMeta"></div>
  </section>
</main>

<script>
  /***********************
   * 1) SUPABASE CONFIG  *
   ***********************/
  // Deine Daten (wie du sie geschickt hast)
  const SUPABASE_URL = "https://okvhllwikukexdggrfbg.supabase.co";
  const SUPABASE_ANON_KEY = "sb_publishable_dm8ukyTUhoUTHwoLTVe-OHQ_I_UCOAKl";

  // Bucket- und Tabellennamen
  const BUCKET = "photos";
  const TABLE  = "entries";

  // Spalten in deiner Tabelle (angepasst an das, was man bei dir gesehen hat)
  // Required: entry_date (text), file_path (text), slot (int4 optional)
  const COL_ENTRY_DATE = "entry_date";
  const COL_FILE_PATH  = "file_path";
  const COL_CREATED_AT = "created_at";
  const COL_SLOT       = "slot";

  const supabase = window.supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

  /***********************
   * 2) UI ELEMENTS      *
   ***********************/
  const elDate = document.getElementById("entryDate");
  const elFile = document.getElementById("photoFile");
  const elSave = document.getElementById("saveBtn");
  const elRefresh = document.getElementById("refreshBtn");
  const elStatus = document.getElementById("status");
  const elItems = document.getElementById("items");
  const elCount = document.getElementById("countInfo");
  const elImg = document.getElementById("img");
  const elPlaceholder = document.getElementById("placeholder");
  const elViewerTitle = document.getElementById("viewerTitle");
  const elViewerMeta = document.getElementById("viewerMeta");
  const elConn = document.getElementById("connState");

  function setStatus(msg, type="") {
    elStatus.className = "status" + (type ? " " + type : "");
    elStatus.textContent = msg;
  }

  function toISODate(d) {
    // YYYY-MM-DD
    const pad = (n) => String(n).padStart(2,"0");
    return `${d.getFullYear()}-${pad(d.getMonth()+1)}-${pad(d.getDate())}`;
  }

  // Standard: Heute
  elDate.value = toISODate(new Date());

  /***********************
   * 3) DATA LOADING      *
   ***********************/
  // Wir laden alles aus entries, gruppieren nach entry_date und zeigen links nur die Daten
  let grouped = []; // [{ entry_date, latest, count, items:[...] }]

  async function pingConnection() {
    // Einfacher "Ping": 1 Zeile aus Tabelle versuchen zu lesen.
    const { error } = await supabase.from(TABLE).select("id").limit(1);
    elConn.textContent = error ? "Nicht verbunden" : "Verbunden";
  }

  async function loadEntries() {
    setStatus("Lade Einträge…");
    elItems.innerHTML = "";
    elCount.textContent = "";

    // Wir holen alle Einträge (neuste zuerst)
    const { data, error } = await supabase
      .from(TABLE)
      .select("*")
      .order(COL_CREATED_AT, { ascending: false });

    if (error) {
      setStatus(
        "Fehler beim Laden aus der Tabelle 'entries'.\n\n" +
        "Sehr wahrscheinlich blockiert eine Policy (RLS).\n\n" +
        "Fehlermeldung:\n" + error.message,
        "err"
      );
      return;
    }

    // Gruppieren nach Datum
    const map = new Map();
    (data || []).forEach(row => {
      const d = row[COL_ENTRY_DATE] || "Unbekannt";
      if (!map.has(d)) map.set(d, []);
      map.get(d).push(row);
    });

    grouped = Array.from(map.entries())
      .map(([entry_date, items]) => ({
        entry_date,
        items,
        count: items.length,
        latest: items[0] // weil oben nach created_at desc sortiert
      }))
      // sortieren nach Datum (desc)
      .sort((a,b) => (a.entry_date < b.entry_date ? 1 : -1));

    if (grouped.length === 0) {
      setStatus("Noch keine Einträge. Lade ein Foto hoch und klicke auf Speichern.", "");
      elCount.textContent = "0 Einträge";
      return;
    }

    renderList();
    setStatus("Einträge geladen.", "ok");
    elCount.textContent = `${data.length} Einträge, ${grouped.length} Daten`;
  }

  function renderList(activeDate=null) {
    elItems.innerHTML = "";
    grouped.forEach(group => {
      const div = document.createElement("div");
      div.className = "item" + (activeDate === group.entry_date ? " active" : "");
      div.innerHTML = `
        <div>
          <div class="date">${group.entry_date}</div>
          <div class="meta">${group.count} Foto(s)</div>
        </div>
        <div class="meta">›</div>
      `;
      div.addEventListener("click", () => {
        // Markieren
        [...elItems.querySelectorAll(".item")].forEach(x => x.classList.remove("active"));
        div.classList.add("active");
        showGroup(group);
      });
      elItems.appendChild(div);
    });
  }

  /***********************
   * 4) IMAGE DISPLAY     *
   ***********************/
  async function getDisplayUrl(filePath) {
    // 1) Versuch: Public URL (Bucket ist public)
    const pub = supabase.storage.from(BUCKET).getPublicUrl(filePath);
    if (pub?.data?.publicUrl) return pub.data.publicUrl;

    // 2) Fallback: Signed URL (wenn Bucket privat ist)
    // Hinweis: Signed URLs laufen ab – du kannst sie aber bei jedem Laden neu erzeugen.
    const { data, error } = await supabase.storage.from(BUCKET).createSignedUrl(filePath, 60 * 60 * 24 * 7); // 7 Tage
    if (error) throw error;
    return data.signedUrl;
  }

  async function showGroup(group) {
    const row = group.latest;
    const filePath = row[COL_FILE_PATH];

    elViewerTitle.textContent = `Foto: ${group.entry_date}`;
    elViewerMeta.textContent = "Lade Foto…";
    elPlaceholder.style.display = "block";
    elImg.style.display = "none";

    try {
      const url = await getDisplayUrl(filePath);
      elImg.src = url;
      elImg.onload = () => {
        elPlaceholder.style.display = "none";
        elImg.style.display = "block";
      };
      elImg.onerror = () => {
        elPlaceholder.style.display = "block";
        elImg.style.display = "none";
        elViewerMeta.textContent = "Konnte das Bild nicht laden (URL/Policy/Bucket prüfen).";
      };

      const created = row[COL_CREATED_AT] ? new Date(row[COL_CREATED_AT]).toLocaleString() : "";
      elViewerMeta.textContent = `Pfad: ${filePath} • gespeichert: ${created}`;
    } catch (e) {
      elPlaceholder.style.display = "block";
      elImg.style.display = "none";
      elViewerMeta.textContent =
        "Fehler beim Erzeugen der Bild-URL.\n" +
        (e?.message || String(e));
    }
  }

  /***********************
   * 5) UPLOAD + SAVE     *
   ***********************/
  function sanitizeFilename(name) {
    // iOS Dateinamen/Leerzeichen entschärfen
    return name
      .toLowerCase()
      .replace(/\s+/g, "-")
      .replace(/[^a-z0-9.\-_]/g, "");
  }

  async function saveEntry() {
    const entryDate = elDate.value;
    const file = elFile.files && elFile.files[0];

    if (!entryDate) {
      setStatus("Bitte ein Datum auswählen.", "err");
      return;
    }
    if (!file) {
      setStatus("Bitte ein Foto auswählen.", "err");
      return;
    }

    elSave.disabled = true;
    setStatus("Speichere…");

    try {
      // 1) Upload in Storage
      const safeName = sanitizeFilename(file.name || "foto.jpg");
      const filePath = `${entryDate}/${Date.now()}-${safeName}`;

      const { error: upErr } = await supabase.storage
        .from(BUCKET)
        .upload(filePath, file, {
          cacheControl: "3600",
          upsert: false,
          contentType: file.type || "image/jpeg"
        });

      if (upErr) {
        setStatus(
          "Upload in Supabase Storage fehlgeschlagen.\n\n" +
          "Sehr wahrscheinlich blockiert eine Storage-Policy (insert).\n\n" +
          "Fehlermeldung:\n" + upErr.message,
          "err"
        );
        return;
      }

      // 2) Eintrag in Tabelle 'entries' speichern
      // Wichtig: file_path darf bei dir nicht NULL sein (das hattest du als Fehlermeldung).
      const payload = {
        [COL_ENTRY_DATE]: entryDate,
        [COL_FILE_PATH]: filePath,
        [COL_SLOT]: 1
      };

      const { error: dbErr } = await supabase
        .from(TABLE)
        .insert(payload);

      if (dbErr) {
        setStatus(
          "Eintrag in der Tabelle 'entries' konnte nicht gespeichert werden.\n\n" +
          "Sehr wahrscheinlich blockiert eine Tabelle-Policy (RLS insert).\n\n" +
          "Fehlermeldung:\n" + dbErr.message,
          "err"
        );
        return;
      }

      setStatus("Gespeichert. Liste wird aktualisiert…", "ok");
      elFile.value = "";
      await loadEntries();

      // automatisch das gespeicherte Datum anzeigen
      const group = grouped.find(g => g.entry_date === entryDate);
      if (group) {
        renderList(entryDate);
        showGroup(group);
      }
    } finally {
      elSave.disabled = false;
    }
  }

  /***********************
   * 6) EVENTS + INIT     *
   ***********************/
  elSave.addEventListener("click", saveEntry);
  elRefresh.addEventListener("click", loadEntries);

  (async function init(){
    await pingConnection();
    await loadEntries();
  })();

  /********************************************************************
   * WICHTIG (damit es "dauerhaft" ist)
   *
   * Wenn nach Reload nichts bleibt, liegt es fast immer an Policies:
   *
   * A) Tabelle entries: braucht SELECT + INSERT (für die Website)
   * B) Storage Bucket photos: braucht SELECT (read) + INSERT (upload)
   *
   * Wenn du KEIN Login verwendest, müssen diese Aktionen für "public"
   * erlaubt sein. Wenn du "authenticated only" eingestellt hast,
   * kann die Website ohne Login nichts speichern.
   ********************************************************************/
</script>
</body>
</html>
