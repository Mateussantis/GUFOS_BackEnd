# GUFOS - Agenda de Eventos - BackEnd C#

## Criação do Projeto
> Criamos nosso projeto de API com: 
```bash
dotnet new webapi
```
<br>

## Entity Framework - Database First

> Instalar o gerenciador de pacotes EF na máquina:
```bash
dotnet tool install --global dotnet-ef
```

<br>

> Baixar Pacote SQL Server:
```bash
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<br>

> Baixar pacote de escrita de códigos do EF:
```bash
dotnet add package Microsoft.EntityFrameworkCore.Design
```

<br>

> Dar um restore na aplicação para ler e aplicar os pacotes instalados:
```bash
dotnet restore
```

<br>

> Testar se o EF está ok
```bash
dotnet ef
```

<br>

> Criar os Models à partir da sua base de Dados
    :point_right: -o = criar o diretorio caso não exista
    :point_right: -d = Incluir as DataNotations do banco
```bash
dotnet ef dbcontext scaffold "Server=DESKTOP-XVGT587\SQLEXPRESS;Database=Gufos;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -d
```

## Controllers

> Criamos nosso primeiro Controller: CategoriaController
> Herdamos nosso novo controller de ControllerBase
> Definimos a "rota" da API logo em cima do nome da classe, utilizando:
- [Route("api/[controller]")]
> Logo abaixo dizemos que é um controller de API, utilizando:
- [ApiController]
<br>

> Damos **CTRL + .** para incluir:

```bash
using Microsoft.AspNetCore.Mvc;
```
