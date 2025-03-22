<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>أكبر موقع إعجابات فري فاير</title>
    <style>
        body, html { margin: 0; padding: 0; width: 100%; height: 100%; font-family: Arial, sans-serif; text-align: center; background: black; color: white; overflow: hidden; }
        .container { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); text-align: center; background: rgba(0, 0, 0, 0.7); padding: 20px; border-radius: 10px; }
        input, select { display: block; width: 80%; margin: 10px auto; padding: 10px; }
        button { background: red; color: white; border: none; padding: 10px; cursor: pointer; transition: 0.3s; }
        button:hover { background: darkred; }
        canvas { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }
    </style>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/gsap.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
</head>
<body>

    <canvas id="bg"></canvas>

    <div class="container" id="loginBox">
        <h1>مرحبًا بك في عالم فري فاير</h1>
        <input type="text" id="username" placeholder="اسم المستخدم">
        <input type="password" id="password" placeholder="كلمة المرور">
        <button onclick="login()">تسجيل الدخول</button>
        <p>ليس لديك حساب؟ <a href="#" onclick="showRegister()">سجّل الآن</a></p>
    </div>

    <div class="container" id="registerBox" style="display: none;">
        <h1>إنشاء حساب</h1>
        <input type="text" id="newUsername" placeholder="اسم المستخدم">
        <input type="password" id="newPassword" placeholder="كلمة المرور">
        <button onclick="register()">تسجيل</button>
        <p>لديك حساب بالفعل؟ <a href="#" onclick="showLogin()">تسجيل الدخول</a></p>
    </div>

    <div class="container" id="dashboard" style="display: none;">
        <h1>لوحة التحكم</h1>
        <input type="text" id="uid" placeholder="أدخل UID">
        <select id="region">
            <option value="me">الشرق الأوسط</option>
            <option value="na">أمريكا الشمالية</option>
            <option value="eu">أوروبا</option>
        </select>
        <button onclick="sendLikes()">إرسال الإعجابات</button>
        <div id="result"></div>
        <button onclick="logout()">تسجيل الخروج</button>
    </div>

    <script>
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer({ canvas: document.getElementById("bg") });
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        const geometry = new THREE.TorusGeometry(10, 3, 16, 100);
        const material = new THREE.MeshBasicMaterial({ color: 0xff0000, wireframe: true });
        const torus = new THREE.Mesh(geometry, material);
        scene.add(torus);
        camera.position.z = 30;

        function animate() {
            requestAnimationFrame(animate);
            torus.rotation.x += 0.01;
            torus.rotation.y += 0.01;
            renderer.render(scene, camera);
        }
        animate();

        gsap.from(".container", { opacity: 0, scale: 0, duration: 1 });

        function showRegister() {
            document.getElementById("loginBox").style.display = "none";
            document.getElementById("registerBox").style.display = "block";
        }

        function showLogin() {
            document.getElementById("registerBox").style.display = "none";
            document.getElementById("loginBox").style.display = "block";
        }

        function login() {
            let username = document.getElementById("username").value;
            let password = document.getElementById("password").value;
            if (username && password) {
                localStorage.setItem("user", username);
                document.getElementById("loginBox").style.display = "none";
                document.getElementById("dashboard").style.display = "block";
            } else {
                alert("الرجاء إدخال بيانات صحيحة.");
            }
        }

        function register() {
            let newUsername = document.getElementById("newUsername").value;
            let newPassword = document.getElementById("newPassword").value;
            if (newUsername && newPassword) {
                localStorage.setItem("user", newUsername);
                alert("تم التسجيل بنجاح! يمكنك تسجيل الدخول الآن.");
                showLogin();
            } else {
                alert("الرجاء إدخال جميع البيانات.");
            }
        }

        function logout() {
            localStorage.removeItem("user");
            document.getElementById("dashboard").style.display = "none";
            document.getElementById("loginBox").style.display = "block";
        }

        function sendLikes() {
            var uid = document.getElementById("uid").value;
            var region = document.getElementById("region").value;
            var resultDiv = document.getElementById("result");

            if (!uid) {
                resultDiv.innerHTML = "الرجاء إدخال UID.";
                return;
            }

            var apiUrl = `https://likes-api-lk-team.vercel.app/like?uid=${uid}&server_name=${region}`;

            fetch(apiUrl)
                .then(response => response.text()) 
                .then(data => {
                    resultDiv.innerHTML = "النتيجة: " + data;
                })
                .catch(error => {
                    resultDiv.innerHTML = "حدث خطأ أثناء الإرسال.";
                });
        }

        window.onload = function() {
            if (localStorage.getItem("user")) {
                document.getElementById("loginBox").style.display = "none";
                document.getElementById("dashboard").style.display = "block";
            }
        };
    </script>

</body>
</html>
