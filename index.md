
<?php

// Initialiser la session

session_start();

// Verifier si l'utilisateur est déjà connecté.

if(isset($_SESSION["login"]) && $_SESSION["login"] === true){

    header("location: dashboard.php");

    exit;

}

// Inclure le fichier de configuration

require_once "includes/config.php";

// Initialisation des variables

$user = $password = $rank = $error = "";

// Verification de la soumission du formulaire

if($_SERVER["REQUEST_METHOD"] == "POST"){

    // Verification de la case nom d'utilisateur

    if(empty(trim($_POST["user"]))){

        $error .= "Veuillez saisir votre nom d'utilisateur.<br>";

    } else{

        $user = trim($_POST["user"]);

    }

   

    // Verification de la case mot de passe

    if(empty(trim($_POST["password"]))){

        $error .= "Veuillez saisir votre mot de passe.<br>";

    } else{

        $password = trim($_POST["password"]);

    }

   

        // Verification que toutes les cases sont remplis

        if(empty($error)){

            // Préparation de la requête SELECT de la base de données

            $sql = "SELECT id_employe, user, password, rank FROM employes WHERE user = :user";

        

        if($stmt = $pdo->prepare($sql)){

            // Bind les variables à la requête

            $stmt->bindParam(":user", $param_user, PDO::PARAM_STR);

           

            // On déclare les paramètres

            $param_user = trim($_POST["user"]);

           

            // Exécution de la requête

            if($stmt->execute()){

                // Verification que l'utilisateur existe bel et bien

                if($stmt->rowCount() == 1){

                    if($row = $stmt->fetch()){

                        $id = $row["id_employe"];

                        $username = $row["user"];

                        $rank = $row["rank"];

                        $hashed_password = $row["password"];

                        if(password_verify($password, $hashed_password)){

                            // Le mot de passe est bon

                            session_start();

                           

                            // On lance les sessions

                            $_SESSION["login"] = true;

                            $_SESSION["id"] = $id;

                            $_SESSION["user"] = $username;

                            $_SESSION["rank"] = $rank;                         

                            

                            // Redirection de l'utilisateure vers son administration

                            header("location: dashboard.php");

                            } else{

                                // Mot de passe erroné! On affiche une erreur générique sans donner d'informations sur lequel est faux

                                $error = "Votre nom d'utilisateur ou votre mot de passe sont incorrectes.<br>";

                            }

                        }

                    } else{

                        // L'utilisateur n'existe pas! On affiche une erreur générique sans donner d'informations sur lequel est faux

                        $error = "Votre nom d'utilisateur ou votre mot de passe sont incorrectes.<br>";

                    }

                } else{

                    $error = "Oups! Une erreur s'est produite. Veuillez réessayer plus tard.<br>";

                }

   

                // Close statement

                unset($stmt);

            }

        }

       

        // Close connection

        unset($pdo);

    }

    ?>

<!DOCTYPE html>

<html lang="en">

<head>

    <meta charset="UTF-8">

    <meta http-equiv="X-UA-Compatible" content="IE=edge">

    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>DP World Djazair - Connexion</title>

    <link rel="stylesheet" href="style/style.css">

</head>

<body>

    <section id="login_container">

        <div id="login">

            <img src="images/logo.svg" class="logo" alt="Logo" />

            <h1>Connectez-vous</h1>

            <p class="desc">Connectez-vous à votre espace DP World Djazair</p>

            <form id="login_form" method="POST" action="">

            <?= $error.'<br>'; ?>

                <label for="user">Nom d'utilisateur</label>

                <input type="text" name="user" placeholder="Votre identifiant" required>

                <label for="password">Mot de passe</label>

                <input type="password" name="password" placeholder="Votre mot de passe" required>

                <button type="submit" class="submit">Se connecter</button>

            </form>

        </div>

    </section>

</body>

</html>

