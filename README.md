$login_db_new_connect = mysql_connect($logindb[db_host],$logindb[db_username],$logindb[db_password]);
$selectdb = mysql_select_db($logindb[db_name],$login_db_new_connect); 
    if ($_GET[h] && $_GET[h] != '' && $_GET[h] != '0')
    {
        $emailz = $newemail1;
        $query = mysql_query("SELECT username FROM `account` WHERE reset_email='$_GET[h]'");
        $res = mysql_fetch_array($query);
        $check = mysql_query("SELECT email FROM `account` WHERE reset_email='$_GET[h]'");
        $ress = mysql_fetch_array($check);
        $check1 = mysql_query("SELECT reset_email2 FROM `account` WHERE reset_email='$_GET[h]'");
        $resss = mysql_fetch_array($check1);
        $emailz = $resss[reset_email2];

        if (mysql_num_rows($query) == 1)
        {
            echo "<b>E-mail was changed.</b>";
            $to = $ress[email];
            $headers = "Headers here";  
            $message = "Email has been Changed (more text to come later)";
            mail($to, '', $message, $headers);
            mysql_query("UPDATE account SET email='$emailz' WHERE reset_email='$_GET[h]'") or die ('query change ...');
            mysql_query("UPDATE account SET reset_email='', reset_email2='' WHERE username='$res[username]'") or die ('query ...');
        }
        else
        {
            echo "<b>Error not changed.</b>";     
        }
    }
    else
    {  
        $oldemail = addslashes($_POST["oldemail"]);
        $newemail1 = addslashes($_POST["newemail1"]);
        $newemail2 = addslashes($_POST["newemail2"]);
        if ($oldemail == "" || $newemail1 == "" || $newemail2 == "")
        {
            $reason =  "
            <form action=\"".$_SERVER['PHP_SELF']."\" method=\"POST\">
            <table width=\"310\">
            <tr><td>Old e-mail:</td><td><input type=\"email\" name=\"oldemail\"></td></tr>
            <tr><td>New e-mail:</td><td><input type=\"email\" name=\"newemail1\"></td></tr>
            <tr><td>New e-mail again:</td><td><input type=\"email\" name=\"newemail2\"></td></tr>
            <tr><td align=\"center\" colspan=\"2\"><br><input type=\"submit\" value=\"Change e-mail\">
            </table>
            </form>";
        }
        else
        {   
            $login_db_new_connect = mysql_connect($logindb[db_host],$logindb[db_username],$logindb[db_password]);
            $selectdb = mysql_select_db($logindb[db_name],$login_db_new_connect);
            $queryy = mysql_query("SELECT username FROM `account` WHERE id='$id'");
            $resu = mysql_fetch_array($queryy);  
            $sql = "SELECT * FROM account WHERE email='$oldemail' AND username='$resu[username]'";
            $result = mysql_query($sql);
            $result2 = mysql_num_rows($result);

            if ($result2 !== 1)
            {
                $reason = "Something is wrong";
            }
            else
            {
                if ($newemail1 == $newemail2)
                {
                    $emailz = $newemail1;       
                    $rand = random_string(40);
                    $sql1 = "UPDATE `account` SET reset_email='$rand' WHERE id='$id'";
                    mysql_query($sql1) or die ('query ...');
                    $sql2 = "UPDATE `account` SET reset_email2='$newemail1' WHERE id='$id'";
                    mysql_query($sql2) or die ('query ...');
                    $to = $_POST["oldemail"];
                    $headers = "";  
                    $message = "Activation link: $config[path_to_thisfile]?h=$rand\n\n\n";
                    mail($to, '', $message, $headers);
                    echo "<b>activation link has been sent</b>";
                }
                else
                {                                                                                  
                    echo "<b>wrong input data</b>";
                }
            }
        } 
    }
?>
