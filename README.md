<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Catatan List dengan Login, Daftar Akun, Tanggal, dan Simpan</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f9;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }

        .navbar {
            position: fixed;
            top: 0;
            right: 0;
            background-color: #4CAF50;
            padding: 10px 20px;
            color: white;
            border-bottom-left-radius: 10px;
        }

        .account-menu {
            position: relative;
            display: inline-block;
        }

        .account-icon {
            background-color: transparent;
            border: none;
            cursor: pointer;
            color: white;
            font-size: 20px;
        }

        .dropdown-content {
            display: none;
            position: absolute;
            right: 0;
            background-color: #fff;
            min-width: 150px;
            box-shadow: 0px 8px 16px 0px rgba(0,0,0,0.2);
            border-radius: 8px;
            overflow: hidden;
        }

        .dropdown-content p, .dropdown-content button {
            color: #333;
            padding: 10px;
            text-align: left;
            text-decoration: none;
            display: block;
            border: none;
            background: none;
            cursor: pointer;
            width: 100%;
        }

        .dropdown-content button:hover {
            background-color: #f1f1f1;
        }

        .account-menu:hover .dropdown-content {
            display: block;
        }

        .container, .login-container, .register-container {
            background-color: #fff;
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
            width: 400px;
            max-height: 90vh;
            overflow-y: auto;
        }

        h1 {
            text-align: center;
            color: #333;
            font-size: 28px;
        }

        textarea, input[type="text"], input[type="password"] {
            width: calc(100% - 24px);
            padding: 15px;
            margin-bottom: 15px;
            border: 1px solid #ccc;
            border-radius: 8px;
            font-size: 16px;
        }

        button {
            padding: 8px 12px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: 14px;
            margin: 0 2px;
        }

        button:hover {
            background-color: #45a049;
        }

        ul {
            list-style-type: none;
            padding: 0;
        }

        li {
            background-color: #e9e9e9;
            margin: 10px 0;
            padding: 15px;
            border-radius: 8px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            flex-wrap: wrap;
        }

        .completed {
            text-decoration: line-through;
            color: gray;
        }

        .delete, .edit, .save {
            background-color: transparent;
            border: none;
            cursor: pointer;
            padding: 3px;
            margin-left: 5px;
        }

        .delete:hover svg, .edit:hover svg, .save:hover svg {
            fill: #c0392b;
        }

        .edit:hover svg {
            fill: #2980b9;
        }

        .save:hover svg {
            fill: #27ae60;
        }

        .note-text {
            flex: 1;
            margin: 0 10px;
            font-size: 16px;
        }

        .date {
            font-size: 12px;
            color: #777;
            margin-top: 5px;
        }

        svg {
            width: 14px;
            height: 14px;
        }
    </style>
</head>
<body>
    <div class="navbar" id="accountMenu" style="display: none;">
        <div class="account-menu">
            <button class="account-icon">👤</button>
            <div class="dropdown-content">
                <p id="usernameDisplay"></p>
                <button onclick="logout()">Keluar</button>
            </div>
        </div>
    </div>

    <div class="login-container" id="loginContainer">
        <h1>Login</h1>
        <input type="text" id="username" placeholder="Nama Pengguna">
        <input type="password" id="password" placeholder="Kata Sandi">
        <button onclick="login()">Masuk</button>
        <button onclick="showRegister()">Daftar Akun</button>
    </div>

    <div class="register-container" id="registerContainer" style="display: none;">
        <h1>Daftar Akun</h1>
        <input type="text" id="newUsername" placeholder="Nama Pengguna Baru">
        <input type="password" id="newPassword" placeholder="Kata Sandi Baru">
        <button onclick="register()">Daftar</button>
        <button onclick="showLogin()">Kembali ke Login</button>
    </div>

    <div class="container" id="noteContainer" style="display: none;">
        <h1>Catatan List</h1>
        <textarea id="noteInput" placeholder="Tulis beberapa catatan, pisahkan dengan baris baru..."></textarea>
        <button onclick="addNotes()">Tambah Catatan</button>
        <ul id="noteList"></ul>
    </div>

    <script>
        let currentUsername = '';
        const users = JSON.parse(localStorage.getItem('users')) || {};

        function login() {
            const username = document.getElementById('username').value;
            const password = document.getElementById('password').value;

            if (users[username] && users[username] === password) {
                currentUsername = username;
                document.getElementById('loginContainer').style.display = 'none';
                document.getElementById('noteContainer').style.display = 'block';
                document.getElementById('accountMenu').style.display = 'block';
                document.getElementById('usernameDisplay').textContent = `👤 ${currentUsername}`;
                loadNotes();
            } else {
                alert('Nama pengguna atau kata sandi salah!');
            }
        }

        function logout() {
            currentUsername = '';
            document.getElementById('loginContainer').style.display = 'block';
            document.getElementById('noteContainer').style.display = 'none';
            document.getElementById('accountMenu').style.display = 'none';
        }

        function register() {
            const newUsername = document.getElementById('newUsername').value;
            const newPassword = document.getElementById('newPassword').value;

            if (newUsername && newPassword) {
                if (users[newUsername]) {
                    alert('Nama pengguna sudah terdaftar!');
                } else {
                    users[newUsername] = newPassword;
                    localStorage.setItem('users', JSON.stringify(users));
                    alert('Pendaftaran berhasil!');
                    showLogin();
                }
            } else {
                alert('Isi semua kolom untuk mendaftar.');
            }
        }

        function showRegister() {
            document.getElementById('loginContainer').style.display = 'none';
            document.getElementById('registerContainer').style.display = 'block';
        }

        function showLogin() {
            document.getElementById('registerContainer').style.display = 'none';
            document.getElementById('loginContainer').style.display = 'block';
        }

        function addNotes() {
            const noteInput = document.getElementById('noteInput');
            const notes = noteInput.value.trim().split('\n').filter(note => note.trim() !== '');

            notes.forEach(note => createNoteItem(note));

            noteInput.value = '';
            saveNotes();
        }

        function createNoteItem(note, date = new Date().toLocaleString()) {
            const noteList = document.getElementById('noteList');
            const li = document.createElement('li');

            const checkbox = document.createElement('input');
            checkbox.type = 'checkbox';
            checkbox.onclick = function() {
                noteText.classList.toggle('completed', checkbox.checked);
                saveNotes();
            };

            const noteText = document.createElement('span');
            noteText.textContent = note;
            noteText.className = 'note-text';

            const dateText = document.createElement('div');
            dateText.className = 'date';
            dateText.textContent = date;

            const editBtn = document.createElement('button');
            editBtn.className = 'edit';
            editBtn.innerHTML = '✏️';
            editBtn.onclick = function() {
                const newText = prompt("Edit catatan:", noteText.textContent);
                if (newText) {
                    noteText.textContent = newText;
                    saveNotes();
                }
            };

            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete';
            deleteBtn.innerHTML = '🗑️';
            deleteBtn.onclick = function() {
                noteList.removeChild(li);
                saveNotes();
            };

            li.appendChild(checkbox);
            li.appendChild(noteText);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
            li.appendChild(dateText);

            noteList.insertBefore(li, noteList.firstChild);
        }

        function saveNotes() {
            const notes = [];
            document.querySelectorAll('#noteList li').forEach(li => {
                const noteText = li.querySelector('.note-text').textContent;
                const dateText = li.querySelector('.date').textContent;
                const completed = li.querySelector('input[type="checkbox"]').checked;
                notes.push({ note: noteText, date: dateText, completed: completed });
            });
            localStorage.setItem(`notes_${currentUsername}`, JSON.stringify(notes));
        }

        function loadNotes() {
            const savedNotes = localStorage.getItem(`notes_${currentUsername}`);
            if (savedNotes) {
                JSON.parse(savedNotes).forEach(savedNote => {
                    createNoteItem(savedNote.note, savedNote.date);
                    const lastNote = document.querySelector('#noteList li:first-child input[type="checkbox"]');
                    if (savedNote.completed) lastNote.checked = true;
                    lastNote.onclick();
                });
            }
        }
    </script>
</body>
</html>
