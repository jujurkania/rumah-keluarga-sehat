<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no" />
<title>Daftar Kesehatan Online</title>
<style>
  /* Reset and base */
  * {
    box-sizing: border-box;
  }
  body {
    margin: 0;
    padding: 0;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #89f7fe 0%, #66a6ff 100%);
    color: #333;
    height: 100vh;
    max-width: 350px;
    margin: auto;
    overflow: hidden;
    display: flex;
    flex-direction: column;
  }
  .container {
    background: white;
    margin: 10px 10px 20px 10px;
    border-radius: 15px;
    padding: 20px;
    flex-grow: 1;
    box-shadow: 0 8px 20px rgba(0,0,0,0.15);
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
  h1, h2 {
    font-weight: 700;
    color: #1e3c72;
  }
  button {
    background: #1e3c72;
    color: white;
    border: none;
    padding: 12px 18px;
    border-radius: 10px;
    font-size: 1.1rem;
    cursor: pointer;
    margin-top: 20px;
    box-shadow: 0 4px 12px rgba(30,60,114,0.7);
    transition: background-color 0.3s ease;
  }
  button:hover, button:focus {
    background: #14406c;
    outline: none;
  }
  label {
    margin-top: 12px;
    font-weight: 600;
    display: block;
    color: #2c3e50;
  }
  input[type="text"], input[type="date"], input[type="number"], textarea, select {
    width: 100%;
    padding: 8px 12px;
    margin-top: 6px;
    border: 2px solid #ddd;
    border-radius: 8px;
    font-size: 1rem;
    transition: border 0.25s ease-in-out;
    resize: vertical;
  }
  input[type="text"]:focus, input[type="date"]:focus, input[type="number"]:focus, textarea:focus, select:focus {
    border-color: #1e3c72;
    outline: none;
  }
  textarea {
    min-height: 60px;
  }
  .hidden {
    display: none;
  }
  /* Responsive and fit for 600px height */
  @media (max-height: 600px) {
    body {
      height: 100vh;
    }
    .container {
      margin: 10px 10px 10px 10px;
      padding: 15px;
    }
    button {
      font-size: 1rem;
      padding: 10px 14px;
    }
  }
  .queue-number {
    font-size: 3rem;
    font-weight: 800;
    color: #1e3c72;
    text-align: center;
    margin: 20px 0;
  }
  .footer-note {
    font-size: 0.9rem;
    color: #666;
    text-align: center;
    margin-top: auto;
  }
</style>
</head>
<body>
  <div class="container" id="page1">
    <h1>Selamat Datang Di Rumah Keluarga Sehat</h1>
    <button id="toPage2Btn">Mulai Pendaftaran</button>
  </div>

  <div class="container hidden" id="page2">
    <h2>Form Pendaftaran Kesehatan</h2>
    <form id="formPendaftaran">
      <label for="faskes1">Pendaftaran Faskes 1:</label>
      <select id="faskes1" name="faskes1" required>
        <option value="" disabled selected>Pilih Faskes 1</option>
        <option value="Puskesmas">Puskesmas</option>
        <option value="Klinik Pratama">Klinik Pratama</option>
        <option value="Dokter Pribadi">Dokter Pribadi</option>
      </select>

      <label for="rujukanRS">Pendaftaran Rujukan Rumah Sakit:</label>
      <select id="rujukanRS" name="rujukanRS" required>
        <option value="" disabled selected>Pilih Rumah Sakit</option>
        <option value="RSUD">RSUD</option>
        <option value="RS Swasta">RS Swasta</option>
        <option value="RS Khusus">RS Khusus</option>
        <option value="Tidak Ada">Tidak Ada</option>
      </select>

      <label for="riwayatPenyakit">Riwayat Penyakit:</label>
      <textarea id="riwayatPenyakit" name="riwayatPenyakit" placeholder="Tuliskan riwayat penyakit (jika ada)"></textarea>

      <label for="gejala">Gejala:</label>
      <textarea id="gejala" name="gejala" placeholder="Tuliskan gejala yang dialami" required></textarea>

      <label for="umur">Umur (tahun):</label>
      <input type="number" id="umur" name="umur" min="0" max="150" required />

      <label for="tglLahir">Tanggal Lahir:</label>
      <input type="date" id="tglLahir" name="tglLahir" required max="" />

      <label for="domisili">Domisili:</label>
      <input type="text" id="domisili" name="domisili" placeholder="Kota/Kabupaten" required />

      <label for="nama">Nama Lengkap:</label>
      <input type="text" id="nama" name="nama" placeholder="Nama lengkap Anda" required />

      <label for="waktuPendaftaran">Waktu Pendaftaran:</label>
      <input type="datetime-local" id="waktuPendaftaran" name="waktuPendaftaran" required />

      <button type="submit">Daftar</button>
    </form>
  </div>

  <div class="container hidden" id="page3">
    <h2>Nomer Antrian Pendaftaran</h2>
    <div class="queue-number" id="queueNumber"></div>
    <div id="summary"></div>
    <button id="btnBackToStart">Kembali ke Awal</button>
  </div>

<script>
  // Pages references
  const page1 = document.getElementById('page1');
  const page2 = document.getElementById('page2');
  const page3 = document.getElementById('page3');

  const toPage2Btn = document.getElementById('toPage2Btn');
  const form = document.getElementById('formPendaftaran');
  const queueNumberDiv = document.getElementById('queueNumber');
  const summaryDiv = document.getElementById('summary');
  const btnBackToStart = document.getElementById('btnBackToStart');

  // Set max date for birth date as today
  const tglLahir = document.getElementById('tglLahir');
  const todayDate = new Date().toISOString().split('T')[0];
  tglLahir.setAttribute('max', todayDate);

  toPage2Btn.addEventListener('click', () => {
    page1.classList.add('hidden');
    page2.classList.remove('hidden');
  });

  btnBackToStart.addEventListener('click', () => {
    page3.classList.add('hidden');
    page1.classList.remove('hidden');
    form.reset();
    queueNumberDiv.textContent = '';
    summaryDiv.innerHTML = '';
  });

  // Function to generate a queue number - simulate by getting from localStorage or start at 1
  function getNextQueueNumber() {
    let lastNumber = localStorage.getItem('lastQueueNumber');
    let nextNumber = 1;
    if (lastNumber) {
      nextNumber = parseInt(lastNumber) + 1;
    }
    localStorage.setItem('lastQueueNumber', nextNumber);
    return nextNumber;
  }

  form.addEventListener('submit', (e) => {
    e.preventDefault();

    // Get form data
    const data = new FormData(form);
    const pendaftaranData = {};
    for (const [key, value] of data.entries()) {
      pendaftaranData[key] = value.trim();
    }

    // Generate queue number
    const queueNum = getNextQueueNumber();

    // Display page 3
    page2.classList.add('hidden');
    page3.classList.remove('hidden');

    queueNumberDiv.textContent = queueNum;

    // Build a summary of entered data except queue number
    let summaryHtml = '<h3>Detail Pendaftaran:</h3><ul>';
    summaryHtml += `<li><strong>Nama:</strong> ${pendaftaranData.nama}</li>`;
    summaryHtml += `<li><strong>Umur:</strong> ${pendaftaranData.umur} tahun</li>`;
    summaryHtml += `<li><strong>Tanggal Lahir:</strong> ${pendaftaranData.tglLahir}</li>`;
    summaryHtml += `<li><strong>Domisili:</strong> ${pendaftaranData.domisili}</li>`;
    summaryHtml += `<li><strong>Pendaftaran Faskes 1:</strong> ${pendaftaranData.faskes1}</li>`;
    summaryHtml += `<li><strong>Pendaftaran Rujukan RS:</strong> ${pendaftaranData.rujukanRS}</li>`;
    summaryHtml += `<li><strong>Riwayat Penyakit:</strong> ${pendaftaranData.riwayatPenyakit || '-'}</li>`;
    summaryHtml += `<li><strong>Gejala:</strong> ${pendaftaranData.gejala}</li>`;
    summaryHtml += `<li><strong>Waktu Pendaftaran:</strong> ${pendaftaranData.waktuPendaftaran.replace('T', ' ')}</li>`;
    summaryHtml += '</ul>';

    summaryDiv.innerHTML = summaryHtml;
  });
</script>

</body>
</html>
</content>
</create_file>
