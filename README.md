<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Prenotazione Scrivania & Posto Moto</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://unpkg.com/lucide@latest"></script>
</head>
<body class="bg-slate-900 text-slate-100 min-h-screen font-sans pb-12">
  <div class="max-w-3xl mx-auto p-4 sm:p-6 space-y-6">

    <!-- Header -->
    <header class="flex justify-between items-center border-b border-slate-800 pb-4">
      <div>
        <h1 class="text-2xl font-bold text-white flex items-center gap-2">
          <i data-lucide="building-2" class="text-indigo-400"></i> Dashboard Ufficio (Locale)
        </h1>
        <p class="text-xs text-slate-400">Archiviazione locale nel browser</p>
      </div>
      <div class="flex gap-2">
        <button onclick="openDatePickerModal()" class="bg-slate-800 hover:bg-slate-700 text-slate-200 border border-slate-700 text-sm font-medium px-3 py-2 rounded-lg flex items-center gap-1.5 transition">
          <i data-lucide="calendar" class="w-4 h-4 text-indigo-400"></i> Calendario
        </button>
        <button onclick="openModal()" class="bg-indigo-600 hover:bg-indigo-500 text-white text-sm font-medium px-4 py-2 rounded-lg flex items-center gap-1.5 shadow-lg shadow-indigo-600/20 transition">
          <i data-lucide="plus" class="w-4 h-4"></i> Nuova Prenotazione
        </button>
      </div>
    </header>

    <!-- Sezione 3 Giorni: Oggi, Domani, Dopodomani -->
    <section class="space-y-3">
      <h2 class="text-xs font-semibold uppercase tracking-wider text-slate-400 flex items-center gap-2">
        <i data-lucide="clock" class="w-4 h-4 text-indigo-400"></i> Oggi, Domani e Dopodomani
      </h2>
      <div class="grid grid-cols-1 md:grid-cols-3 gap-4" id="threeDaysCards"></div>
    </section>

    <!-- Successivi Giorni Lavorativi -->
    <section class="space-y-3 pt-4">
      <h3 class="text-xs font-semibold text-slate-400 uppercase tracking-wider flex items-center gap-2">
        <i data-lucide="calendar-days" class="w-4 h-4 text-slate-400"></i> Successivi Giorni Lavorativi
      </h3>
      <div id="upcomingList" class="space-y-2.5"></div>
    </section>

  </div>

  <!-- Modal Calendario -->
  <div id="datePickerModal" class="fixed inset-0 bg-slate-950/80 backdrop-blur-sm hidden flex items-center justify-center p-4 z-50">
    <div class="bg-slate-800 border border-slate-700 rounded-2xl p-6 w-full max-w-sm shadow-2xl space-y-4">
      <div class="flex justify-between items-center border-b border-slate-700 pb-3">
        <h3 class="text-lg font-semibold text-white flex items-center gap-2">
          <i data-lucide="calendar" class="w-5 h-5 text-indigo-400"></i> Seleziona Data
        </h3>
        <button onclick="closeDatePickerModal()" class="text-slate-400 hover:text-white"><i data-lucide="x"></i></button>
      </div>

      <div class="flex justify-between items-center px-1">
        <button onclick="changeMonth(-1)" class="p-1 rounded-lg text-slate-400 hover:text-white hover:bg-slate-700"><i data-lucide="chevron-left"></i></button>
        <span id="calendarMonthYear" class="font-semibold text-slate-200 capitalize">--</span>
        <button onclick="changeMonth(1)" class="p-1 rounded-lg text-slate-400 hover:text-white hover:bg-slate-700"><i data-lucide="chevron-right"></i></button>
      </div>

      <div class="grid grid-cols-7 text-center text-xs font-medium text-slate-400">
        <div>Lun</div><div>Mar</div><div>Mer</div><div>Gio</div><div>Ven</div><div>Sab</div><div>Dom</div>
      </div>

      <div id="calendarGrid" class="grid grid-cols-7 gap-1 text-sm"></div>
    </div>
  </div>

  <!-- Modal Gestione / Inserimento -->
  <div id="modal" class="fixed inset-0 bg-slate-950/80 backdrop-blur-sm hidden flex items-center justify-center p-4 z-50">
    <div class="bg-slate-800 border border-slate-700 rounded-2xl p-6 w-full max-w-md shadow-2xl space-y-4">
      <div class="flex justify-between items-center border-b border-slate-700 pb-3">
        <h3 class="text-lg font-semibold text-white">Gestisci Prenotazione</h3>
        <button onclick="closeModal()" class="text-slate-400 hover:text-white"><i data-lucide="x"></i></button>
      </div>

      <form id="reservationForm" onsubmit="saveReservation(event)" class="space-y-4">
        <div>
          <label class="block text-xs font-medium text-slate-300 mb-1">Data</label>
          <input type="date" id="formDate" required class="w-full bg-slate-900 border border-slate-700 rounded-lg p-2.5 text-sm text-white focus:outline-none focus:border-indigo-500">
        </div>

        <div>
          <label class="block text-xs font-medium text-slate-300 mb-1">Modalità</label>
          <select id="formType" onchange="toggleFields()" class="w-full bg-slate-900 border border-slate-700 rounded-lg p-2.5 text-sm text-white focus:outline-none focus:border-indigo-500">
            <option value="office">In Ufficio</option>
            <option value="remote">Smart Working</option>
          </select>
        </div>

        <div id="officeFields" class="space-y-4">
          <div>
            <label class="block text-xs font-medium text-slate-300 mb-1">Scrivania</label>
            <input type="text" id="formDesk" placeholder="es. Scrivania 12 - Piano 2" class="w-full bg-slate-900 border border-slate-700 rounded-lg p-2.5 text-sm text-white focus:outline-none focus:border-indigo-500">
          </div>
          <div>
            <label class="block text-xs font-medium text-slate-300 mb-1">Posto Moto <span class="text-slate-500">(Opzionale)</span></label>
            <input type="text" id="formBike" placeholder="es. Moto M-04" class="w-full bg-slate-900 border border-slate-700 rounded-lg p-2.5 text-sm text-white focus:outline-none focus:border-indigo-500">
          </div>
        </div>

        <div class="flex gap-2 pt-2">
          <button type="button" onclick="deleteCurrentReservation()" id="btnDelete" class="hidden flex-1 bg-red-500/20 text-red-300 border border-red-500/30 hover:bg-red-500/30 py-2.5 rounded-lg text-sm font-medium transition">Rimuovi</button>
          <button type="submit" class="flex-1 bg-indigo-600 hover:bg-indigo-500 text-white py-2.5 rounded-lg text-sm font-medium transition">Salva</button>
        </div>
      </form>
    </div>
  </div>

  <script>
    let reservations = {};
    let calendarCurrentDate = new Date();

    function formatDateKey(date) {
      const year = date.getFullYear();
      const month = String(date.getMonth() + 1).padStart(2, '0');
      const day = String(date.getDate()).padStart(2, '0');
      return `${year}-${month}-${day}`;
    }

    function loadReservations() {
      const saved = localStorage.getItem('desk_reservations');
      if (saved) {
        try {
          reservations = JSON.parse(saved);
        } catch (e) {
          reservations = {};
        }
      } else {
        // Dati iniziali di esempio se vuoto
        reservations = {
          "2026-10-21": { type: "office", desk: "Scrivania 12", bike: "Moto M-04" },
          "2026-10-22": { type: "remote", desk: "", bike: "" },
          "2026-10-23": { type: "office", desk: "Scrivania 15", bike: "" }
        };
        saveToLocalStorage();
      }
      render();
    }

    function saveToLocalStorage() {
      localStorage.setItem('desk_reservations', JSON.stringify(reservations));
    }

    function render() {
      const today = new Date();
      today.setHours(0, 0, 0, 0);
      
      const threeDaysCards = document.getElementById('threeDaysCards');
      threeDaysCards.innerHTML = '';

      const labels = ['Oggi', 'Domani', 'Dopodomani'];

      for (let i = 0; i < 3; i++) {
        const targetDate = new Date(today);
        targetDate.setDate(today.getDate() + i);
        const dateKey = formatDateKey(targetDate);
        const data = reservations[dateKey];

        const dayName = targetDate.toLocaleDateString('it-IT', { weekday: 'short' });
        const dayNum = targetDate.toLocaleDateString('it-IT', { day: 'numeric', month: 'short' });

        let statusBadge = '';
        let contentHtml = '';

        if (!data) {
          statusBadge = `<span class="bg-slate-700/50 text-slate-400 text-xs px-2 py-0.5 rounded-full border border-slate-600/40">Non prenotato</span>`;
          contentHtml = `<p class="text-xs text-slate-500 italic mt-2">Nessuna indicazione</p>`;
        } else if (data.type === 'remote') {
          statusBadge = `<span class="bg-emerald-500/10 text-emerald-400 text-xs px-2 py-0.5 rounded-full border border-emerald-500/20 font-medium">Smart Working</span>`;
          contentHtml = `<div class="mt-2 text-xs text-slate-300 flex items-center gap-1.5"><i data-lucide="laptop" class="w-4 h-4 text-emerald-400"></i> Da Remoto</div>`;
        } else {
          statusBadge = `<span class="bg-indigo-500/10 text-indigo-400 text-xs px-2 py-0.5 rounded-full border border-indigo-500/20 font-medium">In Ufficio</span>`;
          contentHtml = `
            <div class="mt-2 space-y-1">
              <div class="text-xs text-slate-200 flex items-center gap-1.5"><i data-lucide="monitor" class="w-3.5 h-3.5 text-indigo-400"></i> <span class="font-medium">${data.desk || 'Non specif.'}</span></div>
              ${data.bike ? `<div class="text-xs text-slate-400 flex items-center gap-1.5"><i data-lucide="bike" class="w-3.5 h-3.5 text-slate-400"></i> ${data.bike}</div>` : ''}
            </div>
          `;
        }

        threeDaysCards.innerHTML += `
          <div onclick="openModal('${dateKey}')" class="bg-slate-800/80 border ${i === 0 ? 'border-indigo-500/50 shadow-indigo-500/10' : 'border-slate-700/60'} hover:border-indigo-500/80 rounded-2xl p-4 shadow-lg cursor-pointer transition relative overflow-hidden">
            <div class="flex justify-between items-start">
              <div>
                <span class="text-[10px] font-bold uppercase tracking-wider text-indigo-400">${labels[i]}</span>
                <h3 class="text-sm font-semibold text-slate-200 capitalize">${dayName} <span class="text-xs text-slate-400 font-normal">${dayNum}</span></h3>
              </div>
              ${statusBadge}
            </div>
            ${contentHtml}
          </div>
        `;
      }

      const upcomingList = document.getElementById('upcomingList');
      upcomingList.innerHTML = '';

      let count = 0;
      let checkDate = new Date(today);
      checkDate.setDate(checkDate.getDate() + 3);

      while (count < 5) {
        const dayOfWeek = checkDate.getDay();
        if (dayOfWeek !== 0 && dayOfWeek !== 6) {
          count++;
          const dateKey = formatDateKey(checkDate);
          const data = reservations[dateKey];

          const dateFormatted = checkDate.toLocaleDateString('it-IT', { weekday: 'short', day: 'numeric', month: 'short' });

          let infoHtml = '';
          if (!data) {
            infoHtml = `<span class="text-xs text-slate-500 italic">Non impostato</span>`;
          } else if (data.type === 'remote') {
            infoHtml = `<span class="text-xs bg-emerald-500/10 text-emerald-400 border border-emerald-500/20 px-2 py-0.5 rounded">Smart Working</span>`;
          } else {
            infoHtml = `<div class="text-xs text-slate-200 font-medium">
              <span class="text-indigo-400">${data.desk || 'Scrivania -'}</span>
              ${data.bike ? `<span class="text-slate-400 ml-1.5">| Moto: ${data.bike}</span>` : ''}
            </div>`;
          }

          upcomingList.innerHTML += `
            <div onclick="openModal('${dateKey}')" class="bg-slate-800/40 hover:bg-slate-800 border border-slate-700/40 rounded-xl p-3 flex justify-between items-center cursor-pointer transition">
              <span class="text-sm font-medium text-slate-300 capitalize">${dateFormatted}</span>
              <div>${infoHtml}</div>
            </div>
          `;
        }
        checkDate.setDate(checkDate.getDate() + 1);
      }

      lucide.createIcons();
    }

    function toggleFields() {
      const type = document.getElementById('formType').value;
      const officeFields = document.getElementById('officeFields');
      if (type === 'remote') {
        officeFields.classList.add('hidden');
      } else {
        officeFields.classList.remove('hidden');
      }
    }

    function openModal(dateKey = null) {
      closeDatePickerModal();
      const formDate = document.getElementById('formDate');
      const key = dateKey || formatDateKey(new Date());
      formDate.value = key;

      const data = reservations[key];
      const btnDelete = document.getElementById('btnDelete');

      if (data) {
        document.getElementById('formType').value = data.type;
        document.getElementById('formDesk').value = data.desk || '';
        document.getElementById('formBike').value = data.bike || '';
        btnDelete.classList.remove('hidden');
      } else {
        document.getElementById('formType').value = 'office';
        document.getElementById('formDesk').value = '';
        document.getElementById('formBike').value = '';
        btnDelete.classList.add('hidden');
      }

      toggleFields();
      document.getElementById('modal').classList.remove('hidden');
    }

    function closeModal() {
      document.getElementById('modal').classList.add('hidden');
    }

    function saveReservation(e) {
      e.preventDefault();
      const dateKey = document.getElementById('formDate').value;
      
      reservations[dateKey] = {
        type: document.getElementById('formType').value,
        desk: document.getElementById('formDesk').value,
        bike: document.getElementById('formBike').value
      };

      saveToLocalStorage();
      closeModal();
      render();
    }

    function deleteCurrentReservation() {
      const dateKey = document.getElementById('formDate').value;
      if (reservations[dateKey]) {
        delete reservations[dateKey];
        saveToLocalStorage();
      }
      closeModal();
      render();
    }

    function openDatePickerModal() {
      renderCalendar();
      document.getElementById('datePickerModal').classList.remove('hidden');
    }

    function closeDatePickerModal() {
      document.getElementById('datePickerModal').classList.add('hidden');
    }

    function changeMonth(delta) {
      calendarCurrentDate.setMonth(calendarCurrentDate.getMonth() + delta);
      renderCalendar();
    }

    function renderCalendar() {
      const year = calendarCurrentDate.getFullYear();
      const month = calendarCurrentDate.getMonth();

      document.getElementById('calendarMonthYear').innerText = new Date(year, month).toLocaleDateString('it-IT', { month: 'long', year: 'numeric' });

      const firstDayOfMonth = new Date(year, month, 1);
      const lastDayOfMonth = new Date(year, month + 1, 0);

      let startingDay = firstDayOfMonth.getDay() - 1;
      if (startingDay === -1) startingDay = 6;

      const grid = document.getElementById('calendarGrid');
      grid.innerHTML = '';

      for (let i = 0; i < startingDay; i++) {
        grid.innerHTML += `<div></div>`;
      }

      const todayKey = formatDateKey(new Date());

      for (let day = 1; day <= lastDayOfMonth.getDate(); day++) {
        const currentDate = new Date(year, month, day);
        const dateKey = formatDateKey(currentDate);
        const hasReservation = !!reservations[dateKey];
        const isToday = dateKey === todayKey;

        let styleClass = "hover:bg-slate-700 text-slate-200 border-slate-700/50";
        if (isToday) {
          styleClass = "bg-indigo-600 font-bold text-white border-indigo-500";
        } else if (hasReservation) {
          styleClass = "bg-slate-700 text-indigo-300 font-semibold border-indigo-500/40";
        }

        grid.innerHTML += `
          <button onclick="openModal('${dateKey}')" class="h-9 w-full rounded-lg border flex items-center justify-center relative ${styleClass} transition">
            ${day}
            ${hasReservation && !isToday ? '<span class="absolute bottom-1 w-1 h-1 bg-indigo-400 rounded-full"></span>' : ''}
          </button>
        `;
      }
    }

    loadReservations();
  </script>
</body>
</html>
