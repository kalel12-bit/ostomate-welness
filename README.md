<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Login & Registrasi</title>
    <link rel="stylesheet" href="login.css">
</head>
<body>

<div class="container" id="formContainer">
    <h2 id="formTitle">Login</h2>
    <form id="loginForm" onsubmit="return handleSubmit(event);">
        <input type="text" id="username" placeholder="Username" required>
        <input type="password" id="password" placeholder="Password" required>
        <button type="submit">Login</button>
    </form>
    <div class="toggle">
        <span>Belum punya akun? <a href="#" id="toggleLink">Registrasi</a></span>
    </div>
    <div id="errorMessage" style="color: red; display: none;"></div>
</div>

<!-- Kotak input untuk memasukkan kode OTP -->
<div class="container" id="otpContainer" style="display: none;">
    <h2>Verifikasi OTP</h2>
    <form id="otpForm" onsubmit="return handleOtpSubmit(event);">
        <input type="text" id="otpCode" placeholder="Masukkan kode OTP" required>
        <button type="submit">Verifikasi</button>
    </form>
    <div id="otpErrorMessage" style="color: red; display: none;"></div>
</div>

<script>
    const formContainer = document.getElementById('formContainer');
    const otpContainer = document.getElementById('otpContainer');
    const formTitle = document.getElementById('formTitle');
    const loginForm = document.getElementById('loginForm');
    const toggleLink = document.getElementById('toggleLink');
    const errorMessage = document.getElementById('errorMessage');
    const otpErrorMessage = document.getElementById('otpErrorMessage');
    let storedOtp;

    toggleLink.addEventListener('click', (e) => {
        e.preventDefault();
        if (formTitle.innerText === 'Login') {
            formTitle.innerText = 'Registrasi';
            loginForm.innerHTML = `
                <input type="text" id="fullName" placeholder="Nama Lengkap" required>
                <input type="text" id="username" placeholder="Username" required>
                <input type="email" id="email" placeholder="Email" required>
                <input type="password" id="password" placeholder="Password" required>
                <select id="gender" required>
                    <option value="" disabled selected>Jenis Kelamin</option>
                    <option value="male">Pria</option>
                    <option value="female">Wanita</option>
                    <option value="other">Lainnya</option>
                </select>
                <input type="date" id="dob" placeholder="Tanggal Lahir" required>
                <input type="text" id="address" placeholder="Alamat" required>
                <input type="tel" id="phone" placeholder="Nomor Handphone" required>
                <button type="submit">Registrasi</button>
            `;
            toggleLink.innerText = 'Sudah punya akun? Login';
            errorMessage.style.display = 'none'; 
        } else {
            formTitle.innerText = 'Login';
            loginForm.innerHTML = `
                <input type="text" id="username" placeholder="Username" required>
                <input type="password" id="password" placeholder="Password" required>
                <button type="submit">Login</button>
            `;
            toggleLink.innerText = 'Belum punya akun? Registrasi';
            errorMessage.style.display = 'none'; 
        }
    });

    function handleSubmit(event) {
        event.preventDefault(); 
        const username = document.getElementById('username').value;
        const password = document.getElementById('password').value;
        
        if (formTitle.innerText === 'Login') {
            const storedUser = JSON.parse(localStorage.getItem(username));
            if (storedUser && storedUser.password === password) {
                localStorage.setItem('loggedInUser', username); 
                window.location.href = 'dashboard.html';
            } else {
                errorMessage.textContent = 'Username atau password salah.';
                errorMessage.style.display = 'block';
            }
        } else {
            const email = document.getElementById('email').value;
            const dob = document.getElementById('dob').value;
            const fullName = document.getElementById('fullName').value;
            const gender = document.getElementById('gender').value;
            const address = document.getElementById('address').value;
            const phone = document.getElementById('phone').value;

            if (localStorage.getItem(username)) {
                errorMessage.textContent = 'Username sudah digunakan.';
                errorMessage.style.display = 'block';
            } else {
                storedOtp = generateOtp(); 
                sendOtpToEmail(email, storedOtp); 
                formContainer.style.display = 'none';
                otpContainer.style.display = 'block';

                // Simpan data registrasi ke localStorage
                const userData = { password, email, dob, fullName, gender, address, phone };
                localStorage.setItem(username, JSON.stringify(userData));
            }
        }
        return false; 
    }

    function handleOtpSubmit(event) {
        event.preventDefault();
        const otpCode = document.getElementById('otpCode').value;

        if (otpCode === storedOtp) {
            alert('Registrasi berhasil! Anda dapat login sekarang.');
            formTitle.innerText = 'Login';
            loginForm.innerHTML = `
                <input type="text" id="username" placeholder="Username" required>
                <input type="password" id="password" placeholder="Password" required>
                <button type="submit">Login</button>
            `;
            toggleLink.innerText = 'Belum punya akun? Registrasi';
            errorMessage.style.display = 'none'; 
            formContainer.style.display = 'block'; 
            otpContainer.style.display = 'none'; 
            document.getElementById('otpCode').value = '';
            storedOtp = ''; 
        } else {
            otpErrorMessage.textContent = 'Kode OTP salah.';
            otpErrorMessage.style.display = 'block';
        }
        return false;
    }

    function generateOtp() {
        return Math.floor(100000 + Math.random() * 900000).toString(); 
    }

    function sendOtpToEmail(email, otp) {
        alert(`Kode OTP telah dikirim ke ${email}: ${otp}`); 
    }

</script>

</body>
</html>

/* Reset dasar */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Arial', sans-serif;
    background: linear-gradient(135deg, #74ebd5, #acb6e5);
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

.container {
    background-color: white;
    padding: 30px;
    border-radius: 15px;
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
    text-align: center;
    width: 100%;
    max-width: 400px;
    transition: transform 0.2s ease-in-out;
}

input {
    display: block;
    width: 100%;
    padding: 10px;
    margin: 10px 0;
    border: 1px solid #ccc;
    border-radius: 5px;
    transition: border 0.3s, background-color 0.3s;
}

input:focus {
    border-color: #74ebd5;
    background-color: #f0f0f0;
}

button {
    width: 100%;
    padding: 10px;
    background-color: #74ebd5;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s;
}

button:hover {
    background-color: #acb6e5;
}

.toggle {
    margin-top: 10px;
}

a {
    color: #74ebd5;
    cursor: pointer;
}

h2 {
    margin-bottom: 20px;
    color: #333;
}

#errorMessage, #otpErrorMessage {
    color: red;
    margin-top: 10px;
}
