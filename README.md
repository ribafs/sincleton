# Singleton

## Classe em PHP com PDO usando Singleton

Gosto do que faz o padrão singleton em conexões com bancos de dados. Mesmo queu alguńe digam que não é um padrão, mas gosto por ser racional e procurar fazer um bom trabalho, conectando apenas quando estiver desconectado.

Fiz uma pesquisa por vários sites e exemplos e o resultado contempla, bons atributos do PDO, SGBD, charset, porta, etc:

## Opções de SGBD
MySQL - 'mysql:host=localhost; port=3306; dbname=test'

PostgreSQL - 'pgsql:host=localhost; port=5432; dbname=test; user=brain; password=bell'

SQLite - 'sqlite:/dbs/brainbell/test'

Oracle - 'oci:dbname=test'

'oci:dbname=//localhost:1521/test'

Firebird - 'firebird:dbname=/path/test.fsb'

- https://www.brainbell.com/php/pdo.html
- https://www.php.net/manual/pt_BR/pdo.drivers.php

Além do SGBD instalado o PDO também requer as respectivas extensões do PHP: 
- PDO_MYSQL,
- PDO_PGSQL, 
- PDO_SQLITE3, 
- PDO_OCI, 
- PDO_SQLSRV

Ao final tem um pequeno exemplo para testar

```php
<?php

class Connection {
    private static $host    = 'localhost';
    private static $sgbd    = 'mysql';// mysql, pgsql, sqlite
    private static $db      = 'testes';
    private static $charset = 'utf8mb4'; // utf8mb4, utf8
    private static $port    = '3306';// 3306, 5432

    private static $dsn     = '';
    private static $user    = 'root';
    private static $pass    = 'root';

    protected static $pdo;

    private function __construct() {
        try {
            $options = [
                PDO::ATTR_EMULATE_PREPARES   => false, // turn off emulation mode for "real" prepared statements
                PDO::MYSQL_ATTR_INIT_COMMAND => 'SET NAMES UTF8',
                PDO::NULL_EMPTY_STRING,
                PDO::ATTR_ERRMODE            => PDO::ERRMODE_EXCEPTION, //turn on errors in the form of exceptions
                PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_OBJ, //make the default fetch be an associative array
            ];
				    self::$dsn = self::$sgbd.':host='.self::$host.';dbname='.self::$db.';port='.self::$port.';charset='.self::$charset;
            self::$pdo = new PDO(self::$dsn, self::$user, self::$pass, $options);
        } catch (PDOException $e) {
            echo "MySql Connection Error: " . $e->getMessage();
        }
    }

    public static function getInstance() {
        if (!self::$pdo) {
            new Connection();
        }

        return self::$pdo;
    }

}

// Testando

    $pdo = Connection::getInstance();
    $stm = $pdo->query("SELECT * FROM clientes order by id");
    $executa = $stm->execute();

    print "<b>ID</b> - <b>Nome</b> - <b>Email</b><br>";
    if($executa){
       while($reg = $stm->fetch()){ // Para recuperar um ARRAY utilize PDO::FETCH_ASSOC 
         print "$reg->id -  $reg->nome ... $reg->email<br>";
       }
    }else{
       echo 'Erro na consulta dos dados';
    }
?>
```

## Referências de onde pesquisei

- http://giuffre.github.io/PHP-Mysql-how-to-optimize-a-connection/
- Http://www.devwilliam.com.br/php/pdo-conexao-seguindo-singleton-php
- https://www.devmedia.com.br/php-singleton-aplicando-o-padrao-de-projeto-na-pratica/28469
- https://gist.github.com/ftonato/2973a55baf8eef6795a48804dcdb71dd
- https://stackoverflow.com/questions/4361459/php-pdo-charset-set-names
- https://github.com/ribafs/phpecia/blob/main/PDO/Conexoes/ConnMultiSGBD.php - Conectando ao mysql, postgresql e sqlite
- https://www.devmedia.com.br/conectando-no-sql-server-utilizando-pdo-em-php/38936

