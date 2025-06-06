# demoix
<!--Connect.php-->
<?php
$host = 'localhost';
$user = 'root';
$password = '';
$dbname = '';
$db = '';
$pass = '';
$charset = 'utf8mb4';

$conn = new mysqli($host, $user, $password, $dbname);

if ($conn->connect_error) {
    die("Ошибка подключения: " . $conn->connect_error);
}

$dsn = "mysql:host=$host;dbname=$db;charset=$charset";
$options = [
    PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
    PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
];

try {
    $pdo = new PDO($dsn, $user, $pass, $options);
} catch (\PDOException $e) {
    die('Ошибка подключения: ' . $e->getMessage());
}

<!--Login.php-->

<?php
include 'connect.php';
session_start();

if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    $email = $_POST['email'] ?? '';
    $password = $_POST['password'] ?? '';

    if (!empty($email) && !empty($password)) {
        $sql = "SELECT * FROM users WHERE email = ?";
        $stmt = $conn->prepare($sql);
        $stmt->bind_param("s", $email);
        $stmt->execute();
        $result = $stmt->get_result();

        if ($result->num_rows > 0) {
            $user = $result->fetch_assoc();

            if (password_verify($password, $user['password_hash'])) {
                // Авторизация успешна — устанавливаем сессии
                $_SESSION['isAuth'] = true;
                $_SESSION['userId'] = $user['id'];
                $_SESSION['profileName'] = $user['name'];
                if ($email === 'admin'){
                    $_SESSION['isAdmin'] = ($email === 'admin');
                }

                header("Location: index.php");
                exit();
            } else {
                echo "Неверный пароль!";
            }
        } else {
            echo "Пользователь не найден!";
        }
        $stmt->close();
    } else {
        echo "Заполните все поля!";
    }
}
function showProfileName()
{
    return $_SESSION['profileName'] ?? 'Гость';
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kroha-Market</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.5/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-SgOJa3DmI69IUzQ2PVdRZhwQ+dy64/BUtbMJw1MZ8t5HZApcHrRKUc4W0kG879m7" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.5/dist/js/bootstrap.bundle.min.js" integrity="sha384-k6d4wzSIapyDyv1kpU366/PK5hCdSbCRGRCMv+eplOQJWyd1fbcAu9OCUj5zNLiq" crossorigin="anonymous"></script>
    <link rel="shortcut icon" href="img/Logo.svg" type="image/png">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Days+One&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="css/style.css">
</head>
<body>
    <!--Хэддер-->
    <header class="d-flex flex-row p-3">
      <div class="d-flex">
          <a href="index.php"><img src="img/Logo.svg" alt=""></a>
          <h2 class="logo-name" style="padding-top: 20px; padding-left: 8px;">Кроха-Маркет</h2>
      </div>
      <nav class="navbar navbar-expand-lg navbar-light ms-auto">
          <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
              <span class="navbar-toggler-icon"></span>
          </button>
          <div class="collapse navbar-collapse" id="navbarNav">
              <ul class="navbar-nav">
                  <li class="nav-item">
                      <a class="nav-link nav-words" href="about.html">О нас</a>
                  </li>
                  <li class="nav-item">
                      <a class="nav-link nav-words" href="cataloge.php">Каталог</a>
                  </li>
                  <li class="nav-item">
                      <a class="nav-link nav-words" href="contact.html">Контакты</a>
                  </li>
              </ul>
              <div class="d-flex align-items-center ms-auto">
                  <a href="favorite.html"><img src="img/favorite.svg" alt=""></a>
                  <a href="cart.html" style="padding-left: 20px;"><img src="img/shopcart.svg" alt=""></a>
                  <a href="" style="padding-left: 30px;"><img src="img/profileicon.svg" alt=""></a>
              </div>
          </div>
      </nav>
  </header>

    <!-- Контейнер для формы входа -->
  <div class="registration-container">
    <form class="registration-form" method="POST" action="login.php">
    <h2 class="mb-4 text-center form-reg-h">Авторизация</h2>
    <div class="mb-3">
        <label for="inputEmail" class="form-label footer-text">Почта</label>
        <input class="form-control form-reg-font" id="inputEmail" name="email" placeholder="E-mail" />
    </div>
    <div class="mb-3">
        <label for="inputPassword" class="form-label footer-text">Пароль</label>
        <input type="password" class="form-control form-reg-font" id="inputPassword" name="password" placeholder="Введите пароль" />
    </div>
    <button type="submit" class="btn btn-light w-100 mb-3 form-reg-btn">Авторизоваться</button>
    <div class="text-center footer-text">
        Ещё нет аккаунта? <a href="reg.php" class="form-link">Зарегистрируйтесь</a>
    </div>
    </form>
  </div>



<!--Футтер-->
<footer class="d-flex flex-column" style="background-color: #01A6D3; padding: 20px;">
  <div class="d-flex justify-content-around w-100">
      <!--Каталог-->
      <div class="t">
          <h5 class="footer-h">Каталог</h5>
          <ul style="list-style-type: none; padding: 0; line-height: 290%;">
              <li><a href="#clothing" class="footer-text">Одежда</a></li>
              <li><a href="#shoes" class="footer-text">Обувь</a></li>
              <li><a href="#toys" class="footer-text">Игрушки</a></li>
              <li><a href="#hygiene" class="footer-text">Гигиена</a></li>
          </ul>
      </div>

      <!--О магазине-->
      <div class="t">
          <h5 class="footer-h">О магазине</h5>
          <ul style="list-style-type: none; padding: 0; line-height: 290%;">
              <li><a href="about.html" class="footer-text">О нас</a></li>
              <li><a href="howtoorder.html" class="footer-text">Как заказать</a></li>
              <li><a href="howtoorder.html" class="footer-text">Доставка</a></li>
              <li><a href="howtoorder.html" class="footer-text">Оплата</a></li>
              <li><a href="contact.html" class="footer-text">Контакты</a></li>
          </ul>
      </div>

      <!--Адрес-->
      <div>
          <h5 class="footer-h">Адрес</h5>
          <p class="footer-text">г. Москва, ул Складочная, д. 9, стр. 1</p>
          <h5 class="footer-h">Телефон</h5>
          <p class="footer-text">8 (495) 194-64-97</p>
          <h5 class="footer-h">E-mail</h5>
          <p class="footer-text">kroha-market@mail.ru</p>
      </div>

  <!--Соц-сети в нижнем углу-->
  <div class="d-flex footer-sm">
      <a href="https://vk.com" target="_blank" class="mx-2"><img src="img/vkicon.svg" alt="VK"></a>
      <a href="https://wa.me" target="_blank" class="mx-2"><img src="img/whatsappicon.svg" alt="WhatsApp"></a>
      <a href="https://telegram.org" target="_blank" class="mx-2"><img src="img/telegramicon.svg" alt="Telegram"></a>
  </div>
  </div>


  <script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.11.8/dist/umd/popper.min.js" integrity="sha384-I7E8VVD/ismYTF4hNIPjVp/Zjvgyol6VFvRkX/vR+Vc4jQkC+hVqc2pM8ODewa9r" crossorigin="anonymous"></script>
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.5/dist/js/bootstrap.min.js" integrity="sha384-VQqxDN0EQCkWoxt/0vsQvZswzTHUVOImccYmSyhJTp7kGtPed0Qcx8rK9h9YEgx+" crossorigin="anonymous"></script>
</footer>
</body>
</html>


<!--Logout.php-->
<?php
session_start();
session_destroy();
header("Location: index.php");

<!--Profile.php-->
<?php
require_once 'connect.php';
session_start();

if (!isset($_SESSION['isAuth']) || $_SESSION['isAuth'] !== true || !isset($_SESSION['userId'])) {
    header("Location: login.php");
    exit;
}

$userId = (int)$_SESSION['userId'];
$userName = htmlspecialchars($_SESSION['profileName'] ?? 'Пользователь');

$stmt = $pdo->prepare("
    SELECT p.id, p.name, p.img, p.price 
    FROM favorite f 
    JOIN products p ON f.productID = p.id 
    WHERE f.userID = ?
");
$stmt->execute([$userId]);
$favorites = $stmt->fetchAll();
?>

<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Личный кабинет</title>
    <link rel="stylesheet" href="style.css">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>

<div class="container my-5">
    <h2 class="mb-4">Здравствуйте, <?= $userName ?>!</h2>

    <div class="mb-4">
        <a href="orders.php" class="btn btn-primary me-2">Мои заказы</a>
        <a href="cart.php" class="btn btn-secondary me-2">Корзина</a>
        <a href="logout.php" class="btn btn-outline-danger">Выйти</a>
    </div>

    <h4 class="mt-5">Избранные товары</h4>
    <?php if (count($favorites) > 0): ?>
        <div class="row row-cols-1 row-cols-sm-2 row-cols-md-3 g-4 mt-3">
            <?php foreach ($favorites as $product): ?>
                <div class="col">
                    <div class="card h-100">
                        <img src="data:image/jpeg;base64,<?= base64_encode($product['img']) ?>" class="card-img-top" alt="<?= htmlspecialchars($product['name']) ?>">
                        <div class="card-body">
                            <h5 class="card-title"><?= htmlspecialchars($product['name']) ?></h5>
                            <p class="card-text"><?= number_format($product['price'], 0, ',', ' ') ?> ₽</p>
                            <a href="product.php?id=<?= $product['id'] ?>" class="btn btn-sm btn-outline-primary">Перейти к товару</a>
                            <a href="removefromfavorite.php?productID=<?= $product['id'] ?>" class="btn btn-sm btn-outline-danger ms-2">Удалить</a>
                        </div>
                    </div>
                </div>
            <?php endforeach; ?>
        </div>
    <?php else: ?>
        <p class="text-muted mt-3">У вас пока нет избранных товаров.</p>
    <?php endif; ?>
    <a href="index.php" class="btn btn-link mt-3">← На главную</a>
</div>

</body>
</html>

<!--Register.php-->

<?php
session_start();
require 'connect.php';

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $username = $_POST['username'] ?? '';
    $password = $_POST['password'] ?? '';

    if ($username && $password) {
        $stmt = $pdo->prepare("SELECT id FROM users WHERE username = ?");
        $stmt->execute([$username]);
        if ($stmt->fetch()) {
            $error = "Имя пользователя уже занято!";
        } else {
            $hashed = password_hash($password, PASSWORD_BCRYPT);
            $stmt = $pdo->prepare("INSERT INTO users (username, password) VALUES (?, ?)");
            $stmt->execute([$username, $hashed]);
            header("Location: login.php");
            exit;
        }
    } else {
        $error = "Заполните все поля!";
    }
}
?>

<!DOCTYPE html>
<html>
<head>
    <title>Регистрация</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <h2>Регистрация</h2>
    <?php if (!empty($error)) echo "<p class='error'>$error</p>"; ?>
    <form method="POST">
        <input type="text" name="username" placeholder="Имя пользователя" required>
        <input type="password" name="password" placeholder="Пароль" required>
        <button type="submit">Зарегистрироваться</button>
    </form>
    <p>Уже есть аккаунт? <a href="login.php">Войти</a></p>
</body>
</html>
