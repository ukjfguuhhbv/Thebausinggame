I created a php-based multi-player time-based  game, but it have problems right now.

the project is in a folder called "gameproject".
there are 1 directory and 2 files in gameproject.

gameproject:
Directory: play
Files:

1.login.php
  the code of it:
```php
// File: gameproject/login.php

error_reporting(0);

session_start();

if ($_SERVER["REQUEST_METHOD"] == "POST") {
  $username = $_POST["username"];
  $password = $_POST["password"];

  $file = "gameproject/users.json";
  $users = json_decode(file_get_contents($file), true);

  if (array_key_exists($username, $users) && $users[$username]["password"] == $password) {
    $_SESSION["username"] = $username;
    header("Location: play/main.php");
  } else {
    echo "Invalid username or password.";
  }
}
?>

<!DOCTYPE html>
<html>
<head>
  <title>Login</title>
</head>
<body>
  <form method="post">
    <label for="username">Username:</label><br>
    <input type="text" id="username" name="username" required><br>
    <label for="password">Password:</label><br>
    <input type="password" id="password" name="password" required><br>
    <input type="submit" value="Login">
  </form>
  <p>Don't have an account? <a href="sign-up.php">Sign up here</a>.</p>
</body>
</html>
```
 
 2.sign-up.php
 the code of it is:
```php
<php error_reporting(0);

if ($_SERVER["REQUEST_METHOD"] == "POST") {
  $name = $_POST["name"];
  $username = $_POST["username"];
  $password = $_POST["password"];

  // Check if the username is already taken
  $file = "gameproject/users.json";
  $users = json_decode(file_get_contents($file), true);
  if (array_key_exists($username, $users)) {
    echo "Username already taken. Please choose another.";
  } else {
    $users[$username] = [
      "name" => $name,
      "password" => $password,
      "empire" => rand(1, 7),
      "resources" => ["resource1" => 100, "resource2" => 100, "resource3" => 100, "resource4" => 100],
      "messages" => []
    ];
    file_put_contents($file, json_encode($users));
    header("Location: login.php");
  }
}
?>

<!DOCTYPE html>
<html>
<head>
  <title>Sign Up</title>
</head>
<body>
  <form method="post">
    <label for="name">Name:</label><br>
    <input type="text" id="name" name="name" required><br>
    <label for="username">Username:</label><br>
    <input type="text" id="username" name="username" required><br>
    <label for="password">Password:</label><br>
    <input type="password" id="password" name="password" required><br>
    <input type="submit" value="Sign Up">
  </form>
  <p>Already have an account? <a href="login.php">Login here</a>.</p>
</body>
</html>
```

in the play directory there are 2 files:
gameproject/play:
 1.main.php
 the code of it is:
```php
// File: gameproject/play/main.php

error_reporting(0);

session_start();

if (!isset($_SESSION["username"])) {
    header("Location: ../login.php");
    exit;
}

$username = $_SESSION["username"];
$file = "../users.json";
$users = json_decode(file_get_contents($file), true);
$user = $users[$username];

$empire_names = ["1" => "Egyptians", "2" => "Romans", "3" => "Persians", "4" => "Greeks", "5" => "Mongols", "6" => "Vikings", "7" => "Mayans"];
?>
<!DOCTYPE html>
<html>
<head>
  <title>Main</title>
</head>
<body>
  <h1>Welcome, <?php echo $user["name"]; ?>!</h1>
  <p>You are playing as the <?php echo $empire_names[$user["empire"]]; ?>.</p>
  <p>Resources:</p>
  <ul>
    <li>Resource 1: <?php echo $user["resources"]["resource1"]; ?></li>
    <li>Resource 2: <?php echo $user["resources"]["resource2"]; ?></li>
    <li>Resource 3: <?php echo $user["resources"]["resource3"]; ?></li>
    <li>Resource 4: <?php echo $user["resources"]["resource4"]; ?></li>
  </ul>
  <p><a href="message.php">Message</a></p>
</body>
</html>
```


 2.message.php
 the code of it is:
```php
// File: gameproject/play/message.php

error_reporting(0);

session_start();

if (!isset($_SESSION["username"])) {
    header("Location: ../login.php");
    exit;
}

$username = $_SESSION["username"];

if ($_SERVER["REQUEST_METHOD"]== "POST") {
  $to = $_POST["to"];
  $message = $_POST["message"];
  // Save the message, for the sake of simplicity, we store it in the users.json file
  $file = "../users.json";
  $users = json_decode(file_get_contents($file), true);
  if (array_key_exists($to, $users)) {
    $users[$to]["messages"][] = ["from" => $username, "message" => $message];
    file_put_contents($file, json_encode($users));
    echo "Message sent!";
  } else {
    echo "Invalid destination username.";
  }
}

$file = "../users.json";
$users = json_decode(file_get_contents($file), true);
$messages = $users[$username]["messages"];

?>
<!DOCTYPE html>
<html>
<head>
  <title>Messages</title>
</head>
<body>
  <h1>Messages</h1>
  <ul>
    <?php foreach ($messages as $message): ?>
      <li><?php echo $message["from"]; ?>: <?php echo $message["message"]; ?></li>
    <?php endforeach; ?>
  </ul>
  <form method="post">
    <label for="to">To:</label><br>
    <input type="text" id="to" name="to" required><br>
    <label for="message">Message:</label><br>
    <textarea id="message" name="message" required></textarea><br>
    <input type="submit" value="Send Message">
  </form>
</body>
</html>
```
