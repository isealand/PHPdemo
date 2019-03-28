# php连接ldap（AD）

php环境
扩展：
curl，openssl，ldap

AD域：win server

```

$host    = 'ldaps://test-ad.testdemo.net'; //test-ad.testdemo.net
$port    = 636;
$account = 'testad@testdemo.net';
$pwd  = demoHellotest$@E';

try {
#-----------------------------------------------------------------------------------------
    //ldap_set_option(NULL, LDAP_OPT_DEBUG_LEVEL, 7);  //连接信息
    $conn = ldap_connect($host, $port);//不要写成ldap_connect($host.':'.$port)的形式
    if (!$conn) {
        echo "ldap_error: " . ldap_error($conn) . PHP_EOL;
        echo '无法连接到AD域服务器';
        exit;
    }

    //设置参数
    ldap_set_option($conn, LDAP_OPT_PROTOCOL_VERSION, 3);//声明使用版本3
    ldap_set_option($conn, LDAP_OPT_REFERRALS, 0); // Binding to ldap server
    ldap_set_option($conn, LDAP_OPT_HOST_NAME, "test-ad.testdemo.net");

    ldap_set_option($conn, LDAP_OPT_NETWORK_TIMEOUT, 3);
    ldap_set_option($conn, LDAP_OPT_TIMELIMIT, 3);
    ldap_set_option($conn, LDAP_OPT_TIMEOUT, 3);

    if (ldap_start_tls($conn)) {
        echo 'LDAP start TLS ok' . PHP_EOL . PHP_EOL;
    } else {
        echo 'LDAP start TLS failed' . PHP_EOL . PHP_EOL;
    }

    $bd = ldap_bind($conn, 'testa@testdemo.net', $pwd);
    if (!$bd) {
        echo "error code: " . ldap_errno($conn) . PHP_EOL;
        echo "ldap_error: " . ldap_error($conn) . PHP_EOL;
        echo 'LDAP 绑定失败' . PHP_EOL . PHP_EOL . PHP_EOL;
        exit;
    }

    ldap_get_option($conn, LDAP_OPT_DIAGNOSTIC_MESSAGE, $err);
    echo "ldap_get_option: $err";

    echo PHP_EOL . 'LDAP 修改操作' . ":<br/>";
    $dn          = "CN=李四,OU=技术部,OU=总部,DC=testdemo,DC=net";
    $newpassword = 'asRdf#123sd%NKded';
    $newpassword .= rand(100, 999);
    $newpassword = '"' . $newpassword . '"';
    echo $newpassword . PHP_EOL;
    $newpassword = iconv('UTF-8', 'UTF-16LE', $newpassword);
//    $newpassword = mb_convert_encoding($newpassword , 'UTF-16LE');
    $user['unicodePwd'] = $newpassword;
    $result             = ldap_mod_replace($conn, $dn, $user);
    echo "error code: " . ldap_errno($conn) . PHP_EOL;
    echo "ldap_error: " . ldap_error($conn) . PHP_EOL;

    //
    ldap_close($conn);
} catch (Exception $e) {
    print_r($e);
}

```

证书问题：
AD配置好后，php机器不需要配置证书；
