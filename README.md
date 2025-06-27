<!-- 略：<headにFirebaseとQRCodeライブラリ読み込み -->
<body>
  <h1 id="title">あみだくじ</h1>
  <canvas id="canvas" width="320" height="500"></canvas>
  <div id="status"></div>
  <div id="qrcode"></div>

<script>
  // Firebase初期化省略

  const code = new URLSearchParams(location.search).get('code');
  const docRef = firestore.collection('amida').doc(code);

  docRef.onSnapshot(async snap => {
    const data = snap.data();
    if (!data) return;

    document.getElementById('title').textContent = data.title;
    const { names, items, lines } = data;

    drawAmida(lines);
    renderResults(data.results);
    genQR(location.href);
  });

  document.getElementById('canvas').addEventListener('click', async evt => {
    const col = pickColumn(evt.offsetX);
    const pathPos = simulate(lines, col, names.length);
    await docRef.update({ [`results.${col}`]: pathPos });
  });
</script>
