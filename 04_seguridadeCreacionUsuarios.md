## 4.Seguridade: Creación de usuarios

Segundo as necesidades da nosa base de datos, pensamos en dous tipos de usuarios que precisaremos de maneira consistente. En vez de configurar os usuarios de xeito individual, definiremos dous roles. Isto axilizaría moito a futura tarefa de asignación de permisos cando expandíramos o número de usuarios creados na nosa base de datos.

Así pois, por un lado queremos personalizar os permisos dun tipo de empregado, que será o dependente. Este deberá encargarse de almacenar a información das ventas que peche, cobrar ós clientes e rexistrar os seus datos persoais cando merquen por primeira vez. Esta operación realizarémola mediante a interface gráfica de SQL Server, creando e definindo o rol **Dependente**. Ademais, xeraremos o usuario Antonio como exemplo de empregado que incluiremos nesa función.

Por outra banda, tamén definimos outro tipo de empregado, que será o encargado de manexar o inventario da empresa. Este organizará os produtos que vende a empresa, polo que tamén debe rexistrar tanto marca como categoría dos produtos. Ademais, deberá ter acceso ó stock de cada tenda, para así realizar pedidos ós provedores. Neste caso, empregaremos código Transact-SQL para crear o rol **Inventario**, no que incluiremos ó usuario Berta tras configurar os privilexios de dita función.

Comezamos por crear o perfil dos dous usuarios en Windows. Isto débese a que empregaremos o sistema operativo como medio de autenticación.

[01](./img/04_01.png)

Tras isto, facemos clic dereito no menú *Seguridade*, e creamos un novo inicio de sesión.

[02](./img/04_02.png)

Isto resulta fundamental para que o usuario que creamos antes no sistema operativo poida acceder a SQL Server dende o seu perfil. Polo tanto, debemos escoller a opción *Autenticación de Windows*. Como parámetros secundarios, podemos asignar unha base de datos predeterminada para tal usuario, ademais dunha configuración de idioma por defecto.

[03](./img/04_03.png)

Tras isto, o usuario Antonio xa ten acceso a SQL Server, pero non á nosa base de datos. Polo tanto, creamos un usuario novo na database *informática*.

[04](./img/04_04.png)

Aí asignamos un nome, que non ter por que ser o mesmo que o establecido antes. O que si resulta importante é que o usuario de inicio de sesión sexa coincidente co recentemente creado. 

[05](./img/04_05.png)

Sen embargo, non nos interesa configurar ó detalle ese usuario. O que faremos será centrar todos os esforzos de asignación de permisos durante a creación e configuración das funcións. Así pois, imos á pestana de *Seguridade* da nosa base de datos, e creamos un novo Rol.

[06](./img/04_06.png)

Na lapela *General*, limitámonos a configurar un nome para o novo rol, e deixar asignado o usuario Antonio.

[07](./img/04_07.png)

Na páxina de elementos protexidos, engadimos as cinco táboas que precisarán os dependentes da nosa empresa. Tanto en ```CLIENTE```, ```VENDA``` e ```DETALLES_VENDA```, queremos que estes empregados teñan privilexios para seleccionar, crear e actualizar entradas. Sen embargo, non permitimos que poidan eliminar rexistros. Nestas tres táboas non terán limitacións entre columnas. Desta maneira, un dependente pode realizar tarefas como editar o número de teléfono dun cliente existente, rexistrar unha nova venda e especificar os detalles da mesma.

[08](./img/04_08.png)

Adicionalmente, os dependentes precisarán tamén dereitos de lectura nas táboas ```STOCK``` e ```PRODUTOS```, con fin de poder ofrecer información ós clientes e rexistrar os compoñentes vendidos. Con isto queda rematada a creación dun usuario por interface gráfica.

[09](./img/04_09.png)

Crear un usuario, darlle acceso, xerar un rol e configurarlle os seus permisos resulta máis sinxelo de facer mediante o código. Realmente, os pasos a seguir son os mesmos que antes. Partindo do perfil creado en Windows, executamos isto por partes: 

```sql
-- Creamos un inicio de sesión en modo de autenticación Windows

CREATE LOGIN [desktop-k9l8no4\Berta] FROM windows;
GO

-- Situámonos na nosa base de datos

USE informatica;
GO

-- Toda conta almacénase na seguinte táboa

SELECT * FROM sys.server_principals;
GO

-- Crear o usuario na base de datos

CREATE USER Berta FOR LOGIN [desktop-k9l8no4\Berta];
go

-- Todos os nomes de usuario da base de datos

SELECT * FROM sys.database_principals;
GO

-- Creamos un rol para os encargados de inventario

CREATE ROLE Inventario;
GO

-- Asignamos permisos nas tres táboas principais do rol Inventario

GRANT SELECT, INSERT, UPDATE ON PRODUTO TO Inventario;
GO
GRANT SELECT, INSERT, UPDATE ON MARCA TO Inventario;
GO
GRANT SELECT, INSERT, UPDATE ON CATEGORIA TO Inventario;
GO

-- Permitimos ademais que visualicen estas dúas táboas

GRANT SELECT ON STOCK TO Inventario;
GO
GRANT SELECT ON DETALLES_VENDA TO Inventario;
GO

-- Engadimos o usuario Berta ó rol Inventario

EXEC sp_addrolemember Inventario, Berta;

```

Nas táboas ```PRODUTO```, ```MARCA``` e ```CATEGORIA```, este tipo de empregados deben ter a posibilidade de seleccionar, crear e modificar rexistros, sen limitación entre columnas. Sen embargo, non se lles permite eliminar nada, pois a súa tarefa non consiste en descatalogar compoñentes, pero si rexistrar novos.

Ademais, deberán ter permisos de lectura nas táboas ```STOCK``` e ```DETALLES_VENDA```, co fin de controlar a cantidade de produtos que poida facer falta solicitar a provedores.
