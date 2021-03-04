## 7.Separar e xuntar os arquivos dunha base de datos

Desligar os arquivos dunha base de datos pode resultar moi útil se queremos mover a database a unha instancia ou SQL Server diferentes, ou ben para realizar unha copia física deles. Estes arquivos atópanse no cartafol */DATA* do directorio do servidor.

[01](./img/07_01.png)

Para liberar os arquivos .mdf e .ldf dunha base de datos, facemos clic dereito na nosa base de datos, despregamos o menú tarefas e escollemos a opción *Separar*.

[02](./img/07_02.png)

No menú emerxente, prememos a xanela para quitar conexións. Tras aceptar, a base de datos quedará aparentemente “eliminada” do servidor SQL, aínda que os arquivos non se moveron. Neste momento, podemos movelos ou copialos con liberdade.

[03](./img/07_03.png)

Para volver a unir a database de novo a un servidor SQL, basta con facer clic dereito en Bases de Datos, e premer en Adjuntar. Despois, mapeamos o arquivo .mdf ubicado en *C:\Program Files\Microsoft SQL Server\MSSQL12.SERVIDOR2\MSSQL\DATA\informaticadat.mdf*. Seleccionando só este, incorporaremos tamén o arquivo log.

[04](./img/07_04.png)

Realizar esta mesma operación mediante código Transact-SQL resulta aínda máis sinxelo.

```sql
USE master;
GO

-- Separar unha base de datos do servidor BD

sp_detach_db informatica;
GO

-- Volver a xuntar os arquivos dunha base de datos co servidor

GO
CREATE DATABASE informatica
ON PRIMARY (FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL12.SERVIDOR2\MSSQL\DATA\informaticadat.mdf')
FOR ATTACH;
```
