<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <title>Интерактивная Канбан-Доска</title>
  <style>
    body { font-family: sans-serif; background: #f0f0f0; margin: 0; padding: 20px; }
    .board {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 10px;
    }
    .cell {
      background: #fff;
      border: 1px solid #ccc;
      padding: 10px;
      min-height: 120px;
      position: relative;
    }
    .cell h4 {
      margin: 0 0 10px 0;
      font-size: 14px;
    }
    .card {
      background: #ffeeba;
      border-left: 5px solid red;
      padding: 8px;
      margin-bottom: 6px;
      cursor: move;
    }
    .card.green { border-left-color: green; background: #d4edda; }
    .card.red { border-left-color: red; background: #f8d7da; }
    .controls {
      margin-top: 20px;
    }
    input, select, button {
      margin-right: 5px;
      padding: 5px;
    }
    label {
      font-size: 12px;
    }
    .card .edit-button, .card .delete-button {
      font-size: 12px;
      background: transparent;
      border: none;
      cursor: pointer;
    }
    .card .delete-button {
      color: red;
    }
  </style>
</head>
<body>

<h2>Интерактивная Канбан-Доска Броней</h2>
<div class="controls">
  <input id="name" placeholder="Имя">
  <input id="phone" placeholder="Телефон">
  <select id="table">
    <option disabled selected>Стол</option>
    <option value="1">1</option>
    <option value="2">2</option>
    <option value="3">3</option>
    <option value="4">4</option>
    <option value="5">5</option>
    <option value="6">6</option>
    <option value="7">7</option>
    <option value="8">8</option>
  </select>
  <input id="time" type="time" placeholder="Время брони">
  <input id="guests" type="number" placeholder="Количество человек">
  <label><input id="hookah" type="checkbox"> Кальян</label>
  <label><input id="vr" type="checkbox"> VR</label>
  <button onclick="addCard()">Добавить бронь</button>
</div>

<div class="board" id="board">
  <div class="cell" ondrop="drop(event)" ondragover="allowDrop(event)"><h4>Стол 1</h4></div>
  <div class="cell" ondrop="drop(event)" ondragover="allowDrop(event)"><h4>Стол 2</h4></div>
  <div class="cell" ondrop="drop(event)" ondragover="allowDrop(event)"><h4>Стол 3</h4></div>
  <div class="cell" ondrop="drop(event)" ondragover="allowDrop(event)"><h4>Стол 4</h4></div>
  <div class="cell" ondrop="drop(event)" ondragover="allowDrop(event)"><h4>Стол 5</h4></div>
  <div class="cell" ondrop="drop(event)" ondragover="allowDrop(event)"><h4>Стол 6</h4></div>
  <div class="cell" ondrop="drop(event)" ondragover="allowDrop(event)"><h4>Стол 7</h4></div>
  <div class="cell" ondrop="drop(event)" ondragover="allowDrop(event)"><h4>Стол 8</h4></div>
</div>

<script>
let cardId = 0;

function allowDrop(ev) {
  ev.preventDefault();
}

function drag(ev) {
  ev.dataTransfer.setData("text", ev.target.id);
}

function drop(ev) {
  ev.preventDefault();
  var data = ev.dataTransfer.getData("text");
  ev.target.closest('.cell').appendChild(document.getElementById(data));
}

function addCard() {
  const name = document.getElementById('name').value;
  const phone = document.getElementById('phone').value;
  const table = document.getElementById('table').value;
  const time = document.getElementById('time').value;
  const guests = document.getElementById('guests').value;
  const hookah = document.getElementById('hookah').checked ? 'Кальян' : '';
  const vr = document.getElementById('vr').checked ? 'VR' : '';
  
  if (!name || !phone || !table || !time || !guests) return;

  // Запись данных в Google Таблицы через HTTP-запрос
  const url = 'ВАШ URL СКРИПТА GOOGLE APPS'; // Сюда вставьте URL вашего развернутого веб-приложения
  const params = {
    method: 'get',
    payload: {
      name: name,
      phone: phone,
      time: time,
      table: table,
      guests: guests,
      hookah: hookah,
      vr: vr,
      status: 'Ожидается'
    }
  };

  fetch(url, params)
    .then(response => response.text())
    .then(data => {
      console.log(data);
      // Можно обновить UI, например, показать уведомление, что бронь добавлена
    })
    .catch(error => console.error('Error:', error));

  const card = document.createElement('div');
  card.className = 'card red';
  card.id = 'card' + cardId++;
  card.draggable = true;
  card.ondragstart = drag;

  card.innerHTML = `
    <strong>${name}</strong><br>
    Телефон: ${phone}<br>
    Время: ${time}<br>
    Количество: ${guests}<br>
    Стол: ${table}<br>
    ${hookah ? `<span>🍹 ${hookah}</span><br>` : ''}
    ${vr ? `<span>🎮 ${vr}</span><br>` : ''}
    <button class="edit-button" onclick="editCard(this.parentNode)">Редактировать</button>
    <button class="delete-button" onclick="deleteCard(this.parentNode)">Удалить</button>
    <button onclick="toggleStatus(this.parentNode)">Статус</button>
  `;

  document.querySelectorAll('.cell')[table - 1].appendChild(card);
}

function toggleStatus(card) {
  if (card.classList.contains('red')) {
    card.classList.remove('red');
    card.classList.add('green');
  } else {
    card.classList.remove('green');
    card.classList.add('red');
  }
}

function deleteCard(card) {
  card.remove();
}

function editCard(card) {
  const name = prompt("Имя", card.querySelector("strong").textContent);
  const phone = prompt("Телефон", card.querySelector("br").nextSibling.textContent.split(":")[1]);
  const time = prompt("Время", card.querySelector("br").nextSibling.nextSibling.textContent.split(":")[1]);
  const guests = prompt("Количество человек", card.querySelector("br").nextSibling.nextSibling.nextSibling.textContent.split(":")[1]);
  
  card.querySelector("strong").textContent = name;
  card.querySelector("br").nextSibling.textContent = "Телефон: " + phone;
  card.querySelector("br").nextSibling.nextSibling.textContent = "Время: " + time;
  card.querySelector("br").nextSibling.nextSibling.nextSibling.textContent = "Количество: " + guests;
}
</script>

</body>
</html>
